---
description: Kubernetes PersistentVolume & PersistentVolumeClaim - Core Concepts, Commands, YAML Configuration, and Practical Examples
---

# Kubernetes Objects - PersistentVolume & PersistentVolumeClaim

## Overview

PersistentVolumes (PVs) and PersistentVolumeClaims (PVCs) provide a way for users to request and consume storage
resources without knowing the details of the underlying storage infrastructure.

## PersistentVolume (PV)

### Purpose

PersistentVolumes are used for:

* **Storage Abstraction**: Providing a unified interface for different storage backends
* **Storage Management**: Centralized management of storage resources
* **Storage Provisioning**: Dynamic and static storage allocation

### Basic PV Example

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
   name: mysqlpv
   labels:
     app: mysql
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  nfs:
    path: /
    server: 10.255.255.10
```

## PersistentVolumeClaim (PVC)

### Purpose

PersistentVolumeClaims are used for:

* **Storage Requests**: Users requesting specific storage resources
* **Storage Consumption**: Applications consuming allocated storage
* **Storage Binding**: Dynamic binding to available PVs

### Basic PVC Example

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
  storageClassName: ""
  selector:
    matchLabels:
      app: mysql
```

## Storage Backends

### NFS Storage

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nfs-pv
  labels:
    app: nfs-storage
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  nfs:
    path: /data
    server: nfs-server.example.com
```

### Local Storage

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv
  labels:
    app: local-storage
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  local:
    path: /mnt/data
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
          - node-1
```

## Access Modes

### ReadWriteOnce (RWO)

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: rwo-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce  # Single node can mount as read-write
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /data
```

### ReadWriteMany (RWX)

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: rwx-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteMany  # Multiple nodes can mount as read-write
  persistentVolumeReclaimPolicy: Retain
  nfs:
    path: /shared-data
    server: nfs-server.example.com
```

## Reclaim Policies

### Retain Policy

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: retain-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain  # Manual cleanup required
  hostPath:
    path: /data
```

### Delete Policy

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: delete-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete  # Automatic cleanup
  awsElasticBlockStore:
    volumeID: vol-12345678
    fsType: ext4
```

## Using PV/PVC with Deployments

### MySQL with Persistent Storage

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysqlsecret
type: Opaque
stringData:
  password: P@ssw0rd!
---
apiVersion: v1
kind: PersistentVolume
metadata:
   name: mysqlpv
   labels:
     app: mysql
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  nfs:
    path: /
    server: 10.255.255.10
---
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
  storageClassName: ""
  selector:
    matchLabels:
      app: mysql
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysqldeployment
  labels:
    app: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql
          ports:
            - containerPort: 3306
          volumeMounts:
            - mountPath: "/var/lib/mysql"
              name: mysqlvolume
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysqlsecret
                  key: password
      volumes:
        - name: mysqlvolume
          persistentVolumeClaim:
            claimName: mysqlclaim
```

## Basic Commands

```shell
# List PersistentVolumes
kubectl get pv
kubectl get persistentvolume

# List PersistentVolumeClaims
kubectl get pvc
kubectl get persistentvolumeclaim

# Check PV status
kubectl describe pv <pv-name>

# Check PVC status
kubectl describe pvc <pvc-name>

# Delete PV
kubectl delete pv <pv-name>

# Delete PVC
kubectl delete pvc <pvc-name>
```

## References

- [Kubernetes PersistentVolumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
- [Kubernetes PersistentVolumeClaims](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)
- [GitHub - Aytitech K8sFundamentals - PV/PVC](https://github.com/aytitech/k8sfundamentals/tree/main/pvpvc)
