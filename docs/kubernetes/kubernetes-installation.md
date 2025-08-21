# Kubernetes Installation

All commands are given through kube-apiserver. These commands can be given in 3 ways:

1. Through **REST Api** (as curl from terminal, etc.),
2. Through **Kubernetes GUI** (Dashboard, Lens, Octant),
3. Through **Kubectl** (CLI).

## Kubectl Installation

For installation details, you can check the [kubectl documentation](kubernetes-tools/kubectl.md#installation).

## Kubernetes Installation

### Which version will we install?

* For the most light-weight version -> **minikube**, **Docker Desktop (Single Node K8s Cluster)**
* Other options -> **kubeadm, kubespray**
* Cloud solutions -> **Azure Kubernetes Service (AKS), Google Kubernetes Engine, Amazon EKS**

### Docker Desktop

* Docker Desktop allows you to set up a Single Node Kubernetes Cluster. This situation **gives you the ability to
  perform operations on Kubernetes without needing another tool.** But the recommendation is **to use minikube!**
* For K8s installation within Docker Desktop, you need to go to `Settings > Kubernetes` and enable it.

### Minikube

For installation details, you can check the [minikube documentation](kubernetes-tools/minikube.md).

## kubeadm Installation

It's another platform that allows us to create a Kubernetes cluster. It's more advanced than minikube.

For installation details, you can check the [kubeadm documentation](kubernetes-tools/kubeadm.md).

## Installation on AWS

* **Amazon EKS** is the service that allows you to create a Kubernetes cluster on AWS.

## Installation on Azure

* **Azure Kubernetes Service (AKS)** is the service that allows you to create a Kubernetes cluster on Azure.

## Installation on Google Cloud Platform

* **Google Kubernetes Engine (GKE)** is the service that allows you to create a Kubernetes cluster on Google Cloud
  Platform.

## Play-with-kubernetes Installation

* If you don't want to give your credit card for cloud or want to quickly do some experiments,
  **[play-with-kubernetes](https://labs.play-with-k8s.com/)** is perfect for you.
* There's a 4-hour usage limit. After 4 hours, the system resets, settings are lost.
* It works browser-based.
* You can create a total max of 5 nodes.

## Tools

* **Lens -->** A very well-prepared management tool for Kubernetes. For other dashboard tools, you can check
  the [Dashboards documentation](kubernetes-tools/dashboards.md).
* **kubectx -->** For quick config/context switching.
* **Krew -->** Plugin-sets for kubectl

## References

- [Kubernetes Official Installation Tools](https://kubernetes.io/docs/tasks/tools/)
- [GitHub - Aytitech K8sFundamentals](https://github.com/aytitech/k8sfundamentals/blob/main/toolbox.md)
