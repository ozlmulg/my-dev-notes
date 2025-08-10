# Docker Compose Commands

Docker Compose is a tool for defining and running multi-container Docker applications. Below are some common Docker
Compose commands with explanations using the new `docker compose` syntax.

---

## Starting Services

```bash
docker compose up
```

- Builds, (re)creates, starts, and attaches to containers for a service defined in `docker-compose.yml`.

---

## Starting Services in Detached Mode

```bash
docker compose up -d
```

- Starts the containers in the background (detached mode).

---

## Stopping Services

```bash
docker compose down
```

- Stops and removes containers, networks, volumes, and images created by `docker compose up`.

---

## Viewing Running Containers

```bash
docker compose ps
```

- Lists containers started by `docker compose`.

---

## Viewing Logs

```bash
docker compose logs
```

- Shows output logs from containers.

---

## Rebuilding Services

```bash
docker compose build
```

- Builds or rebuilds services.

---

## Restarting Services

```bash
docker compose restart
```

- Restarts running containers.

---

## Stopping Services Without Removing Containers

```bash
docker compose stop
```

- Stops running containers but does not remove them.

---

## Summary

| Command                  | Description                             |
|--------------------------|-----------------------------------------|
| `docker compose up`      | Start and attach to containers          |
| `docker compose up -d`   | Start containers in detached mode       |
| `docker compose down`    | Stop and remove containers and networks |
| `docker compose ps`      | List containers                         |
| `docker compose logs`    | Show container logs                     |
| `docker compose build`   | Build or rebuild services               |
| `docker compose restart` | Restart running containers              |
| `docker compose stop`    | Stop containers without removing them   |

---

For more info, visit the official docs: https://docs.docker.com/compose/
