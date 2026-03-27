# Frontend Service

[Back to Developer Docs](../README.md)

**Repo:** [ai-defra-search-frontend](https://github.com/DEFRA/ai-defra-search-frontend)
**Language:** JavaScript (86.8%), Nunjucks templates (9.7%), SCSS (2%)
**Framework:** Node.js + Hapi
**Build tools:** Webpack, Vite

---

## Purpose

The Frontend Service is the browser-based user interface. It:

- Renders the chat UI, upload screens, and knowledge group management pages
- Authenticates users via Microsoft Entra ID
- Initiates document uploads via the CDP Uploader
- Caches conversation state in Redis for resilient display during API disruptions
- Polls the Agent Service for LLM response completion (async pattern)

---

## Directory Structure

```
ai-defra-search-frontend/
├── src/              # Application source (routes, views, handlers)
├── tests/            # Vitest test suite
├── compose/          # Docker Compose configurations
├── mockoon/          # Mock API server for frontend-only development
├── scripts/          # Utility scripts
├── .vite/            # Vite build config
├── .github/          # GitHub Actions workflows
├── package.json      # Node.js dependencies
├── webpack.config.js # Webpack bundler config
├── vitest.config.js  # Test runner config
└── .example.env      # Environment variable template
```

---

## Key Responsibilities

| Responsibility | Detail |
|---|---|
| Chat UI | Renders conversation history, loading spinner during async LLM processing |
| Model selection | Allows users to choose from available Claude models per prompt |
| Knowledge group management | Create and list personal knowledge groups |
| File upload | Integrates with CDP Uploader for virus-scanned S3 delivery |
| Session caching | Redis (or in-memory) via Catbox — configurable via `SESSION_CACHE_ENGINE` |
| Conversation caching | Redis TTL cache for conversation display during Agent Service disruptions |
| Auth | Microsoft Entra ID flow |
| Upload callback handling | Receives CDP Uploader callback, filters complete files, notifies Knowledge Service |

---

## Important Access Note

> **The frontend MUST be accessed via the Traefik proxy at `http://frontend.localhost`.**
> Accessing via `localhost:3000` directly breaks AWS routing.
> Run from the meta-repo, not the individual service Compose file.

---

## Configuration

Key environment variables (see `.example.env`):

| Variable | Description |
|---|---|
| `SESSION_CACHE_ENGINE` | `redis` or `memory` — Redis required for multi-instance |
| `AGENT_SERVICE_URL` | Base URL of the Agent Service |
| `KNOWLEDGE_SERVICE_URL` | Base URL of the Knowledge Service |
| `CDP_UPLOADER_URL` | Base URL of the CDP Uploader |

---

## Running Locally

```bash
cd ai-defra-search-frontend
npm install
cp .example.env .env  # Edit as needed

# Must be run via meta-repo for correct Traefik routing
# See meta-repo README for full stack startup instructions
```

---

## Testing

```bash
npm test          # Vitest unit tests
npm run lint      # ESLint
```

For end-to-end journey and accessibility tests, see [TESTING.md](../TESTING.md) and the `ai-defra-search-journey-tests` repo.
