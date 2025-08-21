# Kubernetes Objects - Namespace

## Namespace

* Let's think of a scenario where 10 different teams use a single **file server**:
    * One person's created file can be overwritten by another or cause name conflicts,
    * I may have difficulty separating files that only Team 1 should see, I need to constantly make file settings.
    * To solve this, we can create a special folder for each team and arrange their permissions according to team
      members.
* In the example above, we can think of the **fileserver** as the **k8s cluster**, and **namespaces** as the **folders**
  opened for each team here.
* **Namespaces are k8s objects. When defining them (especially in YAML) files, definition should be made accordingly.**
* Namespaces must be independent and unique from each other. Namespaces cannot be nested within each other.
* When each k8s is created, 4 default namespaces are created. (_default, kube-node-lease, kube-public, kube-system_)

### Listing Namespace

* By default, all operations and objects are processed under the **default namespace**. When we write
  `kubectl get pods`, since we don't specify any namespace, it gets the pods under the `default namespace`.

```shell
kubectl get pods --namespace <namespaceName>
kubectl get pods -n <namespaceName>

# To list pods in all namespaces:
kubectl get pods --all-namespaces
```

### Creating Namespace

```shell
kubectl create namespace <namespaceName>

kubectl get namespaces
```

#### Creating Namespace using YAML file

```yaml
apiVersion: v1
kind: Namespace # A namespace named development is created.
metadata:
  name: development # We give a name to the namespace.
---
apiVersion: v1
kind: Pod
metadata:
  namespace: development # We define the pod under the namespace we created.
  name: namespacepod
spec:
  containers:
    - name: namespacecontainer
      image: nginx:latest
      ports:
        - containerPort: 80
```

When creating a Pod running in a namespace and connecting to this pod; in short, when doing any operation on these pods,
**namespace** must be specified. If not specified, k8s will start looking for the relevant pod under the **default
namespace**.

### Changing Default Namespace

```
kubectl config set-context --current --namespace=<namespaceName>
```

### Deleting Namespace

⚠️ **ATTENTION!** **No confirmation will be requested when deleting namespace. All objects under the namespace
will be deleted!**

```
kubectl delete namespaces <namespaceName>
```

## References

- [Kubernetes Official Namespace Overview](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)