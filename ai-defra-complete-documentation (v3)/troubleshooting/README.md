# Troubleshooting Guide

**Debugging and resolving common issues**

---

## Quick Help

1. **Service won't start** → [COMMON-ISSUES.md](./COMMON-ISSUES.md)
2. **Something broken** → [DEBUGGING.md](./DEBUGGING.md)
3. **Need to understand logs** → [LOGS.md](./LOGS.md)

---

## Common Issues

### Services Won't Start
→ [COMMON-ISSUES.md](./COMMON-ISSUES.md#services-wont-start)

### Port Conflicts
→ [COMMON-ISSUES.md](./COMMON-ISSUES.md#port-conflicts)

### Database Errors
→ [COMMON-ISSUES.md](./COMMON-ISSUES.md#database-errors)

### API Not Responding
→ [DEBUGGING.md](./DEBUGGING.md#api-debugging)

---

## Debugging Steps

1. Check service status: `docker compose ps`
2. View logs: `docker compose logs -f [service]`
3. Check health endpoints
4. Verify network connectivity

---

## See Also

- [COMMON-ISSUES.md](./COMMON-ISSUES.md)
- [DEBUGGING.md](./DEBUGGING.md)
- [LOGS.md](./LOGS.md)
