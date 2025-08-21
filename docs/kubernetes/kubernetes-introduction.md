# Kubernetes Introduction

## Why is it Necessary?

Let's go through an example:

Let's think of a docker environment where multiple microservices are running. Let's assume that each microservice runs
in a docker container and we expose our application to users. Our application is currently running on a single server,
and when we make updates on the server, interruptions will start to occur in the system.

To solve this problem, we rented a new server, set up the same docker environment (cloned it), and installed a **load
balancer** to transition to a distributed architecture. Despite this, docker containers shut down on their own and we
need to manually intervene in this situation. Since we received more traffic, we rented 2 more servers. 2 more servers,
2 more servers..

We **continue to manage all these servers manually.** Over time, we started spending too much time on DevOps processes
due to manual intervention, and there was no time left for other tasks.

So what will solve this situation? Answer -> **Container Orchestration!**

We can set up all system configurations and entrust this decision mechanism to an orchestra conductor. This conductor is
**Kubernetes!**

Other alternatives -> Docker Swarm, Apache H2o

## K8s History

* Orchestration system developed by Google.
* Google has been using Linux containers for many years. To manage all these containers, they developed a platform
  called **Borg**. However, errors emerged over time and a need for a new platform arose, and the **Omega** platform was
  developed.
* In 2013, 3 Google engineers opened the Kubernetes repo on GitHub as open-source. _Kubernetes: Sea helmsman_ _(k8s)_
* In 2014, the project was donated by Google to CNCF. (Cloud Native Computing Foundation)

## What is Kubernetes?

* Declarative (_declarative configuration_), Container orchestration platform.
* The project is not tied to any company, it is managed by a foundation.
* It's free. Competitor companies are also **open-source**.
* The reason it's so popular is the platform's design and solution approach.
* It follows semantic versioning (_x.y.z. -> x: major, y: minor, z: patch_) and releases a minor version every 4 months.
* It releases a patch version every month.
* A kubernetes platform can be used for a maximum of 1 year, after 1 year it needs to be updated.

![](<images/kubernetes_components.svg>)

### Kubernetes Design and Approach

It consists of multiple developable modules. Each of these modules has a task and all modules focus on their own tasks.
These modules or new modules can be developed when needed. (extendable)

Instead of telling us step by step what we need to do like "_Do this, then do that_" **(imperative method);** K8s offers
the approach of **"I want something like this" (declarative method)**. **It doesn't describe how to do it, we tell it
what we want.**

* The imperative method causes us to waste time, we have to design all the steps.
* In the declarative method, we just tell it what we want and look at the result.

Kubernetes asks us what we want from it, we tell it, and it doesn't deviate from what we want. For example, let's say
the **Desired State (Declared State - _Think of it as requests_)** is as follows:

* Create an image named Example/k8s:latest and run it with 10 containers. Open port 80 to the outside world and when I
  make an update to this service, execute it on 2 tasks simultaneously and wait 10 seconds.

If Kubernetes has 9 containers running, it immediately starts one more container and **optimizes** the platform
according to our requests. This saves us from a very big job. _(Remember, we were starting it manually in Docker.)_

## Kubernetes Components

K8s was created **considering microservice architecture**.

![](<images/kubernetes_cluster.png>)

![](<images/kubernetes_cluster_architecture.svg>)

### **Control Plane** (Master Nodes)

The following 4 components make up the k8s management part and run on the **master-node**.

* **Master-node** -> Where management modules run.
* **Worker-node** -> Where the workload runs.

![](<images/kubernetes_simile.png>) ![](<images/kubernetes_control_plane.png>)

* **kube-apiserver** **(api) –>** The brain of K8s, **the main communication center, entry point**. We can call it a
  kind of **Gateway**. All **components** and **nodes** communicate through **kube-apiserver**. Also, **kube-apiserver**
  provides communication between the outside world and the platform. It is the **only component** that can communicate
  with everyone to this extent. It handles **Authentication and Authorization**.
* **etcd** **->** All cluster data, metadata information, and information about components and objects created in the
  Kubernetes platform are stored here. A kind of **Archive room.** etcd stores data in **key-value** format. Other
  components **cannot communicate directly** with etcd. They do this communication through **kube-apiserver**.
* **kube-scheduler (sched) ->** Where K8s work planning is done. It monitors newly created or unassigned Pods and
  selects a **node** for them to run on. (_Pod = container_) When making this selection, it evaluates various parameters
  such as CPU, Ram, etc. and **decides which node is most suitable for the pod through a selection algorithm.**
* **kube-controller-manager (c-m) ->** The structure where K8s controls are performed. **It checks whether there is a
  difference between the current state and the desired state.** For example; you requested 3 clusters and k8s
  accomplished this. But a problem occurred and 2 containers remained. kube-controller comes into play here and
  immediately starts one more cluster. Although it is compiled as a single binary, it contains many controllers:
    * Node Controller,
    * Job Controller,
    * Service Account & Token Controller,
    * Endpoints Controller.

### **Worker Nodes**

These are where our containers run. They run containers like Container or Docker. Each worker node has **3 basic
components**:

1. **Container runtime ->** Docker by default. But for various reasons, it has **transitioned from Docker to Containerd
   **. The difference between Docker and containerd is minimal enough to say there's no difference. In fact, Docker also
   uses containerd internally. Another supported container type is **CRI-O.**
2. **kubelet ->** It controls etcd through the API Server and creates pods that need to run on its node as determined by
   the scheduler. It sends a message to Containerd and ensures that a container runs with the specified properties.
3. **kube-proxy ->** It manages network rules and traffic flow on nodes. It allows and monitors communication with Pods.

In addition to all these, plugins that provide GUI services, etc. are also installed.

## References

- [Kubernetes Official Components Overview](https://kubernetes.io/docs/concepts/overview/components/)
- [GitHub - Aytitech K8sFundamentals](https://github.com/aytitech/k8sfundamentals/blob/main/README.md)