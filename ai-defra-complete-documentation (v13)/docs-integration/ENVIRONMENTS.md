# Environments

[← Back to Integration & Deployment](README.md)



---

## Environment Summary

| Environment | Purpose | LLM / Embed | S3 / SQS | Auth | Routing |
|---|---|---|---|---|---|
| **Local** | Developer workstation | Bedrock Stub | LocalStack | Mocked | Traefik |
| **Dev** | Shared development | AWS Bedrock | AWS | Entra ID | CDP ingress |
| **Test** | Integration / acceptance | AWS Bedrock | AWS | Entra ID | CDP ingress |
| **Production** | Live | AWS Bedrock | AWS | Entra ID | CDP ingress |

---

## Local Environment

**Start:** `docker compose up` from `ai-defra-search-core`

**Key differences from production:**
- Bedrock Stub replaces AWS Bedrock — fixed mock responses with 2s simulated delay
- LocalStack replaces real AWS S3 and SQS — no cloud account needed
- Entra ID auth may be bypassed or mocked
- Traefik handles routing: `http://frontend.localhost`
- All services on one machine; no redundancy

**Data seeding:** `ai-defra-search-perf-tests` includes MongoDB and PostgreSQL init scripts for automatic test data population.

---

## CDP Environments

Each environment is a separate CDP namespace. Services run as isolated containers in CDP's managed infrastructure.

**Key differences from local:**
- AWS credentials provided via IAM roles — no static keys
- Secrets managed via CDP secrets store — not `.env` files
- MongoDB, PostgreSQL, and Redis are CDP-managed
- Services accessed via CDP managed ingress

---

## Environment Variable Differences

| Variable | Local | CDP |
|---|---|---|
| `AWS_ENDPOINT_URL` | `http://localhost:4566` | Not set (real AWS) |
| `BEDROCK_ENDPOINT_URL` | `http://wiremock:8080` | Not set (real Bedrock) |
| `MONGODB_URI` | `mongodb://localhost:27017` | CDP secret |
| `POSTGRES_URI` | `postgresql://localhost:5432` | CDP secret |
| `REDIS_URI` | `redis://localhost:6379` | CDP secret |
| `ENTRA_CLIENT_SECRET` | Test value | CDP secret |
| `CLOUDWATCH_ENABLED` | `false` | `true` |

---

## Journey Test Targets

| Config | Target |
|---|---|
| `wdio.local.conf.js` | Local Docker stack |
| `wdio.github.conf.js` | GitHub Actions |
| `wdio.conf.js` | CDP |
| `wdio.cdp.a11y.conf.js` | CDP accessibility |

CDP base URL pattern: `https://ai-defra-search-journey-tests.${ENVIRONMENT}.cdp-int.defra.cloud`
