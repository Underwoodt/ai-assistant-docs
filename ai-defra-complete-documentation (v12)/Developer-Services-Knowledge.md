# Knowledge Service

> 👩‍💻 **Developer Docs** — [Home](Home) › [Developer Docs](Home#developer-docs) › Services

**Repo:** [ai-defra-search-knowledge](https://github.com/DEFRA/ai-defra-search-knowledge) · Port: `8085`

> ⚠️ Repository README notes this service is **work in progress** — some API docs may not reflect current implementation.

---

## Overview

Python/FastAPI service managing the full document ingestion pipeline and semantic search. Handles knowledge groups and documents, fetches files from S3, chunks and embeds via AWS Bedrock, stores embeddings in PostgreSQL (pgvector), and serves nearest-neighbour search results to the Agent for RAG.

## Stack

| Technology | Role |
|---|---|
| Python 3.12+, FastAPI, Uvicorn | API server |
| PostgreSQL + pgvector | Vector embeddings and nearest-neighbour search |
| MongoDB | Document metadata and ingest status |
| AWS Bedrock — Titan Embed v2 | Text embedding (`amazon.titan-embed-text-v2:0`, 1024-dim) |
| AWS S3 | Source file storage |
| AWS CloudWatch | Embedded metrics |
| uv, pytest, Ruff | Tooling |

## Endpoints

| Endpoint | Description |
|---|---|
| `GET /health` | Liveness probe |
| `POST /knowledge-groups` | Create knowledge group |
| `GET /knowledge-groups` | List user's groups |
| `POST /knowledge-groups/{id}/documents` | Register document for ingestion |
| `GET /knowledge-groups/{id}/documents/{docId}` | Get document + ingest status |
| `POST /rag/search` | Semantic search (called by Agent) |

## Ingestion Pipeline

For each registered document (`asyncio.create_task()`):
1. Fetch file from S3
2. Extract text (PDF, DOCX, PPTX) or read lines (JSONL)
3. Chunk: 800 chars, 100 char overlap; JSONL = one chunk per line
4. Per chunk: call Bedrock Titan Embed v2 → 1024-dim vector
5. Insert `KnowledgeVector` rows into PostgreSQL
6. Update `Document.status` → `ready` or `failed`

## Key Environment Variables

| Variable | Description |
|---|---|
| `POSTGRES_URI` | PostgreSQL connection |
| `MONGO_URI` | MongoDB connection |
| `BEDROCK_EMBED_MODEL_ID` | `amazon.titan-embed-text-v2:0` |
| `S3_BUCKET` | Uploaded files bucket |

## Running Locally

```bash
pipx install uv && uv sync && pre-commit install
docker compose -f compose.yml up
```

## Testing

```bash
pytest
pre-commit run --all-files
```
