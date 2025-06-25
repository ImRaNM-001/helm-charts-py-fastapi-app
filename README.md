# Python FastAPI Helm Chart

A Helm chart for deploying Python FastAPI applications on Kubernetes.

## Create a tarball (.tgz file) and push to OCI Registry

### 1. Package the Helm chart
From the root directory of the chart:
```bash
helm package python-fastapi-app-helm
```
> This creates a file like `python-fastapi-app-helm-0.1.0.tgz`.

### 2. (Optional) Create a Helm repository index (for static HTTP hosting, not needed for OCI)
```bash
helm repo index .
```
> This step is **not required** for OCI registries like GHCR, but is needed if we want to serve our chart via a static HTTP Helm repo.

### 3. Authenticate to GHCR (GitHub Container Registry)
- **Way 1: Using a Personal Access Token (PAT)**
```bash
export GITHUB_PAT=YOUR_PERSONAL_ACCESS_TOKEN

helm registry login ghcr.io -u YOUR_GITHUB_USERNAME --password-stdin < <(printf "%s" "$GITHUB_PAT")
```
- **Way 2: Interactively (prompts for password)**
```bash
helm registry login ghcr.io -u YOUR_GITHUB_USERNAME
```
> The PAT must have `write:packages` and `read:packages` scopes.

### 4. Push the package as an OCI artifact to GHCR
```bash
helm push python-fastapi-app-helm-0.1.0.tgz oci://ghcr.io/YOUR_GITHUB_USERNAME
```
- Replace `YOUR_GITHUB_USERNAME` with the actual GitHub username (must be lowercase).
- The repository will be created as `ghcr.io/YOUR_GITHUB_USERNAME/python-fastapi-app-helm`.

---

## Installation & Setup

### Add the Helm repository
```bash
helm repo add fastapi-charts https://github.com/ImRaNM-001/helm-charts-py-fastapi-app

helm repo update
```

### Install the chart
```bash
helm install helm-fastapi-app fastapi-charts/python-fastapi-app-helm
```

### With custom values
```bash
helm install helm-fastapi-app fastapi-charts/python-fastapi-app-helm \
  --set replicaCount=3 \
  --set ingress.hosts[0].host=fastapi.local.org
```

### Using values file
```bash
helm install helm-fastapi-app fastapi-charts/python-fastapi-app-helm -f my-values.yaml
```

### Using OCI (Alternative)
```bash
helm install helm-fastapi-app oci://ghcr.io/ImRaNM-001/python-fastapi-app-helm --version 0.1.0
```

## Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of replicas | `2` |
| `image.repository` | Image repository | `04-python-fastapi-app` |
| `image.tag` | Image tag | `latest` |
| `service.type` | Service type | `ClusterIP` |
| `ingress.enabled` | Enable ingress | `true` |
| `ingress.hosts[0].host` | Hostname | `fastapi.local.org` |

## Values

See [values.yaml](python-fastapi-app-helm/values.yaml) for full configuration options.


## Understanding **_helpers.tpl** file

The _helpers.tpl file in Helm is:

🧩 A template helper file used to define reusable variables, labels, or functions across multiple templates (e.g., naming conventions).

Key points:
✅ Not required for packaging (helm package)

🛠️ Just a convenience for cleaner, DRY (Don’t Repeat Yourself) templates

📁 It’s usually placed in the chart’s templates/ directory

So, it’s optional — not informational, but functional.