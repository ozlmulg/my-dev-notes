# Minikube

Minikube is a tool that makes it easy to run Kubernetes locally. Below are some commonly used Minikube commands with
brief explanations.

---

## Installing Minikube

```bash
brew install minikube
```

- Installs Minikube on macOS using Homebrew.

---

## Starting Minikube

### 1. Start with a Docker driver

```bash
minikube start --driver=docker
```

### 2. Start with a VirtualBox driver

```bash
minikube start --driver=virtualbox
```

### 3. Start with multiple nodes

```bash
minikube start --nodes=5 --driver=docker
```

### 4. Start with a named profile

```bash
minikube start -p profileName
```

> The default profile name is `minikube`.

---

## Checking Minikube Status

```bash
minikube status
```

- Shows the current status of the Minikube cluster.

---

## Stopping Minikube

```bash
minikube stop
```

- Stops the running Minikube cluster without deleting it.

---

## Deleting Minikube

```bash
minikube delete
```

- Deletes the Minikube cluster and all its data.

---

## Creating a service url in Minikube

```bash
minikube service --url <service-name>
```

- Starts a service in Minikube and provides the URL to access it. (e.g. http://127.0.0.1:33901)

## Add new node

```bash
minikube node add
```

- Adds a new node to the Minikube cluster.

---

## Using Addons in Minikube

With Minikube addons, you can quickly enable powerful Kubernetes features such as **Ingress**, **Dashboard**, and many
others for local testing and learning.

### 1. Listing Addons

To see all available addons in Minikube:

```bash
minikube addons list
```

This will show a list of supported addons, their status (enabled/disabled), and descriptions.

### 2. Enabling Ingress Addon

The **Ingress addon** provides an NGINX Ingress Controller, useful for routing traffic into your cluster.

Enable it with:

```bash
minikube addons enable ingress
```

After enabling, you can use `Ingress` resources in your cluster to expose services.

### 3. Enabling Kubernetes Dashboard Addon

The **Dashboard addon** provides a web-based UI to manage your Kubernetes cluster.

Enable it with:

```bash
minikube addons enable dashboard
```

Start the dashboard with:

```bash
minikube dashboard
```

This command will automatically open the Dashboard in your default web browser.

---

## Deleting All Docker Objects in Minikube

Minikube runs its own **Docker daemon** inside a virtual machine (VM).  
Because of this, regular Docker commands won’t affect it unless you connect to Minikube’s Docker environment first.

---

### 1. Delete All Docker Objects Inside Minikube

**Step 1:** Connect your shell to Minikube's Docker environment

```bash
eval $(minikube docker-env)
```

**Step 2:** Remove all unused containers, images, networks, and volumes

```bash
docker system prune -a --volumes
```

- eval $(minikube docker-env) → Points your shell to Minikube’s Docker daemon.
- docker system prune -a --volumes → Removes **all** unused containers, images, networks, and volumes.  
  ⚠ **Warning:** This action cannot be undone.

---

### 2. Completely Reset the Minikube Environment

If you want to remove not just Docker objects, but the entire Minikube Kubernetes environment:

```bash
minikube delete --all
```

Afterward, you can start fresh with:

```bash
minikube start
```

---

### 3. Delete All Kubernetes Resources Only

If you only want to remove Kubernetes objects (pods, deployments, services, etc.) without deleting Docker images:

```bash
kubectl delete all --all -A
```

---

## Summary

| Command                                    | Description                        |
|--------------------------------------------|------------------------------------|
| `brew install minikube`                    | Install Minikube                   |
| `minikube start --driver=docker`           | Start Minikube with Docker driver  |
| `minikube start --nodes=5 --driver=docker` | Start Minikube with multiple nodes |
| `minikube status`                          | Check the status of Minikube       |
| `minikube stop`                            | Stop the Minikube cluster          |
| `minikube delete`                          | Delete the Minikube cluster        |
| `minikube service --url <service-name>`    | Create a service url               |
| `minikube node add`                        | Add new node                       |
| `minikube delete --all`                    | Reset the Minikube cluster         |
| `minikube addons list`                     | List addons                        |
| `minikube addons enable dashboard`         | Enable Kubernetes dashboard        |
| `minikube dashboard`                       | Start Kubernetes dashboard         |

---

For more details, visit the official documentation: [Minikube Official Website](https://minikube.sigs.k8s.io/docs/)

> **Note:** Docker must be installed and running on your machine to use Minikube with the Docker driver.
