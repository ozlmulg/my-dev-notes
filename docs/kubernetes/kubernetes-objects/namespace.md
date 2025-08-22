# Kubernetes Objects - Namespace

## Namespace

* Consider a scenario where 10 different teams share a single **file server**:
    * Files created by one person could be overwritten by another, causing name conflicts
    * Separating files that only Team 1 should access becomes challenging, requiring constant permission adjustments
    * To resolve this, we can create dedicated folders for each team and configure permissions according to team membership
* In the example above, we can think of the **file server** as the **Kubernetes cluster**, and **namespaces** as the **dedicated folders** created for each team
* **Namespaces are Kubernetes objects that require proper definition when creating them (especially in YAML files)**
* Namespaces must be independent and unique from each other. **Namespaces cannot be nested within each other**
* When Kubernetes is initially created, four default namespaces are automatically established: `default`, `kube-node-lease`, `kube-public`, and `kube-system`

### Listing Namespaces

* By default, all operations and objects are processed under the **default namespace**. When we execute `kubectl get pods` without specifying a namespace, it retrieves pods from the `default namespace`

```shell
kubectl get pods --namespace <namespaceName>
kubectl get pods -n <namespaceName>

# To list pods across all namespaces:
kubectl get pods --all-namespaces
```

### Creating Namespaces

```shell
kubectl create namespace <namespaceName>

kubectl get namespaces
```

#### Creating Namespaces Using YAML Files

```yaml
apiVersion: v1
kind: Namespace # A namespace named development is created
metadata:
  name: development # We assign a name to the namespace
---
apiVersion: v1
kind: Pod
metadata:
  namespace: development # We define the pod under the created namespace
  name: namespacepod
spec:
  containers:
    - name: namespacecontainer
      image: nginx:latest
      ports:
        - containerPort: 80
```

**Important Note:** When creating Pods that run in a specific namespace and connecting to these Pods (or performing any operations on them), **the namespace must be specified**. If not specified, Kubernetes will search for the relevant Pod under the **default namespace**.

### Changing the Default Namespace

```shell
kubectl config set-context --current --namespace=<namespaceName>
```

### Deleting Namespaces

⚠️ **CRITICAL WARNING!** **No confirmation will be requested when deleting a namespace. All objects within the namespace will be permanently deleted!**

```shell
kubectl delete namespaces <namespaceName>
```

## References

- [Kubernetes Official Namespace Overview](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)