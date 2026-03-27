# Bedrock Stub Service

[← Back to Developer Docs](../README.md)

**Repo:** [ai-defra-search-aws-bedrock-stub](https://github.com/DEFRA/ai-defra-search-aws-bedrock-stub)

---

## Overview

A WireMock-based stub service that replaces AWS Bedrock for local development and testing. It intercepts calls to both the Claude Converse API (LLM) and the Titan Embed Text V2 API (embeddings), returning deterministic mock responses without any AWS account or internet connection.

## Stack

| Technology | Role |
|---|---|
| WireMock | HTTP stub / mock server |
| Docker | Container runtime |
| Docker Compose | Orchestration |

## Stubbed APIs

### Claude Converse API

```
POST /model/anthropic.claude-{model}/converse
```

- Returns a fixed multi-turn conversation response
- Supports priority-based matching for different scenario handling
- Configures a **2-second fixed delay** to simulate realistic LLM latency

### Titan Embed Text V2 API

```
POST /model/amazon.titan-embed-text-v2:0/invoke
```

- Returns a mock 1024-dimensional embedding vector
- Used by the Knowledge Service for indexing and by the Agent for query embedding

## Ports

| URL | Description |
|---|---|
| `http://localhost:8090` | External access (from host) |
| `http://wiremock:8080` | Internal Docker network access |

## Running

```bash
docker compose up -d
curl http://localhost:8090/__admin/health
```

## Admin Interface

| Endpoint | Description |
|---|---|
| `GET /__admin/health` | Verify service is running |
| `GET /__admin/mappings` | View all loaded stub mappings |
| `GET /__admin/requests` | Inspect request/response history |
| `POST /__admin/mappings` | Add a new mapping dynamically |

## Repository Structure

```
wiremock/
├── mappings/     # JSON request-response mapping definitions
└── __files/      # Mock response payload files
```

## Notes

- **Never deploy to production** — the stub accepts all requests without authentication and is not suitable for any internet-accessible environment
- **No application dependencies** — pure Docker; no Python, Node, or Java required to run
- **Latency simulation** — the 2-second delay is intentional; adjust in `mappings/` if needed for faster local testing
- **Pattern matching** — supports JSONPath and text-based request patterns for matching different prompt shapes
