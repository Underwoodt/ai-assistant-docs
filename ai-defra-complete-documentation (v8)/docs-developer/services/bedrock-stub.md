# AWS Bedrock Stub

> **Repo:** `ai-defra-search-aws-bedrock-stub` · **Port:** 8087 · **Stack:** WireMock

---

## Purpose

The Bedrock Stub replaces AWS Bedrock in local development, letting developers run the full system without a real AWS account or internet connectivity. It stubs the two Bedrock APIs used by the system:

- **Claude Converse API** — used by the Agent to generate LLM responses
- **Titan Embed API** — used by the Knowledge service to generate vector embeddings

---

## How It Works

WireMock intercepts HTTP requests matching Bedrock's API paths and returns preconfigured responses. The stub is configured via mapping files that define request matchers and fixed response bodies.

---

## Usage

The stub is included automatically when running `docker compose up` from the core repo (default configuration). It is **not** included in the `compose.real-bedrock.yml` variant.

```bash
# Use stub (default — no AWS account needed)
docker compose up

# Use real Bedrock
docker compose -f compose.yml -f compose.real-bedrock.yml up
```

---

## Limitations

- Stub responses are static — the LLM will return the same canned response regardless of the query
- Embedding vectors returned are fixed — similarity search results will not reflect real semantic similarity
- Suitable for development and integration testing only; not for evaluating answer quality
