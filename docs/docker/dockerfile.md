# Dockerfile

## What is a Dockerfile?

A **Dockerfile** is a plain text file that contains a set of instructions used to build a Docker image. It defines
everything needed for the environment inside the container, including:

- Base operating system
- Application code
- Dependencies
- Configurations
- Runtime instructions

Docker reads the Dockerfile **line by line** and executes the commands to produce an image.

---

## Why Use a Dockerfile?

- **Automation**: Repeatable builds without manual setup.
- **Consistency**: Same environment across development, testing, and production.
- **Portability**: Run your application anywhere Docker is available.
- **Versioning**: Docker images can be tagged and rolled back.

---

## Common Dockerfile Instructions

| Instruction  | Description                                             | Example                                         |
|--------------|---------------------------------------------------------|-------------------------------------------------|
| `FROM`       | Defines the base image                                  | `FROM ubuntu:20.04`                             |
| `WORKDIR`    | Sets the working directory inside the container         | `WORKDIR /app`                                  |
| `COPY`       | Copies files from host into container                   | `COPY . /app`                                   |
| `ADD`        | Similar to `COPY` but can also handle URLs and archives | `ADD https://example.com/app.tar.gz /app/`      |
| `RUN`        | Executes a command during build                         | `RUN apt-get update && apt-get install -y curl` |
| `CMD`        | Default command to run at container start               | `CMD ["node", "server.js"]`                     |
| `ENTRYPOINT` | Defines a fixed executable                              | `ENTRYPOINT ["python3"]`                        |
| `EXPOSE`     | Documents the port the container listens on             | `EXPOSE 8080`                                   |
| `ENV`        | Sets environment variables                              | `ENV NODE_ENV=production`                       |
| `ARG`        | Defines build-time variables                            | `ARG VERSION=1.0.0`                             |
| `VOLUME`     | Declares mount points for persistence                   | `VOLUME /data`                                  |

---

## Example Dockerfile

```dockerfile
# Use an official Node.js image as the base
FROM node:18

# Set working directory
WORKDIR /usr/src/app

# Copy package files and install dependencies
COPY package*.json ./
RUN npm install

# Copy application source code
COPY . .

# Expose application port
EXPOSE 3000

# Default command
CMD ["npm", "start"]
```

### Explanation

- `FROM node:18`: Start from Node.js 18 image.
- `WORKDIR /usr/src/app`: Set working directory inside container.
- `COPY package*.json ./`: Copy package files first (to use Docker layer caching).
- `RUN npm install`: Install dependencies.
- `COPY . .`: Copy all application files.
- `EXPOSE 3000`: Declare that the app listens on port 3000.
- `CMD ["npm", "start"]`: Define the container’s startup command.

---

## ADD vs COPY

- **COPY**: Use for copying local files and directories.
- **ADD**: Can also handle remote URLs and compressed archives.  
  **Best practice:** Use `COPY` unless you specifically need `ADD` features.

---

## CMD vs ENTRYPOINT

- **CMD**: Provides default arguments for the container. Can be overridden at runtime.
- **ENTRYPOINT**: Defines the main executable that cannot be easily overridden.

Example:

```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]
```

Running `docker run myimage` → executes `python app.py`.  
Running `docker run myimage other.py` → executes `python other.py`.

---

## Exec Form vs Shell Form

- **Exec form (recommended):**

```dockerfile
CMD ["nginx", "-g", "daemon off;"]
```

Runs directly, avoids an extra shell process.

- **Shell form:**

```dockerfile
CMD nginx -g "daemon off;"
```

Runs in `/bin/sh -c`, allows shell features like variables but adds overhead.

---

## Build Arguments (ARG)

`ARG` allows you to pass variables at **build time**:

```dockerfile
ARG VERSION=1.0
RUN echo "Building version $VERSION"
```

Build with:

```bash
docker build --build-arg VERSION=2.0 -t myapp:2.0 .
```

---

## Building and Running a Docker Image

- **Build the image**

```bash
docker build -t myapp:1.0 .
```

- **Run a container**

```bash
docker run -d -p 3000:3000 myapp:1.0
```

---

## Updating and Rebuilding an Image

When you make changes (e.g., update source code or dependencies):

- Modify files in your project.

```dockerfile
FROM olduser/oldrepo:version
RUN echo "Added extra changes." > /info.txt
```

- Rebuild the image with a new tag:

```bash
docker build -t newuser/newrepo:version .
```

- View your changes (info.txt) in the new image:

```shell
docker run -it newuser/newrepo:version ls -al /
```

- Run the updated container:

```bash
docker run -d -p 3000:3000 newuser/newrepo:version
```

- Push to a registry (if needed):

```bash
docker push newuser/newrepo:version
```

---

## Inspecting Docker Images

### 1. Check Dockerfile inside an Image

Images do not always include their original Dockerfile. However, you can reconstruct information using:

- **docker history**

```bash
docker history myapp:1.0
```

- **docker inspect**

```bash
docker inspect myapp:1.0
docker image inspect myapp:1.0
```

- **Dive** (recommended tool)  
  [Dive](https://github.com/wagoodman/dive) is a CLI tool that shows layer contents and Dockerfile-like instructions.

Installation (if not already installed):

```bash
brew install dive
dive --version # Verify installation
```

```bash
dive myapp:1.0
```

### 2. Docker Hub

If the image is published on Docker Hub, the repository may include the original Dockerfile. Check under the "
Dockerfile" or "Dockerfile (view on GitHub)" section.

---

## Multi-Stage Builds

Multi-stage builds help create smaller and more secure production images.  
You can use one stage for building and another for the final runtime:

```dockerfile
# Stage 1: Build
FROM golang:1.20 AS builder
WORKDIR /app
COPY . .
RUN go build -o myapp

# Stage 2: Production
FROM alpine:3.18
WORKDIR /app
COPY --from=builder /app/myapp .
CMD ["./myapp"]
```

This way, the final image contains only the binary and no build dependencies.

---

## Best Practices

- Use a **small base image** (e.g., `alpine`) to reduce size.
- Use **multi-stage builds** for production images.
- Leverage **Docker layer caching** by ordering instructions wisely.
- Keep images clean by removing unnecessary files.
- Always pin versions to avoid unexpected changes.

---

## References

- [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)
- [Dive tool](https://github.com/wagoodman/dive)
- [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)  
