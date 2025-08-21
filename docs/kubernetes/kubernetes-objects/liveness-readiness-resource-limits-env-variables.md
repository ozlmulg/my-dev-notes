# Kubernetes Objects - Liveness, Readiness, Resource Limits, Env. Variables

## Liveness Probes

Sometimes applications running inside containers may not work correctly in the full sense. If the running application
hasn't crashed, hasn't shut down, but at the same time isn't performing its full function, kubelet can't detect this.

Thanks to Liveness, we can understand whether the container is working correctly by **sending a request, opening a TCP
connection, or running a command inside the container**.

_Explanation in code_ :arrow_down:

<details>
<summary>liveness_probe.yaml</summary>

```yaml
# Let's send an http get request.
# If it returns 200 and above, it's successful!
# If not, kubelet will restart the container.
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-http
spec:
  containers:
    - name: liveness
      image: k8s.gcr.io/liveness
      args:
        - /server
      livenessProbe:
        httpGet: # We're sending a get request.
          path: /healthz # path definition
          port: 8080 # port definition
          httpHeaders: # if we want to add header to our get request
            - name: Custom-Header
              value: Awesome
        initialDelaySeconds: 3 # the application may not start immediately,
        # send the request after x seconds of running.
        periodSeconds: 3 # how many seconds this request will be sent. 
        # (healthcheck test is done continuously.)
---
# Let's run a command inside the application.
# If exit -1 result is received, the container is restarted from the beginning.
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
    - name: liveness
      image: k8s.gcr.io/busybox
      args:
        - /bin/sh
        - -c
        - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
      livenessProbe:
        exec: # command is executed.
          command:
            - cat
            - /tmp/healthy
        initialDelaySeconds: 5
        periodSeconds: 5
---
# Let's create a tcp connection. If successful, it continues, otherwise 
# the container is restarted from the beginning.
apiVersion: v1
kind: Pod
metadata:
  name: goproxy
  labels:
    app: goproxy
spec:
  containers:
    - name: goproxy
      image: k8s.gcr.io/goproxy:0.1
      ports:
        - containerPort: 8080
      livenessProbe: # tcp connection is created.
        tcpSocket:
          port: 8080
        initialDelaySeconds: 15
        periodSeconds: 20
```

</details>

## Readiness Probes

#### **Deployment Update Process with Readiness Probe**

When a deployment is updated (e.g., with a new image version), Kubernetes follows this sequence:

1. **Deployment Update**: The deployment is updated with the new image (e.g., "v2").
2. **New Pod Creation**: A new pod with the updated image (v2) is created.
3. **Pod Startup**: The new pod (v2) starts running.
4. **Readiness Probe Activation**: The readiness check mechanism begins after the `initialDelaySeconds` period.
5. **First Health Check**: The readiness probe performs its first check.
6. **Service Integration**: Once the check passes, the new pod (v2) is added to the service.
7. **Traffic Switch**: At this point, the old pod (v1) is removed from the service, but it's not terminated yet.
8. **Graceful Shutdown**: The old pod (v1) continues processing any existing requests.
9. **Termination Signal**: Kubernetes sends a SIGTERM signal to allow the pod to shut down gracefully.
10. **Pod Termination**: After completing its processes, the old pod (v1) terminates itself.

#### **Example Scenario**

We have 3 pods and 1 LoadBalancer service. We made an update; we created a new image. Old pods went out of service, new
ones were taken. From the moment new ones are taken, the LoadBalancer will start directing incoming traffic. But what if
my applications connect somewhere and pull data when they first start up, process it, and then start working? During
this time, incoming requests won't be answered correctly. In short, our application is running but not ready to provide
service.

–> **Kubelet** uses **Readiness Probes** to know when a container is ready to accept traffic (Initial status). If all
containers in a Pod pass the Readiness Probes check, **the Service is added behind the Pod.**

In the example above, when new images are created, old Pods are not immediately **terminated**. Because there may be
previously received requests and processes running to process these requests. For this reason, k8s first cuts the
relationship of this Pod with the service and prevents it from receiving new requests. It waits for the existing
requests inside to end.

