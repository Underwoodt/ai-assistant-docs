# Knowledge Service

[Back to Developer Docs](../README.md)

**Repo:** [ai-defra-search-knowledge](https://github.com/DEFRA/ai-defra-search-knowledge)
**Language:** Python 3.12+
**Framework:** FastAPI
**Package manager:** uv

---

## Purpose

The Knowledge Service is the document intelligence layer. It:

- Manages knowledge groups and document records (MongoDB)
- Ingests uploaded documents — extract, chunk, embed, and store vectors
- Serves semantic search results to the Agent Service for RAG retrieval
- Stores 1024-dimensional vector embeddings in PostgreSQL via pgvector

---

## Directory Structure

```
ai-defra-search-knowledge/
├── app/              # FastAPI app, routes, ingest pipeline, extractors
├── migrator/         # PostgreSQL schema migration tooling
├── compose/          # Docker Compose configurations (aws.env, secrets.env)
├── scripts/          # Utility scripts (start_dev_server.sh)
├── tests/            # pytest test suite
├── changelog/        # Version history
├── .github/          # GitHub Actions workflows
├── Dockerfile        # Container image
└── pyproject.toml    # Dependencies and project metadata
```

---

## Key Responsibilities

| Responsibility | Detail |
|---|---|
| Knowledge group management | CRUD for personal knowledge groups scoped by `user-id` header |
| Document ingestion | Async background pipeline: fetch from S3 → extract → chunk → embed → store |
| Embedding generation | AWS Bedrock Titan Embed v2 (`amazon.titan-embed-text-v2:0`), 1024-dim vectors |
| Vector storage | PostgreSQL with pgvector extension (`knowledge_vectors` table) |
| Semantic search | `POST /rag/search` — nearest-neighbour vector similarity search |
| Document status tracking | MongoDB document records with status lifecycle: `not_started → in_progress → ready / failed` |

---

## Document Extractors

| File Type | Extractor Class | Chunking Strategy |
|---|---|---|
| PDF | `PdfChunkExtractor` | ~800 chars, 100-char overlap |
| DOCX | `DocxChunkExtractor` | ~800 chars, 100-char overlap |
| PPTX | `PptxChunkExtractor` | ~800 chars, 100-char overlap |
| JSONL | `JsonlChunkExtractor` | Each line treated as a pre-formed chunk |

---

## Configuration

| File | Purpose |
|---|---|
| `compose/aws.env` | AWS region, S3 bucket, Bedrock endpoint |
| `compose/secrets.env` | MongoDB URI, PostgreSQL connection string, AWS credentials |

> **Note:** Does not use a root `.env` file — follows CDP deployment conventions.

---

## Running Locally

```bash
cd ai-defra-search-knowledge
uv sync
# Edit compose/aws.env and compose/secrets.env
docker compose up

# Run migrations first (creates knowledge_vectors table)
cd migrator && uv run python migrate.py  # verify exact command in migrator/
```

---

## Testing

```bash
uv run pytest
uv run ruff check .
```
