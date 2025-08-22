# Kubernetes Objects - Network Structure, Service

## Overview

Kubernetes provides a sophisticated networking infrastructure that enables seamless communication between Pods and
external services. This document covers the fundamental networking concepts and Service objects that facilitate this
communication.

## Network Architecture

### Core Principles

Kubernetes network architecture operates under three fundamental rules:

1. An **IP address range** (`--pod-network-cidr`) is defined during Kubernetes installation for Pod IP allocation
2. In Kubernetes, each Pod receives a **unique IP address** assigned from this CIDR block
3. **Pods within the same cluster** can communicate with each other by default **without restrictions and without NAT (
   Network Address Translation)**

### Communication Patterns

Containers within Kubernetes are exposed to three types of communication:

1. **External communication:** A container communicates with IP addresses outside the Kubernetes cluster
2. **Intra-node communication:** A container communicates with another container on the same node
3. **Inter-node communication:** A container communicates with a container on a different node

The first two scenarios work without issues, but the third scenario presents NAT-related challenges. To address this,
Kubernetes has implemented the **Container Network Interface (CNI)** project to enable inter-container communication.

### CNI Implementation

**CNI is responsible for container network connectivity and resource cleanup when containers are deleted.**

**Kubernetes has adopted CNI standards for network communication and allows users to choose from various CNI plugins.**
Organizations can select the most suitable CNI plugin from the following resources:

[CNI GitHub Repository](https://github.com/containernetworking/cni)

[Kubernetes Official Documentation - Cluster Networking](https://kubernetes.io/docs/concepts/cluster-administration/networking)

**Container Communication:** We've covered container communication with the "Outside World." Now, how will the "Outside
World" communicate with Containers?

**Answer:** Through the **Service** object.

## Service Objects

### Purpose

The Service object handles the networking aspects of Kubernetes, providing a stable endpoint for accessing Pods and
enabling load balancing.

### Use Cases

Consider a system with 1 Frontend (React) and 1 Backend (Go):

* Organizations create **one deployment each** for both applications, ensuring **3 Pods each** are defined
* How will external access be provided to the 3 Frontend Pods?
* Requests from the Frontend need to be processed by the Backend. This communication is straightforward since each Pod
  has an IP address, and all Pods in Kubernetes can communicate with each other using these IP addresses
* To enable this communication, Frontend Pods **need to know** the IP addresses of Backend Pods. One solution is to
  hardcode all Backend Pod IP addresses in the Frontend deployment. However, Pods can be updated, changed, and during
  these updates, they **may receive new IP addresses, requiring manual updates to the Frontend deployment**

This is why **Service** objects are created to solve all these scenarios. **Kubernetes provides Pods with their own IP
addresses and a single DNS name for a set of Pods, along with load balancing between them.**

## Service Types

### ClusterIP

**Purpose:** Inter-container communication within the cluster

Organizations can create a ClusterIP service and associate it with Pods through labels. When this object is created, it
receives a unique DNS address that all Pods in the cluster can resolve. Additionally, every Kubernetes installation has
a virtual IP range (e.g., 10.0.100.0/16).

When a ClusterIP service object is created, **an IP address is assigned** to this object from the IP range, and **this
IP address is registered with the cluster's DNS mechanism using its name.** This IP address is a **Virtual IP address.**

This IP address is added to the IP tables on all nodes by Kube-proxy. All traffic directed to this IP address is
distributed to Pods using the Round Robin algorithm. This solution addresses two main problems:

1. Organizations can define this IP address in Frontend nodes (and if named `backend`), they can specify to use this IP
   address when accessing the Backend. (**Selector = app:backend**) Organizations no longer need to manually add Backend
   Pod IP addresses to Frontend Pods every time!

**In summary: ClusterIP Service solves inter-container communication by handling Service Discovery and Load Balancing
tasks!**

### NodePort

**Purpose:** External access to cluster services

This service type is used to handle **connections originating from outside the cluster.** The `NodePort` key is used to
configure this service type.

### LoadBalancer

**Purpose:** Cloud service integration

This service type is only available in cloud-managed Kubernetes services like Azure Kubernetes Service, Google
Kubernetes Engine.

### ExternalName

**Purpose:** DNS resolution (Not currently necessary for most deployments)

## Service Configuration

### ClusterIP Example

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend # Service name
spec:
  type: ClusterIP # Service type
  selector:
    app: backend # Which Pods to match (same as the label in the Pod)
  ports:
    - protocol: TCP
      port: 5000
      targetPort: 5000
```

Any object within the cluster will receive a response when sending requests to `clusterIP:5000`.

**Important Note:** Service names follow this format when created: `serviceName.namespaceName.svc.cluster.domain`

If another object in the same namespace wants to access this service, it can write `backend` directly thanks to core DNS
resolution. Objects from other namespaces must access this service using the **full name** above.

### NodePort Example

**Note:** All created NodePort services also have **ClusterIP** functionality. Therefore, this service can be accessed
internally using the ClusterIP.

**Minikube Integration:** When using minikube, organizations can open a tunnel with *
*`minikube service --url <serviceName>`** because they normally cannot access the worker node from outside. _This is
specific to minikube._

```yaml
apiVersion: v1
kind: Service
metadata:
  name: frontend
spec:
  type: NodePort
  selector:
    app: frontend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

### LoadBalancer Example

This service type works on clusters created on cloud services like Google Cloud Service and Azure.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: frontendlb
spec:
  type: LoadBalancer
  selector:
    app: frontend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

## Service Management

### Imperative Creation

```shell
kubectl delete service <serviceName>

# Creating ClusterIP Service
kubectl expose deployment backend --type=ClusterIP --name=backend

kubectl expose deployment backend --type=NodePort --name=backend
```

### Endpoints

**Purpose:** Endpoints determine where requests to Services will be directed

Just as deployments create ReplicaSets, Service objects also create Endpoints in the background. Endpoints determine
where requests to our Services will be directed.

```shell
kubectl get endpoints
```

When a Pod is deleted, a new endpoint is created for the new Pod that will be created.

## References

- [Kubernetes Official Service Overview](https://kubernetes.io/docs/concepts/services-networking/service/)
- [GitHub - Aytitech K8sFundamentals - Network Policy](https://github.com/aytitech/k8sfundamentals/tree/main/networkpolicy)
- [GitHub - Aytitech K8sFundamentals - Service](https://github.com/aytitech/k8sfundamentals/tree/main/service)
