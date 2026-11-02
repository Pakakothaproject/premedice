# API and MCP

**Status:** Preview
**Base URL:** `https://api.premedice.com`
**Canonical model ID:** `Premedice/Premed-4`

---

## Authentication

```http
Authorization: Bearer pm_live_YOUR_KEY
```

Never publish a live key.

## Chat completions

```bash
curl https://api.premedice.com/v1/chat/completions \
  -H "Authorization: Bearer pm_live_YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "Premedice/Premed-4",
    "messages": [
      {
        "role": "user",
        "content": "Explain these lab results and flag what should be discussed with a clinician."
      }
    ]
  }'
```

## Other endpoints

| Endpoint | Purpose |
|---|---|
| `GET /v1/models` | List the public model ID |
| `POST /v1/labs/interpret` | Structured lab-panel interpretation |
| `POST /v1/evidence/verify` | Verify a claim against supplied sources |
| `POST /mcp` | MCP server for other AI clients |

## MCP

The MCP endpoint uses the same `pm_live_*` key. It exposes Premedice as a medical research tool for compatible clients.

## Preview limits

- No public uptime SLA.
- No public latency SLA.
- Rate limits and response fields may change.
- Do not use the API for emergency care.
- Do not send PHI without an appropriate agreement.
- The API is decision support, not a diagnosis.

## Documentation

- [Premedice 4 API docs](https://github.com/Pakakothaproject/premedice-4/blob/main/docs/api.md)
- [Premedice 4 model card](https://github.com/Pakakothaproject/premedice-4/blob/main/docs/model-card.md)
- [Reliability](https://github.com/Pakakothaproject/premedice-4/blob/main/docs/reliability.md)
