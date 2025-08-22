---
description: Kubernetes Image Secret - Core Concepts, Commands, YAML Configuration, and Practical Examples
---

# Kubernetes Objects - Image Secret

## Overview

Image Secrets in Kubernetes are used to authenticate with private Docker registries when pulling container images. These
secrets store registry credentials and are referenced by Pods to enable access to private image repositories.

## Purpose

Image Secrets are used for:

* **Private Registry Access**: Authenticating with private Docker registries
* **Enterprise Security**: Securing access to internal image repositories
* **CI/CD Pipelines**: Enabling automated deployments from private registries

## Creating Image Secrets

### Basic Command

```shell
# Create Docker registry secret
kubectl create secret docker-registry <secret_name> \
  --docker-server=<registry_url> \
  --docker-username=<username> \
  --docker-password=<password>

# Example:
kubectl create secret docker-registry regscrt \
  --docker-server=testregistry.azurecr.io \
  --docker-username=testregistry \
  --docker-password=wqRjEDdVhrM9Hj4D=gWwvV3YXyq9Y4ID
```

### YAML Configuration

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: regscrt
  namespace: default
type: kubernetes.io/dockerconfigjson
data:
  .dockerconfigjson: eyJhdXRocyI6eyJvemp1cm96dHVya3JlZ2lzdHJ5LmF6dXJlY3IuaW8iOnsidXNlcm5hbWUiOiJvemp1cm96dHVya3JlZ2lzdHJ5IiwicGFzc3dvcmQiOiJ3cVJqRUREdmhyTTlIajREPUdXd3ZWM1lYeXE5WTQ5SUQiLCJhdXRoIjoiWVdSdGFXNDZTR0Z5WW05eU1USXpORFU9In19fQ==
```

## Using Image Secrets

### Pod with Image Secret

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: imagesecrettest2
  labels:
    app: imagesecret
spec:
  containers:
    - name: imagesecretcontainer
      image: testregistry.azurecr.io/k8s:latest
      imagePullPolicy: Always
      ports:
        - containerPort: 80
  imagePullSecrets:
    - name: regscrt
```

### Pod without Image Secret

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: imagesecrettest1
  labels:
    app: imagesecret
spec:
  containers:
    - name: imagesecretcontainer
      image: testregistry.azurecr.io/k8s:latest
      ports:
        - containerPort: 80
```

### Deployment with Image Secret

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
      imagePullSecrets:
        - name: production-registry-secret
      containers:
        - name: app
          image: private-registry.com/my-app:latest
          ports:
            - containerPort: 8080
```

## Common Registry Configurations

### Azure Container Registry (ACR)

```shell
# Create ACR secret
kubectl create secret docker-registry acr-secret \
  --docker-server=<registry_name>.azurecr.io \
  --docker-username=<service_principal_id> \
  --docker-password=<service_principal_password>
```

### Google Container Registry (GCR)

```shell
# Create GCR secret with service account key
kubectl create secret docker-registry gcr-secret \
  --docker-server=gcr.io \
  --docker-username=_json_key \
  --docker-password="$(cat service-account-key.json)"
```

### Docker Hub

```shell
# Create Docker Hub secret
kubectl create secret docker-registry dockerhub-secret \
  --docker-server=docker.io \
  --docker-username=<username> \
  --docker-password=<password>
```

## Service Account Integration

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-service-account
  namespace: production
imagePullSecrets:
  - name: production-registry-secret
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-deployment
  namespace: production
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
          image: private-registry.com/my-app:latest
          ports:
            - containerPort: 8080
```

## Basic Commands

```shell
# Create image secret
kubectl create secret docker-registry <secret_name> \
  --docker-server=<registry_url> \
  --docker-username=<username> \
  --docker-password=<password>

# List secrets
kubectl get secrets

# Delete secret
kubectl delete secret <secret_name>

# Check secret details
kubectl describe secret <secret_name>
```

## References

- [Kubernetes Secrets](https://kubernetes.io/docs/concepts/configuration/secret/)
- [Kubernetes Docker Registry Secret](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)
- [GitHub - Aytitech K8sFundamentals - ImageSecret](https://github.com/aytitech/k8sfundamentals/tree/main/imagesecret)
