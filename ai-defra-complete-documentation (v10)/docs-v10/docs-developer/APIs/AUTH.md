# Authentication & Authorisation

[← Back to Developer Docs](../README.md)

---

## Authentication Architecture

```
Browser → Frontend (Entra ID OAuth 2.0)
Frontend → Agent Service (X-API-KEY)
Frontend → Knowledge Service (X-API-KEY + user-id header)
Services → AWS (IAM Role / LocalStack)
```

---

## User Authentication — Microsoft Entra ID

The Frontend manages all user authentication via **Microsoft Entra ID** (Azure AD), using the OAuth 2.0 authorisation code flow.

| Setting | Description |
|---|---|
| Provider | Microsoft Entra ID |
| Flow | OAuth 2.0 authorisation code |
| Session | Server-side session, signed with `SESSION_SECRET` |
| Cache | Conversation state cached in Redis per user session |

Required environment variables:
- `ENTRA_CLIENT_ID`
- `ENTRA_CLIENT_SECRET`
- `ENTRA_TENANT_ID`

> In local development, Entra ID authentication may be bypassed or mocked depending on the local configuration.

---

## Service-to-Service Authentication — API Keys

Inter-service calls use API key authentication via HTTP headers.

### Frontend → Agent Service

```
Header: X-API-KEY: <agent-api-key>
Header: user-id: <user-uuid>
```

The `user-id` header carries the authenticated user's identifier for per-user conversation isolation.

### Frontend → Knowledge Service

```
Header: X-API-KEY: <knowledge-api-key>
Header: user-id: <user-uuid>
```

### Agent Service → Knowledge Service (RAG search)

```
Header: X-API-KEY: <knowledge-api-key>
```

API keys are configured via environment variables (`AGENT_API_KEY`, `KNOWLEDGE_API_KEY`) and must match between caller and service. They are stored as CDP secrets in non-local environments.

---

## AWS Authentication

### Production / CDP

Services use **IAM roles** attached to their CDP deployment. No static AWS credentials are stored or needed.

### Local Development

**LocalStack** emulates S3, SQS, and other AWS services locally. The services connect using dummy credentials (LocalStack accepts any):

```
AWS_ACCESS_KEY_ID=test
AWS_SECRET_ACCESS_KEY=test
AWS_REGION=eu-west-2
AWS_ENDPOINT_URL=http://localhost:4566   # LocalStack endpoint
```

---

## Security Notes

- All API keys must be rotated if compromised — update in CDP secrets store and redeploy
- The `user-id` header is passed by the Frontend after successful Entra ID authentication; downstream services trust it without re-validating
- `SESSION_SECRET` must be cryptographically strong (≥ 32 random bytes) and rotated periodically
- The Bedrock Stub should **never** be deployed to production or any internet-accessible environment
