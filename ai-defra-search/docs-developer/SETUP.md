# Setup & Local Development

[Back to Developer Docs](./README.md)

---

## Prerequisites

| Tool | Version | Notes |
|---|---|---|
| Docker | Latest stable | Required for all services |
| Docker Compose | v2+ | Used for local orchestration |
| Python | 3.12+ | Agent Service and Knowledge Service |
| Node.js | v20+ | Frontend |
| npm | v9+ | Frontend |
| uv | Latest | Python package manager (Agent + Knowledge) |
| Java JRE | 11+ | Journey tests (Allure reporting) |
| pipx | Latest | Used to install uv globally |

---

## Architecture of the Local Stack

The full local stack is orchestrated from a **meta-repo** (not listed in the repo list — see note below). The frontend **must** be accessed via Traefik proxy at `http://frontend.localhost` — accessing via `localhost:3000` breaks AWS routing.

> **Warning: Assumed** — a meta-repo / docker-compose orchestration layer is referenced in the frontend README. Verify its location and setup instructions in the DEFRA organisation.

### Local Infrastructure Services

| Service | Purpose | Local Mock |
|---|---|---|
| MongoDB | Conversation and document metadata | Docker container |
| PostgreSQL + pgvector | Vector embeddings | Docker container |
| Redis | Session cache | Docker container |
| AWS SQS | Message queue | LocalStack |
| AWS S3 | File storage | LocalStack |
| AWS Bedrock | LLM + embeddings | WireMock stub (`ai-defra-search-aws-bedrock-stub`) |

---

## Service Setup

### Agent Service (`ai-defra-search-agent`)

```bash
# Clone the repo
git clone https://github.com/DEFRA/ai-defra-search-agent
cd ai-defra-search-agent

# Install dependencies with uv
uv sync

# Copy environment template
cp .env.example .env
# Edit .env with your local values

# Run via Docker Compose
docker compose up
```

Key environment variables (see `.env.example`):

| Variable | Description |
|---|---|
| `MONGO_URI` | MongoDB connection string |
| `AWS_REGION` | AWS region for Bedrock |
| `SQS_QUEUE_URL` | SQS queue URL (LocalStack in dev) |
| `KNOWLEDGE_SERVICE_URL` | URL of the Knowledge Service |
| `BEDROCK_ENDPOINT` | Override to point at Bedrock stub locally |

---

### Knowledge Service (`ai-defra-search-knowledge`)

```bash
git clone https://github.com/DEFRA/ai-defra-search-knowledge
cd ai-defra-search-knowledge

# Install dependencies
uv sync

# Environment config uses compose/aws.env and compose/secrets.env (not .env)
# Copy and edit these files before running

# Run via Docker Compose
docker compose up
```

> **Note:** The Knowledge Service uses `compose/aws.env` and `compose/secrets.env` rather than a root `.env` file, following CDP deployment conventions.

The `migrator/` directory contains database migration tooling for PostgreSQL schema setup.

---

### Frontend (`ai-defra-search-frontend`)

```bash
git clone https://github.com/DEFRA/ai-defra-search-frontend
cd ai-defra-search-frontend

npm install

cp .example.env .env
# Edit .env with local values

# Must be run via the meta-repo and accessed via Traefik at http://frontend.localhost
```

Key environment variables:

| Variable | Description |
|---|---|
| `SESSION_CACHE_ENGINE` | `redis` or `memory` — Redis recommended for multi-instance |
| `AGENT_SERVICE_URL` | URL of the Agent Service |
| `KNOWLEDGE_SERVICE_URL` | URL of the Knowledge Service |

---

### AWS Bedrock Stub (`ai-defra-search-aws-bedrock-stub`)

Allows fully offline development — no AWS credentials required.

```bash
git clone https://github.com/DEFRA/ai-defra-search-aws-bedrock-stub
cd ai-defra-search-aws-bedrock-stub

docker compose up -d
```

Stub endpoints are available at:
- Local: `http://localhost:8080`
- Docker network: `http://wiremock:8080`

Stubs included: Claude Converse API, Titan Embed Text V2. All responses include a simulated 2-second delay.

---

## Verification

Once the full stack is running, verify with:

1. Navigate to `http://frontend.localhost` — you should see the chat UI
2. Log in via Microsoft Entra ID (or the local stub if configured)
3. Navigate to the Upload section and create a Knowledge Group
4. Upload a PDF — check MongoDB for a document record with `status: ready`
5. Ask a question targeting that Knowledge Group — the response should cite source chunks

---

## Running Tests Locally

See [TESTING.md](./TESTING.md) for journey, unit, and performance test instructions.
