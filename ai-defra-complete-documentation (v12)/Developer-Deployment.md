# Deployment

> 👩‍💻 **Developer Docs** — [Home](Home) › [Developer Docs](Home#developer-docs)

---

## Platform

All services deploy to **DEFRA's Core Delivery Platform (CDP)** as independently containerised services. CDP handles orchestration, secrets, service discovery, and CI/CD integration.

---

## Container Images

| Service | Base Image | Published via |
|---|---|---|
| Agent | Python 3.13 slim | `publish.yml` |
| Knowledge | Python 3.12 slim | `publish.yml` |
| Frontend | Node.js 22 alpine | `publish.yml` |
| Bedrock Stub | WireMock official | `publish.yml` |
| Journey Tests | Node.js 22 + Java 17 | `publish.yml` |

Images tagged with Git SHA and `latest` on merge to main.

---

## CI/CD Pipeline

```
Feature branch push
  → Lint + test
  → Trivy security scan
  → SonarCloud analysis

Merge to main
  → All checks above
  → docker build & push to CDP registry

CDP Portal
  → Deploys new image to target environment
  → Runs journey / accessibility tests
```

---

## Environments

| Environment | Purpose | LLM | AWS | Auth |
|---|---|---|---|---|
| Local | Developer workstation | Bedrock Stub | LocalStack | Mocked |
| Dev | Shared development | AWS Bedrock | Real | Entra ID |
| Test | Integration / acceptance | AWS Bedrock | Real | Entra ID |
| Production | Live | AWS Bedrock | Real | Entra ID |

---

## Secrets Management

All secrets are managed via CDP's secrets store — never committed to source control. `.env.example` files list required variables without values.

**Agent:** `MONGODB_URI`, `SQS_QUEUE_URL`, AWS credentials, `KNOWLEDGE_API_KEY`

**Knowledge:** `POSTGRES_URI`, `MONGO_URI`, AWS credentials, `APP_API_KEY`

**Frontend:** `ENTRA_CLIENT_ID/SECRET/TENANT_ID`, `AGENT_API_KEY`, `KNOWLEDGE_API_KEY`, `SESSION_SECRET`, `REDIS_URI`

---

## Health Checks

| Service | Endpoint |
|---|---|
| Agent | `GET http://localhost:8086/health` |
| Knowledge | `GET http://localhost:8085/health` |
| Bedrock Stub | `GET http://localhost:8090/__admin/health` |

---

## Database Migrations

The Knowledge service includes a `migrator/` directory for PostgreSQL schema migrations. Run before deploying any version that changes the schema.

---

## Monitoring

| Tool | Services | Covers |
|---|---|---|
| AWS CloudWatch | Agent, Knowledge | Request counts, latency, errors |
| Trivy | All (CI/CD) | Container & dependency vulnerabilities |
| SonarCloud | All (CI/CD) | Code quality, coverage, security |
| CDP Portal | All | Deployment history, service health |
| Allure (S3) | Journey Tests | Test run reports |
