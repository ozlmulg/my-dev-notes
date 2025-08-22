# Kubernetes Objects - Namespace

## Overview

Namespaces provide logical isolation within Kubernetes clusters, enabling organizations to organize resources and manage
access control effectively. They serve as virtual clusters within physical clusters, allowing multiple teams or projects
to share infrastructure while maintaining separation.

## Purpose

### Resource Organization

Consider a scenario where 10 different teams share a single **file server**:

* Files created by one person could be overwritten by another, causing name conflicts
* Separating files that only Team 1 should access becomes challenging, requiring constant permission adjustments
* To resolve this, dedicated folders can be created for each team with permissions configured according to team
  membership

In the example above, the **file server** represents the **Kubernetes cluster**, and **namespaces** represent the *
*dedicated folders** created for each team.

### Technical Implementation

**Namespaces are Kubernetes objects that require proper definition when creating them (especially in YAML files).**
Namespaces must be independent and unique from each other. **Namespaces cannot be nested within each other.**

When Kubernetes is initially created, four default namespaces are automatically established: `default`,
`kube-node-lease`, `kube-public`, and `kube-system`.

## Operations

### Listing Namespaces

By default, all operations and objects are processed under the **default namespace**. When executing `kubectl get pods`
without specifying a namespace, the command retrieves Pods from the `default namespace`.

```shell
kubectl get pods --namespace <namespaceName>
kubectl get pods -n <namespaceName>

# To list Pods across all namespaces:
kubectl get pods --all-namespaces
```

### Creation

```shell
kubectl create namespace <namespaceName>

kubectl get namespaces
```

#### YAML Configuration

```yaml
apiVersion: v1
kind: Namespace # A namespace named development is created
metadata:
  name: development # Namespace identifier
---
apiVersion: v1
kind: Pod
metadata:
  namespace: development # Pod defined under the created namespace
  name: namespacepod
spec:
  containers:
    - name: namespacecontainer
      image: nginx:latest
      ports:
        - containerPort: 80
```

**Important Note:** When creating Pods that run in a specific namespace and connecting to these Pods (or performing any
operations on them), **the namespace must be specified**. If not specified, Kubernetes will search for the relevant Pod
under the **default namespace**.

### Default Namespace Configuration

```shell
kubectl config set-context --current --namespace=<namespaceName>
```

### Deletion

⚠️ **CRITICAL WARNING!** **No confirmation will be requested when deleting a namespace. All objects within the namespace
will be permanently deleted!**

```shell
kubectl delete namespaces <namespaceName>
```

## References

- [Kubernetes Official Namespace Overview](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)