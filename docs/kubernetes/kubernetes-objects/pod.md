---
description: What is Pod, Commands, YAML, Multi Container …
---

# Kubernetes Objects - Pod

## What is a Pod?

* **Kubernetes Objects** are the entities we deploy, execute, and manage within Kubernetes clusters.
* **The Pod is the most fundamental Kubernetes object.**
* Unlike traditional container management where we work directly with Docker containers, **Kubernetes operates at the Pod level** - the smallest deployable unit we can create and manage.
* Pods can contain **one or more containers**, though **best practice recommends one container per Pod** for optimal resource management and scalability.
* Each Pod receives a **unique identifier (UID)** and a **unique IP address**. The API server records this information in etcd. The Scheduler identifies unassigned Pods and selects appropriate worker nodes for execution, updating the Pod definition accordingly. The kubelet service running on the worker node then creates and manages the specified containers.
* Containers within the same Pod run on the same node and communicate through localhost interfaces.
* Pods are created using the **`kubectl run`** command.

## Creating Pods

```shell
kubectl run firstpod --image=nginx --restart=Never --port=80 --labels="app=frontend" 

# Output: pod/firstpod created
```

* The `--restart` parameter with `Never` ensures that if the container stops for any reason, it will not be automatically restarted.

### Listing Pods

```shell
kubectl get pods -o wide

# -o wide provides an extended table display with additional details
```

### Inspecting Pod Details

```shell
kubectl describe <object> <objectName>

kubectl describe pods first-pod
```

* This command retrieves comprehensive information about the specified Pod.
* Pay particular attention to the **Events** section, which provides a chronological history of Pod lifecycle events:
    * Initial node assignment by the Scheduler
    * Container image pulling by kubelet
    * Pod creation and startup processes

### Accessing Pod Logs

```shell
kubectl logs <podName>

kubectl logs first-pod
```

**Real-time Log Monitoring**

```shell
kubectl logs -f <podName>
```

### Executing Commands Within Pods

```shell
kubectl exec <podName> -- <command>

kubectl exec first-pod -- ls /
```

### Interactive Container Access

```shell
kubectl exec -it <podName> -- <shellName>

kubectl exec -it first-pod -- /bin/sh
```

**Multi-container Pod Access**

```shell
kubectl exec -it <podName> -c <containerName> -- <bash|/bin/sh>
```

**Useful Commands After Connection:**

```shell
hostname    # Displays the Pod name
printenv    # Shows Pod environment variables
```

**Connecting to Specific Containers in Multi-container Pods:**

```shell
kubectl exec -it <podName> -c <containerName> -- /bin/sh
```

### Pod Deletion

```shell
kubectl delete pods <podName>
```

⚠️ **Warning:** This command executes immediately without confirmation. Exercise extreme caution, especially in production environments!

## YAML Configuration

* Kubernetes supports both **YAML** and **JSON** as declarative configuration methods.
* **Multiple objects can be defined in a single YAML file by separating them with `---` (three dashes).**

```yaml
apiVersion:
kind:
metadata:
spec:
```

* When creating any Kubernetes object, **apiVersion, kind, and metadata** are mandatory fields.
* **`kind`** specifies the type of object to create (e.g., `Pod`).
* **`apiVersion`** indicates which API endpoint serves the object type.
* **`metadata`** contains unique identifying information about the object (e.g., `namespace`, `annotations`).
* **`spec`** defines the object's properties and configuration. The content varies by object type and can be referenced from official documentation.

### Determining apiVersion

1. **Documentation Reference:** Consult official Kubernetes documentation.
2. **Kubectl Explain:** Use the following command:

```shell
kubectl explain pods
```

This command displays Pod properties and shows the appropriate `apiVersion` under the `Versions` field.

### Configuring metadata and spec

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: first-pod    # Pod identifier
  labels:            # Optional label assignments
    app: front-end   # Creates app=front-end label
spec:
  containers:        # Container definitions
    - name: nginx            # Container name
      image: nginx:latest    # Container image
      ports:
        - containerPort: 80  # External access port
