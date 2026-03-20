# Data Flows

**How data moves through the system**

---

## Chat Request Flow

```
1. User types message
   ↓
2. Frontend sends POST /chat
   ↓
3. Agent receives request
   ↓
4. Query embedding generated (Bedrock)
   ↓
5. POST /rag/search to Knowledge API
   ↓
6. Vector search in PostgreSQL
   ↓
7. Fetch metadata from MongoDB
   ↓
8. Generate response (Bedrock)
   ↓
9. Store conversation in MongoDB
   ↓
10. Return response to Frontend
   ↓
11. Display to user
```

⚠️ **UNVERIFIED:** Exact timing - "2-3 seconds" not confirmed
- Mock Bedrock has 2000ms delay
- Real performance unknown

---

## Document Ingestion Flow

```
1. User uploads file
   ↓
2. Frontend stores in S3
   ↓
3. Knowledge API receives notification
   ↓
4. Extract text (format-specific)
   ↓
5. Split into chunks
   ↓
6. Generate embeddings (Bedrock)
   ↓
7. Store vectors in PostgreSQL
   ↓
8. Store metadata in MongoDB
   ↓
9. Ready for search
```

---

## Search Flow

```
1. User query
   ↓
2. Generate query embedding
   ↓
3. Vector similarity search
   ↓
4. Get top-K results
   ↓
5. Fetch full metadata
   ↓
6. Return to Agent
   ↓
7. Include in LLM context
```

---

## See Also

- [SYSTEM-DESIGN.md](./SYSTEM-DESIGN.md) - System overview
- [SERVICE-INTERACTIONS.md](./SERVICE-INTERACTIONS.md) - Service communication
