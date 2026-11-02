# API and MCP

**Status:** Beta
**Base URL:** `https://api.premedice.com`
**Model ID:** `Premedice/Premed-4`

---

## Authentication

```http
Authorization: Bearer pm_live_YOUR_KEY
```

Keep live keys private.

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
| `POST /v1/labs/interpret` | Lab-panel interpretation |
| `POST /v1/evidence/verify` | Verify a claim against supplied sources |
| `POST /mcp` | MCP server for AI clients |

## MCP

The MCP endpoint uses the same `pm_live_*` key. It lets compatible AI clients delegate medical research to Premedice.

## Usage

- Use a key with the appropriate plan.
- Rate limits apply per key.
- Do not use the API for emergency care.
- Do not send PHI without an appropriate agreement.
- Clinical decisions remain with a licensed professional.

## Documentation

- [Premedice 4 API docs](https://github.com/Pakakothaproject/premedice-4/blob/main/docs/api.md)
- [Premedice 4 model card](https://github.com/Pakakothaproject/premedice-4/blob/main/docs/model-card.md)
