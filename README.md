# K3s Homelab

GitOps-managed Kubernetes homelab using ArgoCD and K3s.

## Overview

This repository contains the configuration for a K3s-based homelab cluster managed by ArgoCD. All applications are automatically deployed and synchronized using the GitOps approach.

## Architecture

- **Cluster**: K3s Kubernetes cluster
- **GitOps**: ArgoCD for continuous deployment
- **Infrastructure**: Managed via Ansible (see `infra/k3s-ansible/`)

## Applications

The following applications are deployed:

- **ArgoCD**: GitOps continuous deployment
- **cert-manager**: SSL/TLS certificate management
- **MetalLB**: Bare-metal load balancer
- **NGINX**: Web server
- **Semaphore**: CI/CD platform
- **External-DNS**: Automatic DNS record management with AdGuard Home

## Prerequisites

- K3s cluster up and running
- ArgoCD installed in the cluster
- kubectl configured to access the cluster
- Helm 3.x installed

## Setup

### 1. Deploy ArgoCD Root Application

```bash
kubectl apply -f root.yaml
```

This will automatically deploy all applications defined in the `apps/` directory.

### 2. Configure External-DNS with AdGuard Home

Before External-DNS can function with AdGuard Home, you need to create the secret containing your AdGuard credentials:

```bash
kubectl create secret generic adguard-configuration \
  -n external-dns \
  --from-literal=url='<ADGUARD_URL>' \
  --from-literal=user='<ADGUARD_USER>' \
  --from-literal=password='<ADGUARD_PASSWORD>'
```

Replace the placeholders:
- `<ADGUARD_URL>`: Your AdGuard Home URL (e.g., `https://adguard.example.com/`)
- `<ADGUARD_USER>`: Your AdGuard Home admin username
- `<ADGUARD_PASSWORD>`: Your AdGuard Home admin password

**Note**: The `external-dns` namespace will be created automatically by ArgoCD.

## Repository Structure

```
.
├── apps/                    # ArgoCD application definitions
│   ├── cert-manager.yaml
│   ├── cert-manager-issuers.yaml
│   ├── external-dns.yaml
│   ├── metallb.yaml
│   ├── nginx.yaml
│   └── semaphore.yaml
├── helms/                   # Helm chart customizations
│   ├── cert-manager/
│   └── metallb/
├── infra/                   # Infrastructure configuration
│   └── k3s-ansible/        # Ansible playbooks for K3s setup
├── root.yaml               # ArgoCD ApplicationSet
└── README.md
```

## Managing Applications

### Adding a New Application

1. Create a new YAML file in the `apps/` directory
2. Follow the existing pattern (see `apps/nginx.yaml` as an example)
3. Commit and push to the repository
4. ArgoCD will automatically detect and deploy the new application

### Updating an Application

1. Modify the corresponding YAML file in `apps/`
2. Commit and push changes
3. ArgoCD will automatically sync the changes

### Removing an Application

1. Delete the corresponding YAML file from `apps/`
2. Commit and push
3. ArgoCD will automatically remove the application (with pruning enabled)

## Git Workflow

**Important**: All changes are committed directly to the `main` branch. Do not create feature branches.

```bash
# Make changes
git add .
git commit -m "descriptive message"
git push origin main
```

## Troubleshooting

### Namespace Stuck in Terminating State

If a namespace is stuck in `Terminating` state:

```bash
# Remove namespace finalizer
kubectl get namespace <ns> -o json | jq '.spec.finalizers = []' | kubectl replace --raw "/api/v1/namespaces/<ns>/finalize" -f -

# If CRD resources have finalizers, patch them:
for app in $(kubectl get <crd-resource> -n <ns> --no-headers -o custom-columns=":metadata.name"); do
  kubectl patch <crd-resource> "$app" -n <ns> -p '{"metadata":{"finalizers":[]}}' --type=merge
done
```

### View ArgoCD Applications

```bash
kubectl get applications -n argocd
```

### Check Application Status

```bash
kubectl describe application <app-name> -n argocd
```

## License

MIT

## Contributing

1. Make changes to the configuration files
2. Test in a development environment if possible
3. Commit with descriptive messages following conventional commits
4. Push to main branch

---

*Last updated: March 22, 2026*
