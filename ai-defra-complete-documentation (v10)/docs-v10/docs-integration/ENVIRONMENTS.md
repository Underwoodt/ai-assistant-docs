# Environments

[← Back to Integration Docs](README.md)

---

## Environment Summary

| Environment | Purpose | LLM / Embed | S3 / SQS | Auth | Routing |
|---|---|---|---|---|---|
| **Local** | Developer workstation | Bedrock Stub (WireMock) | LocalStack | Mocked | Traefik (docker-compose) |
| **Dev** | Shared development | AWS Bedrock (real) | AWS (real) | Entra ID (real) | CDP ingress |
| **Test** | Integration / acceptance | AWS Bedrock (real) | AWS (real) | Entra ID (real) | CDP ingress |
| **Production** | Live | AWS Bedrock (real) | AWS (real) | Entra ID (real) | CDP ingress |

---

## Local Environment

**How to run:** `docker compose up` from the `ai-defra-search-core` meta-repo.

**Key differences from production:**
- Bedrock Stub replaces real AWS Bedrock — responses are fixed mock payloads with 2-second simulated delay
- LocalStack replaces real AWS S3 and SQS — no cloud account needed
- Microsoft Entra ID authentication may be bypassed or mocked
- Traefik handles routing; access at `http://frontend.localhost`
- All services run on a single machine; no redundancy

**Data seeding:** The perf-test repo (`ai-defra-search-perf-tests`) includes MongoDB and PostgreSQL init scripts for populating test data automatically.

---

## CDP Environments (Dev / Test / Production)

Deployed via DEFRA's Core Delivery Platform. Each environment is a separate CDP namespace.

**Key differences from local:**
- All services run as isolated containers in CDP's managed infrastructure
- AWS credentials are provided via IAM roles — no static keys
- Secrets are managed via CDP's secrets store — not `.env` files
- Services are accessed via CDP's managed ingress
- MongoDB, PostgreSQL, and Redis are CDP-managed — not self-hosted containers

---

## Environment Variables

Key variables that differ between environments:

| Variable | Local | CDP |
|---|---|---|
| `AWS_ENDPOINT_URL` | `http://localhost:4566` (LocalStack) | Not set (uses real AWS endpoints) |
| `BEDROCK_ENDPOINT_URL` | `http://wiremock:8080` (stub) | Not set (uses real Bedrock) |
| `MONGODB_URI` | `mongodb://localhost:27017` | CDP-managed secret |
| `POSTGRES_URI` | `postgresql://localhost:5432` | CDP-managed secret |
| `REDIS_URI` | `redis://localhost:6379` | CDP-managed secret |
| `ENTRA_CLIENT_SECRET` | Mocked / test value | CDP-managed secret |
| `SESSION_SECRET` | Local test value | CDP-managed secret |
| `CLOUDWATCH_ENABLED` | `false` | `true` |

---

## Journey Test Targets

The journey test suite (`ai-defra-search-journey-tests`) can run against any environment:

| Config file | Target |
|---|---|
| `wdio.local.conf.js` | Local Docker stack |
| `wdio.github.conf.js` | GitHub Actions (docker-compose) |
| `wdio.conf.js` | CDP environment |
| `wdio.cdp.a11y.conf.js` | CDP accessibility tests |

The base URL pattern for CDP is: `https://ai-defra-search-journey-tests.${ENVIRONMENT}.cdp-int.defra.cloud`
