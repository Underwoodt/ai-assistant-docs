# Troubleshooting

[← Back to Developer Docs](README.md)

---

## Frontend Shows No Response / Spinner Doesn't Resolve

**Cause:** SQS Worker may not be running, or SQS (LocalStack) is not healthy.

**Fix:**
```bash
docker compose ps            # check all containers are up
docker compose logs agent    # look for SQS connection errors
curl http://localhost:4566   # verify LocalStack is responding
```

---

## Chat Returns "Error" or Empty Response

**Cause:** Agent cannot reach Bedrock (or Bedrock Stub).

**Fix:**
```bash
# Check stub is running
curl http://localhost:8090/__admin/health

# Confirm Agent is pointing at stub, not real Bedrock
# Check .env: BEDROCK_ENDPOINT_URL should point to LocalStack or stub URL in local dev

docker compose logs agent    # look for connection refused or auth errors
```

---

## Document Stuck at `in_progress` After Upload

**Cause:** Ingestion background task failed (S3 fetch error, chunking error, or embedding call failed).

**Fix:**
```bash
docker compose logs knowledge    # look for ingestion errors
# Check S3 (LocalStack) is accessible and the file was actually uploaded
curl http://localhost:4566/health

# Check Knowledge Swagger for document status
# GET http://localhost:8085/knowledge-groups/{id}/documents
```

---

## `not_started` Status Never Changes After Upload

**Cause:** Frontend did not successfully notify the Knowledge service of the completed CDP Uploader callback.

**Fix:**
```bash
docker compose logs frontend    # look for callback receipt errors
# Confirm CDP_UPLOADER_CALLBACK_URL is set correctly in .env
```

---

## Agent Service Fails to Start

**Common causes:**
- MongoDB not running or wrong URI
- Missing `.env` values

```bash
docker compose up mongodb -d    # ensure MongoDB is up first
docker compose logs agent       # check for config errors at startup
```

---

## Knowledge Service Database Migration Errors

**Cause:** Schema migrations not applied after a service update.

**Fix:**
```bash
cd ai-defra-search-knowledge
# Follow migration instructions in migrator/ directory README
```

---

## Frontend Not Accessible at `http://frontend.localhost`

**Cause:** Traefik reverse proxy not running, or DNS not resolving `.localhost`.

**Fix:**
```bash
docker compose logs traefik    # check Traefik started correctly

# If .localhost doesn't resolve on your OS, try adding to /etc/hosts:
# 127.0.0.1 frontend.localhost
```

---

## Journey Tests Fail — `ECONNREFUSED`

**Cause:** Stack not fully started before tests run.

**Fix:**
```bash
# Use --wait flag to ensure services are healthy before running tests
docker compose up --wait -d
npm run test:local
```

---

## Allure Reports Not Generating

**Cause:** Java JRE not installed locally (Java 17+ required for Allure CLI).

**Fix:**
```bash
# macOS
brew install openjdk@17

# Linux
sudo apt-get install openjdk-17-jre-headless
```

Or use the Docker-based run which includes Java automatically.

---

## Trivy Scan Failing in CI

**Cause:** New vulnerability found that isn't in `.trivyignore`.

**Fix:**
1. Review the vulnerability report to assess severity and applicability
2. If accepted, add the CVE to `.trivyignore` with a comment explaining why
3. If not accepted, update the affected dependency

---

## Debug Mode (Agent)

The Agent supports remote debugging via debugpy on port `5679`:

```bash
docker compose -f docker-compose.yml -f compose.debug.yml up
```

Connect your IDE (VS Code or IntelliJ) to `localhost:5679`. Config in `.vscode/launch.json`.
