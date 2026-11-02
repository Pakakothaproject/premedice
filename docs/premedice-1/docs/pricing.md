# Pricing

The Premedice 1 API uses the same packages as the Premedice web app. **One wallet, one credit balance, two surfaces.** The credits you buy for in-app chat also cover API calls.

There is no separate API rate card, no per-token rate on this page, and no second invoice. Buy credits in the dashboard, spend them wherever you call Premedice.

---

## How billing works

1. **Top up** your account in the Premedice dashboard. The dollar value lands as a `token_balance`.
2. **Both surfaces spend from the same balance.** In-app chat and the Premedice 1 API draw against the same credits, at the same rates.
3. **Usage is metered per call** based on the model used and the tokens consumed. The `usage` field on every API response shows what each call cost.
4. **One invoice, one dashboard.** Manage everything from a single place.

[Open the dashboard →](https://app.premedice.com/dashboard/settings/billing)

---

## The three packages

The three packages shown below are the same packages you see at [premedice.com](https://premedice.com) and inside the app. The API has no plans of its own — it inherits the one you buy.

| Package | Monthly | Yearly | One-time | What you get |
|---|---|---|---|---|
| **Free** | $0 | — | — | $0.10 starting credit. Lightweight model. Basic health conversations, symptom checking. |
| **Pro** | **$5.99** | **$49.99** | **$5** (credits never expire) | Recommended model. Priority responses, unlimited symptom analysis, lab report parsing, medication reference, clinical knowledge databases. |
| **Advanced** | **$20** | **$169.99** | — | Most powerful model. Everything in Pro, plus patient longitudinal timeline analytics, multi-language clinical reports, 24/7 WhatsApp support, early-access features. |

> **Current rates are always shown live in the dashboard.** This page is informational; the in-app pricing card is the source of truth. Yearly billing saves ~30%.

---

## What changes between tiers

The three tiers don't change your usage limits or your rate limits. They change **which model powers your calls** — and therefore the per-interaction cost.

| Package | Cost multiplier | Model |
|---|---|---|
| Free | **1x** | Lightweight model — fast, low cost, best for simple questions |
| Pro | **3x** | Recommended model — significantly better responses |
| Advanced | **10x** | Most powerful model — best-in-class accuracy for complex cases |

A 10-message medical Q&A session costs roughly the same whether you came through the web app or the API. The `usage` field on every API response gives you the exact number.

---

## Free trial

Every account on the Free package starts with a **$0.10 starting credit**, applied automatically. It does not expire. If you upgrade to Pro or Advanced, additional credits are added to the same balance — they don't replace the starting credit.

---

## Why one wallet

The API is not a separate product with separate pricing — it's the same model, served over a different transport. The credits you already trust for in-app work the same way over HTTPS. One invoice, one usage dashboard, one place to manage your balance. No chance of two different totals at the end of the month.

---

## No-show, no-charge

Calls that error before any tokens are generated (auth failure, malformed request, balance zero) are not billed. Calls that produce at least one completion token are billed for the full input + output.

---

## How to top up

1. Sign in at **[app.premedice.com](https://app.premedice.com)**.
2. Open **Settings → Billing**.
3. Pick a package and a billing cycle.
4. Pay with card. Credits land in your balance within seconds.

Once your balance is loaded, your existing API key can spend against it. No re-authentication, no code changes, no new endpoint.

---

## Need higher limits?

The default API rate limits (60 requests/min, 1,000,000 tokens/day) apply to every account. For higher limits — institutional use, high-volume research, embedded products — contact us through the dashboard. Custom limits are configured per account, not per package.
