# Kubernetes Objects - Label, Selector, Annotation

## What are Labels?

* **Labels** are key-value pairs that act as tags for Kubernetes objects.
* **Selectors** are mechanisms for selecting objects based on their labels.

**Example:** `example.com/tier:front-end` where:
* `example.com/` = Prefix (optional)
* `tier` = **key**
* `front-end` = **value**

* **Reserved prefixes:** `kubernetes.io/` and `k8s.io/` are reserved for Kubernetes core components and cannot be used.
* Labels can contain dashes, underscores, and dots.
* **Turkish characters cannot be used.**
* **Labels are used to establish connections between objects like Services, Deployments, and Pods.**

## Label & Selector Implementation

* Label definitions are made in the **metadata** section. Multiple labels can be added to the same object.
* Labels provide grouping and identification capabilities, making CLI operations easier.

### Selectors - Listing Objects by Labels

* To list objects that have a specific "app" key:

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

## Listing with Equality-based Syntax

kubectl get pods -l "app" --show-labels

kubectl get pods -l "app=firstapp" --show-labels

kubectl get pods -l "app=firstapp, tier=front-end" --show-labels

# Objects with app key as "firstapp" and tier not "front-end":
kubectl get pods -l "app=firstapp, tier!=front-end" --show-labels

# Objects with app key and tier as "front-end":
kubectl get pods -l "app, tier=front-end" --show-labels

## Listing with Set-based Syntax

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

* **Important distinction:** While no results are found with the first syntax (equality-based), results appear with the second syntax (set-based selector):

```shell
kubectl get pods -l "app=firstapp, app=secondapp" --show-labels # No results!
kubectl get pods -l "app in (firstapp, secondapp)" --show-labels # Results exist!
```

### Adding Labels via Command Line

```shell
kubectl label pods <podName> <label>

kubectl label pods pod1 app=front-end
```

### Deleting Labels via Command Line

You need to append a dash (-) at the end to indicate deletion.

```shell
kubectl label pods pod1 app-
```

### Updating Labels via Command Line

```shell
kubectl label --overwrite pods <podName> <label>

kubectl label --overwrite pods pod9 team=team3
```

### Adding Labels in Bulk via Command Line

This label is added to all objects.

```shell
kubectl label pods --all foo=bar
```

## Label Relationships Between Objects

* In normal operation, kube-scheduler makes node selections according to its own algorithm. If we want to make this selection manual, we can specify that it should select a node with the `hddtype: ssd` label as shown in the example below. This establishes a relationship between the Pod and node through labels.

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

> **Note:** We can add the `hddtype: ssd` label to the single node in the minikube cluster. After adding this label, the Pod above will transition from "Pending" state to "Running" state. (Because it found the node it was looking for 😊)

```shell
kubectl label nodes minikube hddtype=ssd
```

## Annotations

* Annotations behave similarly to labels and are written under the **metadata** section.
* Since labels are used to establish relationships between objects, they fall into the sensitive information category. For this reason, we can record important information that cannot be used as labels through **Annotations**.
* **Example:** `example.com/notification-email:admin@k8s.com`
    * `example.com` → Prefix (optional)
    * `notification-email` → Key
    * `admin@k8s.com` → Value

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

### Adding Annotations via Command Line

```shell
kubectl annotate pods annotationpod foo=bar

kubectl annotate pods annotationpod foo- # Deletes the annotation
```

## References

- [Kubernetes Official Labels/Selectors Overview](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)
- [GitHub - Aytitech K8sFundamentals](https://github.com/aytitech/k8sfundamentals/tree/main/labelselector)
