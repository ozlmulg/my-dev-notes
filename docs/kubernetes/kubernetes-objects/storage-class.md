---
description: Kubernetes StorageClass - Core Concepts, Commands, YAML Configuration, and Practical Examples
---

# Kubernetes Objects - Storage Class

## Overview

StorageClass provides a way for administrators to describe the "classes" of storage they offer. Different classes might
map to quality-of-service levels, or to backup policies, or to arbitrary policies determined by the cluster
administrators.

## Purpose

StorageClasses are used for:

* **Storage Provisioning**: Automatically provision storage when PVCs are created
* **Storage Policies**: Define different storage types with specific characteristics
* **Quality of Service**: Provide different performance and reliability levels
* **Cost Optimization**: Offer various storage options at different price points

## Basic StorageClass

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: standarddisk
parameters:
  cachingmode: ReadOnly
  kind: Managed
  storageaccounttype: StandardSSD_LRS
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

## Cloud Provider StorageClasses

### Azure Disk StorageClass

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azure-ssd
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
provisioner: kubernetes.io/azure-disk
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
  cachingmode: ReadWrite
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```

### AWS EBS StorageClass

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: aws-gp3
provisioner: ebs.csi.aws.com
parameters:
  type: gp3
  iops: "3000"
  throughput: "125"
  encrypted: "true"
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```

### GCP Persistent Disk StorageClass

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: gcp-ssd
provisioner: pd.csi.storage.gke.io
parameters:
  type: pd-ssd
  replication-type: regional-pd
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```

## On-Premises StorageClasses

### NFS StorageClass

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: nfs-storage
provisioner: example.com/nfs
parameters:
  server: nfs-server.example.com
  path: /exports
  mountOptions: "nfsvers=4"
reclaimPolicy: Retain
volumeBindingMode: Immediate
allowVolumeExpansion: false
```

### Local Storage StorageClass

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-storage
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
reclaimPolicy: Retain
allowVolumeExpansion: false
```

## Volume Binding Modes

### Immediate Binding

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: immediate-binding
provisioner: kubernetes.io/azure-disk
parameters:
  storageaccounttype: Standard_LRS
reclaimPolicy: Delete
volumeBindingMode: Immediate  # Volume is bound immediately when PVC is created
allowVolumeExpansion: false
```

### WaitForFirstConsumer Binding

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: wait-for-consumer
provisioner: kubernetes.io/azure-disk
parameters:
  storageaccounttype: Standard_LRS
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer  # Volume is bound when Pod is scheduled
allowVolumeExpansion: false
```

## Using StorageClasses with PVCs

### PVC with Specific StorageClass

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysqlclaim
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 5Gi
  storageClassName: "standarddisk"  # Use specific StorageClass
```

### PVC with Default StorageClass

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: default-storage-claim
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 10Gi
  # No storageClassName specified - uses default StorageClass
```

## Basic Commands

```shell
# List StorageClasses
kubectl get storageclass
kubectl get sc

# Check StorageClass details
kubectl describe storageclass <storageclass-name>

# Get StorageClass YAML
kubectl get storageclass <storageclass-name> -o yaml

# Delete StorageClass
kubectl delete storageclass <storageclass-name>

# Set default StorageClass
kubectl patch storageclass <storageclass-name> -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## Common Use Cases

### Multi-tier Storage Strategy

```yaml
# Fast SSD StorageClass for databases
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-ssd
provisioner: kubernetes.io/azure-disk
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
  cachingmode: ReadWrite
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
---
# Standard HDD StorageClass for logs
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: standard-hdd
provisioner: kubernetes.io/azure-disk
parameters:
  storageaccounttype: Standard_LRS
  kind: Managed
  cachingmode: ReadOnly
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```

### Database Deployment with StorageClass

```yaml
# StorageClass for database
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: db-storage
provisioner: kubernetes.io/azure-disk
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
  cachingmode: ReadWrite
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
---
# PVC using the StorageClass
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-storage
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 100Gi
  storageClassName: db-storage
---
# Deployment using the PVC
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:8.0
          ports:
            - containerPort: 3306
          volumeMounts:
            - mountPath: "/var/lib/mysql"
              name: mysql-storage
      volumes:
        - name: mysql-storage
          persistentVolumeClaim:
            claimName: mysql-storage
```

## References

- [Kubernetes StorageClasses](https://kubernetes.io/docs/concepts/storage/storage-classes/)
- [GitHub - Aytitech K8sFundamentals - StorageClass](https://github.com/aytitech/k8sfundamentals/tree/main/storageclass)
