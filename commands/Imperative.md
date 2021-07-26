Create a pod with labels
```
kubectl run nginx --image=nginx --labels=tier=db
```

Expose a redis pod as service
```
kubectl expose pod redis --name redis-service --port 6379 --target-port 6379
```

Create deployment
```
kubectl create deploy webapp --image=kodekloud/webapp-color --replicas=3
```