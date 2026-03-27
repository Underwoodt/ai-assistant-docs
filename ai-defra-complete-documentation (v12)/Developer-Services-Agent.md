# Agent Service

> 👩‍💻 **Developer Docs** — [Home](Home) › [Developer Docs](Home#developer-docs) › Services

**Repo:** [ai-defra-search-agent](https://github.com/DEFRA/ai-defra-search-agent) · Port: `8086`

---

## Overview

Python/FastAPI service handling all LLM inference. Receives chat requests from the Frontend, queues them via SQS, and processes them asynchronously — calling AWS Bedrock for generation and the Knowledge Service for RAG retrieval when needed. All conversation state persisted to MongoDB.

## Stack

| Technology | Role |
|---|---|
| Python 3.13+, FastAPI, Uvicorn | API server |
| MongoDB (pymongo) | Conversation & message persistence |
| AWS Bedrock (boto3) | LLM inference (Claude) |
| AWS SQS (boto3) | Async message queue |
| AWS CloudWatch | Embedded metrics |
| uv, pytest, Ruff | Tooling |

## Endpoints

| Endpoint | Description |
|---|---|
| `GET /health` | Liveness probe |
| `POST /chat` | Submit message → 202 + conversation ID |
| `GET /conversations/{id}` | Poll for state and completed response |
| `POST /feedback` | Submit user feedback |
| `GET /docs` | Swagger UI |

## SQS Worker

Background worker polls SQS continuously. Per message:
1. Deserialise `{ message_id, conversation_id, question }`
2. Load conversation history from MongoDB
3. If `knowledge_group_ids` → call `POST /rag/search` on Knowledge Service
4. Call Bedrock `converse(history + RAG chunks, system_prompt, model)`
5. Save `AssistantMessage` to MongoDB
6. Delete SQS message

## Key Environment Variables

| Variable | Description |
|---|---|
| `MONGODB_URI` | MongoDB connection |
| `BEDROCK_MODEL_ID` | e.g. `anthropic.claude-3-5-sonnet-20241022-v2:0` |
| `KNOWLEDGE_SERVICE_URL` | Knowledge service base URL |
| `KNOWLEDGE_API_KEY` | API key for Knowledge |
| `SQS_QUEUE_URL` | Chat queue URL |

## Running Locally

```bash
pipx install uv && uv sync
cp .env.example .env
docker compose --profile service up
```

## Testing

```bash
uv run task test   # pytest
uv run task lint   # Ruff
```

Debug port: `5679` (see `compose.debug.yml`)
