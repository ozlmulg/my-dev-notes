# Kubectl

## Get Resources

- `kubectl get pods` — List pods
- `kubectl get services` — List services
- `kubectl get deployments` — List deployments
- `kubectl get nodes` — List nodes
- `kubectl get namespaces` — List namespaces

## Describe Resources

- `kubectl describe pod <pod-name>`
- `kubectl describe node <node-name>`
- `kubectl describe deployment <deployment-name>`

## Logs and Exec

- `kubectl logs <pod-name>` — View logs of a pod
- `kubectl exec -it <pod-name> -- /bin/bash` — Exec into a pod shell

## Apply and Delete

- `kubectl apply -f <file.yaml>` — Apply config from file
- `kubectl delete -f <file.yaml>` — Delete resources from file

## Scaling and Updating

- `kubectl scale deployment <name> --replicas=<num>` — Scale deployment
- `kubectl rollout status deployment/<name>` — Check rollout status
- `kubectl rollout undo deployment/<name>` — Rollback deployment

## Miscellaneous

- `kubectl config view` — Show kubeconfig
- `kubectl config use-context <context-name>` — Switch context
- `kubectl top pod` — Show pod resource usage (if metrics-server installed)  

