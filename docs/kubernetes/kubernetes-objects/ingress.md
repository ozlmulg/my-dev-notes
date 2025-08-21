# Kubernetes Objects - Ingress

## Ingress

* It's the structure we use for our applications to access the outside world/be accessible from the outside world.

**Example Scenario**

![](<../images/kubernetes_load_balancer.png>)

Let's assume we use a cloud service like Azure. Let's define a LoadBalancer service inside the service. Azure assigns an
IP to this LoadBalancer service on our behalf and all requests coming to this IP are handled by this LoadBalancer. We
also match our domain with this IP address thanks to DNS, allowing users to access it easily.

Let's think we defined one more app and the same services in the same k8s cluster. Let's even exaggerate, 2, 3, 4, then
I need to **pay extra money to Azure and make manual settings for each LoadBalancer.**

**Example Scenario - 2**

![](<../images/kubernetes_path_based_routing.png>)

In this example; when the user enters **example.com**, application A runs; when they enter **example.com/contact**,
application B runs. We can't set this up with LoadBalancer because we can't define the **/contact** path in DNS.
However, I need a load balancer that works like a gateway; that welcomes the user in any case.

We solve both of these examples/problems with **Ingress Controller and Ingress Object**:

## Ingress Controller and Ingress Object

![](<../images/kubernetes_nginx.png>)

* **Ingress Controller** refers to a load balancer application like Nginx, Traefik, KrakenD that we can use. We can
  select one of these applications; deploy it to our k8s cluster and expose it to the outside by setting up the
  LoadBalancer service. Thus, our application gets a **public IP** and we can communicate with users completely through
  this IP.
* **So, how do we direct incoming requests?** This is where **Ingress Objects** come into play. (_Structures defined in
  YAML files_) We can determine how our Ingress Objects and Ingress Controllers should behave against incoming requests
  with configurations we will make in Ingress Controllers.
* It has features like **load balancing, SSL termination and path-name based routing**.

## Ingress Application in Local

### 1) Setting up minikube

* To run Ingress, we need to change the minikube driver;
    * We can choose **Hyper-V** for Windows, **VirtualBox** for macOS and linux. Let's not forget to install before
      choosing.

```shell
minikube start --driver=hyperv
```

### 2) Ingress Controller Selection and Installation

* We will continue with nginx. Each ingress controller has different installation. You can learn installation details
  from the application's own website.

  **Installation details –>** https://kubernetes.github.io/ingress-nginx/deploy/
* minikube offers some ingress controllers like nginx that are heavily used as addons to activate them faster.

```shell
minikube addons enable ingress # activates the ingress addon.
minikube addons list # lists all addons.
```

* :point_right: When **Nginx** is installed, it creates a **namespace named **ingress-nginx** for itself.**

```shell
# to list all objects belonging to ingress-nginx namespace:
kubectl get all -n ingress-nginx 
```

### 3) Deploying Our Ingress Applications

* Let's deploy our yaml file that creates both pods and services for **blueapp, greenapp, todoapp**.
* **Let's not forget that all services are ClusterIP type services.**

<details>
<summary>ingress.yaml</summary>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: blueapp
  labels:
    app: blue
spec:
  replicas: 2
  selector:
    matchLabels:
      app: blue
  template:
    metadata:
      labels:
        app: blue
    spec:
      containers:
        - name: blueapp
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
              path: /ready
              port: 80
            initialDelaySeconds: 5
            periodSeconds: 3
---
apiVersion: v1
kind: Service
metadata:
  name: bluesvc
spec:
  selector:
    app: blue
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: greenapp
  labels:
    app: green
spec:
  replicas: 2
  selector:
    matchLabels:
      app: green
  template:
    metadata:
      labels:
        app: green
    spec:
      containers:
        - name: greenapp
          image: ozlmulg/k8s:green
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
              path: /ready
              port: 80
            initialDelaySeconds: 5
            periodSeconds: 3
---
apiVersion: v1
kind: Service
metadata:
  name: greensvc
spec:
  selector:
    app: green
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: todoapp
  labels:
    app: todo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: todo
  template:
    metadata:
      labels:
        app: todo
    spec:
      containers:
        - name: todoapp
          image: ozlmulg/samplewebapp:latest
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: todosvc
spec:
  selector:
    app: todo
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

</details>

### 4) Deploying and Configuring Ingress Objects

* We selected and installed our Ingress Controller as Nginx, which is necessary for the load balancer.
* After installing the ClusterIP type services necessary for each app, it's time to deploy the **Ingress objects**
  necessary for users to go to A service when they write **example.com/a**.

> _**Research Topic:** –> What is Layer 7? What does it do?_

**Ingress Object definition for blue, green apps:**

* The `pathType` section can be set in 2 ways as `exact` or `Prefix`. For detailed
  information: [Kubernetes Official Documentation](https://kubernetes.io/docs/concepts/services-networking/ingress/)

```shell
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: appingress
  annotations:
  # Settings on Nginx are made through annotations.
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
    - host: example.com
      http:
        paths:
          - path: /blue
            pathType: Prefix 
            backend:
              service:
                name: bluesvc
                port:
                  number: 80
          - path: /green
            pathType: Prefix
            backend:
              service:
                name: greensvc
                port:
                  number: 80
```

* Ingress Object prepared using a different `path`:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: todoingress
spec:
  rules:
    - host: todoapp.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: todosvc
                port:
                  number: 80
```

### 5) Testing defined Ingress Objects:

```yaml
kubectl get ingress
```

* If we want to simulate with URLs, we need to edit the **hosts** file.

## References

- [Kubernetes Official Ingress Overview](https://kubernetes.io/docs/concepts/services-networking/ingress/)
- [GitHub - Aytitech K8sFundamentals](https://github.com/aytitech/k8sfundamentals/tree/main/ingress)