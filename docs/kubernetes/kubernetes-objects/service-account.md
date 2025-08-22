---
description: Kubernetes ServiceAccount - Core Concepts, Commands, YAML Configuration, and Practical Examples
---

# Kubernetes Objects - Service Account

## Overview

Service Accounts in Kubernetes provide an identity for Pods and other workloads to authenticate with the Kubernetes API.
They are used to control what resources and operations a Pod can access.

## Purpose

Service Accounts are used for:

* **Pod Identity**: Give Pods an identity for API access
* **RBAC Integration**: Control Pod permissions through RBAC
* **Security**: Implement least privilege access for applications
* **Automation**: Enable automated operations by Pods

## Basic Service Account

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-service-account
  namespace: default
```

## Service Account with Image Pull Secrets

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-service-account
  namespace: production
imagePullSecrets:
- name: production-registry-secret
```

## Using Service Account in Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  serviceAccountName: app-service-account
  containers:
  - name: app
    image: nginx
```

## Using Service Account in Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      serviceAccountName: app-service-account
      containers:
      - name: app
        image: nginx:latest
        ports:
        - containerPort: 80
```

## Service Account with RBAC

```yaml
# Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: monitoring-sa
  namespace: monitoring
---
# Role for monitoring
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: monitoring
  name: monitoring-role
rules:
- apiGroups: [""]
  resources: ["pods", "services"]
  verbs: ["get", "list", "watch"]
---
# RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: monitoring-binding
  namespace: monitoring
subjects:
- kind: ServiceAccount
  name: monitoring-sa
  namespace: monitoring
roleRef:
  kind: Role
  name: monitoring-role
  apiGroup: rbac.authorization.k8s.io
```

## Default Service Account

Every namespace has a default service account:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: default
  namespace: default
```

## Basic Commands

```shell
# Create Service Account
kubectl create serviceaccount <serviceaccount-name>

# List Service Accounts
kubectl get serviceaccount
kubectl get sa

# Check Service Account details
kubectl describe serviceaccount <serviceaccount-name>

# Delete Service Account
kubectl delete serviceaccount <serviceaccount-name>

# Create token for Service Account
kubectl create token <serviceaccount-name>
```

## Common Use Cases

### Application Service Account

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: web-app-sa
  namespace: web
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  namespace: web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      serviceAccountName: web-app-sa
      containers:
      - name: web-app
        image: nginx:latest
```

### Monitoring Service Account

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: prometheus-sa
  namespace: monitoring
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prometheus
  namespace: monitoring
spec:
  replicas: 1
  selector:
    matchLabels:
      app: prometheus
  template:
    metadata:
      labels:
        app: prometheus
    spec:
      serviceAccountName: prometheus-sa
      containers:
      - name: prometheus
        image: prom/prometheus:latest
```

## References

- [Kubernetes Service Accounts](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/)
- [GitHub - Aytitech K8sFundamentals - ServiceAccount](https://github.com/aytitech/k8sfundamentals/tree/main/serviceaccount)
