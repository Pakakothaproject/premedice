# Lab Memory

**Status:** v1, preview
**Last updated:** 3 November 2026

---

## What it does

Lab Memory turns uploaded lab reports into a longitudinal record.

- Parses analyte, value, unit, reference range, lab name, and collection date.
- Normalizes common SI and conventional units.
- Groups results by analyte.
- Computes change, percent change, and slope.
- Flags high and low values against the range printed on the report.
- Injects the latest values into the chat context.
- Shows the same trend view in the clinician patient record.

## Supported today

- 54 common analytes across CBC, CMP, lipids, thyroid, diabetes, vitamins, iron, inflammation, kidney, and other panels.
- Unit conversion for common SI/conventional pairs.
- Trend API and SVG chart.
- Parser tests: 12/12 passing.

## Current limits

- Clean-text reports are the strongest path. Real-world PDFs and scans still need more coverage.
- Critical thresholds (`critical_high`, `critical_low`) are not complete.
- Backfill for older documents, deduplication, and manual correction are incomplete.
- Reference ranges by lab, age, and sex are not complete.
- Country-specific ranges and localization are not built.
- The analyte dictionary is below the planned 150.
- No published clinical validation of lab interpretation.

## Safety boundary

Lab Memory explains and trends values. It does not diagnose, prescribe, or replace a clinician. Every result should be checked against the source report and the patient's clinical context.

## Acceptance target

- 90% of CBC/CMP/lipid/thyroid panels map at least 80% of values.
- A 20-report trend loads in under 1 second.
- Zero cross-context leakage.
- Critical flags are clinician-reviewed before release.

These are targets. They are not yet published results.
