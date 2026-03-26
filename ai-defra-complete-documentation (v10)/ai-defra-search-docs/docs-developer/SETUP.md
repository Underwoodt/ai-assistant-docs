# Developer Setup Guide

[← Back to Developer Docs](README.md)

> **Source:** `ai-defra-search-core/README.md`, per-service README files

---

## Prerequisites

| Tool | Version | Notes |
|---|---|---|
| Docker + Docker Compose | Latest stable | Required for all services |
| Python | 3.13+ | For core orchestration scripts |
| Node.js | 22.16.0+ | For frontend local dev |
| `uv` | Latest | Python package manager (use instead of pip/poetry) |
| Git | Any | For cloning repos |

---

## Quick Start (Full Stack via Core Repo)

The recommended way to run the complete system locally is via the core orchestration repo.

```bash
# 1. Clone the core repo
git clone https://github.com/DEFRA/ai-defra-search-core.git
cd ai-defra-search-core

# 2. Install Python dependencies
uv sync

# 3. Clone all microservice repos into services/
uv run task clone

# 4. Copy and configure environment
cp .env.example .env
# Edit .env — see Configuration section below

# 5. Start all services (using Bedrock stub — no AWS account needed)
docker compose up --build

# 6. (Optional) Load demo knowledge groups
uv run task load-demo-knowledge-groups
```

Access the app at: **http://frontend.localhost**

Swagger docs:
- Agent API: http://localhost:8086/docs
- Knowledge API: http://localhost:8085/docs

---

## Running with Real AWS Bedrock

To use a real AWS Bedrock account instead of the stub:

```bash
docker compose -f compose.yml -f compose.real-bedrock.yml up --build
```

Ensure your `.env` contains valid AWS credentials and Bedrock model configuration.

---

## Running Individual Services

Each service can be run standalone for development.

### Knowledge Service

```bash
cd services/ai-defra-search-knowledge
pipx install uv
uv sync
pre-commit install
cp compose/aws.env.example compose/aws.env  # configure AWS vars
docker compose --profile service up --build
# API at http://localhost:8085
# Swagger at http://localhost:8085/docs
```

### Agent Service

```bash
cd services/ai-defra-search-agent
uv sync
cp .env.example .env  # configure env vars
docker compose build
docker compose --profile service up
# API at http://localhost:8086
# Swagger at http://localhost:8086/docs
```

### Frontend

```bash
cd services/ai-defra-search-frontend
nvm use  # uses .nvmrc (Node 22)
npm install
npm run dev
# Access via frontend.localhost (Traefik required) or http://localhost:3000
```

> ⚠ The frontend must be accessed via `frontend.localhost` when running in the full stack — direct `localhost:3000` access may not work correctly due to Traefik routing.

---

## Configuration

### Key Environment Variables

| Variable | Service | Description |
|---|---|---|
| `AWS_REGION` | Agent, Knowledge | AWS region (default: `eu-central-1`) |
| `AWS_ENDPOINT_URL_SQS` | Agent | SQS endpoint (use LocalStack URL locally) |
| `BEDROCK_BEARER_TOKEN` | Agent | Bearer token for Bedrock auth |
| `BEDROCK_MODELS_JSON` | Agent | JSON list of available LLM model IDs |
| `KNOWLEDGE_BASE_URL` | Agent | URL of Knowledge service (default: `http://localhost:8085`) |
| `KNOWLEDGE_BASE_SIMILARITY_THRESHOLD` | Agent | Cosine similarity cutoff for RAG results (default: `0.5`) |
| `MONGODB_URI` | Agent | MongoDB connection string |
| `MONGODB_DB_NAME` | Agent | Database name (default: `ai-defra-search-agent`) |
| `SQS_CHAT_QUEUE_URL` | Agent | SQS queue URL for chat jobs |
| `SEARCH_AGENT_API_KEY` | Agent | API key for Frontend → Agent auth (default: `test-api-key` locally) |

Secrets (never commit): `compose/secrets.env` per service, `.env` in core repo.

---

## Development Container

A devcontainer configuration is provided for teams who want a consistent, pre-configured environment. It installs Docker-in-Docker, Java 17, Node.js 22, UV, and all IDE extensions (Ruff, Pyright, Prettier, SonarLint, Vitest Explorer).

---

## Verification

After starting the stack, verify all services are healthy:

```bash
docker compose ps           # all containers should show "healthy" or "running"
curl http://localhost:8086/health   # Agent: should return 200
curl http://localhost:8085/health   # Knowledge: should return 200
```
