# API Reference

Base URL: **`https://api.premedice.com/v1`**

The Premedice 1 API is fully OpenAI-compatible. If you know the OpenAI Chat Completions API, you already know 95% of this.

---

## Authentication

All requests require a Bearer token in the `Authorization` header.

```
Authorization: Bearer pm_live_YOUR_KEY
```

Keys are scoped to a single account, can be named and rotated from the dashboard, and can be revoked instantly. See [getting-started.md](getting-started.md#1-create-an-account-and-get-an-api-key) for the full key lifecycle.

---

## Endpoints

### `POST /v1/chat/completions`

Create a model response. Supports streaming.

#### Request body

| Field | Type | Required | Description |
|---|---|---|---|
| `model` | string | Yes | Must be `"premedice-1"` for the orchestrator model |
| `messages` | array | Yes | Standard OpenAI messages array. Roles: `system`, `user`, `assistant`, `tool` |
| `stream` | boolean | No | `true` for SSE streaming, `false` (default) for a single JSON response |
| `temperature` | number | No | `0.0` – `2.0`. Default `0.2` (Premedice 1 is tuned for grounded, low-variance medical answers — higher values increase hallucination risk) |
| `max_tokens` | integer | No | Cap on completion tokens. Default: model's max |
| `top_p` | number | No | `0.0` – `1.0`. Default `1.0` |
| `stop` | string \| array | No | Stop sequences |
| `user` | string | No | End-user identifier for abuse tracking. Recommend a hashed user ID, not raw PII |

Other OpenAI-compatible fields are accepted and ignored.

#### Response (non-streaming)

Standard OpenAI ChatCompletion object. `model` returns `"premedice-1"`. `usage` reports `prompt_tokens`, `completion_tokens`, and `total_tokens`.

#### Response (streaming)

Server-Sent Events, OpenAI `chat.completion.chunk` format. `data: [DONE]` terminates the stream.

---

### `GET /v1/models`

List available models.

```bash
curl https://api.premedice.com/v1/models \
  -H "Authorization: Bearer pm_live_YOUR_KEY"
```

Response:

```json
{
  "object": "list",
  "data": [
    {
      "id": "premedice-1",
      "object": "model",
      "created": 1737000000,
      "owned_by": "premedice"
    }
  ]
}
```

---

## Health & status

- **`GET https://api.premedice.com/v1/health`** — `200` if the API is serving. No auth required.
- **[status.premedice.com](https://status.premedice.com)** — live incident history, scheduled maintenance, per-component uptime.

---

## Error format

Errors follow the OpenAI error shape:

```json
{
  "error": {
    "message": "Invalid API key",
    "type": "invalid_request_error",
    "param": null,
    "code": "invalid_api_key"
  }
}
```

### Status codes

| Code | Type | Meaning |
|---|---|---|
| `400` | `invalid_request_error` | Malformed payload, missing required field, unknown parameter |
| `401` | `invalid_request_error` | Missing or invalid API key |
| `402` | `billing_error` | Account balance is zero or below the minimum |
| `403` | `permission_error` | Key does not have access to the requested model or feature |
| `404` | `not_found_error` | Unknown model, route, or resource |
| `413` | `invalid_request_error` | Payload too large (request or context window exceeded) |
| `429` | `rate_limit_error` | Per-minute or per-day rate limit hit. Response includes `Retry-After` header |
| `500` | `server_error` | Something on our side failed. Safe to retry |
| `502` / `503` / `504` | `server_error` | Upstream or infrastructure issue. Retry with backoff |
| `529` | `overloaded_error` | All capacity in use. Back off and retry |

---

## Limits

- **Request body**: 10 MB
- **Context window**: 128K tokens (input + output combined)
- **Max output**: 16K tokens per completion
- **Per-minute rate limit**: 60 requests/minute on the default plan. Higher tiers available — see your dashboard
- **Per-day token cap**: 1M tokens/day on the default plan. Configurable per account

For higher limits, contact sales through the dashboard.

---

## SDK compatibility

Works out of the box with:

- **`openai`** (Python, Node, .NET, Go, Java, Ruby, PHP, Rust) — point `base_url` at `https://api.premedice.com/v1`
- **`langchain`** — use `ChatOpenAI` with a custom `base_url`
- **`llama-index`** — same, OpenAI-compatible connector
- **Cursor / opencode / Continue.dev / Cline** — set the API base URL and key in the provider config
- **Anything that speaks OpenAI's `/v1/chat/completions` shape**

Tools are server-side. You do **not** need to define a `tools` array, handle `tool_calls`, or manage function-calling schemas in your client. If you do pass a `tools` array, it will be ignored.
