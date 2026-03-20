# Kubernetes Deployment

**Deploy to Kubernetes (K8s) clusters**

---

## Prerequisites

- kubectl configured
- Kubernetes cluster access
- Docker images built and pushed to registry

---

## Status

⚠️ **KUBERNETES MANIFESTS NOT FOUND IN SOURCE**

Kubernetes deployment files not present in analyzed repositories.

### What You'll Need

- Deployment manifests for each service
- Service definitions
- Ingress configuration
- ConfigMap for configuration
- Secret management
- StatefulSet for databases
- PersistentVolumes for data

---

## References

[Source: ai-defra-search-core](https://github.com/DEFRA/ai-defra-search-core)

Note: Check for Kubernetes files in:
- `k8s/` directory
- `.github/workflows/` for deployment pipelines
- Helm charts (if using Helm)

---

## Next Steps

1. Check repositories for K8s manifests
2. Review CDP deployment documentation
3. Use Docker deployment as reference

---

## See Also

- [DOCKER.md](./DOCKER.md) - Docker deployment
- [CHECKLIST.md](./CHECKLIST.md) - Pre-deployment
- [../troubleshooting/](../troubleshooting/) - Debugging
