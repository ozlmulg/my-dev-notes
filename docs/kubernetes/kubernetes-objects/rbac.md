---
description: Kubernetes RBAC - Core Concepts, Commands, YAML Configuration, and Practical Examples
---

# Kubernetes Objects - RBAC

## Overview

Role-Based Access Control (RBAC) in Kubernetes provides fine-grained access control for users and applications. It
allows you to define who can access what resources and perform what actions.

## Purpose

RBAC is used for:

* **Access Control**: Control who can access cluster resources
* **Security**: Implement least privilege access principles
* **Multi-tenancy**: Separate access between different teams
* **Compliance**: Meet security and audit requirements

## RBAC Components

### Role

A Role defines permissions within a namespace.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

### ClusterRole

A ClusterRole defines permissions across the entire cluster.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: secret-reader
rules:
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get", "watch", "list"]
```

### RoleBinding

A RoleBinding grants a Role to users or groups within a namespace.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### ClusterRoleBinding

A ClusterRoleBinding grants a ClusterRole to users or groups across the cluster.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: read-secrets-global
subjects:
- kind: User
  name: admin
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: secret-reader
  apiGroup: rbac.authorization.k8s.io
```

## Common RBAC Patterns

### Developer Access

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: development
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods", "services", "configmaps"]
  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
- apiGroups: ["apps"]
  resources: ["deployments", "replicasets"]
  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: developer-binding
  namespace: development
subjects:
- kind: User
  name: developer
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```

### Read-Only Access

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: production
  name: viewer
rules:
- apiGroups: [""]
  resources: ["pods", "services", "configmaps"]
  verbs: ["get", "list", "watch"]
- apiGroups: ["apps"]
  resources: ["deployments", "replicasets"]
  verbs: ["get", "list", "watch"]
```

### Admin Access

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-admin
rules:
- apiGroups: ["*"]
  resources: ["*"]
  verbs: ["*"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-binding
subjects:
- kind: User
  name: admin
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
```

## Testing Permissions

### Check User Permissions

```shell
# Test if user can perform action
kubectl auth can-i create pods
kubectl auth can-i delete pods --namespace=default
kubectl auth can-i get secrets --as=jane --as-group=developers

# Examples:
kubectl auth can-i create pods
kubectl auth can-i delete pods --namespace=production
```

### Check Specific Resources

```shell
# Test permissions for specific resource
kubectl auth can-i get pods --subresource=log
kubectl auth can-i create deployments --namespace=development

# Examples:
kubectl auth can-i get pods --subresource=log
kubectl auth can-i create deployments --namespace=development
```

## Basic Commands

```shell
# Create Role
kubectl create role <role-name> --verb=get,list,watch --resource=pods,services

# Create ClusterRole
kubectl create clusterrole <clusterrole-name> --verb=get,list,watch --resource=pods,services

# Create RoleBinding
kubectl create rolebinding <rolebinding-name> --role=<role-name> --user=<user-name>

# Create ClusterRoleBinding
kubectl create clusterrolebinding <clusterrolebinding-name> --clusterrole=<clusterrole-name> --user=<user-name>

# Check permissions
kubectl auth can-i <verb> <resource>
```

## References

- [Kubernetes RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)
- [GitHub - Aytitech K8sFundamentals - RBAC](https://github.com/aytitech/k8sfundamentals/tree/main/rbac)
