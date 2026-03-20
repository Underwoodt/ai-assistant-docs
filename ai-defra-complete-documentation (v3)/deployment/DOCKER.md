# Docker Deployment

**Deploy using Docker and Docker Compose**

---

## Prerequisites

- Docker 20.10+
- Docker Compose 1.29+

---

## Development Setup

See [../setup/INSTALLATION.md](../setup/INSTALLATION.md)

---

## Production Considerations

⚠️ **UNVERIFIED:** Production-specific configurations not fully documented

### Environment Separation

```bash
# Development
docker compose -f compose.yml up

# Production would need:
# - Secure environment variables
# - Volume persistence
# - Network configuration
# - Backup strategies
```

---

## Configuration

[Source: ai-defra-search-core/compose.yml](https://github.com/DEFRA/ai-defra-search-core/blob/main/compose.yml)

Key environment variables:
- Database URLs
- API endpoints
- Security credentials

See [../examples/ENV-VARIABLES.md](../examples/ENV-VARIABLES.md)

---

## Persistence

⚠️ **UNVERIFIED:** Backup and persistence strategies not documented

---

## Scaling

⚠️ **UNVERIFIED:** Docker Compose scaling approach not documented

---

## See Also

- [KUBERNETES.md](./KUBERNETES.md) - Kubernetes deployment
- [CHECKLIST.md](./CHECKLIST.md) - Pre-deployment checklist
- [../troubleshooting/](../troubleshooting/) - Debugging
