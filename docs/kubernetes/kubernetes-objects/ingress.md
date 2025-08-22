# Kubernetes Objects - Ingress

## Ingress

* Ingress is the structure we use to enable our applications to access the outside world and be accessible from external sources.

**Example Scenario 1**

![](<../images/kubernetes_load_balancer.png>)

Consider a scenario where we use a cloud service like Azure. We define a LoadBalancer service within the cluster. Azure assigns an IP address to this LoadBalancer service on our behalf, and all requests directed to this IP are handled by the LoadBalancer. We also match our domain with this IP address through DNS, allowing users to access it easily.

Now consider defining one more application and the same services in the same Kubernetes cluster. Even with 2, 3, or 4 applications, we would need to **pay extra money to Azure and make manual configurations for each LoadBalancer.**

**Example Scenario 2**

![](<../images/kubernetes_path_based_routing.png>)

In this example: when users visit **example.com**, application A runs; when they visit **example.com/contact**, application B runs. We cannot set this up with LoadBalancer because we cannot define the **/contact** path in DNS. However, we need a load balancer that functions like a gateway and welcomes users in all scenarios.

We solve both of these examples/problems using **Ingress Controller and Ingress Objects**:

## Ingress Controller and Ingress Object

![](<../images/kubernetes_nginx.png>)

* **Ingress Controller** refers to load balancer applications like Nginx, Traefik, or KrakenD that we can utilize. We can select one of these applications, deploy it to our Kubernetes cluster, and expose it externally by setting up a LoadBalancer service. This provides our application with a **public IP**, enabling complete user communication through this IP address.
* **So, how do we direct incoming requests?** This is where **Ingress Objects** come into play. (_Structures defined in YAML files_) We can determine how our Ingress Objects and Ingress Controllers should behave against incoming requests through configurations we make in Ingress Controllers.
* Ingress provides features such as **load balancing, SSL termination, and path-based routing**.

## Ingress Implementation in Local Environment

### 1) Setting up minikube

* To run Ingress, we need to change the minikube driver:
    * We can choose **Hyper-V** for Windows, **VirtualBox** for macOS and Linux. Remember to install the appropriate driver before making your selection.

```shell
minikube start --driver=hyperv
```

### 2) Ingress Controller Selection and Installation

* We will continue with Nginx. Each ingress controller has different installation procedures. You can learn installation details from the application's official website.

  **Installation details →** https://kubernetes.github.io/ingress-nginx/deploy/
* minikube offers some ingress controllers like Nginx that are heavily used as add-ons for faster activation.

```shell
minikube addons enable ingress # Activates the ingress add-on
minikube addons list # Lists all available add-ons
```

* ➡️ When **Nginx** is installed, it creates a **namespace named `ingress-nginx`** for itself.

```shell
# To list all objects belonging to the ingress-nginx namespace:
kubectl get all -n ingress-nginx 
```

### 3) Deploying Our Ingress Applications

* Let's deploy our YAML file that creates both Pods and Services for **blueapp, greenapp, todoapp**.
* **Important:** All services must be ClusterIP type services.

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
* After installing the ClusterIP type services necessary for each app, it's time to deploy the **Ingress objects** necessary for users to access service A when they write **example.com/a**.

> _**Research Topic:** What is Layer 7? What does it do?_

**Ingress Object definition for blue and green apps:**

* The `pathType` section can be set in two ways: `exact` or `Prefix`. For detailed information, refer to the [Kubernetes Official Documentation](https://kubernetes.io/docs/concepts/services-networking/ingress/)

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: appingress
  annotations:
  # Nginx settings are configured through annotations
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

* **Ingress Object prepared using a different `path`:**

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

### 5) Testing Defined Ingress Objects

```shell
kubectl get ingress
```

* If we want to simulate with URLs, we need to edit the **hosts** file.

## References

- [Kubernetes Official Ingress Overview](https://kubernetes.io/docs/concepts/services-networking/ingress/)
- [GitHub - Aytitech K8sFundamentals](https://github.com/aytitech/k8sfundamentals/tree/main/ingress)