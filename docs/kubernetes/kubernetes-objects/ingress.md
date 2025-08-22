# Kubernetes Objects - Ingress

## Overview

Ingress provides the infrastructure for applications to access external resources and be accessible from external
sources. It serves as the primary mechanism for managing external access to services within a Kubernetes cluster.

## Use Cases

### Scenario 1: Load Balancer Management

![](<../images/kubernetes_load_balancer.png>)

In cloud environments like Azure, organizations define LoadBalancer services within clusters. Cloud providers assign IP
addresses to these LoadBalancer services, and all requests directed to these IPs are handled by the LoadBalancer. Domain
names are matched with IP addresses through DNS, enabling user access.

**Challenge:** When multiple applications are deployed in the same Kubernetes cluster, organizations need to **pay
additional costs to cloud providers and perform manual configurations for each LoadBalancer.**

### Scenario 2: Path-Based Routing

![](<../images/kubernetes_path_based_routing.png>)

In this scenario: when users visit **example.com**, application A runs; when they visit **example.com/contact**,
application B runs. LoadBalancer services cannot handle this setup because **/contact** paths cannot be defined in DNS.
However, a load balancer that functions like a gateway is required to welcome users in all scenarios.

**Solution:** Both scenarios are resolved using **Ingress Controller and Ingress Objects**.

## Architecture

### Ingress Controller

![](<../images/kubernetes_nginx.png>)

**Ingress Controller** refers to load balancer applications like Nginx, Traefik, or KrakenD that can be deployed.
Organizations select one of these applications, deploy it to the Kubernetes cluster, and expose it externally by setting
up a LoadBalancer service. This provides the application with a **public IP**, enabling complete user communication
through this IP address.

### Ingress Objects

**How are incoming requests directed?** This is where **Ingress Objects** come into play. (_Structures defined in YAML
files_) Organizations determine how Ingress Objects and Ingress Controllers should behave against incoming requests
through configurations made in Ingress Controllers.

**Features:** Ingress provides **load balancing, SSL termination, and path-based routing**.

## Implementation

### Environment Setup

#### 1. Minikube Configuration

To run Ingress, the minikube driver must be changed:

* **Windows:** Choose **Hyper-V**
* **macOS/Linux:** Choose **VirtualBox**

Install the appropriate driver before making the selection.

```shell
minikube start --driver=hyperv
```

#### 2. Ingress Controller Installation

**Nginx Controller:** Each ingress controller has different installation procedures. Installation details can be found
on the application's official website.

**Installation Reference:** https://kubernetes.github.io/ingress-nginx/deploy/

**Minikube Add-ons:** minikube offers some ingress controllers like Nginx that are heavily used as add-ons for faster
activation.

```shell
minikube addons enable ingress # Activates the ingress add-on
minikube addons list # Lists all available add-ons
```

**Namespace Creation:** When **Nginx** is installed, it creates a **namespace named `ingress-nginx`** for itself.

```shell
# To list all objects belonging to the ingress-nginx namespace:
kubectl get all -n ingress-nginx 
```

#### 3. Application Deployment

Deploy the YAML file that creates both Pods and Services for **blueapp, greenapp, todoapp**.

**Important:** All services must be ClusterIP type services.

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

#### 4. Ingress Object Configuration

After selecting and installing the Ingress Controller (Nginx) and installing the ClusterIP type services for each app,
deploy the **Ingress objects** necessary for users to access service A when they write **example.com/a**.

**Ingress Object definition for blue and green apps:**

The `pathType` section can be set in two ways: `exact` or `Prefix`. For detailed information, refer to
the [Kubernetes Official Documentation](https://kubernetes.io/docs/concepts/services-networking/ingress/)

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

**Ingress Object prepared using a different `path`:**

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

#### 5. Verification

```shell
kubectl get ingress
```

**Note:** To simulate with URLs, edit the **hosts** file.

## References

- [Kubernetes Official Ingress Overview](https://kubernetes.io/docs/concepts/services-networking/ingress/)
- [GitHub - Aytitech K8sFundamentals](https://github.com/aytitech/k8sfundamentals/tree/main/ingress)