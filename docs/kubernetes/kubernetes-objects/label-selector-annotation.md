# Kubernetes Objects - Label, Selector, Annotation

## What is Label?

* Label -> Tag
* Selector -> Tag Selection

EX: `example.com/tier:front-end` –>`example.com/` = Prefix (optional) `tier` = **key**, `front-end` = **value**

* `kubernetes.io/` and `k8s.io/` are reserved for Kubernetes core components, they cannot be used.
* It can contain dashes, underscores, dots.
* Turkish characters cannot be used.
* **Used to establish connections between objects like Service, deployment, pods.**

## Label & Selector Application

* Label definition is made in the **metadata** section. We cannot add multiple labels to the same object.
* Label provides grouping and identification capability. It makes listing easier on the CLI side.

### Selector - Listing Objects by Labels

* To list objects that have "app" key for example:

```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: pod8
  labels:
    app: web # app key here. web is its value.
    tier: backend # tier is another key, backend is its value.
...
---
```

```shell
kubectl get pods -l <keyword> --show-labels

## Listing with Equality based Syntax

kubectl get pods -l "app" --show-labels

kubectl get pods -l "app=firstapp" --show-labels

kubectl get pods -l "app=firstapp, tier=front-end" --show-labels

# Those with app key as firstapp, tier not front-end:
kubectl get pods -l "app=firstapp, tier!=front-end" --show-labels

# Objects with app key and tier as front-end:
kubectl get pods -l "app, tier=front-end" --show-labels

## Listing with Set based

# Objects with App as firstapp:
kubectl get pods -l "app in (firstapp)" --show-labels

# query app and get those that don't contain "firstapp":
kubectl get pods -l "app, app notin (firstapp)" --show-labels

kubectl get pods -l "app in (firstapp, secondapp)" --show-labels

# list those that don't have app key
kubectl get pods -l "!app" --show-labels

# get those assigned as firstapp for app, not assigned frontend value to tier key:
kubectl get pods -l "app in (firstapp), tier notin (frontend)" --show-labels
```

* While no result is found in the first syntax (equality based), result comes in the 2nd syntax (set based selector):

```yaml
kubectl get pods -l "app=firstapp, app=secondapp" --show-labels # No result!
kubectl get pods -l "app in (firstapp, secondapp)" --show-labels # Result exists!
```

### Adding label with command

```shell
kubectl label pods <podName> <label>

kubectl label pods pod1 app=front-end
```

### Deleting label with command

You need to put - (dash) at the end. It means delete.

```
kubectl label pods pod1 app-
```

### Updating label with command

```shell
kubectl label --overwrite pods <podName> <label>

kubectl label --overwrite pods pod9 team=team3
```

### Adding label in bulk with command

This label is added to all objects.

```
kubectl label pods --all foo=bar
```

## Label Relationship Between Objects

* In NŞA, kube-sched makes a node selection according to its own algorithm. If we want to make this manual, we can make
  it select the node with `hddtype: ssd` label as in the example below. Thus, we establish a relationship between pod
  and node through labels.

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

> _We can add `hddtype: ssd` label to the single node in the minikube cluster. After adding this, the pod above will
transition from "Pending" state to "Running" state. (Because it found the node it was looking for_ :smile: _)_

```shell
kubectl label nodes minikube hddtype=ssd
```

## Annotation

* It behaves like label and is written under **metadata**.
* Since labels are used to establish relationships between 2 objects, they fall into the sensitive information class.
  For this reason, we can record important information that we cannot use as labels thanks to **Annotation**.
* **example.com/notification-email:admin@k8s.com**
    * example.com –> Prefix (optional)
    * notification-email –> Key
    * admin@k8s.com –> Value

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

### Adding Annotation with command

```shell
kubectl annotate pods annotationpod foo=bar

kubectl annotate pods annotationpod foo- # Deletes.
```

## References

- [Kubernetes Official Labels/Selectors Overview](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)
- [GitHub - Aytitech K8sFundamentals](https://github.com/aytitech/k8sfundamentals/tree/main/labelselector)
