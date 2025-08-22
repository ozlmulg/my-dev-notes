# Kubernetes Objects - Rollout and Rollback

## Overview

Rollout and Rollback concepts become relevant and meaningful during **deployment updates**. When defining deployments
with **YAML**, two strategy types can be selected to control how updates are applied.

## Deployment Strategies

### Recreate Strategy

The Recreate strategy completely replaces all existing Pods with new ones during updates.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rcdeployment
  labels:
    team: development
spec:
  replicas: 3
  selector:
    matchLabels:
      app: recreate 
  strategy:
    type: Recreate # Recreate strategy
... 
```

**Behavior:** "If I make a change in this deployment, first delete all existing pods, then create new ones." This method
is primarily used when **hard migration** is required.

**Use Case:** This strategy is chosen when it is **risky** for the new version of an application to coexist with the old
version.

**Example:** RabbitMQ consumer applications. In such scenarios, having old and new versions running simultaneously is
generally not preferred. Therefore, `Recreate` should be selected as the strategy.

### RollingUpdate Strategy

The RollingUpdate strategy gradually replaces Pods while maintaining service availability.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rolldeployment
  labels:
    team: development
spec:
  replicas: 10
  selector:
    matchLabels:
      app: rolling
  strategy:
    type: RollingUpdate # Rolling update strategy
    rollingUpdate:
      maxUnavailable: 2 # Maximum pods deleted simultaneously during update
      maxSurge: 2       # Maximum total active pod count during update
  template:
  ...
```

**Default Behavior:** If no strategy is specified in the YAML file, **RollingUpdate is automatically selected.** The *
*maxUnavailable and maxSurge** values default to **25%.**

**Behavior:** RollingUpdate is the opposite of Recreate. "When making changes, don't delete all pods at once and *
*create** new ones." This strategy includes two important parameters:

* **`maxUnavailable`** → Maximum number of pods that can be deleted during an update. (Can also be specified as a
  percentage, e.g., 20%.)
* **`maxSurge`** → Maximum number of **active pods** that should exist in the system during the update transition.

## Update Operations

### Image Updates

Consider a deployment with image = nginx. To update the existing deployment with the following command, replacing nginx
with httpd-alpine:

```shell
kubectl set image deployment rolldeployment nginx=httpd-alpine --record=true
```

The `--record=true` parameter records all update stages for future reference. This is particularly useful when rollback
to previous states is needed.

### Deployment History

```shell
# rolldeployment = deploymentName
# Lists all changes made to the deployment
kubectl rollout history deployment rolldeployment 

# To see specific changes for a particular revision:
kubectl rollout history deployment rolldeployment --revision=2
```

### Rollback Operations

```shell
# rolldeployment = deploymentName
# To revert to the previous state:
kubectl rollout undo deployment rolldeployment

# To revert to a specific revision:
kubectl rollout undo deployment rolldeployment --to-revision=1
```

### Monitoring and Control

**Real-time Status Monitoring**

```shell
# rolldeployment = deploymentName
kubectl rollout status deployment rolldeployment -w 
```

**Pausing Updates**

This feature is used when problems occur during updates and you want to investigate the issue without rolling back.

```shell
# rolldeployment = deploymentName
kubectl rollout pause deployment rolldeployment
```

**Resuming Updates**

```shell
kubectl rollout resume deployment rolldeployment
```

## References

- [Kubernetes Official Rolling Update](https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/)
- [Kubernetes Official Kubectl Rollout](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_rollout/)
- [GitHub - Aytitech K8sFundamentals - Deployment](https://github.com/aytitech/k8sfundamentals/tree/main/deployment)