# Install Ingress Controller
### Create Ingress Controller Pod and Service
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```
* Verify
```
kubectl get pods -n ingress-nginx
kubectl get svc -n ingress-nginx
```
### Start Ingress Controller
```
kubectl port-forward svc/ingress-nginx-controller -n ingress-nginx 8080:80
```
* Verify
```
curl http://127.0.0.1:8080/nginx
curl http://127.0.0.1:8080/
```
