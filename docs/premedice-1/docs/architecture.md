# Architecture

How Premedice 1 works — the parts that matter if you're integrating the API or reasoning about its behavior.

This page is intentionally high-level. It explains the *why* and the *what*, not the implementation. To integrate, you don't need to know any of this — just call the API.

---

## What Premedice 1 actually is

Premedice 1 is a **dual-model medical orchestrator**. It is not one large language model. It is two state-of-the-art models, working in concert, behind a single API:

- **Gemini 3.7 Flash** — handles tool routing, source selection, fast iteration, and the initial draft synthesis.
- **Claude Opus 5** — handles deep clinical reasoning, answer verification, and the final-pass quality check.

You never see the seam between them. You send one question to `/v1/chat/completions`; you get one answer. But inside, the two models are running a coordinated loop: one plans, one critiques, they converge on a final response.

Behind the models sits a broader orchestration layer that draws from both Premedice-curated channels and non-proprietary public sources — including PubMed, OpenFDA, RxNorm, DailyMed, ClinicalTrials.gov, Semantic Scholar, Europe PMC, and a persistent file layer. Those public ones are not the complete picture; the full set of channels is broader than what we enumerate. The models reach into whatever combination of channels the question requires, in real time.

```
                       ┌────────────────────────────────────┐
                       │                                    │
                       │       Premedice 1                  │
                       │     (the orchestrator)             │
                       │                                    │
   you (the client)    │   ┌──────────────────────────┐     │   authoritative sources
                       │   │                          │     │
   POST a question ──▶ │   │   Gemini 3.7 Flash       │     │──▶ PubMed
                       │   │   ─ tool routing         │     │──▶ OpenFDA
                       │   │   ─ source selection     │     │──▶ RxNorm
                       │   │   ─ initial draft        │     │──▶ DailyMed
                       │   │                          │     │──▶ ClinicalTrials.gov
                       │   │   Claude Opus 5          │     │──▶ Semantic Scholar
                       │   │   ─ clinical reasoning   │     │──▶ Europe PMC
                       │   │   ─ verification         │     │──▶ create_file
                       │   │   ─ final quality pass   │     │
                       │   │                          │     │
   ◀── one answer ─────│   │   synthesis + citations  │     │
                       │   └──────────────────────────┘     │
                       │                                    │
                       └────────────────────────────────────┘
```

---

## Why two models, not one

A single model — even a very good one — has the same structural problem in medicine: it has to be excellent at everything, and excellence in clinical reasoning is not the same skill as excellence in fast tool routing.

By composing two models with different strengths, Premedice 1 gets:

- **Speed without sacrificing rigor.** Gemini 3.7 Flash does the high-volume work — picking which sources to call, in what order, with what query — quickly and cheaply. Claude Opus 5 does the slower, deeper clinical reasoning on top.
- **Cross-verification by default.** The answer you receive has been read and stress-tested by a second model. If the two disagree, the orchestrator surfaces the disagreement rather than picking silently.
- **Grounding in current evidence.** Neither model is asked to recall medical facts from its weights when the facts could be retrieved live from a primary source. The sources are the source of truth; the models are the readers.

This is the design decision behind Premedice 1. The orchestrator pattern — two models, many channels (public and proprietary), one answer — is the product.

---

## The orchestrator loop

When Premedice 1 receives a question, it runs a four-step loop, with both models participating at each step:

### 1. Intent read
The first pass classifies what the question is actually asking. Drug interaction, mechanism, trial lookup, guideline lookup, differential comparison, patient education. Both models weigh in on the intent read; disagreements are reconciled before moving on.

### 2. Plan
The orchestrator decides which tools to call. Gemini 3.7 Flash drafts the plan — it's faster at this and good at parallelizing independent calls. Claude Opus 5 reviews the plan for completeness and flags any tool that should be added (for example, a source the first pass missed, or a label check that wasn't in the original plan).

### 3. Execute
The chosen tools are called. Each tool runs against its authoritative source. Results are normalized into a common format both models can read.

### 4. Synthesize and verify
Gemini 3.7 Flash drafts the answer from the tool results. Claude Opus 5 then does a verification pass — checking that every cited claim is actually present in the tool output, that the synthesis doesn't introduce unsupported claims, and that the clinical reasoning is sound. The final answer reflects both passes.

If the two models disagree about a claim, the orchestrator does not pick one. It either re-runs the disputed tool, surfaces the disagreement in the answer, or — when neither model can resolve it — explicitly says "the evidence is mixed" rather than guessing.

---

## What the orchestrator is not

- **Not a search engine.** It doesn't return raw results. It synthesizes.
- **Not a RAG system.** There's no pre-indexed vector store that the models pull from. Every request hits the live sources.
- **Not agentic in the autonomous sense.** It plans once, executes, and answers. It doesn't loop until it "thinks" it has enough. (A future version may.)
- **Not transparent at the tool-call level.** The models choose tools internally. You don't see which was called, in what order, or which model produced which part of the answer. You only see the cited answer.

---

## Trade-offs and limits

### Latency
A dual-model orchestration is slower than a single-model call. Most questions complete in **3–10 seconds**. Complex questions that require multiple tool calls and full verification can take up to **30 seconds**. Streaming gives you partial output as the synthesis lands.

### Source coverage gaps
Not every medical question is well-covered by the public sources. Rare diseases, very recent developments, and non-English literature can have thin coverage. Premedice 1 flags this rather than papering over it.

### The models are still the bottleneck
A bad plan from the orchestrator means a bad answer, even with good sources behind it. Premedice 1 is tuned for grounded, low-variance medical answers (`temperature: 0.2` by default). It is conservative — when uncertain, it says so. It is not creative. It is not a brainstorming partner. It is a medical research assistant.

### No live data
The tools query the sources at the time of the request. Premedice 1 does not maintain a continuous index, and it does not push updates. The freshness of every answer is the freshness of the sources at the moment of the call.

---

## What you do vs what Premedice 1 does

| You do | Premedice 1 does |
|---|---|
| Send a well-formed question | Read the question's intent |
| Pass an API key | Authenticate and bill your account |
| (Optionally) set temperature, max_tokens | Pick the right tools |
| Handle the streamed response | Call the tools, in parallel where possible |
| Display the answer to your end user | Read the tool results, draft an answer, verify the draft, synthesize, cite the sources |
| (Optionally) retrieve files from `create_file` | Write structured artifacts when the answer calls for them |
| Stay inside the rate limits | Enforce the rate limits |

That's the whole contract. The dual-model orchestrator lives on our side of the API.
