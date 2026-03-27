# Deployment

[← Back to Developer Docs](README.md)

---

## Platform Overview

All services are deployed on **DEFRA's Core Delivery Platform (CDP)**. CDP handles container orchestration, secrets management, service discovery, and CI/CD integration. Each service is independently containerised and deployed.

---

## Container Images

Each service builds and publishes a Docker image to the CDP registry via GitHub Actions:

| Service | Base Image | Publish Workflow |
|---|---|---|
| Agent | Python 3.13 (slim) | `publish.yml` |
| Knowledge | Python 3.12 (slim) | `publish.yml` |
| Frontend | Node.js 22 (alpine) | `publish.yml` |
| Bedrock Stub | WireMock official | `publish.yml` |
| Journey Tests | Node.js 22 + Java 17 | `publish.yml` |

Images are tagged with the Git SHA and `latest` on merge to main.

---

## CI/CD Pipeline

```
Push to feature branch
  → Lint + test
  → Trivy security scan
  → SonarCloud analysis

Merge to main
  → All checks above
  → docker build & push to CDP registry

CDP Portal
  → Deploys new image to target environment
  → Runs journey / accessibility tests via Test Suites
```

---

## Environments

| Environment | Purpose | Access |
|---|---|---|
| Local | Developer workstation | Docker Compose + Bedrock Stub |
| Dev | Shared development | CDP Portal — dev namespace |
| Test | Integration & acceptance | CDP Portal — test namespace |
| Production | Live service | CDP Portal — prod namespace |

Environment-specific config is managed via CDP secrets and environment variables injected at runtime.

---

## Infrastructure Per Environment

| Component | Local | CDP (Dev/Test/Prod) |
|---|---|---|
| LLM / Embeddings | Bedrock Stub (WireMock) | AWS Bedrock (real) |
| S3 | LocalStack | AWS S3 (real) |
| SQS | LocalStack | AWS SQS (real) |
| MongoDB | Docker container | CDP-managed MongoDB |
| PostgreSQL + pgvector | Custom Docker image | CDP-managed PostgreSQL |
| Redis | Docker container | CDP-managed Redis |
| Auth | Mocked / bypassed | Microsoft Entra ID (real) |
| Routing | Traefik (local) | CDP ingress / load balancer |

---

## Secrets Management

Secrets are **never committed** to source control. All services use `.env.example` templates that list required variables without values. In CDP environments, secrets are injected via the CDP secrets store.

Key secrets per service:

**Agent:** `MONGODB_URI`, `SQS_QUEUE_URL`, AWS credentials, `KNOWLEDGE_API_KEY`

**Knowledge:** `POSTGRES_URI`, `MONGO_URI`, AWS credentials (S3 + Bedrock), `APP_API_KEY`

**Frontend:** `ENTRA_CLIENT_ID`, `ENTRA_CLIENT_SECRET`, `AGENT_API_KEY`, `KNOWLEDGE_API_KEY`, `SESSION_SECRET`, `REDIS_URI`

---

## Health Checks

All services expose a `/health` endpoint for liveness checks:

| Service | Health Endpoint |
|---|---|
| Agent | `GET http://localhost:8086/health` |
| Knowledge | `GET http://localhost:8085/health` |
| Bedrock Stub | `GET http://localhost:8090/__admin/health` |

---

## Database Migrations

The Knowledge service includes a `migrator/` directory with database migration tooling for managing schema changes to PostgreSQL (pgvector). Run migrations before deploying a new version that changes the schema.

---

## Monitoring & Observability

| Tool | Services | What it covers |
|---|---|---|
| AWS CloudWatch | Agent, Knowledge | Embedded metrics: request counts, latency, error rates |
| Trivy | All (CI/CD) | Container & dependency vulnerability scanning |
| SonarCloud | All (CI/CD) | Code quality, coverage, security hotspots |
| CDP Portal | All | Deployment history, service health, test results |
| Allure (S3) | Journey Tests | Test run reports published after each run |
