# Knowledge Service API Reference

**Port:** 8085
**Framework:** FastAPI (Python 3.12+)

> 🔴 **This file has been substantially rewritten.**
> The previous version documented fabricated production endpoints (`POST /documents`, `POST /rag/search`, `GET /knowledge-groups`) and specific library versions that do not appear in any source file. Those have been removed.
>
> Source: [ai-defra-search-knowledge README](https://github.com/DEFRA/ai-defra-search-knowledge#readme)

---

## ⚠️ Work in Progress

The Knowledge service README explicitly states this is a **work-in-progress**. Only the following endpoints are currently documented in the source:

| Method | Path | Purpose | Status |
|---|---|---|---|
| GET | `/docs` | Swagger / OpenAPI UI | ✅ Confirmed |
| GET | `/health` | Health check | ✅ Confirmed |
| GET | `/example/test` | Template test endpoint | Scaffold only |
| GET | `/example/db` | Template DB query example | Scaffold only |
| GET | `/example/http` | Template HTTP client example | Scaffold only |

The `/example/*` endpoints are scaffolding from the template service and are **not production features**.

Source: [ai-defra-search-knowledge README — API Endpoints](https://github.com/DEFRA/ai-defra-search-knowledge#readme)

---

## Discovering the Live API

Once the Knowledge service is running, the full OpenAPI specification is available at:

```
http://localhost:8085/docs
```

This Swagger UI is the authoritative and up-to-date source for all implemented endpoints — use it in preference to any static documentation until the service reaches a stable release.

---

## What the Knowledge Service is Expected to Do

Based on the Agent service's calls and the architecture, the Knowledge service is expected to expose at minimum:

| Method | Path | Expected Purpose | Source of Expectation |
|---|---|---|---|
| POST | `/rag/search` | Semantic search for RAG | [Agent http_client.py](https://github.com/DEFRA/ai-defra-search-agent/blob/main/app/common/http_client.py) |

> ⚠️ This endpoint is referenced by the Agent service but **not yet confirmed** in the Knowledge service's public README. Verify at `http://localhost:8085/docs` when the service is running.

---

## Running the Knowledge Service

**Option 1 — Docker Compose (recommended, from Core repo):**

```bash
# From ai-defra-search-core
docker-compose up --build
```

Source: [ai-defra-search-core README](https://github.com/DEFRA/ai-defra-search-core#readme)

**Option 2 — Standalone:**

```bash
# From ai-defra-search-knowledge
pipx install uv
uv sync
source .venv/bin/activate
./scripts/start_dev_server.sh
```

Source: [ai-defra-search-knowledge README](https://github.com/DEFRA/ai-defra-search-knowledge#readme)

---

## Environment Configuration

Secrets go in `compose/secrets.env` (git-ignored locally):

```bash
# compose/secrets.env — create locally, do not commit
# See ai-defra-search-knowledge/compose/secrets.env.example for required variables
```

AWS/CloudWatch config goes in `compose/aws.env`.

Source: [ai-defra-search-knowledge README](https://github.com/DEFRA/ai-defra-search-knowledge#readme)

---

## Previously Documented (Removed as Unverified)

The following were present in the previous version of this document and have been **removed** because they could not be verified against any source file:

- `POST /documents` — document ingestion endpoint
- `POST /rag/search` — request/response schema
- `GET /knowledge-groups` — list knowledge groups
- Library versions: PyMuPDF==1.23.8, python-docx==0.8.11, python-pptx==0.6.21

If these endpoints are implemented, they will appear in the Swagger UI at `http://localhost:8085/docs`.

---

## See Also

- [ai-defra-search-knowledge repository](https://github.com/DEFRA/ai-defra-search-knowledge)
- [SYSTEM-DESIGN.md](../architecture/SYSTEM-DESIGN.md)
- [SERVICE-INTERACTIONS.md](../architecture/SERVICE-INTERACTIONS.md)
