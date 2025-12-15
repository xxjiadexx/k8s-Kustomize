# Kustomize Learning Project

This project demonstrates how to use **Kustomize** to manage Kubernetes configurations for different environments (Dev, Prod) without duplicating code.

## 📁 Project Structure

```
kustomize/
├── base/                   # 🏗️ Shared configuration (Template)
│   ├── deployment.yaml     # Generic Deployment
│   ├── service.yaml        # Generic Service
│   └── kustomization.yaml  # Base kustomization file
└── overlays/               # 🎨 Environment-specific customizations
    ├── dev/                # Development Environment
    │   ├── kustomization.yaml
    │   └── patch.yaml      # Patch for 5 replicas
    └── prod/               # Production Environment
        ├── kustomization.yaml
        └── patch.yaml      # Patch for 10 replicas
```

## 🚀 Quick Start Commands

### 1. Build & View YAML (Dry Run)
See what Kustomize generates without applying it to the cluster:

```powershell
# Check Base
kubectl kustomize base

# Check Dev Overlay
kubectl kustomize overlays/dev

# Check Prod Overlay
kubectl kustomize overlays/prod
```

### 2. Apply to Cluster (Deploy)
Deploy the resources to your Kubernetes cluster:

```powershell
# Deploy Dev
kubectl apply -k overlays/dev

# Deploy Prod
kubectl apply -k overlays/prod
```

### 3. Delete Resources
Remove resources created by Kustomize:

```powershell
# Delete Dev Resources
kubectl delete -k overlays/dev
```

---

## 🔑 Key Concepts

### 1. Base (`base/`)
- Contains the "default" configuration.
- typically **NOT** deployed directly.
- Referenced by overlays.

### 2. Overlays (`overlays/`)
- Depend on the base.
- Apply patches or changes (e.g., replica count, image tags).
- **This is what you deploy.**

### 3. Patches
- Small YAML snippets that modify specific fields in the base resources.
- Example: Changing `replicas: 1` to `replicas: 5`.

---

## 🛠️ Troubleshooting & Tips

### Relatives Paths
When referencing the base from an overlay, use relative paths to navigate up the directory tree:
```yaml
resources:
- ../../base  # Go up 2 levels (overlays/dev/ -> overlays/ -> root) then into base
```

### `kubectl apply -f` vs `-k`
- **`-f` (filename)**: Applies raw YAML files. No processing.
- **`-k` (kustomize)**: Builds the kustomization first, then applies. **Always use this for Kustomize projects.**

### WSL vs PowerShell
- If running Docker/Kind on Windows, use **PowerShell** to run `kubectl` commands.
- If `kubectl` is not configured in WSL, it won't be able to reach the cluster.

### Deleting Resources
- Deleting a Deployment **does not** delete its Service.
- Use `kubectl delete -k <dir>` to remove everything defined in that kustomization.
