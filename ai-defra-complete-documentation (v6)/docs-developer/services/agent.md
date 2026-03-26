# Agent Service

> **Repo:** `ai-defra-search-agent` · **Port:** 8086 · **Stack:** Python 3.12+, FastAPI

---

## Purpose

The Agent service is the core orchestrator. It receives chat requests from the Frontend, queues them via AWS SQS, retrieves relevant document passages from the Knowledge service, invokes a chosen LLM via AWS Bedrock, and persists the full conversation in MongoDB.

---

## Key Modules

| Path | Responsibility |
|---|---|
| `app/main.py` | FastAPI app setup, middleware, lifespan hooks |
| `app/config.py` | Configuration classes (Bedrock, Mongo, SQS, Chat queue) |
| `app/chat/agent.py` | Chat orchestration — assembles context, calls Bedrock |
| `app/chat/service.py` | Business logic — validates request, triggers queue |
| `app/chat/worker.py` | SQS polling worker — processes queued chat jobs |
| `app/chat/repository.py` | MongoDB read/write for conversations and messages |
| `app/chat/router.py` | FastAPI route definitions (`/chat`, `/conversations`) |
| `app/bedrock/service.py` | AWS Bedrock integration — Converse API calls |
| `app/models/` | `/models` endpoint — lists available LLMs |
| `app/feedback/` | `/feedback` endpoint — stores user ratings |
| `app/health/router.py` | `/health` endpoint |
| `app/prompts/` | Prompt templates used when calling the LLM |

---

## Chat Processing Flow

```
POST /chat
  → Validate request
  → Create pending message in MongoDB
  → Return 202 Accepted {message_id, conversation_id}
  → Enqueue job to SQS

Worker (background)
  → Poll SQS
  → Dequeue job
  → Determine whether RAG lookup is required for this query
      If yes → Query Knowledge service for relevant chunks (vector search)
      If no  → Proceed with LLM call only
  → Build prompt (system prompt + optional RAG context + conversation history)
  → Call AWS Bedrock Claude Converse API
  → Write response to MongoDB
  → Message now visible on GET /conversations/{id}
```

The decision of whether to invoke a RAG lookup is made by the Agent per request. Not all queries are routed to the Knowledge service — the Agent determines relevance based on the presence of `knowledge_group_ids` in the request and internal logic.

---

## Configuration

All configuration is via environment variables (see `SETUP.md` for the full list). Key classes in `app/config.py`:

- `BedrockConfig` — model list, timeouts, temperature, token limits, guardrails
- `MongoConfig` — URI, DB name, connection timeouts, retry settings
- `SQSConfig` — region, endpoint, optional credentials bypass
- `ChatQueueConfig` — batch size (default 10), visibility timeout, polling interval

---

## Running Tests

```bash
uv run task test
# Runs: ruff lint → pytest with coverage (HTML + XML reports in ./coverage)
# Minimum coverage enforced: 90%
```

---

## Debugging

Remote debugging via debugpy is supported. Port 5679 is exposed in the debug Docker Compose variant (`compose.debug.yaml`). Configure VS Code or IntelliJ to attach to `localhost:5679`.

---

## Code Quality Gates

- Ruff (linting + formatting)
- Pre-commit hooks
- Trivy vulnerability scanning (on PRs and daily)
- SonarCloud analysis
