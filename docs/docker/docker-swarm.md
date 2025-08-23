# Docker Swarm

## What is Docker Swarm?

Docker Swarm is Docker’s **native container orchestration tool**.  
It allows you to manage multiple Docker hosts (nodes) as a single virtual system.

With Swarm, you can deploy and scale applications across a cluster, ensuring high availability and load balancing.

---

## Why Use Docker Swarm?

- Native integration with Docker CLI and Docker Compose.
- Easy setup compared to Kubernetes.
- Built-in service discovery and load balancing.
- Scaling and self-healing of services.
- Secure cluster communication using TLS.

---

## Swarm Architecture

- **Manager Nodes**: Responsible for managing the cluster and scheduling tasks.
- **Worker Nodes**: Run containers assigned by the managers.
- **Services**: High-level definition of containers in Swarm.
- **Tasks**: The actual running containers.

---

## Initializing a Swarm

To create a new Swarm cluster:

    docker swarm init

This command outputs a **join token** for adding worker or manager nodes:

    docker swarm join --token <token> <manager-ip>:2377

---

## Creating a Service

Example: Run an Nginx service with 3 replicas.

    docker service create --name web --replicas 3 -p 8080:80 nginx

Check running services:

    docker service ls

Inspect tasks of a service:

    docker service ps web

Scale a service:

    docker service scale web=5

---

## Docker Stack

Stacks let you deploy a full application defined in a `docker-compose.yml`.

Deploy a stack:

    docker stack deploy -c docker-compose.yml mystack

Check stacks:

    docker stack ls

Remove a stack:

    docker stack rm mystack

---

## Networking in Swarm

Swarm supports an **Overlay Network** to enable communication between services across nodes.

Create an overlay network:

    docker network create --driver overlay mynet

Use it in a stack or service:

    docker service create --name api --network mynet myimage

---

## Secrets Management

Swarm provides a way to securely store sensitive data.

Create a secret:

    echo "mydbpassword" | docker secret create db_pass -

Use secret in a service:

    docker service create --name app --secret db_pass myimage

List secrets:

    docker secret ls

---

## Swarm Node Management

### List Nodes

List all nodes in the Swarm:

    docker node ls

Example output:

    ID                            HOSTNAME  STATUS  AVAILABILITY  MANAGER STATUS
    abcd1234efgh *                manager1  Ready   Active        Leader
    ijkl5678mnop                  worker1   Ready   Active        
    qrst9012uvwx                  worker2   Ready   Active        

---

### Inspect Node

Inspect a node:

    docker node inspect <node-id>

---

### Promote a Node to Manager

Promote an existing worker to a manager:

    docker node promote <node-id>

Example:

    docker node promote ijkl5678mnop

Now `worker1` becomes a manager.

---

### Demote a Manager to Worker

Demote a manager back to a worker:

    docker node demote <node-id>

---

### Add a New Manager Node

From an existing manager, get the join command for a new manager:

    docker swarm join-token manager

Output example:

    docker swarm join --token SWMTKN-1-xyz <manager-ip>:2377

Run this on the new node to join the Swarm as a manager.

---

### Node Availability

- **Active**: Node can accept tasks.
- **Drain**: Node won’t accept new tasks (used for maintenance). Draining a node prevents new tasks from running on it.
- **Pause**: Node temporarily stops tasks.

Set node availability:

    docker node update --availability drain <node-id>
    docker node update --availability active <node-id>

---

## Monitoring and Logging

Get real-time stats:

    docker stats

Check service logs:

    docker service logs web

---

## Limitations of Docker Swarm

- Not as feature-rich as Kubernetes.
- Less adoption in large-scale production systems.
- Limited ecosystem support.

---

## References

- [Docker Swarm Overview](https://docs.docker.com/engine/swarm/)
- [Docker Service CLI](https://docs.docker.com/engine/reference/commandline/service/)
- [Docker Stacks and Compose](https://docs.docker.com/compose/compose-file/)  
