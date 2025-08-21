---
description: What is Pod, Commands, YAML, Multi Container …
---

# Kubernetes Objects - Pod

## What is Pod?

* Things we run, execute, deploy on K8s are called **Kubernetes Objects**.
* **The most basic object is Pod.**
* Before K8s, we always worked with docker containers. **In K8s, we don't create containers directly.** In the K8s
  world, the **smallest unit** we can create and manage is **Pod**.
* Pods can contain **one or more** containers. **For Best Practice, each pod contains only one container.**
* Each pod has a **unique ID (uid)** and a **unique IP**. The Api-server **records this uid and IP to etcd**. If the
  Scheduler sees that any pod is not associated with a node, it selects a **suitable worker node** to run that pod and
  adds this information to the pod definition. The **kubelet** service running inside the pod sees this pod definition
  and runs the relevant container.
* Containers in the same pod are run on the same node and these containers communicate through localhost.
* Pods are created with **`kubectl run`**.

## Creating Pod

```shell
kubectl run firstpod --image=nginx --restart=Never --port=80 --labels="app=frontend" 

# Output: pod/firstpod created
```

* `–restart` -> We wrote `Never` so that if the container inside the pod stops for any reason, it won't be restarted.

### Showing Defined Pods

```shell
kubectl get pods -o wide

# -o wide --> For wider table display.
```

### Seeing Details of an Object

```shell
kubectl describe <object> <objectName>

kubectl describe pods first-pod
```

* Gets all information about the `first-pod` pod.
* Pay attention to **Events** in the information. We can see the pod's history, what happened, what k8s did.
    * First, Scheduler makes node assignment,
    * kubelet pulled the container image,
    * kubelet created the pod.

### Seeing Pod Logs

```shell
kubectl logs <podName>

kubectl logs first-pod
```

**Seeing Logs Live (Realtime)**

```shell
kubectl logs -f <podName>
```

### Running Commands Inside Pod

```
kubectl exec <podName> -- <command>

kubectl exec first-pod -- ls /
```

### Connecting to Container Inside Pod

```shell
kubectl exec -it <podName> -- <shellName>

kubectl exec -it first-pod -- /bin/sh
```

**If there is more than 1 container in 1 Pod:**

```
kubectl exec -it <podName> -c <containerName> -- <bash|/bin/sh>
```

\-> Some commands we can use after connecting:

```shell
hostname #gives the pod name.
printenv #Gets Pod env variables.
```

\--> If there are multiple containers in a pod, to connect to the container we want:

```
kubectl exec -it <podName> -c <containerName> -- /bin/sh
```

### Deleting Pod

```shell
kubectl delete pods <podName>
```

\-> Be careful when deleting! Because it doesn't ask for confirmation. **It deletes directly!** Especially be careful in
production!

## YAML

* k8s supports **YAML** or **JSON** as declarative method.
* **We can specify multiple objects in a YAML file by putting `---` (three dashes).**

```yaml
apiVersion:
kind:
metadata:
spec:
```

* When creating any type of object; **apiVersion, kind and metadata** are required.
* **`kind`** –> We write here what type of object we want to create. EX: `pod`
* **`apiVersion`** –> Shows on which API or endpoint the object we want to create is served.
* **`metadata`** –> Where we define unique information about the object. EX: `namespace`, `annotation`, etc.
* **`spec`** –> Where we specify the properties of the object we want to create. The information we will enter is
  different for each object. We can look at the definitions we will write here from the documentation.

### Where Will We Find apiVersion?

1. We can look at the documentation.
2. We can learn through kubectl:

```shell
kubectl explain pods
```

We can learn the properties of the pod by writing the explain command above.

–> What's written opposite `Versions` is our `apiVersion`.

### Writing metadata and spec

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: first-pod    # Pod name
  labels: # We can write the labels we will assign.
    app: front-end  # We created app = front-end label.
spec:
  containers: # We make container definitions.
    - name: nginx            # Container name
    image: nginx:latest
    ports:
      - containerPort: 80 # Port to access container from outside
