[back to Docker index](./dockercontent.md)
---

# Part 3: Dockerfiles & Building Custom Images

## Table of Contents
1. [What is a Dockerfile?](#1-what-is-a-dockerfile)
2. [Writing a Dockerfile (The Analogy)](#2-writing-a-dockerfile-the-analogy)
3. [Anatomy of a Dockerfile & Common Instructions](#3-anatomy-of-a-dockerfile--common-instructions)
4. [What Happens Inside `docker build .`?](#4-what-happens-inside-docker-build-)
5. [Building, Running & Tagging an Image](#5-building-running--tagging-an-image)
6. [Creating an Image from a Running Container](#6-creating-an-image-from-a-running-container)

---

## 1. What is a Dockerfile?

A **Dockerfile** is a plain text file containing a series of instructions that the Docker Daemon (Server) follows step by step to build an image. Think of it as your blueprint for constructing a containerized application.

```mermaid
flowchart LR
    DF["Dockerfile — Configuration defining how our container should behave"] --> CLI["Docker Client"] --> SRV["Docker Server (Daemon)"] --> IMG["Usable Image!"]
```

---

## 2. Writing a Dockerfile (The Analogy)

```mermaid
flowchart LR
    A["Writing a Dockerfile"] === B["Being given a computer with NO OS and being told to install Chrome"]
```

Think about every step you would have to perform to install Chrome on a brand-new computer with no operating system—each group of steps maps directly to one of the **three phases of every Dockerfile**, which Docker executes line by line, in order:

```mermaid
flowchart TB
    subgraph Phase1["Phase 1: Specify a Base Image (FROM)"]
        P1["1. Install an operating system"]
    end

    subgraph Phase2["Phase 2: Run Commands to Install Additional Programs (RUN / COPY)"]
        P2["2. Start up default browser"] --> P3["3. Navigate to chrome.google.com"] --> P4["4. Download installer"] --> P5["5. Open file explorer"] --> P6["6. Execute chrome_installer.exe"]
    end

    subgraph Phase3["Phase 3: Command to Run on Startup (CMD / ENTRYPOINT)"]
        P7["7. Execute chrome.exe"]
    end

    Phase1 --> Phase2 --> Phase3
```

---

## 3. Anatomy of a Dockerfile & Common Instructions

The default filename is `Dockerfile`. Each line consists of an **instruction** (in ALL CAPS, telling the Docker Server what to do) followed by its **argument**:

```mermaid
flowchart LR
    subgraph L1["Line 1 — Base Image"]
        I1["Instruction: FROM"] --- A1["Argument: alpine"]
    end
    subgraph L2["Line 2 — Install Dependency"]
        I2["Instruction: RUN"] --- A2["Argument: apk add --update redis"]
    end
    subgraph L3["Line 3 — Startup Command"]
        I3["Instruction: CMD"] --- A3["Argument: [redis-server]"]
    end
    L1 --> L2 --> L3
```

```dockerfile
FROM alpine                  # Lightweight Alpine Linux base image
RUN apk add --update redis   # Runs while BUILDING the image (install packages)
CMD ["redis-server"]         # Default startup command for containers from this image
```

### Common Instructions

| Instruction | Purpose |
| :--- | :--- |
| **`FROM`** | The first instruction—specifies the base image you are building upon (e.g., `FROM ubuntu:20.04`). Every Dockerfile starts with this. |
| **`RUN`** | Executes commands during the **build process** (installing packages, compiling code, setting up dependencies). |
| **`COPY` / `ADD`** | Copies files from your local machine (build context) into the image. |
| **`WORKDIR`** | Sets the working directory inside the container for subsequent instructions. |
| **`ENV`** | Sets environment variables inside the image/container. |
| **`EXPOSE`** | Documents which network ports the application listens on. |
| **`CMD` / `ENTRYPOINT`** | Defines the default startup command executed when a container starts from this image. |

A slightly fuller example using more of these instructions:

```dockerfile
FROM python:3.9                     # Official Python base image
WORKDIR /app                        # Set the working directory
COPY . .                            # Copy build context into the image
RUN pip install -r requirements.txt # Install dependencies
CMD ["python", "app.py"]            # Startup command
```

---

## 4. What Happens Inside `docker build .`?

Each instruction runs against a **temporary container** built from the previous step's intermediate image, takes a snapshot, shuts down the temporary container, and passes the new intermediate image forward. Every instruction therefore produces a new read-only **layer**:

```mermaid
flowchart TB
    subgraph Step1["Step 1: FROM alpine"]
        S1["Download alpine base image"]
    end

    subgraph Step2["Step 2: RUN apk add --update redis"]
        R1["Take image from Step 1"] --> R2["Create temporary container"]
        R2 --> R3["Run 'apk add --update redis' inside it (modified FS)"]
        R3 --> R4["Take FS snapshot & shut down temp container"]
        R4 --> R5["Intermediate Image #1 ready"]
    end

    subgraph Step3["Step 3: CMD [redis-server]"]
        C1["Take Intermediate Image #1"] --> C2["Create temporary container"]
        C2 --> C3["Set primary startup command to 'redis-server'"]
        C3 --> C4["Shut down temp container & commit snapshot"]
        C4 --> C5["Intermediate Image #2 ready"]
    end

    subgraph Done["Build Complete"]
        F1["No more steps! Output is the final image generated from the last step"]
    end

    Step1 --> Step2 --> Step3 --> Done
```

### Build Layer Caching
* Docker caches each intermediate image (layer) produced between steps to drastically speed up rebuilds.
* If an instruction (and everything above it) has not changed, Docker **reuses the cached layer** instead of re-downloading or re-installing packages.
* Modifying an instruction or changing the order invalidates the cache for that step **and every step below it**. *(See [Part 4](./04-real-world-node-project.md#4-optimizing-performance-with-build-caching) for how to order instructions to exploit this.)*
* Common base layers are also shared across different images, saving disk space.

---

## 5. Building, Running & Tagging an Image

```bash
docker build .              # Build -> outputs an image ID
docker run <image_id>       # Run a container from it
```

> Modern Docker Desktop has **BuildKit** enabled by default, which produces `sha256`-based image IDs (`sha256:<image_id>`).

Instead of referencing random hex `image_id` hashes, **tag** your image with a human-readable name using `-t`:

```bash
docker build -t <docker_user_id>/<project_name>:<version> .
# Example:
docker build -t stephengrider/redis:latest .
docker run stephengrider/redis
```

```mermaid
flowchart LR
    subgraph BuildCmd["Tagging During Build"]
        direction LR
        B1["docker"] --- B2["build"] --- B3["-t stephengrider/redis:latest"] --- B4["."]
    end
    B3 -.-> T1["Tags the resulting image"]
    B4 -.-> T2["Build context directory (files/folders sent to Docker)"]
```

### Image Tag Naming Convention

```mermaid
flowchart TB
    TAG["-t stephengrider / redis : latest"]

    TAG --> P1["stephengrider"]
    TAG --> P2["redis"]
    TAG --> P3["latest"]

    P1 -.-> D1["Your Docker ID"]
    P2 -.-> D2["Repo / Project Name"]
    P3 -.-> D3["Version Tag"]
```

---

## 6. Creating an Image from a Running Container

The image ↔ container relationship works in both directions: you can snapshot a modified container back into a new image using `docker commit`:

```bash
docker commit -c 'CMD ["redis-server"]' <container_id>
```

### Command Breakdown: `docker commit`

```mermaid
flowchart TB
    CMD["docker commit -c 'CMD [redis-server]' container_id"]

    CMD --> P1["docker"]
    CMD --> P2["commit"]
    CMD --> P3["-c 'CMD [redis-server]'"]
    CMD --> P4["container_id"]

    P1 -.-> D1["Reference the Docker Client"]
    P2 -.-> D2["Create a new image from a container's changes"]
    P3 -.-> D3["Set the default startup command for the new image"]
    P4 -.-> D4["ID of the existing container to snapshot"]
```

### How It Works Step-by-Step

```mermaid
flowchart TB
    S1["1. Run a base container interactively: docker run -it alpine sh"] --> S2["2. Manually install packages inside container: apk add --update redis"]
    S2 --> S3["3. Snapshot container & attach startup command: docker commit -c 'CMD [redis-server]' container_id"]
    S3 --> S4["4. Outputs new Image ID (contains modified FS snapshot + startup command)"]
```

This outputs a new `image_id` containing the container's current file system state and specified startup command, ready to be run via `docker run <new_image_id>`.

---

**Next:** [Part 4: Real-World Docker Project (Node.js Web Server) →](./04-real-world-node-project.md)
