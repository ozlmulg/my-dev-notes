---
description: Kubernetes Affinity, Taint, and Toleration - Core Concepts, Commands, YAML Configuration, and Practical Examples
---

# Kubernetes Objects - Affinity, Taints, and Tolerations

## Overview

Affinity, Taints, and Tolerations are mechanisms in Kubernetes that control how Pods are scheduled on nodes. They help
ensure Pods run on appropriate nodes and prevent unwanted scheduling.

## Node Affinity

### Purpose

Node Affinity allows you to specify rules that determine which nodes a Pod can be scheduled on.

### Basic Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: affinity-pod
spec:
  containers:
  - name: app
    image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: kubernetes.io/os
            operator: In
            values:
            - linux
```

## Pod Affinity

### Purpose

Pod Affinity allows Pods to be scheduled near other Pods based on labels.

### Basic Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-affinity
  labels:
    app: web
spec:
  containers:
  - name: app
    image: nginx
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - database
        topologyKey: kubernetes.io/hostname
```

## Pod Anti-Affinity

### Purpose

Pod Anti-Affinity prevents Pods from being scheduled on the same node.

### Basic Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: anti-affinity-pod
  labels:
    app: web
spec:
  containers:
  - name: app
    image: nginx
  affinity:
    podAntiAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        podAffinityTerm:
          labelSelector:
            matchExpressions:
            - key: app
              operator: In
              values:
              - web
          topologyKey: kubernetes.io/hostname
```

## Taints and Tolerations

### Purpose

Taints allow nodes to repel Pods that don't tolerate the taint. Tolerations allow Pods to be scheduled on tainted nodes.

### Node Taint

```shell
# Add taint to node
kubectl taint nodes <node-name> key=value:NoSchedule

# Remove taint from node
kubectl untaint nodes <node-name> key:NoSchedule-
```

### Pod with Toleration

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: toleration-pod
spec:
  containers:
  - name: app
    image: nginx
  tolerations:
  - key: "key"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"
```

## Common Use Cases

### Database Pods on Specific Nodes

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: database
  labels:
    app: database
spec:
  containers:
  - name: mysql
    image: mysql
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: node-type
            operator: In
            values:
            - database
```

### Web Pods Spread Across Nodes

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web
  labels:
    app: web
spec:
  containers:
  - name: nginx
    image: nginx
  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - web
        topologyKey: kubernetes.io/hostname
```

## Basic Commands

```shell
# Add taint to node
kubectl taint nodes <node-name> key=value:NoSchedule

# Remove taint
kubectl untaint nodes <node-name> key:NoSchedule-

# Check node taints
kubectl describe node <node-name>

# Check pod affinity
kubectl describe pod <pod-name>
```

## References

- [Kubernetes Affinity and Anti-affinity](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity)
- [Kubernetes Taints and Tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/)
- [GitHub - Aytitech K8sFundamentals - Affinity](https://github.com/aytitech/k8sfundamentals/tree/main/affinity)
