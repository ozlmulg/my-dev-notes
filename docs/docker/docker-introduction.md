# Docker Introduction

## Introduction

### Why Docker?

Docker is a platform designed to simplify the development, deployment, and running of applications inside
**containers**.

Key benefits:

- Consistency across environments (dev, test, production).
- Lightweight compared to virtual machines.
- Faster startup times.
- Portable: runs anywhere Docker is supported.
- Scalable: easily orchestrated with tools like Docker Swarm or Kubernetes.

### Operating System & Kernel Basics

- An **operating system** consists of the **kernel** (core) and user-space programs.
- The **kernel** manages resources such as CPU, memory, networking, and storage.
- Containers share the **host’s kernel**, making them lightweight compared to virtual machines (which run a full OS).

### Virtualization vs Containers

- **Virtual Machines (VMs):** Require a full OS per instance and a hypervisor to manage them.
- **Containers:** Share the host OS kernel while isolating processes and resources.

### What is a Container?

A **container** is an isolated unit that packages application code with its dependencies.  
It ensures the application runs the same way, regardless of the environment.

---

## Docker Fundamentals

### Docker History

- Released in **2013** by DotCloud (later renamed Docker, Inc.).
- Built on **Linux container (LXC)** technology.
- Revolutionized DevOps practices by making containers accessible and easy to use.

### Docker Engine

Docker Engine is the core component that runs and manages containers. It consists of:

- **Docker Daemon (`dockerd`)**: Background service that manages images, containers, networks, and volumes.
- **Docker CLI**: Command-line tool to interact with the daemon.
- **REST API**: Provides programmatic access.

### Images & Containers

- **Image:** A read-only template used to create containers. (e.g., `ubuntu:20.04`)
- **Container:** A running instance of an image with its own writable layer.

### Containers vs Virtual Machines

| Feature         | Containers         | Virtual Machines |
|-----------------|--------------------|------------------|
| Startup time    | Seconds            | Minutes          |
| OS per instance | Shared host kernel | Full OS          |
| Resource usage  | Lightweight        | Heavy            |
| Portability     | High               | Medium           |

### Windows Containers

- Windows supports containers with two modes:
    - **Windows Server containers:** Share the kernel with the host.
    - **Hyper-V containers:** Run with extra isolation using a lightweight VM.

---

## Docker Versions & Ecosystem

### Docker Editions

- **Docker CE (Community Edition):** Free and open-source, widely used.
- **Docker EE (Enterprise Edition):** Commercial, with support and enterprise features.
- **Docker Desktop:** GUI for Windows/macOS with Docker Engine, CLI, and Compose bundled.

### Docker Hub

- [Official registry](https://hub.docker.com/) for container images.
- Users can **push** and **pull** images (`docker push`, `docker pull`).
- Public and private repositories are available.

---

## Docker CLI Basics

### CLI Overview

Commonly used commands:

```bash
docker --version
docker ps -a
docker run hello-world
docker stop <container_id>
docker rm <container_id>
docker rmi <image_id>
```

### Container Lifecycle

1. **Create** → `docker create`
2. **Start** → `docker start`
3. **Run** → `docker run` (create + start)
4. **Pause/Unpause** → `docker pause` / `docker unpause`
5. **Stop** → `docker stop`
6. **Remove** → `docker rm`

### Docker’s Layered File System

- Images are built in **layers**.
- Each instruction in a Dockerfile adds a new layer.
- Containers add a **writable layer** on top of the image.

### Volumes & Data Persistence

- **Volumes:** Persistent storage managed by Docker (`docker volume create`).
- **Bind mounts:** Map a host directory into a container.
- **Anonymous volumes:** Automatically created, removed when the container is deleted.

**Empty vs Pre-populated Volume Behavior:**

- If a named volume is empty, Docker copies container data into it.
- If it already has data, Docker does not overwrite it.

---

## Networking & Logging

### Networking

- **Bridge:** Default network driver (isolated private network).
- **Host:** Shares the host’s networking namespace.
- **Overlay:** Used in multi-host setups (Swarm/Kubernetes).

### Publishing Ports

```bash
docker run -d -p 8080:80 nginx
```

Maps port **80 in container → 8080 on host**.

### Logging & Monitoring

- **STDIN, STDOUT, STDERR:** Container input/output streams.
- **docker logs <container_id>:** View container logs.
- **docker stats:** Real-time CPU, memory, and network usage.

### Resource Limits

```bash
docker run -d --memory="512m" --cpus="1.0" nginx
```

### Environment Variables

```bash
docker run -e ENV=production nginx
```

---

## Working with Images

### Naming & Tagging

Format: `repository:tag` (default tag is `latest`).  
Example:

```bash
docker pull ubuntu:20.04
```

### Save & Load Images

```bash
docker save myapp:1.0 > myapp.tar
docker load < myapp.tar
```

### Docker Commit

Create an image from a running container:

```bash
docker commit <container_id> myapp:custom
```

### Private Registries

- Host your own registry:

```bash
docker run -d -p 5000:5000 --name registry registry:2
```

---

## References

- [Docker Documentation](https://docs.docker.com/get-started/)
- [Docker Hub](https://hub.docker.com/)  
