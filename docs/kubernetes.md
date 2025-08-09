# Kubernetes Cheatsheet

## Pod Yönetimi
```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl delete pod <pod-name>
```

## Deployment
```bash
kubectl create deployment myapp --image=nginx
kubectl scale deployment myapp --replicas=3
```
