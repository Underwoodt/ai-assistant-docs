# Deployment Checklist

**Pre-deployment verification steps**

---

## Pre-Deployment

- [ ] All services tested locally
- [ ] Databases verified
- [ ] Environment variables configured
- [ ] Secrets secured
- [ ] Backups configured
- [ ] Monitoring setup

---

## Deployment

- [ ] Services started in order
- [ ] Health checks passing
- [ ] Logs monitored
- [ ] Performance metrics recorded
- [ ] User testing complete

---

## Post-Deployment

- [ ] Application responding
- [ ] All endpoints accessible
- [ ] Databases connected
- [ ] File storage working
- [ ] Cache operational

---

## Troubleshooting

If something fails:

1. Check [../troubleshooting/COMMON-ISSUES.md](../troubleshooting/COMMON-ISSUES.md)
2. Review logs: `docker compose logs -f`
3. See [../troubleshooting/DEBUGGING.md](../troubleshooting/DEBUGGING.md)

---

## See Also

- [DOCKER.md](./DOCKER.md)
- [KUBERNETES.md](./KUBERNETES.md)
- [../troubleshooting/](../troubleshooting/)
