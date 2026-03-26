# Knowledge Service

> **Repo:** `ai-defra-search-knowledge` · **Port:** 8085 · **Stack:** Python 3.13+, FastAPI, pgvector

---

## Purpose

The Knowledge service is the document library. It handles document ingestion (upload, extraction, chunking, embedding), stores vectors in PostgreSQL with the pgvector extension, and serves semantic search results to the Agent.

---

## Key Modules

| Path | Responsibility |
|---|---|
| `app/main.py` | FastAPI app setup |
| `app/config.py` | Configuration via pydantic-settings |
| `app/ingest/service.py` | Orchestrates upload → extract → chunk → embed → store |
| `app/ingest/extractors/pdf.py` | PDF text extraction (PyMuPDF) |
| `app/ingest/extractors/docx.py` | Word document extraction (python-docx) |
| `app/ingest/extractors/pptx.py` | PowerPoint extraction (python-pptx) |
| `app/ingest/extractors/jsonl.py` | JSON Lines extraction |
| `app/ingest/extractors/chunking.py` | Text chunking logic |
| `app/ingest/vector_store.py` | Stores embeddings in pgvector |
| `app/ingest/s3_client.py` | Stores source documents in S3 (LocalStack locally) |
| `app/rag/vector_search.py` | Cosine similarity search against stored vectors |
| `app/rag/router.py` | `/rag/search` endpoint (called by Agent) |
| `app/knowledge_group/` | CRUD for personal knowledge groups |
| `app/document/router.py` | Document upload endpoint |
| `app/common/bedrock.py` | Titan Embed API calls for generating embeddings |

---

## Document Ingestion Pipeline

Ingestion is triggered by the Frontend after the CDP Uploader delivers a file to S3 and fires its callback.

```
CDP Uploader callback → Frontend notifies Knowledge Service
  → Knowledge Service fetches file from S3
  → Extract text (format-specific extractor)
  → Chunk text (chunking.py)
  → Generate 1024-dim embeddings (AWS Bedrock Titan Embed v2)
  → Store vectors in PostgreSQL/pgvector
  → Store document metadata and ingest status in MongoDB
```

---

## Vector Search

When the Agent calls `/rag/search`:
1. The query is vectorised using Bedrock Titan Embed
2. Cosine similarity search runs against pgvector
3. Results above the similarity threshold (configurable, default 0.5) are returned
4. Returned chunks are passed to the Agent as context for the LLM prompt

---

## Databases

The Knowledge service uses two databases with distinct responsibilities:

- **PostgreSQL + pgvector** — stores 1024-dimensional Titan Embed v2 vectors; used for nearest-neighbour similarity search at query time. Schema migrations managed via `migrator/`.
- **MongoDB** — stores document metadata (filename, knowledge group, upload date) and ingestion status (processing, complete, failed). See `app/common/mongo.py`.

---

## Running Tests

```bash
uv run task test
# Format, lint, then pytest with coverage reporting
```

---

## Running Locally

```bash
docker compose --profile service up --build
# API at http://localhost:8085
# Swagger at http://localhost:8085/docs
# Hot-reload enabled — changes to ./app sync automatically
```
