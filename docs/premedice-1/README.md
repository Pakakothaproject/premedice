# Premedice 1

**The medical AI orchestrator.** One model, every authoritative medical source behind it.

Premedice 1 is a medical reasoning system that doesn't try to memorize medicine. It's a **dual-model orchestrator** — two state-of-the-art models working in concert behind a single API:

- **Gemini 3.7 Flash** does the high-volume work: tool routing, source selection, and the initial draft.
- **Claude Opus 5** does the deep clinical reasoning, verification, and final quality pass.

Around the models, Premedice 1 orchestrates live calls to the sources clinicians and researchers actually trust — peer-reviewed literature, drug databases, clinical trial registries, drug labels — and grounds every answer in what they return. One model drafts from the sources, the other verifies the draft. You ask the question. Premedice 1 picks the right tools, runs them, and synthesizes the result.

There are two ways to use it:

- **[Premedice Web](https://app.premedice.com)** — A consumer chat product for people who want better answers about their health, fast. No setup, no API keys, no jargon.
- **Premedice 1 API** — An OpenAI-compatible API for developers building medical features, clinical tools, research assistants, or anything that needs grounded medical reasoning at scale. **This repo documents the API.**

Both surfaces use the same packages, the same credits, and the same model. Top up once, spend it in either place.

**Premedice is a paid product with a free tier.** Three packages fund both the web app and the API from a single balance:

| Package | Price | What you get |
|---|---|---|
| **Free** | $0 | $0.10 starting credit. Lightweight model. Basic health conversations and symptom checks. |
| **Pro** | **$5.99** / month (or $5 one-time, credits never expire) | Recommended model. Priority responses, unlimited symptom analysis, lab report parsing, medication reference, clinical knowledge databases. |
| **Advanced** | **$20** / month | Most powerful model. Everything in Pro, plus patient longitudinal timeline analytics, multi-language clinical reports, 24/7 WhatsApp support. |

[See full pricing →](docs/pricing.md)

---

## Why an orchestrator?

Medical knowledge moves faster than any single model can be retrained. Drug recalls, new trial readouts, label updates, post-market safety signals — a model frozen at training time is a liability in a clinical context.

Premedice 1 treats the model as the *reasoning layer*, not the *knowledge layer*. When you ask it a question, it plans which authoritative sources are needed, calls them in parallel where it can, and weaves the evidence into a single, sourced answer. Every claim is traceable back to a tool result.

You call one model. Behind it, the orchestrator reaches into a mix of Premedice-curated channels and non-proprietary public sources. Below are some of the non-proprietary channels Premedice 1 draws from — these are not the complete picture:

| Source | What it provides |
|---|---|
| **PubMed** | 35M+ peer-reviewed biomedical abstracts (NCBI/NLM) |
| **OpenFDA** | Drug adverse events, recalls, labels, device data (US FDA) |
| **RxNorm** | Standardized medication naming and interactions (NIH) |
| **DailyMed** | FDA-approved drug labels (NIH/NLM) |
| **ClinicalTrials.gov** | Active and completed clinical trials worldwide |
| **Semantic Scholar** | 200M+ academic papers across science |
| **Europe PMC** | European life-sciences literature (EMBL-EBI) |
| **create_file** | Persistent artifacts — citations, summaries, exports |

The full set of channels is broader than this and includes proprietary Premedice-curated sources we don't enumerate publicly. The non-proprietary channels above are loaded on every request, automatically — you don't enable them, you don't pay per-tool. The orchestration is the model.

---

## Two surfaces, one model

### Web — for people
[app.premedice.com](https://app.premedice.com) is the consumer product. Sign in, ask a question, get a grounded answer. The orchestrator runs invisibly. Best for: patients researching a condition, caregivers, students, anyone who wants a trustworthy second opinion.

### API — for builders
This repo. One endpoint, OpenAI-compatible. Drop it into any OpenAI SDK, point at `https://api.premedice.com/v1`, pass an API key, ship. Best for: telehealth apps, clinical decision support, research tools, EHR integrations, anything medical that needs to be defensible.

---

## Quickstart (60 seconds)

```bash
curl https://api.premedice.com/v1/chat/completions \
  -H "Authorization: Bearer pm_live_YOUR_KEY_HERE" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "premedice-1",
    "messages": [
      {"role": "user", "content": "What are the current first-line treatments for type 2 diabetes, and what does the latest evidence say about GLP-1 agonists?"}
    ]
  }'
```

The response is a normal OpenAI-style chat completion. Tool calls happen server-side. You get one streamed answer with citations in the text.

More examples and SDK snippets: [`docs/getting-started.md`](docs/getting-started.md)

---

## What's in this repo

| Document | What it covers |
|---|---|
| [`docs/getting-started.md`](docs/getting-started.md) | Get an API key, make your first call, auth basics |
| [`docs/api-reference.md`](docs/api-reference.md) | Endpoints, request/response shapes, streaming, error codes |
| [`docs/models/premedice-1.md`](docs/models/premedice-1.md) | Capabilities, context window, rate limits, what the model does and doesn't do |
| [`docs/tools.md`](docs/tools.md) | The non-proprietary public channels Premedice 1 draws from, with what each is for |
| [`docs/architecture.md`](docs/architecture.md) | How the orchestrator works under the hood — the planning loop, grounding, and how sources get combined |
| [`docs/pricing.md`](docs/pricing.md) | The in-app packages that fund both surfaces, free credit on signup, billing model |

---

## Important: not a medical device

Premedice 1 is an information tool, not a diagnostic or treatment device. Outputs may be incomplete, outdated, or wrong. Always verify clinical decisions with a licensed professional and primary sources. Premedice 1 cites what it returns — read the citations.

---

## License

MIT. See [LICENSE](LICENSE).
