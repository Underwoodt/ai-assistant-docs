# Agent Service API Reference

**Port:** 8086
**Framework:** FastAPI (Python ≥ 3.13.7)
**Swagger UI:** `http://localhost:8086/docs`

> ✅ Verified against [ai-defra-search-agent README](https://github.com/DEFRA/ai-defra-search-agent#readme).
> 🔴 = corrected from previous version

---

## Endpoints

| Method | Path | Purpose | Status |
|---|---|---|---|
| POST | `/chat` | Submit a question to the AI assistant | ✅ Confirmed |
| POST | `/feedback` | Submit feedback on a response | ✅ Confirmed 🔴 |
| GET | `/conversations/{id}` | Retrieve a conversation by ID | ✅ Confirmed 🔴 |
| GET | `/health` | Health check | ✅ Confirmed |
| GET | `/docs` | Swagger / OpenAPI UI | ✅ Confirmed |

Source: [ai-defra-search-agent README — API Endpoints](https://github.com/DEFRA/ai-defra-search-agent#readme)

> 🔴 **Corrections from previous version:**
> - `POST /feedback` was marked "unverified" — it is confirmed in the Agent README.
> - `GET /conversations/{id}` was omitted entirely — the Agent README curl examples show it explicitly.

---

## POST /chat

Submit a question to the AI assistant.

### Request

```json
{
  "question": "What is the permitted development policy?",
  "model_id": "anthropic.claude-3-haiku"
}
```

> 🔴 **Correction:** The previous version used `"message"` and `"session_id"` as field names. The Agent README curl examples confirm the actual fields are `"question"` and `"model_id"`.
> Source: [ai-defra-search-agent README](https://github.com/DEFRA/ai-defra-search-agent#readme)

**Example curl (from source):**

```bash
curl -X POST http://localhost:8086/chat \
  -H "Content-Type: application/json" \
  -d '{"question": "What is AI?", "model_id": "anthropic.claude-3-haiku"}'
```

Source: [ai-defra-search-agent README](https://github.com/DEFRA/ai-defra-search-agent#readme)

### Response

```json
{
  "response": "string",
  "session_id": "string",
  "sources": ["string"]
}
```

> ⚠️ **UNVERIFIED:** The exact response schema above is not confirmed in source code. The README documents the request shape only. Check `http://localhost:8086/docs` for the live schema.

---

## GET /conversations/{id}

Retrieve a conversation by its ID.

**Example curl (from source):**

```bash
curl http://localhost:8086/conversations/<conversation_id>
```

Source: [ai-defra-search-agent README](https://github.com/DEFRA/ai-defra-search-agent#readme)

> ⚠️ **UNVERIFIED:** Full response schema not documented in README. Check Swagger at `http://localhost:8086/docs`.

---

## POST /feedback

Submit user feedback on an AI response.

> ✅ Confirmed endpoint — listed explicitly in the Agent README.
> ⚠️ Request/response schema not documented in README. Check Swagger at `http://localhost:8086/docs`.

Source: [ai-defra-search-agent README](https://github.com/DEFRA/ai-defra-search-agent#readme)

---

## GET /health

```json
{ "status": "ok" }
```

---

## Key Environment Variables

| Variable | Required | Default | Purpose |
|---|---|---|---|
| `AWS_REGION` | Yes | `eu-central-1` | AWS services region |
| `AWS_ACCESS_KEY_ID` | Yes | `test` (local) | AWS credentials |
| `AWS_SECRET_ACCESS_KEY` | Yes | `test` (local) | AWS credentials |
| `AWS_BEDROCK_DEFAULT_GENERATION_MODEL` | Yes | — | Default model for generation |
| `AWS_BEDROCK_AVAILABLE_GENERATION_MODELS` | Yes | — | JSON array of selectable models |
| `KNOWLEDGE_BASE_URL` | No | — | URL of Knowledge service for RAG |
| `KNOWLEDGE_SIMILARITY_THRESHOLD` | No | `0.5` | RAG match threshold (0–1) |

Source: [ai-defra-search-agent README — Environment Variables](https://github.com/DEFRA/ai-defra-search-agent#readme)

---

## Authentication

> No authentication mechanism is documented in the Agent README or source. The service appears to be internal-only, with access controlled at the network/infrastructure layer.

---

## Running Standalone (Without Core)

```bash
# Clone and set up
git clone https://github.com/DEFRA/ai-defra-search-agent.git
cd ai-defra-search-agent
pipx install uv
uv sync

# Start with Docker Compose
docker compose --profile service up
```

Source: [ai-defra-search-agent README](https://github.com/DEFRA/ai-defra-search-agent#readme)

---

## Developer Tools

```bash
# Lint
uv run ruff check --fix
uv run ruff format
uv run task lint

# Test
uv run task test
```

Remote debugging: `debugpy` on port `5679`. VS Code and IntelliJ configurations are documented in the README.

Source: [ai-defra-search-agent README](https://github.com/DEFRA/ai-defra-search-agent#readme)

---

## See Also

- [ai-defra-search-agent repository](https://github.com/DEFRA/ai-defra-search-agent)
- [KNOWLEDGE-API.md](./KNOWLEDGE-API.md)
- [SERVICE-INTERACTIONS.md](../architecture/SERVICE-INTERACTIONS.md)
