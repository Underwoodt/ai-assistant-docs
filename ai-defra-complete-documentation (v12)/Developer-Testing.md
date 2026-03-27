# Testing

> 👩‍💻 **Developer Docs** — [Home](Home) › [Developer Docs](Home#developer-docs)

---

## Test Strategy

| Layer | Tooling | Scope |
|---|---|---|
| Unit / Integration | pytest (Agent, Knowledge) | API endpoints, RAG logic, ingestion pipeline |
| Unit | Vitest (Frontend) | Components, utilities |
| End-to-End | WebdriverIO | Full user flows via browser |
| Accessibility | WebdriverIO + axe-core | WCAG 2.1 compliance |
| Performance | Apache JMeter | Load, stress, throughput |
| Security | Trivy | Container & dependency vulnerabilities |
| Code Quality | SonarCloud | Static analysis |

---

## Unit & Integration Tests

### Agent

```bash
uv run task test    # pytest
uv run task lint    # Ruff
```

### Knowledge

```bash
pytest
pre-commit run --all-files
```

### Frontend

```bash
npm test      # Vitest
npm run lint  # ESLint
```

---

## Journey & Accessibility Tests

Repo: `ai-defra-search-journey-tests` — WebdriverIO v9, Mocha, Page Object Model, axe-core.

### Run locally

```bash
./run-journey-tests-local.sh
# or manually:
docker compose up --wait -d && npm run test:local
```

### Run accessibility tests

```bash
./run-accessibility-tests-local.sh
# or:
docker compose up --wait -d && npm run test:a11y
```

### Run against CDP

```bash
./run-accessibility-tests-cdp.sh
npm run test:a11y:cdp
```

### BrowserStack

```bash
npm run test:browserstack           # CDP Portal
npm run test:github:browserstack    # GitHub Actions
```

### Allure Reports

```bash
npm run report            # Generate HTML report
npm run report:publish    # Generate + publish to S3
```

> **Note:** Java JRE 17+ required for Allure. Included automatically in Docker; install separately for local runs.

### Config Files

| File | Target |
|---|---|
| `wdio.conf.js` | Base / CDP |
| `wdio.local.conf.js` | Local Docker |
| `wdio.github.conf.js` | GitHub Actions |
| `wdio.browserstack.conf.js` | BrowserStack (CDP Portal) |
| `wdio.cdp.a11y.conf.js` | CDP accessibility |

---

## Performance Tests

Repo: `ai-defra-search-perf-tests` — JMeter with pre-seeded data.

```bash
# Containerised (recommended — no local JMeter needed)
./run-tests-local-docker.sh

# Local JMeter + Docker services
jmeter -n -t scenarios/ai-assistant.jmx \
  -l reports/test-results.csv -e -o reports \
  -Jthreads=50 -Jrampup=60 -Jduration=300
```

| Parameter | Default | Description |
|---|---|---|
| `threads` | 10 | Concurrent virtual users |
| `rampup` | 30s | Ramp-up time |
| `duration` | 300s | Total run time |
| `http_timeout` | 30000ms | Per-request timeout |

---

## CI/CD Pipelines

| Workflow | Trigger | Action |
|---|---|---|
| `check-pull-request.yml` | PR / push | Lint, test, Trivy scan |
| `publish.yml` | Merge to main | Build & push Docker image |
| `journey-tests.yml` | Manual | Run journey tests |
| `accessibility-tests.yml` | Manual | Run accessibility tests |
| `scan.yml` | Scheduled | Trivy vulnerability scan |
