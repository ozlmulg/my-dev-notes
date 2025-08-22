# Kubernetes Objects - Deployment, ReplicaSet

## Deployment

In Kubernetes culture, "Singleton (Single) Pods" are generally not created directly. Instead, we create higher-level objects that manage them, and these Pods are managed by these controller objects. (Example: Deployment)

**Why Don't We Create Pods Directly?**

Consider a scenario where we deploy a frontend application with a container in a Pod. If an error occurs in this container and our RestartPolicy is set to "Always" or "OnFailure", kube-scheduler can recover by restarting the container and continuing operation. However, **if the problem occurs on the node itself, kube-scheduler doesn't automatically migrate the Pod to another worker node!**

As a solution, we could define 3 nodes and place a load balancer in front of them. If one fails, the others continue operating, solving the availability problem. **BUT...** Consider application development scenarios. We would need to refresh container images on all nodes individually. If we want to add labels, we need to add them to all nodes. **This approach becomes increasingly complex and difficult to manage.**

**SOLUTION: "Deployment" Object**

* Deployment is a controller object that continuously strives to bring the **desired state** we define for one or more Pods to the **current state**. The **deployment-controller** within Deployments takes the necessary actions to transition the current state to the desired state.
* With the Deployment object, we can easily perform operations like the image update mentioned above across all nodes.
* We can also specify how the Deployment should behave during operations using the (**Rollout**) parameter.
* **If errors occur during new operations in a Deployment, we can revert to the previous state with a single command.**
* ⚠️⚠️⚠️ **Critical Point:** For example, if we define a **replica** count when creating a deployment, the Kubernetes cluster will always attempt to maintain that many replicas running. Even if you manually delete one of the Pods created by the deployment, a new Pod will be started automatically in the background. This is why we don't create **Singleton Pods** directly. Instead, we delegate this optimization to Kubernetes.
* **Best Practice:** Even if you're going to create a single Pod, you should create it using a Deployment! (Official Kubernetes recommendation)

### Creating Deployments via Command Line

```shell
kubectl create deployment <deploymentName> --image=<imageName> --replicas=<replicasNumber>

kubectl create deployment <deploymentName> --image=nginx:latest --replicas=2

kubectl get deployment
# Pay attention to the ready column for all deployments!
```

### Updating Images in Deployments

```shell
kubectl set image deployment/<deploymentName> <containerName>=<newImage>

kubectl set image deployment/firstdeployment nginx=httpd
```

* By default, the strategy updates one Pod at a time. This behavior can be customized.

### Scaling Deployment Replicas

```shell
kubectl scale deployment <deploymentName> --replicas=<newReplicaNumber>
```

### Deleting Deployments

```shell
kubectl delete deployments <deploymentName>
```

### **Creating Deployments with YAML**

1. Copy the commands under **`metadata`** in any YAML file that creates a Pod:

```yaml
# podexample.yaml

apiVersion: v1
kind: Pod
metadata:
  name: examplepod
  labels:
    app: frontend
spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80
```

2. Paste it under the **`template`** section in the YAML file that creates the deployment. _(Pay attention to indentation!)_
3. **Delete the `name` field from the Pod template.**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: firstdeployment
  labels:
    team: development
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend # Label to be used to match with the Pod in the template
  template: # Area where we specify the properties of the Pods to be created
    metadata:
      labels:
        app: frontend # Label of the Pod matching with the deployment
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80 # Port to be opened to the outside
```

* Each deployment must have **at least one** `selector` definition.
* **If you're going to create multiple deployments, you must use different labels.** Otherwise, deployments may confuse which Pods belong to them. **Also, creating a singleton Pod using the same labels is risky!**

## ReplicaSet

In Kubernetes, the object type that actually creates and manages a specific number of Pods is **not Deployment** - it's **ReplicaSet**. When we tell the deployment the desired derived state, the Deployment object creates a ReplicaSet object, and ReplicaSet performs all these management tasks.

When Kubernetes was first introduced, we had an object called **Replication-controller**. It still exists but is no longer used.

```shell
kubectl get replicaset # Lists active ReplicaSets
```

When defining a deployment and making changes to it, the deployment **creates a new ReplicaSet**, and this ReplicaSet starts creating new Pods. Meanwhile, old Pods are deleted.

### Rolling Back Changes Made to Deployments

```shell
kubectl rollout undo deployment <deploymentName>
```

In this case, the old deployment is recreated, and the old ReplicaSet starts creating the previous Pods. This is why, **to avoid managing all these operations manually**, we don't create ReplicaSets directly; we continue our operations by creating Deployments.

**Hierarchy:** **Deployment > ReplicaSet > Pods**

* When ReplicaSet is desired to be created as YAML, **it is created exactly the same way as Deployment.**

## References

- [Kubernetes Official Deployment Overview](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [Kubernetes Official ReplicaSet Overview](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)
- [GitHub - Aytitech K8sFundamentals - Deployment](https://github.com/aytitech/k8sfundamentals/tree/main/deployment)
- [GitHub - Aytitech K8sFundamentals - ReplicaSet](https://github.com/aytitech/k8sfundamentals/tree/main/deployment/replicaset)
