# Getting Started with the Premedice 1 API

This walks you from zero to a working API call in under five minutes.

---

## 1. Create an account and get an API key

1. Sign in at **[app.premedice.com](https://app.premedice.com)**.
2. Open **Settings → API Keys**.
3. Click **Generate new key**.
4. Give it a name (e.g. `prod-backend`, `local-dev`).
5. Copy the key. **It is shown once.** Store it in a secret manager immediately.

Keys look like this:

```
pm_live_a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6
```

The `pm_live_` prefix is intentional — it makes keys trivially identifiable in logs and secret scanners, and impossible to confuse with other services.

> **Treat API keys like passwords.** Anyone with your key can spend your balance. Rotate immediately if you suspect a leak from your dashboard.

---

## 2. Make your first call

The Premedice 1 API is OpenAI-compatible. Any OpenAI SDK, any HTTP client, any language — it just works.

### cURL

```bash
curl https://api.premedice.com/v1/chat/completions \
  -H "Authorization: Bearer pm_live_YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "premedice-1",
    "messages": [
      {"role": "user", "content": "Summarize the mechanism of action of semaglutide and list the most common adverse events reported in FAERS."}
    ]
  }'
```

### Python (OpenAI SDK)

```bash
pip install openai
```

```python
from openai import OpenAI

client = OpenAI(
    api_key="pm_live_YOUR_KEY",
    base_url="https://api.premedice.com/v1",
)

response = client.chat.completions.create(
    model="premedice-1",
    messages=[
        {"role": "user", "content": "Summarize the mechanism of action of semaglutide and list the most common adverse events reported in FAERS."}
    ],
)

print(response.choices[0].message.content)
```

### Node (OpenAI SDK)

```bash
npm install openai
```

```javascript
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "pm_live_YOUR_KEY",
  baseURL: "https://api.premedice.com/v1",
});

const response = await client.chat.completions.create({
  model: "premedice-1",
  messages: [
    { role: "user", content: "Summarize the mechanism of action of semaglutide and list the most common adverse events reported in FAERS." }
  ],
});

console.log(response.choices[0].message.content);
```

That's it. No separate tool registration, no function-calling schema, no extra config. The orchestrator is built in.

---

## 3. Streaming

For longer answers, stream the response token-by-token. Standard OpenAI SSE format.

```python
stream = client.chat.completions.create(
    model="premedice-1",
    messages=[{"role": "user", "content": "Compare first-line antihypertensives for a patient with stage 2 CKD."}],
    stream=True,
)

for chunk in stream:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="", flush=True)
```

---

## 4. Verify the call

A successful response looks like:

```json
{
  "id": "chatcmpl-...",
  "object": "chat.completion",
  "created": 1737000000,
  "model": "premedice-1",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "Semaglutide is a GLP-1 receptor agonist that..."
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 28,
    "completion_tokens": 412,
    "total_tokens": 440
  }
}
```

`usage.total_tokens` is what you pay for. See [pricing.md](pricing.md).

---

## 5. What to read next

- **[api-reference.md](api-reference.md)** — every endpoint, every parameter.
- **[models/premedice-1.md](models/premedice-1.md)** — what the model does well, what it doesn't, and its hard limits.
- **[tools.md](tools.md)** — the non-proprietary public channels Premedice 1 draws from (alongside proprietary ones we don't enumerate).
- **[architecture.md](architecture.md)** — how the orchestrator decides what to call and when.

---

## Common first-call failures

| Status | Meaning | Fix |
|---|---|---|
| `401 Unauthorized` | Key is missing, malformed, or revoked | Check the `pm_live_` prefix and that the key is active in your dashboard |
| `402 Payment Required` | Account balance is zero | Top up from Settings → Billing |
| `429 Too Many Requests` | Per-minute or per-day rate limit hit | See [models/premedice-1.md](models/premedice-1.md) for current limits; upgrade your plan or slow the call rate |
| `400 Bad Request` | Malformed payload | Compare to the [api-reference.md](api-reference.md) schema; the most common cause is forgetting `model: "premedice-1"` |
| `500 / 502 / 503` | Our side | Retry with exponential backoff; if it persists for more than a few minutes, check [status.premedice.com](https://status.premedice.com) |
