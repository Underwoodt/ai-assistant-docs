# API Examples

**Working code examples for both APIs**

---

## Chat API Examples

### cURL

```bash
curl -X POST http://localhost:8086/chat \
  -H "Content-Type: application/json" \
  -d '{
    "message": "What is DEFRA?",
    "session_id": "user-123"
  }'
```

### Python

```python
import httpx
import asyncio

async def chat():
    async with httpx.AsyncClient() as client:
        response = await client.post(
            "http://localhost:8086/chat",
            json={
                "message": "What is DEFRA?",
                "session_id": "user-123"
            }
        )
        print(response.json())

asyncio.run(chat())
```

### JavaScript

```javascript
const response = await fetch('http://localhost:8086/chat', {
  method: 'POST',
  headers: {'Content-Type': 'application/json'},
  body: JSON.stringify({
    message: 'What is DEFRA?',
    session_id: 'user-123'
  })
});
const data = await response.json();
console.log(data);
```

---

## Knowledge API Examples

### Search Documents

```bash
curl -X POST http://localhost:8085/rag/search \
  -H "Content-Type: application/json" \
  -d '{
    "query": "agricultural policy",
    "limit": 5
  }'
```

---

## See Also

- [AGENT-API.md](./AGENT-API.md) - Chat API docs
- [KNOWLEDGE-API.md](./KNOWLEDGE-API.md) - Search API docs
- [../examples/API-REQUESTS.md](../examples/API-REQUESTS.md) - More examples
