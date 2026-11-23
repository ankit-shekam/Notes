[back to previous file](../dockercontent.md)
---

# Building Custom Images & Dockerfile Flow

## Creating a Dockerfile (The Analogy)

```mermaid
flowchart LR
    A["Writing a Dockerfile"] === B["Being given a computer with NO OS and being told to install Chrome"]
```

Think about every step you would have to perform to install Chrome on a brand-new computer with no operating system—each group of steps maps directly to a Dockerfile phase:

```mermaid
flowchart TB
    subgraph Phase1["Specify a Base Image (FROM)"]
        P1["1. Install an operating system"]
    end

    subgraph Phase2["Run Commands to Install Additional Programs (RUN)"]
        P2["2. Start up default browser"] --> P3["3. Navigate to chrome.google.com"] --> P4["4. Download installer"] --> P5["5. Open file explorer"] --> P6["6. Execute chrome_installer.exe"]
    end

    subgraph Phase3["Command to Run on Startup (CMD)"]
        P7["7. Execute chrome.exe"]
    end

    Phase1 --> Phase2 --> Phase3
```

---

## Anatomy of a Dockerfile

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

Example `Dockerfile`:

```dockerfile
FROM alpine
RUN apk add --update redis
CMD ["redis-server"]
```

* **`FROM alpine`**: Uses lightweight Alpine Linux as the base image.
* **`RUN <command>`**: Runs a command while building the image (e.g., installing packages).
* **`CMD ["command"]`**: Sets the default startup command when containers are launched from this image.

To build and run:
```bash
docker build .
docker run <image_id>
```

---

## What Happens Inside `docker build .`?

Each instruction runs against a temporary container built from the previous step's intermediate image, takes a snapshot, shuts down the temporary container, and passes the new intermediate image forward:

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
* Docker caches each intermediate image produced between steps to drastically speed up rebuilds.
* If an instruction (and everything above it) has not changed, Docker **reuses the cached intermediate image** instead of re-downloading or re-installing packages.
* Modifying an instruction or changing the order invalidates the cache for that step and every step below it.

---

## Tagging an Image

Instead of referencing random hex `image_id` hashes, tag your image with a human-readable name using `-t`:

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

## Creating an Image from a Running Container

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
