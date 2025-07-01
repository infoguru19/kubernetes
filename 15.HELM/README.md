# HELM

Helm is a `package manager` for Kubernetes, like apt for Ubuntu or yum for CentOS. It simplifies the deployment and management of applications on Kubernetes using Helm Charts, which are pre-configured application resources.

## What is Helm?
Helm uses Charts (packages of Kubernetes YAML files) to deploy applications.
A Helm Chart can define:
- Deployment
- Service
- Ingress
- ConfigMaps
- PVCs, etc.

## Install Helm (if not already)
**On Linux/macOS:**
```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

## Example: Deploy NGINX using Helm
**Step 1: Add Helm Repo**
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```
**Step 2: Install NGINX with Helm**
```bash
helm install my-nginx bitnami/nginx
```
`my-nginx`: Release name
`bitnami/nginx`: Chart name from the repo

**Step 3: Check Installed Resources**
```bash
helm list
kubectl get all
```
**Step 4: Get NGINX Service Info**
```bash
kubectl get svc --selector=app.kubernetes.io/name=nginx
```

## Helm Chart Structure (for your own chart)
```bash
helm create mychart
```
### Directory structure:

```bash
mychart/
├── Chart.yaml        # Chart metadata
├── values.yaml       # Default config values
├── templates/        # Kubernetes manifest templates
│   ├── deployment.yaml
│   ├── service.yaml
│   └── _helpers.tpl
```

## Install Your Own Chart
```bash
cd mychart/
helm install myapp .
```
### Override Values at Install Time
```bash
helm install my-nginx bitnami/nginx --set service.type=NodePort --set service.nodePorts.http=30080
```
**Or use a file:**

```bash
helm install my-nginx -f custom-values.yaml bitnami/nginx
```
## Uninstall a Release
```bash
helm uninstall my-nginx
```
## Rendered Kubernetes YAML
```bash
helm template my-nginx bitnami/nginx
```