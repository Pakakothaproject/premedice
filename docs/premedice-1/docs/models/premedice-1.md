# Premedice 1

The model. One identifier, all of Premedice's medical reasoning behind it.

```json
{ "model": "premedice-1" }
```

---

## What it is

Premedice 1 is a **dual-model medical orchestrator** — a coordinated composition of two state-of-the-art language models, working together behind a single API:

- **Gemini 3.7 Flash** — handles tool routing, source selection, and the initial draft synthesis. Fast, high-volume, optimized for picking the right tools and assembling the first version of the answer.
- **Claude Opus 5** — handles deep clinical reasoning, answer verification, and the final-pass quality check. Slower and more expensive, but it catches what the first pass misses and grounds the answer in rigorous clinical judgment.

You never see the seam between them. You call `/v1/chat/completions` with `model: "premedice-1"` and get one answer. Inside, the two models are running a coordinated loop: one plans, one critiques, they converge on a final response.

Behind the models, the orchestrator reaches into a mix of Premedice-curated channels and non-proprietary public sources — PubMed, OpenFDA, RxNorm, DailyMed, ClinicalTrials.gov, Semantic Scholar, Europe PMC, and a persistent file layer are some of the public ones. The full set of channels is broader than this list. See [tools.md](../tools.md) for the public channels we enumerate.

This design is deliberate. Medicine changes faster than any single model can be retrained. A composition that *retrieves* against authoritative sources — and cross-checks itself with a second model — stays current by construction, and stays rigorous by composition.

---

## What it's good at

- **Clinical question answering** — "What's the first-line treatment for X in a patient with Y?"
- **Drug information** — mechanisms, indications, interactions, adverse events, label changes, recalls
- **Literature synthesis** — summarizing a body of evidence across PubMed, Europe PMC, Semantic Scholar
- **Trial lookup and analysis** — finding active trials, eligibility criteria, results
- **Differential reasoning** — comparing treatment options, weighing guideline-recommended vs off-label
- **Patient education drafts** — translating clinical language for non-specialists
- **Medical writing support** — drafting notes, summaries, citations, references

---

## What it's not for

- **Diagnosis or treatment decisions.** Premedice 1 is an information tool. Outputs are not a substitute for a licensed clinician.
- **Real-time clinical monitoring.** No live device feeds, no EHR streaming.
- **Image or signal interpretation.** Text in, text out. No DICOM, no ECG, no imaging.
- **Bulk document review at scale.** Useful for summarizing a paper, not for processing a million charts.

If you need those, build a layer on top of the API or talk to us about enterprise features.

---

## Specifications

| Property | Value |
|---|---|
| **Model ID** | `premedice-1` |
| **Owner** | `premedice` |
| **Architecture** | Dual-model orchestrator (Gemini 3.7 Flash + Claude Opus 5) |
| **Channel layer** | Mix of non-proprietary public sources (PubMed, OpenFDA, RxNorm, DailyMed, ClinicalTrials.gov, Semantic Scholar, Europe PMC) and Premedice-curated channels. Always-on, server-side. |
| **Context window** | 128K tokens (input + output combined) |
| **Max output** | 16K tokens per completion |
| **Default temperature** | 0.2 (low — tuned for grounded answers) |
| **Streaming** | Yes (Server-Sent Events, OpenAI format) |
| **Function calling (client-side)** | Not exposed. Tool orchestration is server-side and automatic |
| **Modalities** | Text in, text out |
| **Languages** | English (primary). Other major languages work but citation coverage is best in English |

---

## Limits (default plan)

- **60 requests/minute**
- **1,000,000 tokens/day**
- **10 MB request body**

Higher limits are available — see your dashboard or contact sales.

---

## How it handles citations

Premedice 1 is built to ground every claim. In the response, it:

- Names the source (e.g. "According to the FDA label for semaglutide…")
- Inlines identifiers where possible (NCT IDs for trials, PMIDs for papers, RxCUI for drugs)
- Distinguishes what came from a tool result from what is general medical knowledge

If a question cannot be answered from the available sources, Premedice 1 says so. It does not fabricate references.

The dual-model composition is what makes the citation discipline reliable. Claude Opus 5's verification pass specifically checks that every cited claim is actually present in the tool output — so an unsourced claim in the draft gets caught before it reaches you.

For now, citations are inline in the text. A future release will expose them as a structured `citations` field in the response.

---

## Prompting tips

Premedice 1 is tuned for grounded answers. A few patterns that work well:

**Be specific about the patient context, if relevant.**
```
What antihypertensive would you start in a 67-year-old with stage 3 CKD,
eGFR 38, and a history of hyperkalemia on lisinopril?
```

**Ask for the level of evidence you want.**
```
Summarize the evidence for metformin in PCOS. I want RCTs and meta-analyses,
not case reports.
```

**Ask it to flag uncertainty.**
```
Compare GLP-1 agonists and SGLT2 inhibitors for cardiovascular outcomes in
diabetic patients. Flag where the evidence is weak or conflicting.
```

**Ask it to plan a search, not just answer.**
```
I'm trying to figure out if there's evidence for using low-dose naltrexone
in Long Covid. What sources would you check, and what do they say?
```

**Don't** ask it to be a doctor. Frame clinical questions as questions to research, not questions to diagnose.

---

## Versioning

`premedice-1` is a rolling identifier — it always points to the latest production composition. When we ship a breaking change to behavior, we publish a new model ID (`premedice-1.5`, `premedice-2`, etc.) and keep `premedice-1` online for a deprecation window. You'll get at least 90 days' notice before any retirements.

`GET /v1/models` always returns the current set of available IDs.
