# Bedrock Stub

[← Back to Developer Docs](../README.md)



**Repo:** [ai-defra-search-aws-bedrock-stub](https://github.com/DEFRA/ai-defra-search-aws-bedrock-stub) · Port: `8090` (host) / `8080` (Docker network)

---

## Overview

WireMock stub replacing AWS Bedrock for local development. Intercepts both the Claude Converse API (LLM) and Titan Embed v2 API (embeddings), returning deterministic mock responses — no AWS account or internet connection needed.

## Stubbed APIs

| API | Endpoint | Notes |
|---|---|---|
| Claude Converse | `POST /model/anthropic.claude-{model}/converse` | Fixed response, 2s simulated delay, priority-based matching |
| Titan Embed v2 | `POST /model/amazon.titan-embed-text-v2:0/invoke` | Returns mock 1024-dim vector |

## Running

```bash
docker compose up -d
curl http://localhost:8090/__admin/health
```

## Admin Interface

| Endpoint | Description |
|---|---|
| `GET /__admin/health` | Verify running |
| `GET /__admin/mappings` | View all stub mappings |
| `GET /__admin/requests` | Inspect request/response history |
| `POST /__admin/mappings` | Add mapping dynamically |

## Structure

```
wiremock/
├── mappings/     # JSON request-response mapping definitions
└── __files/      # Mock response payload files
```

## Notes

- ⛔ **Never deploy to production** — accepts all requests without authentication
- No application dependencies — pure Docker
- 2-second delay is intentional — adjust in `mappings/` if needed for faster local tests
- Supports JSONPath and text-based request pattern matching
