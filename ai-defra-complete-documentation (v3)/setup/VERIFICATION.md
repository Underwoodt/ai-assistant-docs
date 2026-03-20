# Verification Checklist

**Confirm everything is set up correctly**

---

## Checklist

### ✅ Services Running

```bash
# Check all containers
docker compose ps
```

Look for:
- ✅ frontend (healthy)
- ✅ agent (healthy)
- ✅ knowledge (healthy)
- ✅ postgres (healthy)
- ✅ mongo (healthy)
- ✅ redis (healthy)

### ✅ Frontend Accessible

```bash
curl http://frontend.localhost
# Should return HTML
```

Or open browser: http://frontend.localhost

### ✅ Agent API Health

```bash
curl http://localhost:8086/health
# Should return {"status": "ok"} or similar
```

[Source: ai-defra-search-agent/app/entrypoints/](https://github.com/DEFRA/ai-defra-search-agent/blob/main/app/entrypoints/)

### ✅ Knowledge API Health

```bash
curl http://localhost:8085/health
# Should return {"status": "ok"} or similar
```

[Source: ai-defra-search-knowledge/app/entrypoints/](https://github.com/DEFRA/ai-defra-search-knowledge/blob/main/app/entrypoints/)

### ✅ Databases Connected

```bash
# PostgreSQL
docker compose exec postgres psql -U postgres -d postgres -c "SELECT version();"

# MongoDB
docker compose exec mongo mongosh --eval "db.adminCommand('ping')"

# Redis
docker compose exec redis redis-cli ping
```

---

## Test Chat

```bash
curl -X POST http://localhost:8086/chat \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Hello",
    "session_id": "test-123"
  }'
```

⚠️ **UNVERIFIED:** Exact response format not documented  
See [../api-reference/AGENT-API.md](../api-reference/AGENT-API.md) for current schema

---

## Everything Working?

✅ All checks passed? Great! You're ready to:

1. [../api-reference/EXAMPLES.md](../api-reference/EXAMPLES.md) - Try API calls
2. [../architecture/SYSTEM-DESIGN.md](../architecture/SYSTEM-DESIGN.md) - Learn architecture
3. [../examples/API-REQUESTS.md](../examples/API-REQUESTS.md) - Copy examples

---

## Something Not Working?

1. Check [../troubleshooting/COMMON-ISSUES.md](../troubleshooting/COMMON-ISSUES.md)
2. Review [../troubleshooting/DEBUGGING.md](../troubleshooting/DEBUGGING.md)
3. Check logs: `docker compose logs -f [service-name]`
