# Testing

[← Back to Developer Docs](README.md)

---

## Test Strategy Overview

| Layer | Tooling | Scope | Where |
|---|---|---|---|
| Unit / Integration | pytest (Agent, Knowledge) | API endpoints, RAG logic, ingest pipeline | Per-service |
| Unit | Vitest (Frontend) | Components, utilities | Frontend service |
| End-to-End | WebdriverIO (Journey Tests) | Full user flows via browser | `ai-defra-search-journey-tests` |
| Accessibility | WebdriverIO + axe-core | WCAG 2.1 compliance | `ai-defra-search-journey-tests` |
| Performance | Apache JMeter | Load, stress, throughput | `ai-defra-search-perf-tests` |
| Security | Trivy | Container & dependency vulnerabilities | All services (CI/CD) |
| Code Quality | SonarCloud | Static analysis | All services (CI/CD) |

---

## Unit & Integration Tests

### Agent Service (pytest)

```bash
cd ai-defra-search-agent
uv run task test
```

Covers API endpoints (`/chat`, `/conversations`, `/feedback`, `/health`) and core RAG orchestration logic.

### Knowledge Service (pytest)

```bash
cd ai-defra-search-knowledge
pytest
```

Covers ingestion pipeline, embedding generation calls, pgvector search, and health endpoints.

### Frontend (Vitest)

```bash
cd ai-defra-search-frontend
npm test
```

Covers UI components, session management utilities, and upload flow handlers.

---

## Journey & Accessibility Tests

Repository: `ai-defra-search-journey-tests`

Uses **WebdriverIO v9** with **Mocha** and the **Page Object Model** pattern. Accessibility testing uses **axe-core** for WCAG 2.1 compliance checks.

### Run locally (full stack via Docker Compose)

```bash
# Option 1: one command
./run-journey-tests-local.sh

# Option 2: manual
docker compose up --wait -d
npm run test:local
```

### Run accessibility tests locally

```bash
./run-accessibility-tests-local.sh
# or
docker compose up --wait -d && npm run test:a11y
```

### Run against CDP environment

```bash
./run-accessibility-tests-cdp.sh
# or
npm run test:a11y:cdp
```

### Run on BrowserStack

```bash
npm run test:browserstack         # CDP Portal
npm run test:github:browserstack  # GitHub Actions
```

### Generate Allure Reports

```bash
npm run report             # Generate HTML report locally
npm run report:publish     # Generate and push to S3
```

> **Note:** Allure report generation requires Java JRE 17+. Docker includes it automatically; local runs need a separate install.

### Test Structure

```
test/
├── specs/
│   ├── e2e/
│   │   └── ai-assistant.e2e.js        # End-to-end journey flows
│   └── accessibility/
│       └── ai-assistant.a11y.js       # WCAG accessibility checks
├── page-objects/
│   └── ai-assistant.page.js           # Page Object Model
├── helpers/
│   └── accessibility-helper.js        # axe-core utilities
└── components/                        # Reusable test components
```

### Configuration Files

| File | Target |
|---|---|
| `wdio.conf.js` | Base / CDP |
| `wdio.local.conf.js` | Local Docker stack |
| `wdio.github.conf.js` | GitHub Actions |
| `wdio.browserstack.conf.js` | BrowserStack (CDP Portal) |
| `wdio.github.browserstack.conf.js` | BrowserStack (GitHub Actions) |
| `wdio.cdp.a11y.conf.js` | CDP accessibility |

---

## Performance Tests

Repository: `ai-defra-search-perf-tests`

Uses **Apache JMeter** with pre-seeded MongoDB and PostgreSQL data for reproducible scenarios.

### Containerised run (recommended — no local JMeter required)

```bash
./run-tests-local-docker.sh
```

### Local JMeter + Docker services

```bash
./run-tests-local.sh

# Or with custom parameters:
jmeter -n -t scenarios/ai-assistant.jmx \
  -l reports/test-results.csv -e -o reports \
  -Jthreads=50 -Jrampup=60 -Jduration=300
```

### Key Parameters

| Parameter | Default | Description |
|---|---|---|
| `threads` | 10 | Concurrent virtual users |
| `rampup` | 30s | Time to reach full thread count |
| `duration` | 300s | Total test run time |
| `http_timeout` | 30000ms | Per-request timeout |

Reports are generated in `reports/` as both HTML dashboard and CSV.

---

## Security & Quality (CI/CD)

### Trivy (all services)

```bash
trivy repository --include-dev-deps --format table --exit-code 1 \
  --severity CRITICAL,HIGH,MEDIUM,LOW --ignorefile .trivyignore .
```

Configured exceptions are listed in `.trivyignore` per service.

### SonarCloud

Runs automatically on push / PR via GitHub Actions. Configuration in `sonar-project.properties`.

---

## CI/CD Pipelines

Each service includes GitHub Actions workflows:

| Workflow | Trigger | Action |
|---|---|---|
| `check-pull-request.yml` | PR open / push | Lint, test, Trivy scan |
| `publish.yml` | Merge to main | Build & push Docker image to CDP registry |
| `journey-tests.yml` | Manual / trigger | Run journey tests against target env |
| `accessibility-tests.yml` | Manual / trigger | Run accessibility tests |
| `scan.yml` | Scheduled | Trivy vulnerability scan |
