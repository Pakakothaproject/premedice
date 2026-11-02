# Clinical Evaluation

**Status:** Protocol only — no results published
**Last updated:** 3 November 2026

---

## Position

Premedice has no published clinical accuracy benchmark.

We do not claim clinical validation, diagnostic accuracy, or superiority to a clinician. The product is decision support, not a diagnosis.

## What will be measured

- citation precision
- red-flag recall
- unsupported-claim rate
- lab mapping accuracy
- refusal correctness on out-of-scope requests
- time to first token and total latency
- cross-context leakage (must be zero)

## Method

The full protocol is maintained in the model repository:

[Premedice 4 — Clinical Evaluation Protocol](https://github.com/Pakakothaproject/premedice-4/blob/main/docs/evaluation.md)

It requires:

- 200–300 de-identified cases; if fewer, publish the smaller denominator as preliminary
- at least two independent clinician reviewers
- a clinician-authored reference standard
- a third adjudicator for disagreements
- published failures, not only successes
- a clear statement of what the evaluation does not prove

## Current status

| Item | Status |
|---|---|
| Protocol | Drafted |
| Case set | Not assembled |
| Clinical reviewers | Not recruited |
| Adjudication | Not started |
| Results | Not published |
| External validation | Not started |

## What this does not prove

Even a completed retrospective evaluation will not prove improved patient outcomes, safety superiority, or regulatory clearance. Those require prospective studies, governance, and regulatory review.

## Public structural harness

A structural smoke test is available in the model repository:

[Public evaluation harness](https://github.com/Pakakothaproject/premedice-4/tree/main/evaluation)

It checks response shape, sources, emergency language, uncertainty language, disclaimers, and latency. It is not clinical validation.
