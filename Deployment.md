## Commands

kubectl create -f file.yaml
kubectl get deployments
kubectl apply -f file.yaml
kubectl set image deployment-name nginx=nginx:1.2


kubectl rollout status deployment
kubectl rollout history deployment
kubectl rollout undo deployment-name