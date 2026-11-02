# Security Policy

**Last updated:** 3 November 2026

---

## Reporting a vulnerability

Use GitHub private vulnerability reporting for this repository. Do not open a public issue for a security problem.

Include:

- affected surface or endpoint
- reproduction steps
- impact
- whether PHI or credentials may be exposed
- a redacted example if useful

Do not include:

- live API keys
- patient identifiers
- full patient records
- production database dumps

## Scope

In scope:

- authentication and API-key handling
- cross-context or cross-patient access
- doctor-link scope, expiry, or revocation failures
- prompt injection that changes tool behavior
- PHI in logs
- evidence or citation spoofing
- unsafe emergency handling

## Disclosure

We will acknowledge a report, investigate it, and coordinate disclosure after a fix is available. Do not publish exploit details before coordination.