`terminationGracePeriodSconds: 30` –> Existing processes end, wait 30 seconds and close. (_30 seconds is the default
setting, it's quite sufficient._)

**–> The difference between Readiness and Liveness is that Readiness is based on the first working moment, while
Liveness checks whether it's working continuously.**

> For example; There is a time for Backend to connect to MongoDB when it first starts. It makes sense for the Service to
> be added behind the Pod after the MongoDB connection is established. **For this reason, we can use readiness here.**

There are 3 different methods as in Liveness:

* **http/get**, **tcp connection** and **command execution**.

<details>
<summary>readiness_probe.yaml</summary>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  labels:
    team: development
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
        - name: frontend
          image: ozlmulg/k8s:blue
          ports:
            - containerPort: 80
          livenessProbe:
            httpGet:
              path: /healthcheck
              port: 80
            initialDelaySeconds: 5
            periodSeconds: 5
          readinessProbe:
            httpGet:
              path: /ready    # A request is sent to this endpoint, if it returns OK, the application is run.
              port: 80
            initialDelaySeconds: 20 # First check is made after 20 seconds delay from the start.
            periodSeconds: 3 # Continues trying every 3 seconds.
            terminationGracePeriodSconds: 50 # Its explanation was written above.
---
apiVersion: v1
kind: Service
metadata:
  name: frontend
spec:
  selector:
    app: frontend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

</details>

## Resource Limits

\-> It allows us to manage CPU and Memory restrictions of Pods. Unless we specify otherwise, Pods can use 100% of the
CPU and Memory of the machine they run on K8s. This situation creates a problem. For this reason, we can specify how
much CPU and Memory Pods will use.

### CPU Definition

![](<../images/kubernetes_resource_cpu.png>)

### Memory Definition

![](../images/kubernetes_memory.png)

### Definition in YAML File

```shell
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: requestlimit
  name: requestlimit
spec:
  containers:
  - name: requestlimit
    image: ozlmulg/stress
    resources:
      requests: # Minimum requirements needed for the Pod to work
        memory: "64M"	# This pod needs at least 64M 250m (i.e., quarter core)
        cpu: "250m" # = Quarter CPU core = "0.25"
      limits: # Maximum limit needed for the Pod to work
        memory: "256M"
        cpu: "0.5" # = "Half CPU Core" = "500m"
```

–> If requirements cannot be met, **container cannot be created.**

–> Memory works differently than CPU. There's no such situation as K8s blocking when memory requests more value than
limits. If memory needs more than limits, it goes into "OOMKilled" state and the pod is restarted.

> **Research topic:** How should we determine the limits and min. requirements of a pod?

## Environment Variables

For example, let's think we created a node.js server and stored database information in server files. If the container
image we created from server files falls into someone else's hands, a major security vulnerability occurs. For this
reason, we need to use **Environment Variables**.

### YAML Definition

```shell
apiVersion: v1
kind: Pod
metadata:
  name: envpod
  labels:
    app: frontend
spec:
  containers:
  - name: envpod
    image: ozlmulg/env:latest
    ports:
    - containerPort: 80
    env:
      - name: USER   # first we enter its name.
        value: "TestName"  # then we enter its value.
      - name: database
        value: "testdb.example.com"
```

### Seeing Env. Var.'s defined in Pod

```shell
kubectl exec <podName> -- printenv
```

### Viewing Application

```shell
kubectl port-forward pod/envpod 8080:80
```

Then open `localhost:8080` in the browser.

## References

- [Kubernetes Official Liveness Readiness Probes Overview](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)
- [GitHub - Aytitech K8sFundamentals - Liveness Readiness Probes](https://github.com/aytitech/k8sfundamentals/tree/main/liveready)
- [GitHub - Aytitech K8sFundamentals - Request Limit](https://github.com/aytitech/k8sfundamentals/tree/main/requestlimit)
