Specify a namespace in definition file.

```
apiVersion: v1
kind: Pod
metadata:
  name: postgres
  namespace: dev
  labels:
    tier: db-tier
spec:
  containers:
    - name: postgres
      image: postgres
      env:
        - name: POSTGRES_PASSWORD
          value: mysecretpassword
```

Creating a namespace

```
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

Switch namespace
```
kubectl config set-context --current --namespace=<namespace-name>
```

To limit a resources created in namespaces, create a resource quota
```
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 5Gi
    limits.cpu: "10"
    limits.memory: 10Gi
```