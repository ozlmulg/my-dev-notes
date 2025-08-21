# Kubernetes Objects - Network Structure, Service

## K8s Basic Network Infrastructure

K8s network structure has been handled within the following three rules (_essential_):

1. An **IP address range** (`--pod-network-cidr`) is determined for IP distribution to pods during K8s installation.
2. In K8s, each pod gets a **unique IP address** assigned from this cidr block.
3. **Pods within the same cluster** can communicate with each other by default **without any restrictions and without
   NAT (Network Address Translation).**

* Containers within K8s are exposed to 3 types of communication:
    1. A container communicates with an IP outside k8s,
    2. A container communicates with another container within its own node,
    3. A container communicates with another container in a different node.
* There's no problem in the first 2 scenarios, but in the 3rd scenario, there's a problem with NAT. For this reason, k8s
  has put the **Container Network Interface (CNI)** project into operation for containers to communicate with each
  other.
* **CNI only deals with container network connectivity and dropping resources allocated to containers when containers
  are deleted.**
* **K8s accepted CNI standards for network communication and allowed everyone to choose one of the CNI plugins they
  choose.** You can choose the most suitable CNI plugin from the following addresses:

[CNI GitHub Repository](https://github.com/containernetworking/cni)

[Kubernetes Official Documentation - Cluster Networking](https://kubernetes.io/docs/concepts/cluster-administration/networking)

**We covered the topic of Container communication with the "Outside World". So, how will the "Outside World" communicate
with Containers?**

Answer: **Service** object.

## Service

* It's the object that handles the K8s network side.

### Service Object Exit Scenario

Let's think we have a system with 1 Frontend (React), 1 Backend (Go):

* We wrote **one deployment each** for both applications and ensured **3 pods each** were defined.
* How will I provide access from the outside world to the 3 Frontend pods?
* Requests coming from Frontend should be processed in backend. There's not much problem here. Because each pod has an
  IP address and every pod in K8s can communicate with each other thanks to these IP addresses.
* To enable this communication; Frontend pods **need to know** the IP addresses of Backend pods. To solve this, I wrote
  all backend pod IP addresses to the frontend deployment. However, pods can be updated, changed, and during these
  updates, they **can get a new IP address. I need to define the newly created IP addresses again in the Frontend
  deployment.**

This is why we create a **Service** object to solve all these situations. **K8s gives Pods their own IP addresses and a
single DNS name for a set of Pods and load balances between them.**

## Service Types

### **ClusterIP** (Between Containers)

–> We can create a ClusterIP service and associate it with pods through labels. When we create this object, it has a
unique DNS address that all pods in the Cluster can resolve. Also, every k8s installation has a virtual IP range. (EX:
10.0.100.0/16)

–> When a ClusterIP service object is created, **an IP is assigned** to this object from this IP range and **this IP
address is registered to the Cluster's DNS mechanism with its name.** This IP address is a **Virtual IP address.**

–> This IP address is added to the IP Table on all nodes by Kube-proxy. All traffic coming to this IP address is
directed to Pods with Round Robin algorithm. This situation solves our 2 problems:

1. I can define this IP address to Frontend nodes (and if I call it `backend`), I can say use this IP address when you
   need to go to Backend. (**Selector = app:backend**) I don't have to add Backend pod IP addresses to Frontend pods
   every time!

**In summary: ClusterIP Service solves communication between Containers by taking on Service Discovery and
Load Balancing tasks!**\\

***

### NodePort Service (Outside World -> Container)

–> This service type is used to solve **connections coming from outside the Cluster.**

–> `NodePort` key is used.\\

### LoadBalancer Service (For Cloud Services)

–> This service type is only used in places like Agent K8s, Google K8s Engine.\\

### ExternalName Service (Unnecessary for now.)

## Service Object Application

### Cluster IP Example

```shell
apiVersion: v1
kind: Service
metadata:
  name: backend # Service name
spec:
  type: ClusterIP # Service type
  selector:
    app: backend # Which pod it will match with (same as the label in the pod)
  ports:
    - protocol: TCP
      port: 5000
      targetPort: 5000
```

Any object will find a response when it sends a request to `clusterIP:5000` formed within the cluster.

**Important Note:** Service names are in this format when created: `serviceName.namespaceName.svc.cluster.domain`

If another object in the same namespace wants to go to this service, it can write `backend` directly thanks to core DNS
resolution. Any object from another namespace must reach this service using the **long name** above.

### NodePort Example

* It should not be forgotten that all created NodePort services also have **ClusterIP**. So, this service can be talked
  to from inside using this ClusterIP.
* We can open a tunnel when using minikube with **`minikube service –url <serviceName>`**. Because we normally cannot
  access this worker node from outside. _This is completely related to minikube._

```shell
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

### Load Balancer Example

It's a service that will work on clusters created on Google Cloud Service, Azure.

```shell
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

### Creating Service Imperatively

```shell
kubectl delete service <serviceName>

# Creating ClusterIP Service
kubectl expose deployment backend --type=ClusterIP --name=backend

kubectl expose deployment backend --type=NodePort --name=backend
```

### What is Endpoint?

Just as deployments actually created ReplicaSet, Service objects also create Endpoints in the background. Where requests
coming to our Services will be directed is determined by Endpoints.

```shell
kubectl get endpoints
```

When a pod is deleted, a new endpoint is created for the new pod that will be created.

## References

- [Kubernetes Official Service Overview](https://kubernetes.io/docs/concepts/services-networking/service/)
- [GitHub - Aytitech K8sFundamentals - Network Policy](https://github.com/aytitech/k8sfundamentals/tree/main/networkpolicy)
- [GitHub - Aytitech K8sFundamentals - Service](https://github.com/aytitech/k8sfundamentals/tree/main/service)
