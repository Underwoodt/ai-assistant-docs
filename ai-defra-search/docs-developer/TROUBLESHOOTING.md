# Troubleshooting

[Back to Developer Docs](./README.md)

---

## Common Issues

### Frontend shows blank page or auth redirect loop

| Check | Action |
|---|---|
| Accessing via `localhost:3000` | **Must** access via Traefik proxy at `http://frontend.localhost`. Direct port access breaks AWS routing. |
| Entra ID not configured locally | Check `.example.env` for `AUTH_*` variables; use a local auth stub if available |
| Redis not running | Frontend session cache requires Redis — verify `docker compose ps` shows Redis healthy |

---

### Chat response never appears (stuck on loading spinner)

The async polling pattern means several things could be failing silently.

| Check | Action |
|---|---|
| SQS queue not running | Verify LocalStack is up: `docker compose ps`. Check Agent Service logs for SQS connection errors |
| Agent Service worker not polling | Check Agent Service logs for SQS poll errors or Bedrock connection failures |
| Bedrock stub not running | If running locally, verify WireMock stub is up at `http://localhost:8080/health` |
| Message stuck in `queued` status | Check MongoDB `conversations` collection — if status never moves to `processing`, the SQS worker is not picking up messages |
| AWS credentials missing | Even locally, AWS region/key env vars must be set (can be dummy values when using the stub) |

---

### Document upload fails or stays in `in_progress` forever

| Check | Action |
|---|---|
| CDP Uploader callback not received | Check Frontend logs for `POST /upload/callback` — if missing, CDP Uploader may not have reached the callback URL |
| Document status stuck at `in_progress` | Check Knowledge Service logs for S3 fetch errors, extractor errors, or Bedrock embedding failures |
| Unsupported file type | Only PDF, DOCX, PPTX, JSONL are supported — other formats will fail extraction |
| S3 / LocalStack not running | Verify LocalStack is healthy and the S3 bucket exists |
| Embedding failure | Check AWS Bedrock (or stub) connectivity from the Knowledge Service container |

---

### RAG search returns no results

| Check | Action |
|---|---|
| Document not yet ingested | Check MongoDB document status — must be `ready` before it can be searched |
| Wrong knowledge group selected | Confirm the prompt is targeting a knowledge group that has documents in `ready` status |
| pgvector extension missing | Run database migrations (`migrator/`) to ensure the `knowledge_vectors` table and pgvector extension exist |
| Empty embedding | Check Knowledge Service logs during ingest — a failed embedding would leave zero vectors for that document |

---

### Local stack won't start

| Check | Action |
|---|---|
| Port conflicts | Check for existing services on ports used by MongoDB (27017), PostgreSQL (5432), Redis (6379), LocalStack (4566), WireMock (8080) |
| Docker daemon not running | Start Docker Desktop or the Docker daemon |
| Must run from meta-repo | The full stack requires the meta-repo's Compose configuration — running individual service Compose files may result in missing dependencies |

---

## Useful Diagnostic Commands

```bash
# Check all container statuses
docker compose ps

# Tail Agent Service logs
docker compose logs -f agent

# Tail Knowledge Service logs
docker compose logs -f knowledge

# Check MongoDB directly
docker compose exec mongo mongosh --eval "db.conversations.find().limit(5).pretty()"

# Check pgvector embeddings exist
docker compose exec postgres psql -U postgres -c "SELECT COUNT(*) FROM knowledge_vectors;"

# Test Bedrock stub health
curl http://localhost:8080/__admin/health
```

---

## Getting Help

- Check the **ADR-004** architecture document for design intent and sequence diagrams
- Raise issues on the relevant GitHub repo (DEFRA organisation)
- For CDP platform issues, contact the CDP platform team directly
