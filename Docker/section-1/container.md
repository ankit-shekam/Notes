[back to previous file](../dockercontent.md)
---

# Container Internals

## How Your OS Works

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

## Why Containers Are Better for Development

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

### How Docker Runs on macOS and Windows

Because **Namespaces** and **cgroups** are **Linux-specific** kernel features, installing Docker Desktop on macOS or Windows installs a lightweight **Linux Virtual Machine** on top of your host OS to provide a real Linux Kernel for your containers:

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

## Container Analogy & Mental Model

A **Container** is **not** a physical box—it is simply a running process (or group of processes) paired with an isolated, resource-limited slice of the host's hardware managed by the kernel.

### Hard Disk Segmentation Analogy (Python v2 vs. Python v3)

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

### Simplified General Representation of a Container

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

## What Happens Behind the Scenes When Creating a Container from an Image?

An **Image** contains two core things:
1. **File System (FS) Snapshot:** A packaged set of required directories, binaries, and tools (e.g., Chrome, Python).
2. **Startup Command:** The default command to execute when the container starts (e.g., `> Run Chrome`).

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
