# AI DEFRA Search — Documentation

> Reference implementation of a RAG chat assistant built for DEFRA's Core Delivery Platform (CDP).
> Licensed under the Open Government Licence v3.

---

## Contents

### [HIGH_LEVEL_OVERVIEW.md](HIGH_LEVEL_OVERVIEW.md)
The primary narrative document — start here. Covers what was built, why, what it can do, its limitations, and ideas for future work. Suitable for all audiences.

---

### [docs-developer/](docs-developer/README.md) — Developer Documentation

Technical reference for engineers working on or adopting the codebase.

| Document | Description |
|---|---|
| [ARCHITECTURE.md](docs-developer/ARCHITECTURE.md) | System diagram, service responsibilities, data flows, auth layers |
| [SETUP.md](docs-developer/SETUP.md) | Prerequisites, local setup, environment configuration, verification |
| [DESIGN-PATTERNS.md](docs-developer/DESIGN-PATTERNS.md) | RAG pipeline, async queue, 12-factor config, layered testing |
| [TESTING.md](docs-developer/TESTING.md) | Test strategy, how to run tests per service, CI/CD quality gates |
| [DEPLOYMENT.md](docs-developer/DEPLOYMENT.md) | CDP deployment, containers, infrastructure, monitoring |
| [TROUBLESHOOTING.md](docs-developer/TROUBLESHOOTING.md) | Common issues and fixes |
| [services/](docs-developer/README.md#services) | Per-service deep-dives |
| [APIs/](docs-developer/README.md#apis) | REST endpoint reference and authentication |

---

### [docs-stakeholder/](docs-stakeholder/README.md) — Stakeholder Documentation

Plain-English documentation for non-technical audiences — policy leads, programme managers, and senior stakeholders.

| Document | Description |
|---|---|
| [OVERVIEW.md](docs-stakeholder/OVERVIEW.md) | What the tool is, why it was built, who it's for |
| [FEATURES.md](docs-stakeholder/FEATURES.md) | What users can do and what the tool does not support |
| [ARCHITECTURE-SIMPLE.md](docs-stakeholder/ARCHITECTURE-SIMPLE.md) | How it works in plain English — no code or technical terms |
| [SECURITY-PRIVACY.md](docs-stakeholder/SECURITY-PRIVACY.md) | Data handling, access controls, AI model usage |
| [ROADMAP.md](docs-stakeholder/ROADMAP.md) | Considerations for teams adopting this codebase |
| [FAQ.md](docs-stakeholder/FAQ.md) | Common questions and plain-English answers |
