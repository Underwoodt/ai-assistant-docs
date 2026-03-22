# Deployment — CDP (Common Deployment Platform)

> 🔴 **This file replaces the previous KUBERNETES.md.**
> The previous version was a placeholder that admitted "KUBERNETES MANIFESTS NOT FOUND IN SOURCE." No Kubernetes manifests exist in any source repository. The system is deployed to **Defra's CDP (Common Deployment Platform)**, not self-managed Kubernetes.

---

## How This System is Deployed

AI DEFRA Search is deployed to CDP, Defra's shared delivery platform. Docker images are built automatically on merges to `main` for services that include GitHub Actions workflows.

Source: [ai-defra-search-journey-tests README](https://github.com/DEFRA/ai-defra-search-journey-tests#readme) (references CDP pipeline pattern shared across repos)

---

## Docker Images

Each service builds its own Docker image. All services include a `Dockerfile` at the repo root.

| Service | Image built by |
|---|---|
| [ai-defra-search-agent](https://github.com/DEFRA/ai-defra-search-agent) | `.github/workflows` on `main` merge |
| [ai-defra-search-knowledge](https://github.com/DEFRA/ai-defra-search-knowledge) | `.github/workflows` on `main` merge |
| [ai-defra-search-frontend](https://github.com/DEFRA/ai-defra-search-frontend) | `.github/workflows` on `main` merge |
| [ai-defra-search-aws-bedrock-stub](https://github.com/DEFRA/ai-defra-search-aws-bedrock-stub) | `.github/workflows` on `main` merge |

---

## Local / Development Deployment

For local development, all services run via Docker Compose from the Core repo.

**Default (with WireMock Bedrock stub):**

```bash
# From ai-defra-search-core
docker-compose up --build
```

**With real AWS Bedrock:**

```bash
docker compose -f compose.yml -f compose.real-bedrock.yml up
```

Requires `AWS_BEARER_TOKEN_BEDROCK` set in `.env`. Regenerate expired tokens via the AWS Bedrock console.

Source: [ai-defra-search-core README](https://github.com/DEFRA/ai-defra-search-core#readme)

---

## Journey / Acceptance Tests on CDP

Tests are run as CDP Test Suites. The test image must:
- Return exit code `0` for a passing suite
- Publish reports to S3 via `./bin/publish-tests.sh`

Build time after merging to `main`: typically 1–2 minutes.

Source: [ai-defra-search-journey-tests README](https://github.com/DEFRA/ai-defra-search-journey-tests#readme)

---

## Environment Variables

Each service documents its required environment variables in its own README. Set these in your CDP environment configuration.

Key variables for production:

| Variable | Service | Notes |
|---|---|---|
| `AWS_REGION` | Agent | e.g. `eu-west-2` |
| `AWS_BEDROCK_DEFAULT_GENERATION_MODEL` | Agent | Production model ID |
| `AWS_BEDROCK_AVAILABLE_GENERATION_MODELS` | Agent | JSON array |
| `KNOWLEDGE_BASE_URL` | Agent | URL of Knowledge service |
| `SESSION_CACHE_ENGINE` | Frontend | Set to `redis` in production |

Sources:
- [ai-defra-search-agent README](https://github.com/DEFRA/ai-defra-search-agent#readme)
- [ai-defra-search-frontend README](https://github.com/DEFRA/ai-defra-search-frontend#readme)

---

## ⚠️ What is Not Yet Documented

- CDP-specific deployment manifests or Helm values — not present in source repos
- Production infrastructure topology (number of instances, resource limits)
- DNS / domain configuration for production

If you need this information, consult Defra's internal CDP documentation or the platform team.

---

## See Also

- [DOCKER.md](./DOCKER.md) — single-host Docker deployment
- [ai-defra-search-core](https://github.com/DEFRA/ai-defra-search-core) — Docker Compose orchestration
- [SYSTEM-DESIGN.md](../architecture/SYSTEM-DESIGN.md)
