# API Request Examples

**Working examples in multiple languages**

---

## Chat API

### cURL

```bash
curl -X POST http://localhost:8086/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello"}'
```

### Python

```python
import httpx
import asyncio

async def main():
    async with httpx.AsyncClient() as client:
        response = await client.post(
            "http://localhost:8086/chat",
            json={"message": "Hello"}
        )
        print(response.json())

asyncio.run(main())
```

### JavaScript

```javascript
const response = await fetch('http://localhost:8086/chat', {
  method: 'POST',
  headers: {'Content-Type': 'application/json'},
  body: JSON.stringify({message: 'Hello'})
});
console.log(await response.json());
```

---

## Search API

### cURL

```bash
curl -X POST http://localhost:8085/rag/search \
  -H "Content-Type: application/json" \
  -d '{"query": "agriculture"}'
```

---

## See Also

- [../api-reference/AGENT-API.md](../api-reference/AGENT-API.md)
- [../api-reference/KNOWLEDGE-API.md](../api-reference/KNOWLEDGE-API.md)
