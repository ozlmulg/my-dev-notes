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

```bash
minikube start --driver=docker
```

- Starts a Minikube cluster using the Docker driver.

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

# Deleting All Docker Objects in Minikube

Minikube runs its own **Docker daemon** inside a virtual machine (VM).  
Because of this, regular Docker commands won’t affect it unless you connect to Minikube’s Docker environment first.

---

## 1️⃣ Delete All Docker Objects Inside Minikube

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

## 2️⃣ Completely Reset the Minikube Environment

If you want to remove not just Docker objects, but the entire Minikube Kubernetes environment:

```bash
minikube delete --all
```

Afterwards, you can start fresh with:

```bash
minikube start
```

---

## 3️⃣ Delete All Kubernetes Resources Only

If you only want to remove Kubernetes objects (pods, deployments, services, etc.) without deleting Docker images:

```bash
kubectl delete all --all -A
```

---

## Summary

| Command                                 | Description                       |
|-----------------------------------------|-----------------------------------|
| `brew install minikube`                 | Install Minikube                  |
| `minikube start --driver=docker`        | Start Minikube with Docker driver |
| `minikube status`                       | Check the status of Minikube      |
| `minikube stop`                         | Stop the Minikube cluster         |
| `minikube delete`                       | Delete the Minikube cluster       |
| `minikube service --url <service-name>` | Create a service url              |
| `minikube node add`                     | Add new node                      |
| `minikube delete --all`                 | Reset the Minikube cluster        |

---

For more details, visit the official documentation: https://minikube.sigs.k8s.io/docs/

> **Note:** Docker must be installed and running on your machine to use Minikube with the Docker driver.
