---
description: Kubernetes DaemonSet - Core Concepts, Commands, YAML Configuration, and Practical Examples
---

# Kubernetes Objects - DaemonSet

## Overview

A DaemonSet ensures that all (or some) nodes run a copy of a Pod. As nodes are added to the cluster, Pods will be added
to them. As nodes are removed from the cluster, those Pods are garbage collected.

## Purpose

DaemonSets are used for:

* **Node-level Services**: Logging, monitoring, storage
* **System Services**: Network plugins, security agents
* **Cluster Maintenance**: Backup tools, maintenance scripts

## Basic DaemonSet

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-elasticsearch
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd-elasticsearch
  template:
    metadata:
      labels:
        name: fluentd-elasticsearch
    spec:
      tolerations:
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: fluentd-elasticsearch
        image: k8s.gcr.io/fluentd-elasticsearch:1.20
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
```

## DaemonSet with Node Selector

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: ssd-monitor
spec:
  selector:
    matchLabels:
      name: ssd-monitor
  template:
    metadata:
      labels:
        name: ssd-monitor
    spec:
      nodeSelector:
        disk: ssd
      containers:
      - name: ssd-monitor
        image: nginx
```

## DaemonSet with Rolling Update

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nginx-daemon
spec:
  updateStrategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.19
        ports:
        - containerPort: 80
```

## Basic Commands

```shell
# Create DaemonSet
kubectl apply -f daemonset.yaml

# List DaemonSets
kubectl get daemonset
kubectl get ds

# Check DaemonSet status
kubectl describe daemonset <daemonset-name>

# Update DaemonSet image
kubectl set image daemonset/<daemonset-name> <container-name>=<new-image>

# Delete DaemonSet
kubectl delete daemonset <daemonset-name>
```

## Common Use Cases

### Logging Agent

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: log-aggregator
spec:
  selector:
    matchLabels:
      app: log-aggregator
  template:
    metadata:
      labels:
        app: log-aggregator
    spec:
      containers:
      - name: log-aggregator
        image: fluentd:latest
        volumeMounts:
        - name: varlog
          mountPath: /var/log
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
```

### Monitoring Agent

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-exporter
spec:
  selector:
    matchLabels:
      app: node-exporter
  template:
    metadata:
      labels:
        app: node-exporter
    spec:
      containers:
      - name: node-exporter
        image: prom/node-exporter:latest
        ports:
        - containerPort: 9100
```

## References

- [Kubernetes DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)
- [GitHub - Aytitech K8sFundamentals - DaemonSet](https://github.com/aytitech/k8sfundamentals/tree/main/daemonset)
