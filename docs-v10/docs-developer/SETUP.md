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
| uv | Latest | Python package manager for Agent & Knowledge |
| Java JRE | 17+ | Required only for Allure test reports |

---

## Quick Start (Full Stack)

The recommended way to run the full stack locally is via the core meta-repo, which orchestrates all services together.

```bash
git clone https://github.com/DEFRA/ai-defra-search-core.git
cd ai-defra-search-core
cp .env.example .env
docker compose up
```

Access the app at `http://frontend.localhost` (via Traefik proxy — do not use `localhost:3000` directly).

> The Bedrock Stub runs automatically in the full stack — no AWS account required for local development.

---

## Running Services Individually

### Agent Service

```bash
git clone https://github.com/DEFRA/ai-defra-search-agent.git
cd ai-defra-search-agent
pipx install uv
uv sync
cp .env.example .env   # fill in AWS, MongoDB, Bedrock model ID
docker compose build
docker compose --profile service up
```

API available at `http://localhost:8086` · Swagger UI: `http://localhost:8086/docs`

### Knowledge Service

```bash
git clone https://github.com/DEFRA/ai-defra-search-knowledge.git
cd ai-defra-search-knowledge
pipx install uv
uv sync
pre-commit install
docker compose -f compose.yml up
```

API available at `http://localhost:8085`

### Frontend

Must run inside the core meta-repo (uses Traefik routing):

```bash
git clone https://github.com/DEFRA/ai-defra-search-core.git
cd ai-defra-search-core
npm install
cp .env.example .env
docker compose -f compose/docker-compose.yml up
```

Access at `http://frontend.localhost`

### Bedrock Stub

```bash
git clone https://github.com/DEFRA/ai-defra-search-aws-bedrock-stub.git
cd ai-defra-search-aws-bedrock-stub
docker compose up -d
```

Stub available at `http://localhost:8090` (local) or `http://wiremock:8080` (Docker network).
Verify: `curl http://localhost:8090/__admin/health`

---

## Environment Variables

### Agent Service (`.env.example` keys)

| Variable | Description |
|---|---|
| `MONGODB_URI` | MongoDB connection string |
| `AWS_REGION` | AWS region (e.g. `eu-west-2`) |
| `BEDROCK_MODEL_ID` | LLM model (e.g. `anthropic.claude-3-5-sonnet-20241022-v2:0`) |
| `KNOWLEDGE_SERVICE_URL` | URL of Knowledge service |
| `KNOWLEDGE_API_KEY` | API key for Knowledge service |
| `SQS_QUEUE_URL` | AWS SQS queue URL |
| `CLOUDWATCH_ENABLED` | Enable CloudWatch metrics (`true`/`false`) |

### Knowledge Service (`.env.example` keys)

| Variable | Description |
|---|---|
| `POSTGRES_URI` | PostgreSQL connection string (with pgvector) |
| `MONGO_URI` | MongoDB connection string |
| `AWS_REGION` | AWS region |
| `BEDROCK_EMBED_MODEL_ID` | Embedding model (`amazon.titan-embed-text-v2:0`) |
| `S3_BUCKET` | S3 bucket for uploaded files |

### Frontend (`.env.example` keys)

| Variable | Description |
|---|---|
| `AGENT_SERVICE_URL` | URL of Agent service |
| `AGENT_API_KEY` | API key for Agent service |
| `KNOWLEDGE_SERVICE_URL` | URL of Knowledge service |
| `KNOWLEDGE_API_KEY` | API key for Knowledge service |
| `ENTRA_CLIENT_ID` | Microsoft Entra ID OAuth client ID |
| `ENTRA_CLIENT_SECRET` | Microsoft Entra ID OAuth client secret |
| `ENTRA_TENANT_ID` | Microsoft Entra ID tenant |
| `REDIS_URI` | Redis connection string |
| `SESSION_SECRET` | Session signing secret |

---

## Development Commands

### Agent Service

```bash
uv run task lint       # Ruff linting & formatting check
uv run task test       # Run pytest suite
```

Debug port: `5679` (see `compose.debug.yml`)

### Knowledge Service

```bash
pytest                 # Run test suite
pre-commit run --all-files  # Run all quality checks
```

### Frontend

```bash
npm test               # Run Vitest unit tests
npm run lint           # ESLint
```

---

## Verifying the Stack

Once all services are running:

1. Open `http://frontend.localhost` — you should see the chat UI
2. Sign in (Entra ID — may be mocked locally)
3. Create a knowledge group and upload a test document
4. Wait for ingest status to show `ready`
5. Start a conversation and ask a question about the document
6. Check Agent Swagger at `http://localhost:8086/docs` for API state
