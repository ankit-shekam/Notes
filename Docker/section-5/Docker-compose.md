[back to previous file](../dockercontent.md)
---

# Docker Compose

## What / Why Docker Compose?

Instead of manually typing long, repetitive `docker build` and `docker run` commands with network and port flags to wire multiple containers together, **Docker Compose** lets you define your entire multi-container setup declaratively in a single YAML file.

```mermaid
flowchart TB
    DC["Docker Compose Overview"]

    DC --> A["1. CLI bundled with Docker (docker compose)"]
    DC --> B["2. Starts & connects multiple containers simultaneously"]
    DC --> C["3. Replaces verbose, repetitive 'docker run' CLI flags"]
```

### How Compose Replaces Manual CLI Commands

```mermaid
flowchart LR
    OLD["Manual CLI: docker build -t user/visits . & docker run -p 8080:8080 user/visits"] -->|Captured declaratively in| YML["docker-compose.yml — Contains all options normally passed to Docker CLI"]
    YML -->|Executed by| CLI["Docker Compose CLI (docker compose up)"]
```

---

## Structure of `docker-compose.yml`

Think of `docker-compose.yml` as telling Docker: *"Here are the containers (services) I want created and wired together:"*

```mermaid
flowchart TB
    ROOT["docker-compose.yml — Containers to create"]
    ROOT --> S1["Service 1: redis-server"]
    ROOT --> S2["Service 2: node-app"]

    S1 --> S1A["Build from the 'redis' image on Docker Hub"]
    S2 --> S2A["Build using the Dockerfile in the current directory (.)"]
    S2 --> S2B["Map host port to container port (e.g. 443:8080 or 8081:8081)"]
```

### Example `docker-compose.yml`

```yaml
version: '3'               # (Note: optional/deprecated in modern Compose V2)
services:                  # Each entry defines one containerized service
  redis-server:            # Service 1: stores the visitor count
    image: 'redis'         # Pull official redis image from Docker Hub
  node-app:                # Service 2: Node HTTP server
    build: .               # Build image using Dockerfile in current directory
    ports:
      - "443:8080"         # Map host port 443 -> container port 8080
```

> **Modern Update:** See [Section 6: Modern Docker Compose (V2)](../section-6/production-docker-and-internals.md#5-modern-docker-compose-v2-for-multi-container-apps) for modern `compose.yaml` features like `healthcheck`, `depends_on`, named `volumes`, custom `networks`, and `.env` files.

---

## `docker run` vs. Docker Compose Commands

```mermaid
flowchart LR
    R1["docker run myimage"] -->|Compose equivalent| C1["docker compose up (or docker-compose up)"]
    R2["docker build . + docker run myimage"] -->|Compose equivalent| C2["docker compose up --build (or docker-compose up --build)"]
```

| Single-Container Docker CLI | Docker Compose Equivalent | Purpose |
| :--- | :--- | :--- |
| `docker run myimage` | `docker compose up` | Start all services defined in `docker-compose.yml` |
| `docker build . && docker run myimage` | `docker compose up --build` | Rebuild local images and start all services |
| `docker stop ...` / `docker rm ...` | `docker compose down` | Stop and remove all containers & default network |
