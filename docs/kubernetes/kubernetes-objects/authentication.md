---
description: Kubernetes Authentication - Core Concepts, Commands, YAML Configuration, and Practical Examples
---

# Kubernetes Objects - Authentication

## Overview

Authentication in Kubernetes determines who can access the cluster. It verifies the identity of users and service
accounts before allowing access to cluster resources.

## Authentication Methods

### Client Certificates

The most common authentication method using X.509 certificates.

#### Generate Private Key

```shell
# Generate private key
openssl genrsa -out <username>.key 2048

# Example:
openssl genrsa -out john.key 2048
```

#### Generate Certificate Signing Request (CSR)

```shell
# Generate CSR
openssl req -new -key <username>.key -out <username>.csr -subj "/CN=<username>/O=<group>"

# Example:
openssl req -new -key john.key -out john.csr -subj "/CN=john/O=developers"
```

#### Submit CSR to Kubernetes

```shell
# Submit CSR
cat <username>.csr | base64 | tr -d '\n' | kubectl apply -f -

# Example:
cat john.csr | base64 | tr -d '\n' | kubectl apply -f -
```

#### Approve CSR

```shell
# Approve CSR
kubectl certificate approve <username>-<hash>

# Example:
kubectl certificate approve john-abc123
```

#### Extract Certificate

```shell
# Extract approved certificate
kubectl get csr <username>-<hash> -o jsonpath='{.status.certificate}' | base64 -d > <username>.crt

# Example:
kubectl get csr john-abc123 -o jsonpath='{.status.certificate}' | base64 -d > john.crt
```

### Bearer Tokens

Simple token-based authentication for service accounts.

#### Create Service Account Token

```shell
# Create service account
kubectl create serviceaccount <serviceaccount-name>

# Create token
kubectl create token <serviceaccount-name>
```

## Configuration

### Set Credentials

```shell
# Set credentials in kubeconfig
kubectl config set-credentials <username> --client-certificate=<username>.crt --client-key=<username>.key

# Example:
kubectl config set-credentials john --client-certificate=john.crt --client-key=john.key
```

### Set Context

```shell
# Set context
kubectl config set-context <context-name> --cluster=<cluster-name> --user=<username>

# Example:
kubectl config set-context john-context --cluster=minikube --user=john
```

### Use Context

```shell
# Switch to context
kubectl config use-context <context-name>

# Example:
kubectl config use-context john-context
```

## Testing Authentication

### Check Permissions

```shell
# Test if user can perform action
kubectl auth can-i create pods
kubectl auth can-i delete pods --namespace=<namespace>
kubectl auth can-i get secrets --as=<user> --as-group=<group>

# Examples:
kubectl auth can-i create pods
kubectl auth can-i delete pods --namespace=default
```

### Check Specific Resources

```shell
# Test permissions for specific resource
kubectl auth can-i get pods --subresource=log
kubectl auth can-i create deployments --namespace=<namespace>

# Examples:
kubectl auth can-i get pods --subresource=log
kubectl auth can-i create deployments --namespace=default
```

## Common Use Cases

### Developer Access

```shell
# Create developer user
openssl genrsa -out developer.key 2048
openssl req -new -key developer.key -out developer.csr -subj "/CN=developer/O=developers"

# Submit and approve
cat developer.csr | base64 | tr -d '\n' | kubectl apply -f -
kubectl certificate approve developer-<hash>

# Extract certificate
kubectl get csr developer-<hash> -o jsonpath='{.status.certificate}' | base64 -d > developer.crt

# Configure access
kubectl config set-credentials developer --client-certificate=developer.crt --client-key=developer.key
kubectl config set-context developer-context --cluster=minikube --user=developer
kubectl config use-context developer-context
```

### Service Account Access

```shell
# Create service account
kubectl create serviceaccount app-service-account

# Create token
kubectl create token app-service-account

# Use token in kubeconfig
kubectl config set-credentials app-user --token=<token>
```

## Basic Commands

```shell
# Check current context
kubectl config current-context

# List contexts
kubectl config get-contexts

# Switch context
kubectl config use-context <context-name>

# Check permissions
kubectl auth can-i <verb> <resource>

# View kubeconfig
kubectl config view
```

## References

- [Kubernetes Authentication](https://kubernetes.io/docs/reference/access-authn-authz/authentication/)
- [Kubernetes Certificates](https://kubernetes.io/docs/tasks/tls/managing-tls-certificates/)
- [GitHub - Aytitech K8sFundamentals - Authentication](https://github.com/aytitech/k8sfundamentals/tree/main/authetication)