```

### Declaring YAML File to K8s

```shell
kubectl apply -f pod1.yaml
```

* Take the pod1.yaml file and create the object defined here for me.
* All properties of the created pod are displayed with `kubectl describe pods firstpod`.
* YAML method can be used to create in pipeline.
* :thumbsup: **Declarative Method Advantage** –> When we define a pod with imperative method, we get "Already exists."
  error when we want to update one of its properties. But if we wanted to change the YAML declaratively and update it
  and use the apply command, we would get the "pod configured" success message.

### Stopping Declared YAML File

```shell
kubectl delete -f pod1.yaml
```

### Directly Changing Pods with Kubectl (Edit)

```shell
kubectl edit pods <podName>
```

* Used to directly change the properties of any defined pod.
* We press the `i` key to go into `INSERT` mode and do editing.
* We can exit with CMD + C and exit VIM with `:wq`.
* We see the message that the pod was edited.
* It's not a preferred method, YAML + `kubectl apply` should be preferred.

## Pod Lifecycle

* **Pending** –> When we write a YAML file to create a pod, the configs written in the YAML file are mixed with defaults
  and recorded to etcd.
* **Creating** –> kube-sched constantly monitors etcd and if it sees any pod not assigned to a node, it intervenes and
  selects the most suitable node and adds the node information. If it gets stuck at this stage, **it means a suitable
  node cannot be found.**
    * It constantly monitors etcd and detects pods assigned to its node. Accordingly, it downloads images to create
      containers. If the image cannot be found or pulled from the repo, it goes into **ImagePullBackOff** state.
    * If the image is pulled correctly and containers start to be created, the Pod goes into **Running** state.

> _Let's give an S here.. Let's talk about the working logic of Containers:_

* There is an application in container images that needs to run continuously. As long as this application runs, the
  container is also in running state. The application ends its operation in 3 ways:
    1. The application completes all its tasks and closes without error.
    2. User or system sends shutdown signal and closes without error.
    3. It gives an error, crashes, closes.

> _Let's return to the cycle.._

* In response to the container application stopping, a **RestartPolicy** is defined in the Pod and takes 3 values:
    * **`Always`** -> Kubelet restarts this container.
    * **`Never`** -> Kubelet **does not restart** this container.
    * **`On-failure`** -> Kubelet only starts the container when it gets an error.\\
* **Succeeded** -> If the pod is created successfully, it goes to this state.
* **Failed** -> If the pod is not created successfully, it goes to this state.
* **Completed** -> If the pod is created successfully, runs and closes without error, it goes to this state.
* ⚠️ **CrashLookBackOff** -> If a pod is created and frequently shuts down and is constantly tried to be
  restarted due to RestartPolicy, k8s detects this and puts this pod into this state. **Pods in this state should be
  examined.**

## Multi Container Pods

### **Why don't we put 2 applications in the same container?**

–> Answer: Isolation. Let 2 applications work in isolation. If you don't provide this isolation, you can't do horizontal
scaling. When this situation needs to be duplicated and when we get the 2nd container, there will be 2 MySQL, 2
Wordpress which is not a good thing.

:ok_hand: Therefore **1 Pod = 1 Container = 1 application** should be! Other scenarios become **Anti-Pattern**.

### So, why do pods allow multi-container?

–> Answer: Some applications work integrated (dependent). That is, when the main application runs, it should run, when
it stops, it should stop. In such cases, we can put multiple containers in one pod.

–> Two containers in one pod don't need network, they can work through localhost.

\-> If there is a pod with multi-container and we want to connect to one of these containers:

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

### Running Multiple Containers in a Pod with Init Container

It's like the `init()` command in Go, it's the first running container. For example, for the application container to
start, it needs to fetch some config files. We can do this operation in the init container.

1. Before the application container is started, the **Init Container** runs first.
2. The Init Container does what it needs to do and closes.
3. The application container starts running after the Init Container closes. **The application container doesn't start
   until the Init Container closes.**

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