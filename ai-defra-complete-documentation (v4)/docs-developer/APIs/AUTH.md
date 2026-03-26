# Authentication

> **Source:** `ai-defra-search-agent` API schema analysis

---

## API Key Authentication

All Agent and Knowledge service endpoints (except `/health` and `/docs`) require an `X-API-KEY` header.

| Scenario | HTTP Status |
|---|---|
| Header missing | `401 Unauthorized` |
| Header present but wrong key | `403 Forbidden` |
| Header correct | Request proceeds |

### Local development

The default local API key is `test-api-key`, set via the `SEARCH_AGENT_API_KEY` environment variable.

### Production

Set `SEARCH_AGENT_API_KEY` to a strong random secret in your deployment secrets store. The Frontend holds this key and injects it on every API call — users never see or interact with it directly.

---

## AWS Authentication

Both Agent and Knowledge services authenticate with AWS (for Bedrock, SQS, S3) via standard boto3 credential resolution:

1. Environment variables (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`)
2. IAM role (recommended for production on CDP)
3. `BEDROCK_BEARER_TOKEN` for Bedrock-specific bearer auth

In local development, LocalStack and the Bedrock stub handle AWS calls without real credentials.
