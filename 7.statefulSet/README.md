## To Access mysql
``` 
kubectl port-forward service/mysql-service 3306:3306 -n mysql
```
## Open new Terminal
``` 
kubectl exec -it mysql-stateful-0 -n mysql -- bash
```
``` 
mysql -u root -p root
```
``` 
show databases
```


<img width="416" height="382" alt="image" src="https://github.com/user-attachments/assets/56f382ab-f434-4ec6-9b2d-438784e770b8" />
