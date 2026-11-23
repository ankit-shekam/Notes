[back to Docker index](./dockercontent.md)
---

# Part 1: Docker Basics, Command Flow & Container Internals

## Table of Contents
1. [Docker Basics](#1-docker-basics)
2. [What Happens When You Run a Docker Command](#2-what-happens-when-you-run-a-docker-command)
3. [Container Internals](#3-container-internals)

---

## 1. Docker Basics

### Why Use Docker?

One of the primary reasons to use Docker is to make installing and running software packages effortless, without worrying about setup or dependency conflicts.

```mermaid
flowchart LR
    Q["Why use Docker?"] --> A["Docker makes it really easy to install and run software without worrying about setup or dependencies"]
```

---

### What is Docker?

Docker is a platform and ecosystem built around **creating and running containers**.

```mermaid
flowchart TB
    subgraph Ecosystem["Docker Ecosystem"]
        direction TB
        CLI["Docker Client (CLI)"] ~~~ MAC["Docker Machine"] ~~~ HUB["Docker Hub"]
        SRV["Docker Server (Daemon)"] ~~~ IMG["Docker Images"] ~~~ CMP["Docker Compose"]
    end

    Ecosystem --> DEF["Docker is a platform or ecosystem around creating and running containers"]
```

* **Docker Client (CLI):** The command-line interface tool you type `docker` commands into, which sends requests to the Docker Server.
* **Docker Server (Daemon):** The background service (`dockerd`) responsible for building images, running containers, and managing local Docker objects.
* **Docker Machine:** A tool that provisions and manages virtual hosts with the Docker Engine installed (historically used to run Docker on non-Linux hosts or remote cloud VMs).
* **Docker Images:** Read-only blueprint files containing the filesystem snapshot, dependencies, and startup command needed to create a container.
* **Docker Hub:** The default public cloud registry for finding, pulling, and sharing container images.
* **Docker Compose:** A tool for defining and running multi-container applications declaratively using a single YAML configuration file.

---

### What is a Container?

A **Docker container** is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries, and settings. It packages an application and its dependencies together, making it easier to create, deploy, and run applications consistently anywhere.

```mermaid
flowchart TB
    subgraph CA["Container A"]
        direction TB
        A1["Python App"] ~~~ A2["Python runtime, libraries, dependencies"]
    end

    subgraph CB["Container B"]
        direction TB
        B1["Java App"] ~~~ B2["Java runtime, libraries, dependencies"]
    end

    subgraph CC["Container C"]
        direction TB
        C1["C++ App"] ~~~ C2["C++ runtime, libraries, dependencies"]
    end

    A2 --> DOC["Docker Engine"]
    B2 --> DOC
    C2 --> DOC
    DOC --> OS["Host Operating System"]
    OS --> HW["Infrastructure (Hardware)"]
```

---

### What is an Image?

* An **Image** is a single file (template/snapshot) containing a **File System snapshot** plus a **Startup Command** — everything required to run an application.
* A **Container** is a running instance of an Image. *(How an image becomes a container is covered in [Section 3](#what-happens-behind-the-scenes-when-creating-a-container-from-an-image).)*

---

### What is Docker Desktop?

Docker relies on Linux kernel features, so it runs natively only on **Linux**. **Docker Desktop** makes it work on Windows and macOS by:
1. Creating a lightweight **Linux VM** on your host OS. *(Why a VM is needed is explained in [How Docker Runs on macOS and Windows](#how-docker-runs-on-macos-and-windows).)*
2. Forwarding `docker` CLI commands from the host OS to the Docker Daemon running inside that VM — so it *appears* Docker runs natively.

Docker Desktop also bundles the **Docker Client (CLI)** and **Docker Server (Daemon)** in a single application — we interact with the CLI, and the CLI communicates with the Daemon:

```mermaid
flowchart TB
    subgraph DD["Docker Desktop (Windows / macOS)"]
        CLI["Docker Client (Docker CLI) — Tool we issue commands to"]
        SRV["Docker Server (Docker Daemon) — Responsible for creating images, running containers, etc."]
        CLI -->|Sends API requests| SRV
    end
```

---

## 2. What Happens When You Run a Docker Command

When you run your first command in the Docker CLI:

```bash
docker run hello-world
```

The flow behind the scenes is:

1. **Docker CLI (Client)** sends the request to the **Docker Daemon (Server)**.
2. **Docker Daemon** checks whether the **Local Image Cache** already contains the `hello-world` image.
3. If not found locally, the Daemon pulls the `hello-world` image from **Docker Hub** and stores it in the Local Image Cache.
4. The Daemon creates a new **Container** from the cached image and executes its startup command.

```mermaid
flowchart TB
    CMD["docker run hello-world"] --> CLI

    subgraph PC["Your Computer"]
        CLI["Docker Client (CLI)"] --> SRV["Docker Server (Daemon)"]
        SRV -->|1. Check local cache| CACHE
        subgraph CACHE["Local Image Cache"]
            HW_LOCAL["hello-world"]
        end
    end

    subgraph HUB["Docker Hub (Remote Registry)"]
        direction TB
        HW_HUB["hello-world (Single file with everything needed to run the program)"] ~~~ REDIS["redis"] ~~~ BUSYBOX["busybox"] ~~~ OTHER["Other Images..."]
    end

    SRV -->|2. Cache miss: pull image| HW_HUB
    HW_HUB -->|3. Download and cache locally| HW_LOCAL
```

---

## 3. Container Internals

### How Your OS Works

* The **OS Kernel** is a core software process that governs access between all running processes on your computer and the physical hardware.
* **System Calls** are the kernel's exposed endpoints that running applications invoke whenever they need to interact with hardware (CPU, memory, disk, network).

```mermaid
flowchart TB
    subgraph Procs["Processes Running on Your Computer"]
        direction LR
        P1["Chrome"]
        P2["Terminal"]
        P3["Spotify"]
        P4["NodeJS"]
    end

    SC["System Calls — Running program requests kernel to interact with hardware"]
    K["OS Kernel"]

    subgraph HW["Hardware"]
        direction LR
        CPU["CPU"]
        MEM["Memory (RAM)"]
        HD["Hard Disk"]
    end

    Procs --> SC --> K
    K --> CPU
    K --> MEM
    K --> HD
```

---

### Why Containers Are Better for Development

Suppose a hypothetical situation where **Chrome (App A)** needs **Python v2** and **NodeJS (App B)** needs **Python v3**, with the limitation that a standard OS filesystem normally expects one system installation at a time.

Linux solves this using two kernel primitives:

1. **Namespacing:** Isolates resources per process (or group of processes) so each process sees its own dedicated slice of the system (e.g., Chrome's process sees a hard disk segment containing Python v2, while NodeJS sees a segment containing Python v3).
2. **Control Groups (cgroups):** Limits the *amount* of hardware resources (memory, CPU, disk I/O, network bandwidth) a process can consume.

```mermaid
flowchart TB
    subgraph NS_GROUP["1. Namespacing — Isolating resources per process"]
        direction TB
        N1["Processes (PID)"] ~~~ N4["Users"]
        N2["Hard Drive (Mount)"] ~~~ N5["Hostnames (UTS)"]
        N3["Network"] ~~~ N6["Inter-Process Communication (IPC)"]
    end

    subgraph CG_GROUP["2. Control Groups (cgroups) — Limiting resource usage per process"]
        direction TB
        C1["Memory"] ~~~ C3["HD I/O"]
        C2["CPU Usage"] ~~~ C4["Network Bandwidth"]
    end

    NS_GROUP ~~~ CG_GROUP
```

#### How Docker Runs on macOS and Windows

Because **Namespaces** and **cgroups** are **Linux-specific** kernel features, the Linux VM that [Docker Desktop](#what-is-docker-desktop) installs is what provides a real Linux Kernel for your containers:

```mermaid
flowchart TB
    subgraph VM["Linux Virtual Machine"]
        direction TB
        subgraph Conts["Isolated Containers"]
            direction LR
            R1["Running Process A"]
            R2["Running Process B"]
            R3["Running Process C"]
        end
        LK["Linux Kernel (Namespaces + cgroups)"]
        R1 --> LK
        R2 --> LK
        R3 --> LK
    end

    VM --> HOST["macOS / Windows Host OS"]
    HOST --> HW["Your Computer's Hardware"]
```

---

### Container Analogy & Mental Model

A **Container** is **not** a physical box—it is simply a running process (or group of processes) paired with an isolated, resource-limited slice of the host's hardware managed by the kernel.

#### Hard Disk Segmentation Analogy (Python v2 vs. Python v3)

```mermaid
flowchart TB
    subgraph C1["Container (Isolated Scope)"]
        A["Chrome (Needs Python v2)"]
    end
    B["NodeJS (Needs Python v3)"]

    SC["System Call to read Hard Disk"]
    K["Kernel: Which process is making this system call?"]

    subgraph HD["Hard Disk"]
        direction LR
        SEG1["HD Segment for Chrome: Python v2"]
        SEG2["HD Segment for NodeJS: Python v3"]
    end

    A --> SC
    B --> SC
    SC --> K
    K -->|Chrome| SEG1
    K -->|NodeJS| SEG2
```

#### Simplified General Representation of a Container

```mermaid
flowchart TB
    subgraph Container["Container"]
        P["Running Process (e.g. Chrome)"]
        K["Linux Kernel (enforcing Namespaces & cgroups)"]
        subgraph Slice["Portion of Resources Made Available to Process"]
            direction LR
            HD["Hard Drive"]
            NET["Network"]
            RAM["RAM"]
            CPU["CPU"]
        end
        P --> K --> Slice
    end
```

---

### What Happens Behind the Scenes When Creating a Container from an Image?

Recall the two parts of an [Image](#what-is-an-image):
* **FS Snapshot** — the required directories, binaries, and tools (e.g., Chrome, Python).
* **Startup Command** — the default command executed when the container starts (e.g., `Run Chrome`).

When you run the image:
1. The **Kernel** carves out an isolated set of resources (Namespaces + cgroups) for the container.
2. The **FS Snapshot** is mounted/placed into that container's isolated hard drive segment.
3. The **Startup Command** executes, launching the target process inside the container.

```mermaid
flowchart TB
    subgraph IMG["Docker Image"]
        FS["FS Snapshot: Chrome, Python"]
        CMD["Startup Command: Run Chrome"]
    end

    subgraph CONT["Running Container"]
        K["Linux Kernel"]
        RES["Isolated Resources: RAM | CPU | Network | Disk"]
        HD["Isolated Hard Drive: Chrome, Python installed"]
        PROC["Running Process: Chrome"]

        K ==>|"1. Kernel isolates hardware slice"| RES
        RES -->|"Disk slice ready"| HD
        HD -->|"Binaries ready"| PROC
    end

    FS ==>|"2. FS Snapshot placed into isolated disk"| HD
    CMD ==>|"3. Startup Command launches process"| PROC
```

---

**Next:** [Part 2: Manipulating Containers with the Docker CLI →](./02-docker-cli-commands.md)
