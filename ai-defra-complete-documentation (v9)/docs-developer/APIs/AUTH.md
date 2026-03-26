# Authentication

[← Back to Developer Docs](../README.md)

> **Source:** `ADR-004 - AI Assistant Architecture`, repo analysis

---

## User Authentication — Microsoft Entra ID

End users authenticate to the Frontend via **Microsoft Entra ID** (formerly Azure AD) using OAuth 2.0. The auth flow is handled server-side by the Frontend service (`src/server/auth/`). Users are not authenticated directly against the backend API services.

In local development, Entra ID auth may be bypassed or mocked — verify in the Frontend `.env.example`.

---

## API Key Authentication — Backend Services

All Agent and Knowledge service endpoints (except `/health` and `/docs`) require an `X-API-KEY` header. The Frontend holds this key and injects it on every API call — end users never interact with it directly.

| Scenario | HTTP Status |
|---|---|
| Header missing | `401 Unauthorized` |
| Header present but wrong key | `403 Forbidden` |
| Header correct | Request proceeds |

**Local development default:** `test-api-key` (set via `SEARCH_AGENT_API_KEY` environment variable).

**Production:** Inject a strong random secret via the deployment platform's secrets management. Never commit to version control.

---

## AWS Authentication

Both Agent and Knowledge services authenticate with AWS (Bedrock, SQS, S3) via standard boto3 credential resolution:

1. IAM role — recommended for production on CDP
2. Environment variables (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`)
3. `BEDROCK_BEARER_TOKEN` — for Bedrock-specific bearer auth

In local development, LocalStack and the Bedrock Stub handle all AWS calls without real credentials.

---

## Summary

| Layer | Mechanism |
|---|---|
| User → Frontend | Microsoft Entra ID (OAuth 2.0) |
| Frontend → Agent API | `X-API-KEY` header |
| Frontend → Knowledge API | `X-API-KEY` header |
| Services → AWS | IAM role (production) / LocalStack (local dev) |
