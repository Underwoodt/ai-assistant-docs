# Environment Variables

**Configuration examples**

---

## Standard Variables

⚠️ **UNVERIFIED:** Complete list of environment variables not found in source

### Common Variables

[Source: ai-defra-search-agent/app/config.py](https://github.com/DEFRA/ai-defra-search-agent/blob/main/app/config.py)

- `BEDROCK_ENDPOINT` - Bedrock API URL
- `KNOWLEDGE_BASE_URL` - Knowledge service URL
- `DATABASE_URL` - PostgreSQL connection
- `MONGO_URI` - MongoDB connection
- `LOG_LEVEL` - Logging level

### Database

```bash
DATABASE_URL=postgresql://user:password@postgres:5432/db
MONGO_URI=mongodb://mongo:27017
REDIS_URL=redis://redis:6379
```

---

## See Also

- [../setup/INSTALLATION.md](../setup/INSTALLATION.md)
- [../deployment/DOCKER.md](../deployment/DOCKER.md)
