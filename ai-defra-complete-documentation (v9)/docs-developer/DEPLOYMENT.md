# Deployment Guide

[← Back to Developer Docs](README.md)

> **Source:** Repo structure, Docker Compose configurations, devcontainer analysis

---

## Target Platform

All services are designed for deployment to **DEFRA's Common Deployment Platform (CDP)**. Each service is containerised via a `Dockerfile` and follows CDP conventions for configuration, secrets management, and network topology.

---

## Container Images

Each service produces its own Docker image:

| Service | Dockerfile Location | Port |
|---|---|---|
| Frontend | `ai-defra-search-frontend/Dockerfile` | 3000 |
| Agent | `ai-defra-search-agent/Dockerfile` | 8086 |
| Knowledge | `ai-defra-search-knowledge/Dockerfile` | 8085 |
| Bedrock Stub | `ai-defra-search-aws-bedrock-stub/Dockerfile` | 8087 |

---

## Infrastructure Dependencies

The following managed services must be provisioned before deploying:

| Service | Provider | Notes |
|---|---|---|
| PostgreSQL + pgvector | Self-hosted or managed | Custom `pgvector.Dockerfile` used locally |
| MongoDB 6.0 | Managed (CDP or Atlas) | Used by Agent for conversation state |
| Redis 7.2 | Managed | Used by Frontend for session cache |
| AWS SQS | AWS | Chat job queue |
| AWS S3 | AWS | Document storage (Knowledge service) |
| AWS Bedrock | AWS | LLM and embedding API |

In local development, LocalStack replaces SQS, S3, and other AWS services. The Bedrock Stub replaces the Bedrock API.

---

## Environment Configuration

All services are configured exclusively via environment variables. Secrets should be injected at runtime by the deployment platform — never committed to version control.

Key variables per service are documented in `SETUP.md`. Each service also provides a `.env.example` template.

---

## CI/CD

GitHub Actions workflows are defined in each repo's `.github/` directory. Key pipeline stages:

1. Lint (Ruff / ESLint)
2. Test (pytest / Vitest)
3. Security scan (Trivy)
4. SonarCloud analysis
5. Docker image build
6. Deploy to CDP (configuration-dependent — verify per repo)

---

## Database Migrations

The Knowledge service includes a `migrator/` directory for managing PostgreSQL schema migrations (pgvector tables). Run migrations before starting the service in a new environment.

> ⚠ Migration tooling details — verify in `ai-defra-search-knowledge/migrator/`

---

## Monitoring

Both Agent and Knowledge services emit CloudWatch metrics via the AWS Embedded Metrics Format (`aws-embedded-metrics` library). Ensure `AWS_REGION` and appropriate IAM permissions are configured in production for metrics to reach CloudWatch.

Structured JSON logging is configured via `ecs-logging` and `asgi-logger` for log aggregation compatibility.
