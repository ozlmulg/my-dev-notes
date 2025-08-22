# Kubernetes Objects - Label, Selector, Annotation

## Overview

Labels and Selectors provide the fundamental mechanism for organizing and identifying Kubernetes objects. Labels act as
key-value pairs that tag Kubernetes objects, while Selectors enable object selection based on these labels.

## Labels

### Definition

**Labels** are key-value pairs that act as tags for Kubernetes objects. **Selectors** are mechanisms for selecting
objects based on their labels.

**Format:** `example.com/tier:front-end` where:

* `example.com/` = Prefix (optional)
* `tier` = **key**
* `front-end` = **value**

### Constraints

* **Reserved prefixes:** `kubernetes.io/` and `k8s.io/` are reserved for Kubernetes core components and cannot be used
* **Valid characters:** Labels can contain dashes, underscores, and dots
* **Language restrictions:** Turkish characters cannot be used
* **Purpose:** Labels are used to establish connections between objects like Services, Deployments, and Pods

## Implementation

### Label Definition

Label definitions are made in the **metadata** section. Multiple labels can be added to the same object. Labels provide
grouping and identification capabilities, making CLI operations easier.

### Selectors - Object Filtering

To list objects that have a specific "app" key:

```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: pod8
  labels:
    app: web # app key with value "web"
    tier: backend # tier key with value "backend"
...
---
```

```shell
kubectl get pods -l <keyword> --show-labels

## Equality-based Syntax

kubectl get pods -l "app" --show-labels

kubectl get pods -l "app=firstapp" --show-labels

kubectl get pods -l "app=firstapp, tier=front-end" --show-labels

# Objects with app key as "firstapp" and tier not "front-end":
kubectl get pods -l "app=firstapp, tier!=front-end" --show-labels

# Objects with app key and tier as "front-end":
kubectl get pods -l "app, tier=front-end" --show-labels

## Set-based Syntax

# Objects with app as "firstapp":
kubectl get pods -l "app in (firstapp)" --show-labels

# Query app and get those that don't contain "firstapp":
kubectl get pods -l "app, app notin (firstapp)" --show-labels

kubectl get pods -l "app in (firstapp, secondapp)" --show-labels

# List those that don't have app key:
kubectl get pods -l "!app" --show-labels

# Get those assigned as "firstapp" for app, not assigned "frontend" value to tier key:
kubectl get pods -l "app in (firstapp), tier notin (frontend)" --show-labels
```

### Syntax Differences

**Important distinction:** While no results are found with the first syntax (equality-based), results appear with the
second syntax (set-based selector):

```shell
kubectl get pods -l "app=firstapp, app=secondapp" --show-labels # No results!
kubectl get pods -l "app in (firstapp, secondapp)" --show-labels # Results exist!
```

## Label Management

### Adding Labels

```shell
kubectl label pods <podName> <label>

kubectl label pods pod1 app=front-end
```

### Removing Labels

Append a dash (-) at the end to indicate deletion.

```shell
kubectl label pods pod1 app-
```

### Updating Labels

```shell
kubectl label --overwrite pods <podName> <label>

kubectl label --overwrite pods pod9 team=team3
```

### Bulk Operations

This label is added to all objects.

```shell
kubectl label pods --all foo=bar
```

## Object Relationships

### Node Selection

In normal operation, kube-scheduler makes node selections according to its own algorithm. To make this selection manual,
specify that it should select a node with the `hddtype: ssd` label as shown in the example below. This establishes a
relationship between the Pod and node through labels.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod11
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
  nodeSelector:
    hddtype: ssd
```

**Implementation:** Add the `hddtype: ssd` label to the single node in the minikube cluster. After adding this label,
the Pod above will transition from "Pending" state to "Running" state. (Because it found the node it was looking for 😊)

```shell
kubectl label nodes minikube hddtype=ssd
```

## Annotations

### Purpose

Annotations behave similarly to labels and are written under the **metadata** section. Since labels are used to
establish relationships between objects, they fall into the sensitive information category. For this reason, important
information that cannot be used as labels can be recorded through **Annotations**.

### Format

**Example:** `example.com/notification-email:admin@k8s.com`

* `example.com` → Prefix (optional)
* `notification-email` → Key
* `admin@k8s.com` → Value

### Configuration

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: annotationpod
  annotations:
    owner: "Name Surname"
    notification-email: "admin@example.com"
    releasedate: "01.01.2021"
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
spec:
  containers:
  - name: annotationcontainer
    image: nginx
    ports:
    - containerPort: 80
```

### Management Operations

**Adding Annotations:**

```shell
kubectl annotate pods annotationpod foo=bar

kubectl annotate pods annotationpod foo- # Deletes the annotation
```

## References

- [Kubernetes Official Labels/Selectors Overview](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)
- [GitHub - Aytitech K8sFundamentals](https://github.com/aytitech/k8sfundamentals/tree/main/labelselector)
