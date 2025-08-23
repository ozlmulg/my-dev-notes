# Docker Compose

## What is Docker Compose?

Docker Compose is a tool for defining and running **multi-container Docker applications**.  
It uses a YAML file (`docker-compose.yml`) to configure application services, networks, and volumes.

With a single command (`docker compose up`), you can start all the defined services.

---

## Why Use Docker Compose?

- Simplifies running multi-container applications.
- Configuration as code (`docker-compose.yml`).
- Easy to share and reproduce environments.
- Supports scaling services up and down.

---

## Installing Docker Compose

Docker Desktop includes Compose by default.  
Check version:

    docker compose version

For Linux, install separately:  
[Install Docker Compose](https://docs.docker.com/compose/install/)

---

## Docker Compose CLI Basics

| Command                      | Description                                      |
|------------------------------|--------------------------------------------------|
| `docker compose up`          | Start and attach to containers                   |
| `docker compose up -d`       | Start containers in detached mode (background)   |
| `docker compose down`        | Stop and remove containers, volumes and networks |
| `docker compose ps`          | List running services (containers)               |
| `docker compose logs`        | Show services logs                               |
| `docker compose exec web sh` | Run a shell inside a service container           |
| `docker compose build`       | Build or rebuild services                        |
| `docker compose restart`     | Restart running containers                       |
| `docker compose stop`        | Stop containers without removing them            |

---

## docker-compose.yml Structure

A basic YAML file structure includes:

- **services**: Application containers (e.g., web, db).
- **networks**: Communication between services.
- **volumes**: Data persistence.

Example:

    version: "3.9"

    services:
      web:
        image: nginx:alpine
        ports:
          - "8080:80"
        volumes:
          - ./html:/usr/share/nginx/html

      db:
        image: postgres:15
        environment:
          POSTGRES_USER: admin
          POSTGRES_PASSWORD: secret
          POSTGRES_DB: mydb
        volumes:
          - db-data:/var/lib/postgresql/data

    volumes:
      db-data:

---

## Example: Multi-Container App

### docker-compose.yml

    version: "3.9"

    services:
      backend:
        build: ./backend
        ports:
          - "5000:5000"
        environment:
          - DATABASE_URL=postgres://admin:secret@db:5432/mydb
        depends_on:
          - db

      frontend:
        build: ./frontend
        ports:
          - "3000:3000"

      db:
        image: postgres:15
        restart: always
        environment:
          POSTGRES_USER: admin
          POSTGRES_PASSWORD: secret
          POSTGRES_DB: mydb
        volumes:
          - db-data:/var/lib/postgresql/data

    volumes:
      db-data:

### Commands

    docker compose up -d      # Start all services in background
    docker compose ps         # Check running services
    docker compose logs db    # View logs of db service
    docker compose down       # Stop and clean up

---

## Overriding and Extending Compose Files

- Default file is `docker-compose.yml`.
- You can use multiple files for environments (e.g., `docker-compose.override.yml`).

```bash
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d
```

---

## Scaling Services

You can scale services with a single command:

```bash
docker compose up -d --scale web=3
```

This runs **3 replicas** of the `web` service.

---

## Best Practices

- Keep secrets outside YAML files (use `.env` files).
- Use version control for your Compose files.
- Separate development and production configurations.
- Use named volumes for persistent data.

---

## References

- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Compose file reference](https://docs.docker.com/compose/compose-file/)  
