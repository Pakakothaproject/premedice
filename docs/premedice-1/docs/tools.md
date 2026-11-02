# Non-proprietary public channels

Premedice 1 is an orchestrator. When you ask it a question, it decides which channels to draw from — public and proprietary — and weaves the results into one answer.

This page documents the **non-proprietary public channels** Premedice 1 uses. The full set of channels is broader and includes Premedice-curated sources we don't enumerate publicly; the public ones below are the ones we can name and link to.

**The public channels are loaded on every request. You don't enable them. You don't pay per-channel call. They're part of the model.**

---

## The non-proprietary public channels

### 1. PubMed
**Source:** U.S. National Library of Medicine (NCBI/NLM)
**What it is:** 35M+ citations for biomedical literature from MEDLINE, life-science journals, and online books.
**When Premedice 1 uses it:** Any question that needs published evidence — efficacy, safety, mechanism, epidemiology, case reports, RCTs, meta-analyses.
**Returns:** Abstracts, metadata, MeSH terms, publication dates, author lists, PubMed IDs (PMIDs).

### 2. OpenFDA
**Source:** U.S. Food and Drug Administration
**What it is:** Public datasets of FDA-regulated products — drugs, devices, foods, cosmetics.
**When Premedice 1 uses it:** Drug adverse events (FAERS), recalls, drug labels, device events, substance identification.
**Returns:** Structured records with counts, outcomes, dates, manufacturer info.

### 3. RxNorm
**Source:** U.S. National Library of Medicine
**What it is:** Standardized naming system for medications. Maps drug names across vocabularies to a common concept identifier (RxCUI).
**When Premedice 1 uses it:** Drug name disambiguation, ingredient lookup, dose-form identification, drug-class relationships, interaction checks.
**Returns:** RxCUI, ingredient names, brand names, dose forms, related concepts.

### 4. DailyMed
**Source:** U.S. National Library of Medicine
**What it is:** FDA-approved drug labels (the structured, versioned documents that come with every approved drug).
**When Premedice 1 uses it:** Anything that requires the official label — indications, dosage, contraindications, warnings, adverse reactions, drug interactions, use in specific populations.
**Returns:** Sections of the drug label, structured by section type.

### 5. ClinicalTrials.gov
**Source:** U.S. National Library of Medicine
**What it is:** Registry of clinical trials worldwide — privately and publicly funded.
**When Premedice 1 uses it:** Finding active or completed trials, eligibility criteria, study design, outcomes, results where posted.
**Returns:** Trial records with NCT IDs, status, conditions, interventions, locations, contact info.

### 6. Semantic Scholar
**Source:** Allen Institute for AI (AI2)
**What it is:** 200M+ academic papers across science, with AI-extracted citations, key claims, and influence metrics.
**When Premedice 1 uses it:** Broader scientific literature beyond biomedicine — computational biology, public health, psychology, AI-in-medicine.
**Returns:** Abstracts, citation graphs, highly-cited references, TLDR summaries.

### 7. Europe PMC
**Source:** European Bioinformatics Institute (EMBL-EBI)
**What it is:** Europe PubMed Central — life-sciences literature with a focus on European research, full-text preprints, and patent data.
**When Premedice 1 uses it:** European clinical guidelines, full-text access, preprints, patent-integrated literature.
**Returns:** Abstracts, full-text links, author manuscripts, patent citations.

### 8. create_file
**Source:** Premedice
**What it is:** A persistent file layer. Lets Premedice 1 write structured artifacts — citation lists, evidence tables, comparison sheets, summary documents — that you can retrieve.
**When Premedice 1 uses it:** When the answer is a list of references, a side-by-side comparison, or any structured artifact that benefits from being separate from the chat message.
**Returns:** A file ID and a URL to retrieve the artifact. The response tells you how to fetch it.

---

## How the orchestrator picks

You don't see the planning step, but it's there. The model treats channel selection as a reasoning problem. A few patterns:

- A question about drug adverse events → OpenFDA, DailyMed
- A question about a clinical guideline → PubMed, Europe PMC, DailyMed
- A question about an active trial → ClinicalTrials.gov, PubMed
- A question about off-label use → PubMed, Semantic Scholar, ClinicalTrials.gov
- A question that needs a structured artifact → create_file

It calls the needed channels **in parallel** when the question allows it, then synthesizes the returned evidence. If two sources disagree, the orchestrator surfaces the disagreement rather than picking one silently.

---

## What you don't have to do

- ❌ Define a `tools` array in your request
- ❌ Write function-calling schemas
- ❌ Handle `tool_calls` in the response
- ❌ Implement a tool-use loop
- ❌ Pay per tool call

Premedice 1 orchestrates. You call `/v1/chat/completions`, you get an answer.

If you do pass a `tools` array in your request, it's ignored — tool choice is the model's job.

---

## Tool data freshness

| Tool | Update cadence | Notes |
|---|---|---|
| PubMed | Daily | New citations appear within 24h of NLM indexing |
| OpenFDA | Daily | FAERS lags real-world reporting; not real-time |
| RxNorm | Monthly | NLM releases a new version monthly |
| DailyMed | Continuous | Updated as FDA approves label changes |
| ClinicalTrials.gov | Daily | New registrations and updates daily |
| Semantic Scholar | Weekly | New papers and citation updates weekly |
| Europe PMC | Daily | Mirrors PubMed + adds preprints and patents |
| create_file | Real-time | Written as part of the response |

---

## When a tool is unavailable

If a tool errors or times out, Premedice 1:

1. Falls back to the remaining tools.
2. Explicitly notes in the answer that one source was unavailable.
3. Does not fabricate results to fill the gap.

You will never see a citation to a source that didn't actually return.
