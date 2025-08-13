# Docker Cheatsheet

## Image Commands

- `docker build` — Build an image from a Dockerfile
- `docker images` — List images
- `docker rmi` — Remove one or more images
- `docker tag` — Tag an image for a repository

## Container Commands

- `docker run` — Run a container
- `docker ps` — List running containers
- `docker stop` — Stop one or more running containers
- `docker start` — Start one or more stopped containers
- `docker restart` — Restart containers
- `docker rm` — Remove one or more containers
- `docker exec` — Run a command inside a running container
- `docker logs` — Fetch logs from a container

## Volume Commands

- `docker volume create` — Create a volume
- `docker volume ls` — List volumes
- `docker volume rm` — Remove a volume

## Network Commands

- `docker network create` — Create a network
- `docker network ls` — List networks
- `docker network rm` — Remove a network

## System Commands

- `docker system prune` — Remove unused data
- `docker system prune -a --volumes` — Remove all unused containers, images, networks, and volumes
- `docker info` — Display system-wide information
- `docker version` — Show Docker version info
