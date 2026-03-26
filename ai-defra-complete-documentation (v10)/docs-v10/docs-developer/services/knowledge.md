# Knowledge Service

[← Back to Developer Docs](../README.md)

**Repo:** [ai-defra-search-knowledge](https://github.com/DEFRA/ai-defra-search-knowledge)

> ⚠ The repository README notes this service is **work in progress**. Some API documentation may not fully reflect the current implementation.

---

## Overview

Python/FastAPI service responsible for the entire document ingestion pipeline and semantic search. It manages knowledge groups and documents, fetches uploaded files from S3, chunks and embeds them via AWS Bedrock, stores embeddings in PostgreSQL (pgvector), and serves nearest-neighbour search results to the Agent Service for RAG lookups.

## Stack

| Technology | Role |
|---|---|
| Python 3.12+ | Runtime |
| FastAPI | REST API framework |
| PostgreSQL + pgvector | Vector embedding storage and nearest-neighbour search |
| MongoDB | Document metadata and ingest status |
| AWS Bedrock — Titan Embed v2 | Text embedding generation (1024-dim) |
| AWS S3 | Source file storage |
| AWS CloudWatch | Embedded metrics |
| uv | Package manager |
| pytest | Test framework |
| Ruff | Linting & formatting |

## Ports

| Port | Service |
|---|---|
| `8085` | FastAPI HTTP |

## Key Endpoints

| Endpoint | Description |
|---|---|
| `GET /health` | Liveness probe |
| `POST /knowledge-groups` | Create knowledge group |
| `GET /knowledge-groups` | List user's knowledge groups |
| `POST /knowledge-groups/{id}/documents` | Register document for ingestion |
| `GET /knowledge-groups/{id}/documents/{docId}` | Get document + ingest status |
| `POST /rag/search` | Semantic search (called by Agent) |

## Ingestion Pipeline

For each document registered:

1. `asyncio.create_task()` starts background ingestion
2. Fetch file bytes from S3
3. Extract text (PDF, DOCX, PPTX) or read lines (JSONL)
4. Chunk text: 800 characters, 100 character overlap; JSONL = one chunk per line
5. For each chunk: call Bedrock `amazon.titan-embed-text-v2:0` → 1024-dim vector
6. Insert `KnowledgeVector` rows into PostgreSQL
7. Update `Document.status` → `ready` (or `failed`)

## Two MongoDB Databases

| Database | Contents |
|---|---|
| Knowledge | `KnowledgeGroup` + `Document` records (metadata, ingest status) |
| (Agent-owned) | The Agent service has its own separate MongoDB database |

## Database Migrations

The `migrator/` directory contains PostgreSQL schema migration tooling. Run migrations before deploying a new service version that changes the schema.

## Environment Variables

| Variable | Description |
|---|---|
| `POSTGRES_URI` | PostgreSQL connection string |
| `MONGO_URI` | MongoDB connection string |
| `AWS_REGION` | AWS region |
| `BEDROCK_EMBED_MODEL_ID` | `amazon.titan-embed-text-v2:0` |
| `S3_BUCKET` | Bucket name for uploaded files |
| `APP_API_KEY` | Inbound API key (validated on requests) |

## Running Locally

```bash
pipx install uv && uv sync
pre-commit install
docker compose -f compose.yml up
```

## Testing

```bash
pytest
pre-commit run --all-files
```
