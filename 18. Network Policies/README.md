# What is a NetworkPolicy? (Quick Explanation)
A NetworkPolicy is a Kubernetes resource that controls pod-to-pod and pod-to-external traffic.

## By default in Kubernetes:

- All pods can talk to all pods (allow-all)
- NetworkPolicy lets you restrict traffic using:
    - Pod selectors
    - Namespace selectors
    - Ingress (incoming)
    - Egress (outgoing)

### ⚠️ Important:

- NetworkPolicy does nothing by itself
- You need a CNI that supports it (Calico, Cilium, Weave, etc.)

## Why Calico ?
- Calico provides:
    - NetworkPolicy enforcement
    - Advanced security (GlobalNetworkPolicy, egress control)

## Demo Overview

We will:

1. Create a kind cluster
2. Install Calico
3. Deploy two apps:
    - frontend
    - backend
4. Show allow-all traffic
5. Apply NetworkPolicy
6. Verify traffic is blocked

## Create kind Cluster (No Default CNI)

kind-calico.yaml
```
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
networking:
  disableDefaultCNI: true
nodes:
- role: control-plane
  image: kindest/node:v1.31.2
```
```
kind create cluster --name calico-demo --config kind-calico.yaml
```
## Install Calico
```
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.0/manifests/calico.yaml

```

Wait until all pods are ready:
```
kubectl get pods -n kube-system
```
## Deploy Test Applications
Backend (nginx)
```
kubectl create namespace demo
```
```
kubectl run backend \
  --image=nginx \
  --labels app=backend \
  -n demo
```
```
kubectl expose pod backend \
  --port 80 \
  --name backend-svc \
  -n demo
```
Frontend (busybox for curl)
```
kubectl run frontend \
  --image=busybox \
  --labels app=frontend \
  -n demo \
  -- sleep 3600
```
## Test Connectivity (Before NetworkPolicy)
```
kubectl exec -n demo frontend -- wget -qO- backend-svc
```

✅ Output: nginx welcome page
➡️ Traffic is allowed

## Apply Default Deny NetworkPolicy

This blocks all ingress traffic to backend.
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-backend
  namespace: demo
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Ingress
```

Apply it:
```
kubectl apply -f deny-backend.yaml
```
## Test Again (Traffic Blocked)
```
kubectl exec -n demo frontend -- wget -qO- backend-svc
```

❌ Connection timeout
➡️ NetworkPolicy is working

## Allow Frontend → Backend Only
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend
  namespace: demo
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 80
```
```
kubectl apply -f allow-frontend.yaml
```
## Test Again (Allowed)
```
kubectl exec -n demo frontend -- wget -qO- backend-svc
```

## Delete Kind Cluster
```
kind delete cluster --name calico-demo
```
```
kind get clusters
```

✅ Works again

## Key Concepts Recap
- podSelector	Which pods policy applies to
- ingress	Incoming traffic rules
- egress	Outgoing traffic rules
- default deny	Created when a policy exists without allow rules
- Calico	Enforces NetworkPolicy
## Common Interview / Real-World Tips

- First policy = implicit deny (Zero Trust)
- Policies are additive
- Use labels consistently
- For production:
    - Add egress policies
    - Use namespace isolation
- Calico also supports:
    - GlobalNetworkPolicy
    - Egress gateways
    - Network sets
