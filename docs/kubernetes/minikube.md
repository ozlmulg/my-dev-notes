# Minikube Usage & Basic Commands

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

---

For more details, visit the official documentation: https://minikube.sigs.k8s.io/docs/

> **Note:** Docker must be installed and running on your machine to use Minikube with the Docker driver.
