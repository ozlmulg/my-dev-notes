# Kubernetes Objects - Deployment, ReplicaSet

## Deployment

In K8s culture, "Singleton (Single) Pods" are generally not created. We create higher-level objects that manage them and
these pods are managed by these objects. (EX: Deployment)

**So, why don't we create them?**

For example, let's think that we deploy a frontend object with a container in a pod. If an error occurs in this
container and our RestartPolicy is "Always or On-failure", kube-sched recovers by restarting the container and continues
its operation. However, **if the problem occurs on the node, kube-sched doesn't say "Let me go and run this on another
worker-node"!**

So as a solution to this, we defined 3 nodes and put a load balancer in front of them. If something happens to one, the
others will continue to be online, so we solved the problem. **BUT..** Let's think we developed the application. We will
have to refresh the container images on all nodes one by one. If we want to add a label, we need to add it to all of
them. **So, things get complicated.**

**SOLUTION: "Deployment" Object**

* Deployment is a type of object that continuously tries to bring the **desired state** we determine for one or more
  pods to the **current state**. The **deployment-controller** within deployments takes the necessary actions to bring
  the current state to the desired state.
* With the Deployment object, we can easily do the image update operation above on all nodes, for example.
* We can also specify how the Deployment should behave during operations with the (**Rollout**) parameter.
* **If we get an error in new operations done in Deployment, we can return it to its old state with a single command.**
* ⚠️⚠️⚠️ For example, if we make a **replica** definition when creating a deployment, the k8s
  cluster will always try to keep that many replicas alive. Even if you manually delete one of the pods created by the
  deployment, a new pod will be started in the background. This is why we don't create **Singleton Pods**. That is, we
  don't create pods directly manually or with yaml, leaving this optimization to k8s.
* Even if you're going to create a single pod, you should create it with deployment! (k8s official recommendation)

### Creating Deployment with Command

```shell
kubectl create deployment <deploymentName> --image=<imageName> --replicas=<replicasNumber>

kubectl create deployment <deploymentName> --image=nginx:latest --replicas=2

kubectl get deployment
# Pay attention to the ready column for all deployments!
```

### Updating image in Deployment

```shell
kubectl set image deployment/<deploymentName> <containerName>=<newImage>

kubectl set image deployment/firstdeployment nginx=httpd
```

* As default strategy, it first updates one pod, then the other, then the other. We can change this.

### Changing Deployment Replicas

```shell
kubectl scale deployment <deploymentName> --replicas=<newReplicaNumber>
```

### Deleting Deployment

```shell
kubectl delete deployments <deploymentName>
```

### **Creating Deployment with YAML**

1. Copy the commands under **`metadata`** in any yaml file that will create a pod:

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

1. Paste it under the **`template`** section in the yaml file that will create the deployment. _(Pay attention to
   indentation!)_
2. **Delete the `name` field from the pod template.**

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
      app: frontend # Label to be used to match with the pod in the template.
  template: # Area where we specify the properties of the pods to be created.
    metadata:
      labels:
        app: frontend # Label of the pod matching with the deployment.
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80 # port to be opened to the outside.
```

* Each deployment must have **at least one** `selector` definition.
* **If you're going to create multiple deployments, you must use different labels.** Otherwise, deployments may confuse
  which pods belong to them. **Also, creating a singleton pod using the same labels is risky!**

## ReplicaSet

In K8s, the object type that creates and manages x number of pods is actually **not deployment.** **ReplicaSet** takes
on all these tasks. When we tell the deployment the desired derived state, the deployments object creates a ReplicaSet
object and ReplicaSet performs all these tasks.

When K8s first came out, we had an object called **Replication-controller**. It still exists but is not used.

```shell
kubectl get replicaset # Lists active ReplicaSets.
```

When defining a deployment, when we make a change on it; deployment **creates a new ReplicaSet** and this ReplicaSet
starts creating new pods. Meanwhile, old pods are deleted.

### Undoing changes made on Deployment

```shell
kubectl rollout undo deployment <deploymentName>
```

In this case, the old deployment is recreated and the old ReplicaSet starts creating the previous pods. This is why, *
*to avoid managing all these operations manually**, we don't create ReplicaSet directly, we continue our operations by
creating deployment.

—> **Deployment > ReplicaSet > Pods**

* When ReplicaSet is wanted to be created as YAML, **it is created exactly the same way as deployment.**

## References

- [Kubernetes Official Deployment Overview](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [Kubernetes Official ReplicaSet Overview](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)
- [GitHub - Aytitech K8sFundamentals - Deployment](https://github.com/aytitech/k8sfundamentals/tree/main/deployment)
- [GitHub - Aytitech K8sFundamentals - ReplicaSet](https://github.com/aytitech/k8sfundamentals/tree/main/deployment/replicaset)
