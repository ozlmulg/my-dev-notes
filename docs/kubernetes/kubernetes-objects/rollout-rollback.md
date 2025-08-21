# Kubernetes Objects - Rollout and Rollback

Rollout and Rollback concepts come into play and gain meaning during **deployment** updates.

When making deployment definitions with **YAML**, 2 types are selected as **`strategy`**:

### Rollout Strategy - **`Recreate`**

```shell
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rcdeployment
  labels:
    team: development
spec:
  replicas: 3
  selector:
    matchLabels:
      app: recreate 
  strategy:
    type: Recreate # recreate === Rollout strategy
... 
```

* "_If I make a change in this deployment, first delete all pods, then create new ones._" This method is mostly used
  when **hardcore migration** is done.

This method is chosen if it is **risky** for the new version of our application to work together with the old
version. ****

**For example,** let's assume we have a RabbitMQ consumer. In such an application, the old version and new version
working together is generally not a preferred situation. For this reason, `recreate` should be preferred as strategy.

### Rollback Strategy - **`RollingUpdate`**

```shell
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rolldeployment
  labels:
    team: development
spec:
  replicas: 10
  selector:
    matchLabels:
      app: rolling
  strategy:
    type: RollingUpdate # Rollback Strategy
    rollingUpdate:
      maxUnavailable: 2 # How many pods will be deleted simultaneously during update
      maxSurge: 2 # Maximum total active pod count during update
  template:
  ...
```

* If you don't specify strategy in the YAML file, **RollingUpdate is selected as default.** **maxUnavailable and
  maxSurge** values are default **25%.**
* RollingUpdate is the opposite of Create. "When I make a change, don't delete all of them and **create** new ones."
  There are 2 important parameters in this strategy:
    * **`maxUnavailable`** –> Delete at most this many pods. The maximum number of pods that will be deleted when an
      update starts. (We can also write %20.)
    * **`maxSurge`** –> The number of **maximum active pods** that should be in the system during the update transition.

**Example**

Let's think we set up a deployment. Let image = nginx. Let's make an update on the existing deployment with the
following command. Let's want httpd-alphine image instead of nginx image:

```shell
kubectl set image deployment rolldeployment nginx=httpd-alphine --record=true
```

* The `--record=true` parameter records all update stages for us. It's especially useful when we want to go back to the
  previous state.

### Listing changes made

```shell
# rolldeployment = deploymentName
# all change list is brought.
kubectl rollout history deployment rolldeployment 

# to see specifically what changed:
kubectl rollout history deployment rolldeployment --revision=2
```

### Undoing changes made

```shell
# rolldeployment = deploymentName
# To go back to the previous state:
kubectl rollout undo deployment rolldeployment

# To go back to a specific revision:
kubectl rollout undo deployment rolldeployment --to-revision=1
```

### Watching deployment update live

```shell
# rolldeployment = deploymentName
kubectl rollout status deployment rolldeployment -w 
```

### Pausing deployment update

Used when a problem occurs during update and we don't want to go back, and we also want to detect where the problem
originates from.

```shell
# rolldeployment = deploymentName
kubectl rollout pause deployment rolldeployment
```

### Continuing paused deployment update

```shell
kubectl rollout resume deployment rolldeployment
```

## References

- [Kubernetes Official Rolling Update](https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/)
- [Kubernetes Official Kubectl Rollout](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_rollout/)
- [GitHub - Aytitech K8sFundamentals - Deployment](https://github.com/aytitech/k8sfundamentals/tree/main/deployment)