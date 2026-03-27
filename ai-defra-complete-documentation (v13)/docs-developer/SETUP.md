# Local Development Setup

[← Back to Developer Docs](README.md)



---

## Prerequisites

| Tool | Version | Notes |
|---|---|---|
| Docker | 20.10+ | Required for all services |
| Docker Compose | 2.0+ | Multi-service orchestration |
| Node.js | ≥ 22.13.1 | Frontend & journey tests |
| Python | ≥ 3.13 (Agent) / ≥ 3.12 (Knowledge) | Per service |
| pipx | Latest | For installing `uv` |
| uv | Latest | Python package manager |
| Java JRE | 17+ | Only for Allure test reports |

---

## Quick Start — Full Stack

```bash
git clone https://github.com/DEFRA/ai-defra-search-core.git
cd ai-defra-search-core
cp .env.example .env
docker compose up
```

Access at **`http://frontend.localhost`** (via Traefik — do not use `localhost:3000` directly).

> The Bedrock Stub runs automatically — no AWS account required.

---

## Individual Services

### Agent Service

```bash
git clone https://github.com/DEFRA/ai-defra-search-agent.git
cd ai-defra-search-agent
pipx install uv && uv sync
cp .env.example .env
docker compose --profile service up
```

Swagger UI: `http://localhost:8086/docs`

### Knowledge Service

```bash
git clone https://github.com/DEFRA/ai-defra-search-knowledge.git
cd ai-defra-search-knowledge
pipx install uv && uv sync && pre-commit install
docker compose -f compose.yml up
```

### Frontend (must run inside core meta-repo)

```bash
git clone https://github.com/DEFRA/ai-defra-search-core.git
cd ai-defra-search-core
npm install && cp .env.example .env
docker compose up
```

### Bedrock Stub

```bash
git clone https://github.com/DEFRA/ai-defra-search-aws-bedrock-stub.git
cd ai-defra-search-aws-bedrock-stub
docker compose up -d
curl http://localhost:8090/__admin/health
```

---

## Key Environment Variables

### Agent (`.env.example`)

| Variable | Description |
|---|---|
| `MONGODB_URI` | MongoDB connection string |
| `AWS_REGION` | e.g. `eu-west-2` |
| `BEDROCK_MODEL_ID` | e.g. `anthropic.claude-3-5-sonnet-20241022-v2:0` |
| `KNOWLEDGE_SERVICE_URL` | Knowledge service base URL |
| `KNOWLEDGE_API_KEY` | API key for Knowledge service |
| `SQS_QUEUE_URL` | AWS SQS queue URL |
| `CLOUDWATCH_ENABLED` | `true` / `false` |

### Knowledge (`.env.example`)

| Variable | Description |
|---|---|
| `POSTGRES_URI` | PostgreSQL connection string |
| `MONGO_URI` | MongoDB connection string |
| `AWS_REGION` | AWS region |
| `BEDROCK_EMBED_MODEL_ID` | `amazon.titan-embed-text-v2:0` |
| `S3_BUCKET` | Bucket for uploaded files |

### Frontend (`.env.example`)

| Variable | Description |
|---|---|
| `AGENT_SERVICE_URL` | Agent service URL |
| `AGENT_API_KEY` | API key for Agent |
| `KNOWLEDGE_SERVICE_URL` | Knowledge service URL |
| `KNOWLEDGE_API_KEY` | API key for Knowledge |
| `ENTRA_CLIENT_ID` / `_SECRET` / `_TENANT_ID` | Microsoft Entra ID OAuth config |
| `REDIS_URI` | Redis connection string |
| `SESSION_SECRET` | Session signing secret (min 32 bytes) |

---

## Dev Commands

### Agent

```bash
uv run task lint    # Ruff linting & formatting
uv run task test    # Run pytest suite
```

Debug port: `5679` (see `compose.debug.yml`)

### Knowledge

```bash
pytest
pre-commit run --all-files
```

### Frontend

```bash
npm test      # Vitest
npm run lint  # ESLint
```

---

## Verifying the Stack

1. Open `http://frontend.localhost` — chat UI should appear
2. Sign in (Entra ID — may be mocked locally)
3. Create a knowledge group and upload a test document
4. Wait for ingest status to show `ready`
5. Start a conversation and ask a question about the document
