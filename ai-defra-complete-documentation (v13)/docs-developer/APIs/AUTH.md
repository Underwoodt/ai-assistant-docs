# Authentication & Authorisation

[← Back to Developer Docs](../README.md)



---

## Architecture

```
Browser → Frontend          Entra ID OAuth 2.0
Frontend → Agent            X-API-KEY header
Frontend → Knowledge        X-API-KEY + user-id header
Services → AWS              IAM Role (prod) / LocalStack dummy creds (local)
```

---

## User Authentication — Microsoft Entra ID

| Setting | Value |
|---|---|
| Provider | Microsoft Entra ID (Azure AD) |
| Flow | OAuth 2.0 authorisation code |
| Session | Server-side, signed with `SESSION_SECRET` |
| Cache | Conversation state in Redis per session |

Required env vars: `ENTRA_CLIENT_ID`, `ENTRA_CLIENT_SECRET`, `ENTRA_TENANT_ID`

> In local development, Entra ID may be bypassed or mocked depending on the local configuration.

---

## Service-to-Service — API Keys

### Frontend → Agent

```
X-API-KEY: <agent-api-key>
user-id: <user-uuid>
```

### Frontend → Knowledge

```
X-API-KEY: <knowledge-api-key>
user-id: <user-uuid>
```

### Agent → Knowledge (RAG search)

```
X-API-KEY: <knowledge-api-key>
```

Keys are configured in environment variables and stored as CDP secrets in non-local environments.

---

## AWS Authentication

**Production / CDP:** IAM roles attached to CDP deployments — no static credentials.

**Local development:** LocalStack accepts dummy credentials:

```
AWS_ACCESS_KEY_ID=test
AWS_SECRET_ACCESS_KEY=test
AWS_REGION=eu-west-2
AWS_ENDPOINT_URL=http://localhost:4566
```

---

## Security Notes

- API keys must be rotated if compromised — update in CDP secrets store and redeploy
- The `user-id` header is passed by Frontend after successful Entra ID auth; downstream services trust it without re-validating
- `SESSION_SECRET` must be cryptographically strong (≥ 32 random bytes)
- The Bedrock Stub must **never** be deployed to production or any internet-accessible environment
