# Troubleshooting

[← Back to Developer Docs](README.md)

> Common issues and fixes based on service configuration and architecture analysis.

---

## Frontend returns errors / blank page

**Check:** Is Traefik running? The frontend must be accessed via `frontend.localhost`, not `localhost:3000`.

```bash
docker compose ps traefik   # should show "running"
```

**Check:** Is Redis available? The frontend uses Redis for session caching.

```bash
docker compose ps redis
curl http://localhost:6379/ping
```

---

## Chat responses never arrive (stuck on "pending")

The Agent uses an asynchronous queue. If responses never move from `"pending"`:

1. Check the SQS worker is running:
   ```bash
   docker compose logs ai-defra-search-agent | grep "worker"
   ```
2. Check LocalStack is healthy (local dev):
   ```bash
   curl http://localhost:4566/_localstack/health
   ```
3. Check the Agent logs for Bedrock errors:
   ```bash
   docker compose logs ai-defra-search-agent | grep "bedrock\|error"
   ```

---

## Knowledge service returns no results

**Check:** Was the document actually ingested? Look for errors at upload time in the Knowledge service logs.

**Check:** Similarity threshold — results below 0.5 (default) are filtered out. This is correct behaviour if the query is unrelated to the uploaded documents.

**Check:** Is PostgreSQL/pgvector healthy?
```bash
docker compose ps postgres
docker compose exec postgres pg_isready
```

---

## Bedrock errors in local dev

Ensure you're using the stub (default) and not pointing at real Bedrock without credentials:

```bash
# Confirm BEDROCK_ENDPOINT is pointing at the stub, not AWS
docker compose exec ai-defra-search-agent env | grep BEDROCK
```

If testing with real Bedrock, ensure `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, and `BEDROCK_BEARER_TOKEN` are set correctly.

---

## Authentication errors (401 / 403)

All API endpoints except `/health` and `/docs` require `X-API-KEY`. In local dev the default is `test-api-key`.

```bash
curl -H "X-API-KEY: test-api-key" http://localhost:8086/models
```

---

## Database connection failures

```bash
# MongoDB
docker compose logs mongodb | tail -20

# PostgreSQL
docker compose logs postgres | tail -20
docker compose exec postgres pg_isready -U postgres
```

---

## Service won't start — port already in use

Default ports: Frontend 3000, Agent 8086, Knowledge 8085, Stub 8087, Postgres 5432, Mongo 27017, Redis 6379.

```bash
lsof -i :8086   # check what's using the port
```
