# What Is a Service Mesh?
A service mesh is a set of networking capabilities built into your infrastructure to manage:

- Traffic routing (load balancing, retries, failovers)

- Security (mTLS, authentication, authorization)

- Observability (metrics, tracing, logs)

- Policy enforcement (rate limiting, access controls)

## Architecture
Most service meshes use a sidecar proxy (like Envoy) deployed alongside each application pod. The most common architecture includes:

- **Data Plane**: Sidecars that intercept and manage traffic

- **Control Plane**: Central component to configure the data plane (e.g., traffic rules, security policies)

## Example: Istio Service Mesh on Kubernetes
```bash
# Install Istio CLI
curl -L https://istio.io/downloadIstio | sh -

# Install Istio in your cluster
istioctl install --set profile=demo -y

# Label your namespace for sidecar injection
kubectl label namespace default istio-injection=enabled

# Deploy your services (sidecar proxies will be injected automatically)
kubectl apply -f your-app-deployment.yaml
```
## When Should You Use a Service Mesh?

- Fine-grained control over service communication

- Secure and observable traffic between services

- Fault-tolerant and resilient microservice interactions
