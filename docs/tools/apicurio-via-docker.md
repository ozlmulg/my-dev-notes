# Apicurio via Docker

Apicurio Registry is an open-source registry for storing and retrieving API artifacts such as OpenAPI definitions,
AsyncAPI definitions, Avro schemas, JSON Schema, and more. This guide explains how to install and run Apicurio **(both
backend and UI)** using Docker.

---

## Prerequisites

- **Docker** installed on your system
- Ensure that ports **8080** (backend) and **8888** (UI) are free before starting
- You can check if a port is in use with:

```
lsof -i:8080  
lsof -i:8888
```

---

## Step 1 — Install Docker (MacOS example with Homebrew)

`brew install docker`

---

## Step 2 — Run the Backend

1. Pull the Apicurio Registry backend image:

   `docker pull apicurio/apicurio-registry:3.0.6`

2. Run the backend container:

   `docker run -it -p 8080:8080 apicurio/apicurio-registry:3.0.6`

3. Open your browser and navigate to: `http://localhost:8080`

---

## Step 3 — Run the UI

1. Pull the Apicurio Registry UI image:

   `docker pull apicurio/apicurio-registry-ui:3.0.6`

2. Run the UI container:

   `docker run -it -p 8888:8080 apicurio/apicurio-registry-ui:3.0.6`

3. Open your browser and navigate to: `http://localhost:8888`

---

## Step 4 — Upload and Validate API Artifacts

Once the UI is connected to the backend:

1. Go to the UI (`http://localhost:8888`)
2. Use the **Upload** feature to add your API definition file (e.g., OpenAPI, Avro, JSON Schema).
3. Explore detailed documentation, validation results, and example payloads.

> **Note:** Postman does not provide schema-level validation and example previews for these formats — using Apicurio UI
> is more powerful for this purpose.

---

## Step 5 — Stopping Containers

To stop the backend:

`docker ps`  # find the container ID for the backend  
`docker stop <container_id>`

To stop the UI:

`docker ps`  # find the container ID for the UI  
`docker stop <container_id>`
