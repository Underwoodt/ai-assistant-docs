# Testing Guide

[← Back to Developer Docs](README.md)

> **Source:** Per-service `pyproject.toml`, `package.json`, repo structure analysis

---

## Test Strategy

| Layer | Tool | Services | Coverage Target |
|---|---|---|---|
| Unit tests | pytest (Python) | Agent, Knowledge | 90% minimum |
| Unit tests | Vitest (JS) | Frontend | Not specified |
| Integration tests | pytest + testcontainers | Agent | — |
| End-to-end / journey | WebdriverIO | All (via Frontend) | Key user flows |
| Performance / load | Apache JMeter | Agent | Baseline TBD |
| Accessibility | WebdriverIO | Frontend | WCAG 2.1 AA |

Journey tests and performance tests are maintained in separate repos (`ai-defra-search-journey-tests`, `ai-defra-search-perf-tests`) and are excluded from this documentation scope.

---

## Running Tests

### Agent

```bash
cd services/ai-defra-search-agent
uv run task test
# Runs: ruff lint → pytest with HTML + XML coverage reports in ./coverage
```

### Knowledge

```bash
cd services/ai-defra-search-knowledge
uv run task test
# Runs: format + lint → pytest with coverage
```

### Frontend

```bash
cd services/ai-defra-search-frontend
npm test
# Runs: ESLint + Stylelint → Webpack build → Vitest with coverage
```

---

## Python Testing Standards

- Framework: pytest (function-style only — no class-based tests)
- Use pytest built-in fixtures (`tmp_path`, `monkeypatch`, `mocker`)
- File naming: `test_*.py` or `*_test.py`
- Minimum 90% unit test coverage enforced in CI
- `testcontainers` used in Agent for integration tests requiring real databases

---

## CI/CD

All services run tests on pull requests via GitHub Actions. Additional quality gates:

- **Trivy** — vulnerability scanning on PRs and daily schedule
- **SonarCloud** — code quality and coverage analysis
- **Ruff** — Python linting (Agent, Knowledge)
- **ESLint + Stylelint** — JS/CSS linting (Frontend)
- **Pre-commit hooks** — enforced locally and in CI