```

### Applying YAML Configuration

```shell
kubectl apply -f pod1.yaml
```

* This command creates the object defined in the YAML file.
* All Pod properties can be verified using `kubectl describe pods firstpod`.
* YAML configuration is ideal for CI/CD pipeline integration.
* ✅ **Declarative Method Advantage:** Unlike imperative commands that may return "Already exists" errors during updates, YAML modifications with `kubectl apply` provide "pod configured" success messages.

### Removing YAML-defined Resources

```shell
kubectl delete -f pod1.yaml
```

### Direct Pod Editing

```shell
kubectl edit pods <podName>
```

* This command allows direct modification of Pod properties.
* Press `i` to enter `INSERT` mode for editing.
* Exit with `Ctrl+C` and save with `:wq` in Vim.
* A confirmation message indicates successful Pod modification.
* **Note:** This method is not recommended; prefer YAML configuration with `kubectl apply`.

## Pod Lifecycle

* **Pending** → When a YAML file is submitted, the configuration merges with defaults and is recorded in etcd.
* **Creating** → The kube-scheduler continuously monitors etcd for unassigned Pods and selects suitable nodes for execution. If this stage persists, **it indicates that no suitable node can be found**.
    * The kubelet continuously monitors etcd for Pods assigned to its node and downloads required container images. If image retrieval fails, the Pod enters **ImagePullBackOff** state.
    * Upon successful image retrieval and container creation, the Pod transitions to **Running** state.

> **Container Operation Logic:**
> 
> Container images contain applications designed for continuous operation. Applications terminate in three scenarios:
> 1. **Normal completion:** The application finishes all tasks and exits without errors.
> 2. **Graceful shutdown:** User or system sends a shutdown signal, and the application exits cleanly.
> 3. **Error termination:** The application encounters an error, crashes, and exits.

> **Returning to the lifecycle...**

* When container applications stop, a **RestartPolicy** defined in the Pod determines the response, with three possible values:
    * **`Always`** → Kubelet automatically restarts the container.
    * **`Never`** → Kubelet **does not restart** the container.
    * **`OnFailure`** → Kubelet only restarts the container when errors occur.
* **Succeeded** → Pods that complete successfully enter this state.
* **Failed** → Pods that fail to complete enter this state.
* **Completed** → Pods that run successfully and exit without errors enter this state.
* ⚠️ **CrashLoopBackOff** → When a Pod frequently crashes and restarts due to RestartPolicy, Kubernetes detects this pattern and places the Pod in this state. **Pods in this state require investigation.**

## Multi-container Pods

### **Why Not Place Multiple Applications in the Same Container?**

**Answer:** **Isolation.** Multiple applications should operate independently. Without proper isolation, horizontal scaling becomes problematic. When scaling requires duplication, having multiple applications in the same container results in multiple instances of each application (e.g., 2 MySQL instances, 2 WordPress instances), which is not optimal.

✅ **Therefore, the recommended pattern is 1 Pod = 1 Container = 1 Application.** Alternative approaches become **anti-patterns**.

### Why Do Pods Support Multiple Containers?

**Answer:** Some applications require **tight integration and dependency management**. When the main application starts or stops, dependent containers should follow the same lifecycle. In such cases, multiple containers can be placed in a single Pod.

**Note:** Containers within the same Pod communicate through localhost without requiring network configuration.

**Accessing Specific Containers in Multi-container Pods:**

```shell
kubectl exec -it <podName> -c <containerName> -- /bin/sh
```

<details>
<summary>podmulticontainer.yaml</summary>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multicontainer
spec:
  containers:
    - name: webcontainer
      image: nginx
      ports:
        - containerPort: 80
      volumeMounts:
        - name: sharedvolume
          mountPath: /usr/share/nginx/html
    - name: sidecarcontainer
      image: busybox
      command: [ "/bin/sh" ]
      args: [ "-c", "while true; do wget -O /var/log/index.html https://raw.githubusercontent.com/ozlmulg/my-dev-notes/refs/heads/master/docs/kubernetes/assets/index.html; sleep 15; done" ]
      volumeMounts:
        - name: sharedvolume
          mountPath: /var/log
  volumes:
    - name: sharedvolume
      emptyDir: { }
```

</details>

### Init Containers

Init containers function similarly to the `init()` function in Go - they execute first before the main application container. For example, if an application requires configuration files before startup, this operation can be performed in the init container.

1. **Init Container Execution:** Before the application container starts, the **Init Container** runs first.
2. **Task Completion:** The Init Container performs its required tasks and terminates.
3. **Application Startup:** The application container starts only after the Init Container completes successfully. **The application container will not start until the Init Container finishes.**

<details>
<summary>podinitcontainer.yaml</summary>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: initcontainerpod
spec:
  containers:
    - name: appcontainer
      image: busybox
      command: [ 'sh', '-c', 'echo The app is running! && sleep 3600' ]
  initContainers:
    - name: initcontainer
      image: busybox
      command: [ 'sh', '-c', "until nslookup myservice; do echo waiting for myservice; sleep 2; done" ]
```

</details>

## References

- [Kubernetes Official Pods Overview](https://kubernetes.io/docs/concepts/workloads/pods/)
- [GitHub - Aytitech K8sFundamentals](https://github.com/aytitech/k8sfundamentals/tree/main/pod)