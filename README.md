<div align="center">
  <img src="assets/banner.svg" alt="Premedice — evidence-first AI medical assistant" width="100%" />
</div>

<h1 align="center">Premedice</h1>

<p align="center">
  <strong>Evidence-first AI medical assistant for patients and doctors.</strong><br/>
  Reads labs, medicines, documents and health history together — then shows its sources.
</p>

<p align="center">
  <a href="https://premedice.com">Website</a> ·
  <a href="https://app.premedice.com">Open the app</a> ·
  <a href="https://premedice.com/doctors/">For clinicians</a> ·
  <a href="https://api.premedice.com">API</a> ·
  <a href="https://premedice.com/news/">Research</a>
</p>

<p align="center">
  <img alt="Status: beta" src="https://img.shields.io/badge/status-beta-00C853" />
  <img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-blue" />
  <img alt="Domain: medical" src="https://img.shields.io/badge/domain-medical%20AI-0B0B0B" />
  <img alt="Sources: cited" src="https://img.shields.io/badge/answers-cited-00FF66" />
</p>

---

## Why Premedice?

Most health tools answer one question at a time. Premedice reads the **whole record**:

- lab results and trends,
- prescriptions and medication history,
- uploaded documents and reports,
- symptoms and observations,
- the patient's own questions.

Then it returns a plain-English answer with sources, keeps the record over time, and gives clinicians a workspace to review the same evidence.

That is the difference between a chat demo and a clinical workflow.

---

## What it does

| Capability | What you get |
|---|---|
| **Whole-record chat** | Ask about symptoms, labs, medicines or a report with the patient's own context |
| **Lab interpretation** | 50+ markers, reference-range checks, trends and plain-English explanations |
| **Medication safety** | Drug-interaction review, CPIC/ClinPGx pharmacogenomics, renal/hepatic calculators |
| **Rare disease** | HPO/Orphanet/MONDO phenotype matching with OMIM and Orphanet references |
| **Evidence layer** | Source capture, citation chips, evidence verification and explicit uncertainty |
| **Clinician workspace** | Patient links, notes, access logs, SOAP notes, referrals and doctor-ready summaries |
| **Medicine cards** | Related options with class, cautions and tiny DailyMed label images |
| **API** | OpenAI-compatible medical reasoning API for builders and agents |

### Product surfaces

- **Patients and caregivers** — [app.premedice.com](https://app.premedice.com)
- **Clinicians** — [premedice.com/doctors](https://premedice.com/doctors/)
- **Developers** — [api.premedice.com](https://api.premedice.com)
- **Model page** — [Premedice 4](https://github.com/Pakakothaproject/premedice-4)

---

## Inside the workspace

<p align="center">
  <img src="https://premedice.com/og-home.jpg" alt="Premedice homepage and AI medical assistant" width="900" />
</p>

<p align="center">
  <img src="https://premedice.com/atlas/human-atlas-poster.jpg" alt="Premedice Human Atlas" width="900" />
</p>

---

## The clinical toolbelt

Premedice does not just generate prose. It runs clinical tools.

| Layer | Examples |
|---|---|
| **Literature** | PubMed, Europe PMC, Cochrane, medRxiv/bioRxiv, Crossref |
| **Drug data** | OpenFDA, RxNorm, DailyMed, DrugBank (licensed key), CPIC |
| **Pharmacogenomics** | CPIC and ClinPGx/PharmGKB annotations |
| **Trials and targets** | ClinicalTrials.gov, Open Targets, ChEMBL, PubChem |
| **Rare disease** | HPO, Orphanet, MONDO via Monarch |
| **Structure** | AlphaFold protein structure and confidence |
| **Calculators** | Wells, CHA₂DS₂-VASc, HAS-BLED, CURB-65, eGFR, Cockcroft-Gault, Child-Pugh, MELD |
| **Workflow** | SOAP notes, referrals, patient summaries, health briefs |

---

## API

Premedice exposes an OpenAI-compatible endpoint for patient-education, lab-interpretation and clinical-reasoning products.

```bash
curl https://api.premedice.com/v1/chat/completions \
  -H "Authorization: Bearer pm_live_YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "premedice-1",
    "messages": [
      {"role": "user", "content": "Explain these lab results and flag what should be discussed with a clinician."}
    ]
  }'
```

See the API documentation at [api.premedice.com](https://api.premedice.com) and the [Premedice 1 docs](docs/premedice-1/).

---

## What Premedice is not

- Not a diagnosis.
- Not a replacement for a doctor.
- Not a telehealth or prescribing service.
- Not an emergency service.
- Not a guarantee of accuracy.

Clinical decisions belong with a qualified clinician who knows the patient's history. Emergency symptoms require local emergency services.

---

## Status

Premedice is in active beta. Features, model routing and API limits may change. The production app source is private; this repository is the public product and API overview.

---

## License

MIT for this documentation repository. The Premedice name, logo and product marks remain the property of Diagnostics AI, Inc.

See [LICENSE](LICENSE).
