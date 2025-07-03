## 4. Setting Up the Kubernetes Dashboard
Deploy the Dashboard
Apply the Kubernetes Dashboard manifest:
```bash

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```
Create an Admin User
Create a dashboard-admin-user.yml file with the following content:

```bash yaml

apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```
Apply the configuration:

```bash

kubectl apply -f dashboard-admin-user.yml
```

Get the Access Token
Retrieve the token for the admin-user:

```bash

kubectl -n kubernetes-dashboard create token admin-user
```
Copy the token for use in the Dashboard login.

Access the Dashboard
Start the Dashboard using kubectl proxy:

```bash

kubectl proxy
```
Open the Dashboard in your browser:

```bash

http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```
Use the token from the previous step to log in.

## If Want to access via IP (safest for dev/test)
Edit Deployment.
```bash
kubectl -n kubernetes-dashboard get deployment kubernetes-dashboard -o yaml | grep -A10 args:
```

Add this line
- --enable-insecure-login
```bash
containers:
- name: kubernetes-dashboard
  args:
    - --namespace=kubernetes-dashboard
    - --enable-insecure-login
```
Rollout Deployemet
```bash
kubectl rollout restart deployment kubernetes-dashboard -n kubernetes-dashboard
```
Generate TOKEN
```bash
kubectl -n kubernetes-dashboard create token admin-user
```
Port-forward method (safest for dev/test):
```bash
 kubectl port-forward -n kubernetes-dashboard service/kubernetes-dashboard 8443:443 --address='192.168.0.171'
```
open port 8443 

Open Browser.
```bash
https://192.168.0.171:8443/
```

