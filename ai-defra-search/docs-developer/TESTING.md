# Testing

[Back to Developer Docs](./README.md)

---

## Test Strategy Overview

| Test Type | Repo | Framework | Coverage |
|---|---|---|---|
| Unit & integration | `ai-defra-search-agent` | pytest | Agent Service business logic, Bedrock calls, SQS handling |
| Unit & integration | `ai-defra-search-knowledge` | pytest | Ingest pipeline, chunking, embedding, vector search |
| Unit & integration | `ai-defra-search-frontend` | Vitest | Frontend routes, caching, upload flows |
| End-to-end / journey | `ai-defra-search-journey-tests` | WebdriverIO | User-facing flows: chat, upload, knowledge group creation |
| Accessibility | `ai-defra-search-journey-tests` | WebdriverIO + axe | WCAG compliance on key pages |
| Performance / load | `ai-defra-search-perf-tests` | Apache JMeter | Latency, throughput, concurrent user simulation |

---

## Unit & Integration Tests

### Agent Service

```bash
cd ai-defra-search-agent
uv run pytest
```

Uses **pytest**. Tests cover FastAPI route handlers, SQS message processing, Bedrock integration, and MongoDB persistence. Ruff is used for linting.

### Knowledge Service

```bash
cd ai-defra-search-knowledge
uv run pytest
```

Tests cover the ingest pipeline (extractors, chunking), embedding generation, pgvector insertion, and the `/rag/search` endpoint.

### Frontend

```bash
cd ai-defra-search-frontend
npm test
```

Uses **Vitest**. Tests cover Hapi route handlers, Redis/Catbox caching behaviour, and CDP Uploader integration.

---

## Journey & Accessibility Tests (`ai-defra-search-journey-tests`)

### Prerequisites

- Node.js v20+, npm v9+
- Java JRE 11+ (for Allure reporting)
- Docker + Docker Compose (for local stack)

### Running Locally

```bash
cd ai-defra-search-journey-tests
npm install

# Start local stack first (from meta-repo)
docker compose up -d

# Run journey tests
npm test

# Run accessibility tests
npm run test:accessibility

# Run with BrowserStack (cross-browser)
npm run test:browserstack
```

### Environments

Tests use a three-tier configuration system:

| Config layer | Purpose |
|---|---|
| Defaults | Shared base configuration |
| Service-specific | Per-environment overrides (local, CDP portal) |
| Local overrides | Developer machine overrides |

Test reports are generated with **Allure** — open `allure-results/` after a test run.

---

## Performance Tests (`ai-defra-search-perf-tests`)

### Test Scenario

The primary test (`ai-assistant.jmx`) simulates concurrent users interacting with the full RAG chat flow.

| Parameter | Default | Override Flag |
|---|---|---|
| Concurrent threads | 20 | `-Jthreads=N` |
| Ramp-up period | 10 seconds | `-Jrampup=N` |
| Sustained duration | 60 seconds | `-Jduration=N` |
| Max response time | 20 seconds | `-Jmaxresponse=N` |

The scenario also validates that AI responses do not contain RAG retrieval error strings.

### Running Locally

```bash
cd ai-defra-search-perf-tests

# Option 1: Direct JMeter (requires JMeter installed)
jmeter -n -t ai-assistant.jmx -Jthreads=10 -Jduration=30

# Option 2: Automated local run with services
./run-tests-local.sh

# Option 3: Fully containerised (no local JMeter needed)
./run-tests-local-docker.sh
```

The local stack for performance tests includes MongoDB, PostgreSQL, Redis, LocalStack, the Bedrock stub, and all three application services — orchestrated via Docker Compose.

---

## CI/CD Integration

All repos include **GitHub Actions** workflows that:

- Run unit/integration tests on every PR
- Run Trivy vulnerability scanning
- Run SonarCloud static analysis
- Journey tests run against the CDP portal environment on deployment
