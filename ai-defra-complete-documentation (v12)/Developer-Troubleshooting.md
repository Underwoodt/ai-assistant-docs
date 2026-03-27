# Troubleshooting

> 👩‍💻 **Developer Docs** — [Home](Home) › [Developer Docs](Home#developer-docs)

---

## Spinner Never Resolves / No Response

**Cause:** SQS Worker not running, or LocalStack unhealthy.

```bash
docker compose ps
docker compose logs agent    # look for SQS connection errors
curl http://localhost:4566   # verify LocalStack
```

---

## Chat Returns "Error" or Empty Response

**Cause:** Agent can't reach Bedrock or Bedrock Stub.

```bash
curl http://localhost:8090/__admin/health   # verify stub
docker compose logs agent                  # connection refused / auth errors?
# Check .env: BEDROCK_ENDPOINT_URL should point to stub in local dev
```

---

## Document Stuck at `in_progress`

**Cause:** Ingestion background task failed (S3, chunking, or embedding error).

```bash
docker compose logs knowledge   # look for ingestion errors
curl http://localhost:4566/health  # LocalStack S3 accessible?
# Check document status via Knowledge Swagger: http://localhost:8085/docs
```

---

## Document Never Leaves `not_started`

**Cause:** Frontend didn't successfully notify Knowledge after CDP Uploader callback.

```bash
docker compose logs frontend    # look for callback errors
# Verify CDP_UPLOADER_CALLBACK_URL in .env
```

---

## Agent Fails to Start

```bash
docker compose up mongodb -d    # ensure MongoDB is up first
docker compose logs agent       # check for config errors
```

---

## Knowledge Migration Errors

```bash
cd ai-defra-search-knowledge
# Follow migration instructions in migrator/ directory
```

---

## Frontend Not at `http://frontend.localhost`

**Cause:** Traefik not running, or `.localhost` DNS not resolving.

```bash
docker compose logs traefik

# If .localhost doesn't resolve, add to /etc/hosts:
# 127.0.0.1  frontend.localhost
```

---

## Journey Tests — `ECONNREFUSED`

```bash
docker compose up --wait -d   # use --wait to ensure services are healthy first
npm run test:local
```

---

## Allure Reports Not Generating

**Cause:** Java JRE not installed locally.

```bash
# macOS
brew install openjdk@17
# Linux
sudo apt-get install openjdk-17-jre-headless
```

---

## Debug Mode (Agent)

```bash
docker compose -f docker-compose.yml -f compose.debug.yml up
```

Connect IDE to `localhost:5679`. Config in `.vscode/launch.json`.
