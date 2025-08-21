# Kubernetes Dashboards

This guide provides an overview and installation instructions for three popular Kubernetes dashboard tools:

- [Kubernetes Dashboard](https://github.com/kubernetes/dashboard)
- [Lens](https://k8slens.dev/)
- [Headlamp](https://github.com/headlamp-k8s/headlamp)

These tools make it easier to visualize, manage, and troubleshoot Kubernetes clusters.

---

## 1. Kubernetes Dashboard

### Overview

The **Kubernetes Dashboard** is a web-based UI that allows you to manage applications, monitor cluster resources, and
troubleshoot workloads within your cluster.

### Installation

Deploy the dashboard with the following command:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

### Accessing the Dashboard

Create a Service Account and ClusterRoleBinding:

```bash
kubectl create serviceaccount dashboard-admin-sa -n kubernetes-dashboard
kubectl create clusterrolebinding dashboard-admin-sa   --clusterrole=cluster-admin   --serviceaccount=kubernetes-dashboard:dashboard-admin-sa
```

Get the authentication token:

```bash
kubectl -n kubernetes-dashboard create token dashboard-admin-sa
```

Start the proxy:

```bash
kubectl proxy
```

Access the dashboard at:  
[http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/](http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/)

### Using Kubernetes Dashboard with Minikube

If you are running Kubernetes with **Minikube**, enabling the dashboard is even easier.

Enable the Dashboard Addon:

```bash
minikube addons enable dashboard
```

Start the Dashboard:

```bash
minikube dashboard
```

This command will automatically open the Dashboard in your default web browser.

---

## 2. Lens

### Overview

[**Lens**](https://k8slens.dev/) is a popular desktop application for Kubernetes cluster management. It provides an
intuitive interface for monitoring workloads, nodes, logs, and cluster health.

### Installation

- **macOS (Homebrew):**

```bash
brew install --cask lens
```

- **Windows (Chocolatey):**

```bash
choco install lens
```

- **Linux (AppImage):**
  Download the latest `.AppImage` from the [Lens GitHub releases](https://github.com/lensapp/lens/releases).

- Or directly download Lens from the [Lens Official Website](https://k8slens.dev/).

### Usage

- Start Lens and add your cluster via `~/.kube/config`.
- Explore resources such as pods, deployments, and services.
- Use built-in terminal and monitoring features for troubleshooting.

---

## 3. Headlamp

### Overview

[**Headlamp**](https://github.com/headlamp-k8s/headlamp) is an open-source, web-based Kubernetes UI. Unlike Lens, it
runs as a web app and supports plugins for customization.

### Installation

You can run Headlamp as a desktop app or deploy it into a cluster.

#### Option 1: Desktop App

- **macOS (Homebrew):**

```bash
brew install headlamp
```

- **Linux & Windows:**
  Download binaries from the [Headlamp releases](https://github.com/headlamp-k8s/headlamp/releases).

#### Option 2: Deploy in Cluster

```bash
kubectl apply -f https://raw.githubusercontent.com/headlamp-k8s/headlamp/main/kubernetes-headlamp.yaml
```

Expose the service (e.g., via `kubectl port-forward` or Ingress).

### Usage

- Log in with your kubeconfig file or token.
- Navigate through workloads, namespaces, and nodes.
- Extend functionality using available plugins.

---

## Comparison

| Feature        | Kubernetes Dashboard | Lens (Desktop)  | Headlamp (Web/Desktop) |
|----------------|----------------------|-----------------|------------------------|
| Interface      | Web UI               | Desktop app     | Web & Desktop app      |
| Installation   | In-cluster YAML      | Binary/AppImage | In-cluster or Desktop  |
| Authentication | Token                | kubeconfig      | kubeconfig / token     |
| Extensibility  | Limited              | No plugins      | Plugin system          |

---

✅ You can choose the tool that best fits your needs:

- **Kubernetes Dashboard** → Official and lightweight.
- **Lens** → Powerful desktop client.
- **Headlamp** → Flexible, extensible, and web-based.  
