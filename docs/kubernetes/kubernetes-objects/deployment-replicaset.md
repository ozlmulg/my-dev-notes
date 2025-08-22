# Kubernetes Objects - Deployment, ReplicaSet

## Overview

In Kubernetes architecture, "Singleton (Single) Pods" are generally not created directly. Instead, higher-level
controller objects manage Pods, providing enhanced functionality and operational efficiency. The **Deployment**
represents the primary controller for managing Pod lifecycles.

## Why Use Deployments Instead of Direct Pod Management?

### Operational Challenges

Consider a scenario where a frontend application is deployed with a container in a Pod. If an error occurs in the
container and the RestartPolicy is set to "Always" or "OnFailure", kube-scheduler can recover by restarting the
container and continuing operation. However, **if the problem occurs on the node itself, kube-scheduler doesn't
automatically migrate the Pod to another worker node.**

### Traditional Solutions and Limitations

As a solution, organizations could define multiple nodes and place a load balancer in front of them. If one node fails,
others continue operating, solving the availability problem. **However, this approach introduces significant operational
complexity:**

* Container image updates require manual refresh across all nodes
* Label management becomes cumbersome and error-prone
* Configuration changes require individual node updates
* **This approach becomes increasingly complex and difficult to manage**

## Deployment Controller

### Core Functionality

The **Deployment** object continuously strives to bring the **desired state** defined for one or more Pods to the *
*current state**. The **deployment-controller** within Deployments takes the necessary actions to transition the current
state to the desired state.

### Key Benefits

* **Simplified Operations:** Easily perform operations like image updates across all managed Pods
* **Rollout Control:** Specify how Deployments should behave during operations using the **Rollout** parameter
* **Rollback Capability:** Revert to previous states with a single command if errors occur during operations
* **Automatic Recovery:** Kubernetes automatically maintains the specified replica count

### Critical Operational Behavior

⚠️⚠️⚠️ **Important:** When defining a **replica** count during deployment creation, the Kubernetes cluster continuously
attempts to maintain that many replicas running. Even if Pods are manually deleted, new Pods are automatically started
in the background. This is why direct Pod creation is not recommended - Kubernetes handles this optimization
automatically.

**Best Practice:** Even for single Pod scenarios, create them using Deployments! (Official Kubernetes recommendation)

## Deployment Operations

### Creation via Command Line

```shell
kubectl create deployment <deploymentName> --image=<imageName> --replicas=<replicasNumber>

kubectl create deployment <deploymentName> --image=nginx:latest --replicas=2

kubectl get deployment
# Pay attention to the ready column for all deployments!
```

### Image Updates

```shell
kubectl set image deployment/<deploymentName> <containerName>=<newImage>

kubectl set image deployment/firstdeployment nginx=httpd
```

**Default Behavior:** The strategy updates one Pod at a time. This behavior can be customized through deployment
configuration.

### Scaling Operations

```shell
kubectl scale deployment <deploymentName> --replicas=<newReplicaNumber>
```

### Resource Management

```shell
kubectl delete deployments <deploymentName>
```

## YAML Configuration

### Conversion Process

1. **Extract Pod Configuration:** Copy the configuration under **`metadata`** from any YAML file that creates a Pod:

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

2. **Template Integration:** Paste the configuration under the **`template`** section in the deployment YAML file. _(Pay
   attention to indentation!)_

3. **Template Modification:** **Remove the `name` field from the Pod template.**

### Deployment Configuration

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

### Configuration Requirements

* Each deployment must have **at least one** `selector` definition
* **For multiple deployments, use different labels** to avoid conflicts and ensure proper Pod management
* **Creating singleton Pods with the same labels as deployments is risky and not recommended**

## ReplicaSet

### Architecture

In Kubernetes, the object type that actually creates and manages a specific number of Pods is **not Deployment** - it's
**ReplicaSet**. When defining a deployment and specifying the desired state, the Deployment object creates a ReplicaSet
object, and ReplicaSet performs all Pod management tasks.

### Historical Context

When Kubernetes was first introduced, the **Replication-controller** object provided this functionality. While it still
exists, it is no longer used in modern Kubernetes deployments.

```shell
kubectl get replicaset # Lists active ReplicaSets
```

### Update Behavior

When modifying a deployment, the deployment **creates a new ReplicaSet**, and this ReplicaSet starts creating new Pods.
Meanwhile, old Pods are deleted according to the specified strategy.

### Rollback Operations

```shell
kubectl rollout undo deployment <deploymentName>
```

In this scenario, the old deployment is recreated, and the old ReplicaSet starts creating the previous Pods. This is
why, **to avoid managing all these operations manually**, ReplicaSets are not created directly; operations continue
through Deployments.

**Hierarchy:** **Deployment > ReplicaSet > Pods**

### YAML Configuration

When ReplicaSet is desired to be created as YAML, **it is created exactly the same way as Deployment.**

## References

- [Kubernetes Official Deployment Overview](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [Kubernetes Official ReplicaSet Overview](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)
- [GitHub - Aytitech K8sFundamentals - Deployment](https://github.com/aytitech/k8sfundamentals/tree/main/deployment)
- [GitHub - Aytitech K8sFundamentals - ReplicaSet](https://github.com/aytitech/k8sfundamentals/tree/main/deployment/replicaset)
