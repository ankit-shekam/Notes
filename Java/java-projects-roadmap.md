[back to previous page](../README.md)
---

# Java & JVM Mastery Roadmap (3–4 Months): LLD, Concurrency, Project Loom, JVM Tuning & Agentic Systems

> **Goal:** Master the exact Java stack required of a **Senior Software Engineer (SDE-2 / SDE-3 / Senior Backend & Platform Engineer)** at top tech and deep-tech product companies (**Uber, Netflix, LinkedIn, Amazon, Apple, Stripe, Confluent, Elastic, Goldman Sachs, Atlassian**) in **16 focused weeks (3–4 months)**.
>
> Instead of spending 18 months writing compilers or hacking OpenJDK C++ code, this plan is ruthlessly calibrated for **high-ROI Senior Engineer mastery**:
> 1. **Production-Grade OOP, LLD & Design Patterns** in Modern Java (JDK 21+)
> 2. **Multithreading, `java.util.concurrent` & Project Loom** (Virtual Threads, Structured Concurrency, `ScopedValue`)
> 3. **JVM Internals, Garbage Collection (G1GC & Generational ZGC) & Practical Fine-Tuning** (JFR, `async-profiler`, GC logs, Heap/Thread dumps)
> 4. **Resume-Ready Systems & Agentic Java Projects** powered by **live, high-volume real-world APIs**—combining deterministic Java concurrency with modern **Agentic AI / MCP workflows** where they solve real engineering problems.

---

## Table of Contents

1. [How to Execute This 16-Week Plan](#1-how-to-execute-this-16-week-plan)
2. [What Makes a Java Project "Senior-Resume-Ready"](#2-what-makes-a-java-project-senior-resume-ready)
3. [Month 1 (Weeks 1–4): Advanced OOP, Clean LLD, Design Patterns & Modern Java 21+](#month-1-weeks-14-advanced-oop-clean-lld-design-patterns--modern-java-21)
4. [Month 2 (Weeks 5–8): Multithreading, Core Concurrency & Project Loom (Virtual Threads)](#month-2-weeks-58-multithreading-core-concurrency--project-loom-virtual-threads)
5. [Month 3 (Weeks 9–12): High-Throughput Event Streaming, Lock-Free Patterns & Concurrent Caching](#month-3-weeks-912-high-throughput-event-streaming-lock-free-patterns--concurrent-caching)
6. [Month 4 (Weeks 13–16): JVM Internals, GC Fine-Tuning & Agentic Production Forensics](#month-4-weeks-1316-jvm-internals-gc-fine-tuning--agentic-production-forensics)
7. [Design Pattern & LLD Concept → Project Map](#design-pattern--lld-concept--project-map)
8. [Senior Engineer's JVM Tuning & Production Debugging Playbook](#senior-engineers-jvm-tuning--production-debugging-playbook)
9. [Real-World Live APIs & Data Firehoses Used in This Plan](#real-world-live-apis--data-firehoses-used-in-this-plan)
10. [Curated Resources (Fast-Track for 3–4 Months)](#curated-resources-fast-track-for-34-months)
11. [Senior Engineer Interview & Resume Alignment](#senior-engineer-interview--resume-alignment)
12. [16-Week Execution Schedule & Checklist](#16-week-execution-schedule--checklist)

---

## 1. How to Execute This 16-Week Plan

- **Time commitment:** **14–16 weeks (~3.5–4 months)** at **10–15 hours/week** (~30% reading/studying internals, ~70% coding, profiling, and benchmarking).
- **Use JDK 21 LTS (or JDK 24/25):** You will use **Records, Sealed Interfaces, Pattern Matching for `switch`, Virtual Threads, Structured Concurrency (`StructuredTaskScope`), `ScopedValue`**, and the modern **`HttpClient`** throughout.
- **4 Flagship Resume Projects (⭐):** Each month produces **one flagship resume project** (plus an optional weekend drill project). Furthermore, the 4 flagship projects **connect together** into a unified platform if you want a single standout monorepo on your GitHub and resume!
- **Real APIs only—no fake `Thread.sleep()` mocks:** Every project ingests live data from free, public, high-volume endpoints (**GitHub API, Hacker News Firebase API, OpenAlex, SEC EDGAR, OSV.dev, Wikimedia EventStreams, Bluesky Jetstream, Binance WebSockets**).
- **Where Agentic AI fits in Senior Java Engineering:** Enterprises run their core backends on the JVM and are actively building **agentic gateways, MCP (Model Context Protocol) tool servers, parallel research/triage orchestrators, and automated SRE/JVM diagnostics agents** directly in Java—because Project Loom's Virtual Threads + `StructuredTaskScope` are uniquely suited for fanning out dozens of parallel tool and LLM calls with strict timeouts, type safety, and backpressure.

---

## 2. What Makes a Java Project "Senior-Resume-Ready"

Apply this engineering checklist to your 4 flagship projects so interviewers immediately recognize senior-level craftsmanship:

| Criterion | Senior Engineer Standard |
|---|---|
| **Clean LLD & Extensibility** | Explicit domain interfaces, **SOLID** boundaries, **Hexagonal (Ports & Adapters)** package layout, immutable DTOs (`record`), exhaustive state modeling (`sealed interface`), and GoF design patterns used where they genuinely eliminate coupling. |
| **Thread-Safety & Concurrency Rigor** | Explicit thread-safety annotations/docs, zero race conditions, proper lock scope minimization, bounded queues (never unbounded queues in production!), and clean graceful shutdown (`SIGTERM` draining). |
| **Measured with JMH & HdrHistogram** | Hot paths benchmarked with **JMH** (reporting throughput + allocation rate via `-prof gc`) and end-to-end p50/p95/p99 latency tracked with **HdrHistogram** / Micrometer. |
| **Profiled with JFR & `async-profiler`** | Each README includes a real **CPU or Allocation Flamegraph** (`async-profiler`) or **JDK Flight Recorder (JFR)** screenshot showing a bottleneck you found and fixed. |
| **JVM & GC Tuned** | Tested under both **G1GC** and **Generational ZGC** (`-XX:+UseZGC -XX:+ZGenerational`), with Unified GC logs (`-Xlog:gc*`) verifying low pause times and zero memory leaks. |
| **Production Hygiene** | Gradle or Maven build, JUnit 5 + Testcontainers/WireMock tests, structured JSON logs (SLF4J/Logback + MDC/`ScopedValue`), Dockerized, and a crisp architecture diagram in the README. |

---

## Month 1 (Weeks 1–4): Advanced OOP, Clean LLD, Design Patterns & Modern Java 21+

> **Focus:** Elevate your code from "working classes" to **extensible, interview-proof Low-Level Design (LLD)**. You will master how modern Java 21+ features (`record`, `sealed interface`, pattern matching, generics, functional interfaces) combine with classic **GoF & Enterprise Resilience Design Patterns**—directly putting your [LLD notes](../LLD/LLD.md) into production code.

### Concepts to Master (Weeks 1–2)
1. **Modern OOP & Type System Depth:**
   - Composition over inheritance, **SOLID** principles in practice, Law of Demeter, Hexagonal / Ports-and-Adapters architecture.
   - **Data-Oriented Programming in Java 21+:** `record` (compact constructors, defensive copies), `sealed interface` + exhaustive `switch` pattern matching & record deconstruction (replacing fragile `instanceof` chains and visitor boilerplate).
   - **Generics & Variance:** Type erasure, bridge methods, bounded wildcards (`? extends T` vs `? super T` — **PECS: Producer Extends, Consumer Super**), self-bounded generics (`Builder<T extends Builder<T>>`).
   - **Reflection, Annotations & `MethodHandle`:** How frameworks inspect annotations at runtime and why `MethodHandle` / `LambdaMetafactory` is faster and safer than raw `Method.invoke()`.
2. **High-Impact Design Patterns for Senior Engineers:**
   - **Creational:** Fluent **Builder** (for complex immutable configs), **Factory Method & Abstract Factory** (provider families), **Object Pool** (reusing expensive buffers/connections).
   - **Structural:** **Adapter** (unifying third-party APIs), **Decorator** (wrapping caching, logging, metrics, retries around a core client without modifying it), **Dynamic Proxy** (`java.lang.reflect.Proxy` for declarative cross-cutting concerns), **Facade**, **Flyweight**.
   - **Behavioral:** **Strategy** (swappable rate-limiting/routing algorithms), **Chain of Responsibility** (request interceptor/middleware pipelines), **State Pattern** (explicit finite-state machines like Circuit Breakers), **Observer / Event Bus** (decoupled domain events), **Command** (encapsulated tool executions with audit/retry).

---

### ⭐ Flagship Project 1.1: `nexus-gateway` — Extensible API & Agentic LLM Resilience Gateway (LLD & Design Patterns Showcase)
- **Real-world live APIs:** Sits in front of live upstream APIs with real schemas and rate limits—**[GitHub REST API](https://api.github.com)**, **[OpenAlex Scholarly API](https://api.openalex.org/works)**, **[SEC EDGAR Financial API](https://data.sec.gov/api/xbrl/companyfacts/CIK0000320193.json)** (strict 10 req/sec limit), and **LLM Providers** (OpenAI / Gemini / Anthropic / local Ollama).
- **What you build:** A pure-Java, zero-Spring-magic resilience gateway & routing library (+ standalone HTTP server) that protects upstream data/LLM APIs and routes requests intelligently.
- **How it forces mastery of OOP, LLD & Design Patterns:**
  1. **Chain of Responsibility:** Configurable request pipeline (`AuthFilter` $\rightarrow$ `QuotaFilter` $\rightarrow$ `CacheFilter` $\rightarrow$ `CircuitBreakerFilter` $\rightarrow$ `RetryFilter` $\rightarrow$ `UpstreamDispatchFilter`).
  2. **Decorator Pattern:** Wrap any `UpstreamClient<Req, Resp>` with composable decorators (`CachingClientDecorator`, `MetricsClientDecorator`, `RetryClientDecorator`, `RateLimitedClientDecorator`).
  3. **State Pattern (with `sealed interface`):** Implement a thread-safe **Circuit Breaker** from scratch with explicit immutable/atomic state transitions (`ClosedState` $\rightarrow$ `OpenState` $\rightarrow$ `HalfOpenState`) using Java 21 sealed interfaces and pattern-matching `switch`.
  4. **Strategy Pattern:** Pluggable **Rate Limiters** (*Token Bucket*, *Sliding Window Log*, *Fixed Window Counter*) and **Agentic Model Routing Strategies** (*Cost-Optimized Router* that classifies prompt complexity and routes simple extraction prompts to fast/cheap models and hard reasoning to frontier models; *Latency-Aware EWMA Router*; *Fallback Cascade*).
  5. **Adapter & Abstract Factory Patterns:** Normalize heterogeneous upstream responses (OpenAI vs Gemini vs Anthropic JSON/SSE streams, or GitHub vs OpenAlex pagination) into a unified domain contract.
  6. **Observer Pattern:** Publish typed domain events (`CircuitTrippedEvent`, `RateLimitExceededEvent`, `TokenBudgetWarningEvent`) to decoupled subscribers.
- **Senior Check & Deliverable:**
  - Draw the **UML Class & Sequence Diagrams** in your README (linking to your [LLD notes](../LLD/LLD.md)).
  - Prove via integration tests that 200 concurrent callers hammering the SEC EDGAR route are smoothly shaped to $\le 10$ req/sec with zero upstream HTTP 429s, and automatic failover triggers within 50 ms when an LLM or API endpoint returns 503.

### Weekend Drill Project 1.2: `triage-rules` — Extensible Event Rule Engine & State-Machine Workflow (LLD Machine-Coding Prep)
- **Real-world live APIs:** Stream live public events from **[GitHub Events API](https://api.github.com/events)** and vulnerability advisories from **[OSV.dev API](https://api.osv.dev/v1/)**.
- **What you build (in a crisp 4–6 hour machine-coding style):**
  - An in-memory rule evaluation and workflow engine using the **Specification / Composite Pattern** (`Rule.and(otherRule).or(not(thirdRule))`), **Command Pattern** (idempotent actions with undo/compensation logs), and a **Plugin Registry** so new event types and actions can be registered with zero `if-else` modifications (**Open-Closed Principle**).
- **Why this matters:** Directly prepares you to ace 60–90 minute **Senior LLD Machine Coding interviews** (Uber, Flipkart, Atlassian, Stripe, Amazon).

---

## Month 2 (Weeks 5–8): Multithreading, Core Concurrency & Project Loom (Virtual Threads)

> **Focus:** Deeply understand the **Java Memory Model (JMM)**, `java.util.concurrent` internals, thread pools, locks, and **Java 21+ Project Loom (Virtual Threads, Structured Concurrency, `ScopedValue`)**—and apply them to build a high-concurrency **Agentic Research & Code-Intelligence Orchestrator**.

### Concepts to Master (Weeks 5–6)
1. **The Java Memory Model (JMM) & Visibility:**
   - CPU caches vs main memory, instruction reordering, **Happens-Before guarantees** (program order, monitor lock release $\rightarrow$ acquire, `volatile` write $\rightarrow$ read, thread `start()` / `join()`, `final` field safe publication).
   - Why `volatile` guarantees visibility & ordering for single reads/writes but **not** compound atomicity (`count++`), and how **CAS (Compare-And-Swap)** (`AtomicReference`, `AtomicInteger`, `VarHandle`) and **`LongAdder` (cell striping)** work under high contention.
2. **Locks, Synchronizers & Concurrent Collections Internals:**
   - Intrinsic locks (`synchronized`, object monitor Mark Word lock bits, `wait`/`notifyAll`) vs explicit locks (`ReentrantLock` fair vs non-fair, `Condition` queues, `ReentrantReadWriteLock`).
   - **`StampedLock`:** How optimistic reads (`tryOptimisticRead()` + `validate()`) avoid CAS cache-line contention on read-heavy caches.
   - **How `ConcurrentHashMap` actually works in Java 8–21+:** Node array + CAS on empty buckets + fine-grained `synchronized` on the first node of a bucket + Red-Black `TreeBin` when bucket length $>8$ + cooperative multi-threaded resizing (`helpTransfer`) + why `computeIfAbsent` can deadlock if you mutate the same map recursively!
   - **Coordination Primitives:** `CountDownLatch`, `CyclicBarrier`, `Phaser`, `Semaphore` (concurrency bulkheads), `BlockingQueue` (`ArrayBlockingQueue` single-lock vs `LinkedBlockingQueue` two-lock queue, `PriorityBlockingQueue`, `DelayQueue`).
3. **Thread Pools, `CompletableFuture` & Project Loom (Java 21+):**
   - `ThreadPoolExecutor` internals (corePoolSize, maxPoolSize, workQueue, `RejectedExecutionHandler` policies, why `Executors.newFixedThreadPool` uses an unbounded queue that can OOM your heap!), `ForkJoinPool` work-stealing deque.
   - **Project Loom Virtual Threads (`java.lang.VirtualThread`):**
     - How the JVM mounts/unmounts heap-allocated **Continuations** onto a small `ForkJoinPool` of OS **Carrier Threads** whenever blocking I/O or `java.util.concurrent` lock parking occurs.
     - **The #1 Production Loom Trap — Carrier Thread Pinning:** Why blocking I/O inside a `synchronized` block (in JDK 21–23; solved by JEP 491 in JDK 24+) or JNI call **pins** the OS carrier thread, starving the pool—and how to detect it with `-Djdk.tracePinnedThreads=full` and JFR `jdk.VirtualThreadPinned` events.
     - **Why you should never pool Virtual Threads** (use `Semaphore` for rate/concurrency limiting instead!) and why `ThreadLocal` object caches across 100,000 virtual threads blow up the heap (use **`ScopedValue`** for context propagation).
     - **Structured Concurrency (`StructuredTaskScope`):** `ShutdownOnFailure` (all-or-nothing parallel fan-out with automatic child cancellation) and `ShutdownOnSuccess` (hedged requests / fastest responder wins).

---

### ⭐ Flagship Project 2.1: `loom-orchestrator` — High-Concurrency Agentic Research & Code-Intelligence Engine (Project Loom + Structured Concurrency + MCP)
- **Real-world live APIs:**
  - **[GitHub REST & GraphQL API](https://api.github.com)** (repo dependency trees, open issues, PR diffs, commit history)
  - **[OSV.dev Vulnerability API](https://api.osv.dev/v1/querybatch)** & **[Maven Central / PyPI API](https://search.maven.org/)** (live CVE security advisories for dependency graphs)
  - **[Hacker News Firebase API](https://hacker-news.firebaseio.com/v0/)** & **[OpenAlex API](https://api.openalex.org/works)** (technical community discussions & academic papers)
- **What you build:** A high-concurrency engine + **Model Context Protocol (MCP)** server/agent in pure Java 21+ that ingests a GitHub repository or technical query, fans out hundreds/thousands of parallel live API calls using **Virtual Threads** and **`StructuredTaskScope`**, and executes an autonomous **Agentic Investigation Loop** (e.g., *"Audit this repository's transitive dependencies for live CVEs, inspect affected source files in the repo via GitHub API, search recent issues/PRs, and generate a verified remediation diff report"*).
- **Concurrency & Agentic Engineering Highlights:**
  1. **Structured Concurrency Fan-Out (`StructuredTaskScope.ShutdownOnFailure`):** Simultaneously crawl 500+ transitive package metadata records, OSV vulnerability advisories, and HN/OpenAlex references with strict deadline propagation (if one mandatory branch fails or times out, in-flight sibling virtual threads are cleanly interrupted).
  2. **Hedged Calls (`StructuredTaskScope.ShutdownOnSuccess`):** Race multiple mirror/search endpoints or fallback LLM providers and automatically cancel slower stragglers.
  3. **`ScopedValue` Request & Trace Context:** Propagate immutable `TraceContext(requestId, tenantId, tokenBudget, deadline)` across thousands of child virtual threads without `ThreadLocal` memory leaks.
  4. **Per-Host Concurrency Bulkheads (`Semaphore` + Token Bucket):** Limit concurrent outbound requests per upstream domain (e.g., max 20 concurrent calls to GitHub, max 8 req/s to SEC EDGAR) while allowing 10,000 virtual threads to stay suspended cheaply in RAM.
  5. **Read-Heavy Graph Cache with `StampedLock`:** Cache resolved package/citation subgraphs in memory using optimistic reads (`tryOptimisticRead()`), falling back to read/write locks only on cache contention.
  6. **Explicit Java Agent State Machine (No Black-Box Frameworks):** Model the agent loop (`PlanStep` $\rightarrow$ `ParallelToolExecutionStep` $\rightarrow$ `VerificationStep` $\rightarrow$ `CompletedStep`) using **Sealed Interfaces + State Pattern + Command Pattern** and expose your live API tools via an **MCP-compliant JSON-RPC server** so Claude/Cursor or your own Java agent loop can invoke them.
- **Senior Check & Deliverable:**
  - Run a **3-Way Benchmark** (Fixed OS ThreadPool vs `CompletableFuture` vs Loom Virtual Threads) fetching 2,000 live Hacker News items / OSV dependency checks; deliberately introduce a `synchronized` pinning bug, capture the JFR `jdk.VirtualThreadPinned` event, fix it with `ReentrantLock`, and publish the throughput + memory comparison table in your README!

### Weekend Drill Project 2.2: `chrono-scheduler` — Concurrent Delayed Job Scheduler & Bounded Work Queue From Scratch
- **Real-world live APIs:** Schedule recurring polling jobs against **[Binance 24hr Ticker API](https://api.binance.com/api/v3/ticker/24hr)** and **[Wikimedia Pageviews API](https://wikimedia.org/api/rest_v1/)**.
- **What you build:**
  - Implement your own **Bounded Blocking Queue** using a circular array, `ReentrantLock`, and two `Condition` variables (`notFull`, `notEmpty`).
  - Build a thread-safe **Delayed & Recurring Task Scheduler** (using a custom min-heap + `Condition.awaitNanos` leader-follower pattern, plus dead-letter retry queues, task cancellation, and graceful `SIGTERM` draining via `CountDownLatch`).
- **Why this matters:** Implementing a blocking queue, thread pool, or delayed task scheduler with `ReentrantLock` + `Condition` is the **#1 most common Senior Java Concurrency coding interview question**.

---

## Month 3 (Weeks 9–12): High-Throughput Event Streaming, Lock-Free Patterns & Concurrent Caching

> **Focus:** Bridge **Concurrency + LLD** into high-throughput real-time data systems. Learn how senior engineers design low-contention, high-throughput pipelines (Single-Writer Principle, bounded ring buffers, backpressure, segmented caches, and non-blocking WebSocket/SSE streams) consuming **live global data firehoses**.

### Concepts to Master (Weeks 9–10)
1. **Mechanical Sympathy & Low-Contention Concurrency:**
   - Why locks degrade under high thread contention (context switches, kernel `futex`, CPU cache-line bouncing).
   - **False Sharing:** How two threads updating independent variables on the same 64-byte CPU cache line destroy performance, and how cache-line padding (`@Contended`) prevents it.
   - **The Single-Writer Principle & Ring Buffers (LMAX Disruptor concept):** Why partitioning a stream by key (`hash(key) % numPartitions`) so each partition is mutated by **a single dedicated thread** eliminates locks entirely on the state-update path!
   - **Backpressure Strategies:** Drop-oldest, drop-newest, blocking producer, credit-based flow control, and adaptive sampling when consumers fall behind a live firehose.
2. **High-Performance In-Memory Caching & Persistence Basics:**
   - Why naive `Collections.synchronizedMap(new LinkedHashMap<>(...))` bottlenecks multi-core servers.
   - Designing a high-concurrency **Segmented LRU / W-TinyLFU Cache** (inspired by **Caffeine / Guava Cache**): striped locks or `ConcurrentHashMap` + ring-buffer read/write access logs drained asynchronously + TTL wheel expiration.
   - **Append-Only Write-Ahead Log (WAL) & Group Commit:** Using `FileChannel` and `ByteBuffer` pooling (`Object Pool Pattern`) to persist state snapshots cleanly without blocking every single event on `fsync`.

---

### ⭐ Flagship Project 3.1: `pulse-stream` — Real-Time Firehose Stream Processor, Concurrent Cache & Agentic Anomaly Investigator
- **Real-world live firehoses (Zero Auth, 1,000+ live events/sec):**
  - **[Bluesky Jetstream Firehose](https://docs.bsky.app/blog/jetstream)** (`wss://jetstream2.us-east.bsky.network/subscribe` — 500–2,000+ live global social posts/likes/follows per second over WebSocket JSON)
  - **[Wikimedia EventStreams](https://stream.wikimedia.org/v2/stream/recentchange)** (`https://stream.wikimedia.org/v2/stream/recentchange` — continuous live global Wikipedia & Wikidata edits via SSE)
  - **[Binance Live Trade & Order-Book Stream](https://developers.binance.com/docs/binance-spot-api-docs/web-socket-streams)** (`wss://stream.binance.com:9443/ws/btcusdt@aggTrade` — real-time crypto trades/sec)
- **What you build:** A standalone high-throughput Java streaming engine that ingests live events from 1–2 of these firehoses simultaneously, performs stateful windowed aggregations in memory, serves sub-millisecond queries from a custom concurrent cache, and triggers an **Agentic Anomaly Investigator** when live spikes occur.
- **Core Systems & Concurrency Architecture:**
  1. **Resilient Async Firehose Connectors:** Non-blocking `java.net.http.HttpClient` WebSocket/SSE clients with automatic exponential-backoff reconnection, sequence cursor tracking, and zero-allocation streaming JSON parsing (Jackson Streaming `JsonParser` token reader).
  2. **Key-Partitioned Single-Writer Pipeline:** Events are routed via consistent key hashing (`symbol`, `wiki_domain`, or `hashtag`) across $N$ bounded **Ring-Buffer Partitions** so each partition's tumbling/sliding window state (1-min & 5-min VWAP, edit velocity, top-K heavy hitters via Count-Min Sketch, and EWMA z-score anomaly detector) is updated **lock-free by a single partition thread**!
  3. **Custom Segmented Concurrent Cache + WAL Snapshotter:** Store hot keys and window results in a custom **Segmented LRU + TTL Cache** (using `StampedLock` optimistic reads + async eviction ring buffers + `LongAdder` hit/miss counters) backed by periodic group-commit WAL checkpoints on disk.
  4. **Agentic Anomaly Investigator (Event-Driven via Observer Pattern):**
     - When a partition's z-score detector flags a live anomaly (e.g., sudden Wikipedia edit-war surge on an article, a trending Bluesky topic spike, or a 5$\sigma$ Binance volume burst), it emits an `AnomalyDetectedEvent` without blocking the hot stream thread.
     - A background **Virtual-Thread Agent** picks up the event, calls live external context tools (fetches the exact Wikipedia revision diff API / OpenAlex / Hacker News search), synthesizes a structured **Live Incident Brief** with verifiable links, and broadcasts it to connected dashboard clients over SSE.
- **Senior Check & Deliverable:**
  - Benchmark your streaming pipeline with **JMH** and **HdrHistogram** while ingesting the live firehose for 60+ minutes: report sustained events/sec, cache hit latency (p50/p99 in nanoseconds), and prove memory stays flat under artificial downstream backpressure.

---

## Month 4 (Weeks 13–16): JVM Internals, GC Fine-Tuning & Agentic Production Forensics

> **Focus:** Master **how the HotSpot JVM actually executes, allocates, and garbage-collects your code**, and how a Senior Engineer systematically diagnoses **high GC pauses, memory leaks, thread deadlocks, CPU spikes, carrier-thread pinning, and container OOMs** using **JFR, `async-profiler`, GC logs, Thread Dumps, Heap Dumps, and NMT**.

### Concepts to Master (Weeks 13–14)
1. **JVM Architecture, ClassLoading & JIT Compilation (Practical Senior Depth):**
   - **ClassLoader Subsystem:** Bootstrap $\rightarrow$ Platform $\rightarrow$ Application classloaders, delegation model, dynamic classloading, and why leaked classloaders cause `OutOfMemoryError: Metaspace`.
   - **Execution Engine & Tiered JIT (C1 & C2):** Interpreter $\rightarrow$ C1 (Tier 1–3 with profiling counters) $\rightarrow$ C2 (Tier 4 server compiler).
   - **What Senior Engineers Must Know About JIT Optimizations:**
     - **Method Inlining:** Why small, clean methods inline into hot loops (`-XX:MaxInlineSize`, `-XX:FreqInlineSize`).
     - **Monomorphic vs Megamorphic Dispatch:** Why an interface with 1–2 active implementations inlines for free, whereas 3+ implementations in the same hot loop hit a `vtable`/`itable` lookup penalty.
     - **Escape Analysis & Scalar Replacement:** How C2 detects that a temporary object (like an iterator or small record) never escapes a method (`NoEscape`) and replaces its heap allocation with CPU registers/stack variables—and how to verify allocation elimination with `JMH -prof gc`.
     - **Safepoints & Time-To-Safepoint (TTSP):** Why all application threads must reach a safepoint poll before a STW phase starts, and how a thread stuck in a huge counted loop or slow `mmap` disk fault delays the entire JVM.
2. **JVM Memory Layout & Object Sizing:**
   - **Heap Regions:** Young Generation (Eden + Survivor S0/S1) & Old/Tenured Generation; **TLABs (Thread-Local Allocation Buffers)** enabling lock-free bump-pointer allocation in Eden (~10 CPU cycles!).
   - **Non-Heap Memory (Why Container RSS > `-Xmx`):** Metaspace, CodeCache (JIT compiled native code), Thread Stacks (`-Xss1m` $\times$ OS thread count), DirectByteBuffers / Off-Heap memory, and GC internal structures (Card Tables & Remembered Sets take 5–15% of heap size!).
   - **Object Headers & CompressedOops:** 12-byte object header (64-bit Mark Word + 32-bit compressed Klass pointer; shrinking to 8 bytes with **Project Lilliput `-XX:+UseCompactObjectHeaders`**), 8-byte alignment padding, and why **CompressedOops turns off above 32 GB heap** (meaning a 33 GB heap holds *fewer* objects than a 31 GB heap!). Inspect layouts with **JOL (Java Object Layout)**.
3. **Garbage Collection Internals & Practical Fine-Tuning:**
   - **G1GC (Default Collector — Throughput + Bounded Pause Balance):**
     - Heap divided into 2,048 regions (1 MB–32 MB). Young GC, Concurrent Marking (triggered at **IHOP** — `-XX:InitiatingHeapOccupancyPercent`), and Mixed GC (collecting Young + most garbage-dense Old regions).
     - **G1 Failure Modes & Tuning:**
       - **Humongous Allocations:** Any object/array $\ge 50\%$ of a G1 region allocates directly in contiguous Old regions, causing fragmentation $\rightarrow$ fix by increasing `-XX:G1HeapRegionSize=16m/32m` or streaming data instead of allocating massive `byte[]`.
       - **Premature Promotion & Evacuation Failure (`to-space exhausted`):** Objects surviving Young GC overflow Survivor/Old regions, triggering a slow Full GC $\rightarrow$ fix by tuning heap headroom, `-XX:MaxGCPauseMillis=50..200`, or reducing medium-lived cache churn.
   - **Generational ZGC (`-XX:+UseZGC -XX:+ZGenerational` — Ultra-Low Tail Latency):**
     - Uses **Colored Pointers & Load Barriers** to relocate objects concurrently while application threads run—keeping **STW pauses $<0.5\text{ ms}$** regardless of heap size!
     - **ZGC Failure Mode & Tuning:** **Allocation Stalls** (when application allocation rate in MB/s exceeds concurrent GC background reclamation speed, application threads block waiting for memory) $\rightarrow$ fix by reducing hot-path allocation rate, giving more heap headroom (`-Xmx`), or increasing `-XX:ConcGCThreads`.
   - **When to Choose Which Collector (Senior Interview Favorite):**
     - **Parallel GC (`-XX:+UseParallelGC`):** Offline batch jobs where only total throughput matters.
     - **G1GC (`-XX:+UseG1GC`):** General microservices & streaming workers where 20–150 ms p99 pauses are acceptable and low CPU/memory footprint is preferred.
     - **Generational ZGC (`-XX:+UseZGC`):** Latency-critical APIs, gateways, trading/caching nodes where p99.9/p99.99 latency must stay under a few milliseconds.
4. **The Senior Engineer's Diagnostic Toolkit:**
   - **Unified GC Logs:** `-Xlog:gc*,gc+ergo*,safepoint:file=gc.log:time,uptime,level,tags`
   - **JDK Flight Recorder (JFR) & JDK Mission Control (JMC):** `jcmd <pid> JFR.start duration=60s filename=profile.jfr` (<1% overhead in production!).
   - **`async-profiler`:** Generating CPU (`-e cpu`), allocation (`-e alloc`), and lock-contention (`-e lock`) flamegraphs without Safepoint bias.
   - **Thread Dumps & Heap Dumps:** `jcmd <pid> Thread.print` (deadlocks, blocked/parked threads), `jcmd <pid> GC.heap_dump` + **Eclipse MAT** (dominator tree, retained size vs shallow size), and **Native Memory Tracking (`-XX:NativeMemoryTracking=detail` + `jcmd <pid> VM.native_memory detail.diff`)**.

---

### ⭐ Flagship Project 4.1: `jvm-copilot` — The JVM Pathology Benchmark Lab & Agentic SRE Diagnostics Co-Pilot
- **Real-world data workload:** Drives your Month 2 (`loom-orchestrator`) and Month 3 (`pulse-stream`) engines with live **[Bluesky Jetstream](https://docs.bsky.app/blog/jetstream)**, **[Wikimedia EventStreams](https://stream.wikimedia.org/v2/stream/recentchange)**, and 15–30 MB nested financial JSON filings from **[SEC EDGAR CompanyFacts API](https://data.sec.gov/api/xbrl/companyfacts/CIK0000320193.json)**.
- **What you build (Two Connected Modules):**
  1. **Part A — The 5-Pathology Reproducer & G1GC vs Generational ZGC Benchmark Suite:**
     A toggleable chaos & performance harness where you deliberately trigger, measure, and fix **5 classic production JVM incidents** under live data load:
     - **Incident 1: G1 Humongous Allocation & Evacuation Spike:** Slurping 20 MB SEC EDGAR JSON responses into contiguous `byte[]` / DOM trees on a 4 MB G1 region size vs streaming Jackson token parsing + tuned `-XX:G1HeapRegionSize=16m`.
     - **Incident 2: Slow Heap Memory Leak (`OutOfMemoryError: Java heap space`):** A subtle listener/cache key leak (`equals`/`hashCode` bug or unremoved event subscriber) diagnosed via **Eclipse MAT Dominator Tree** and JFR `OldObjectSample` events.
     - **Incident 3: Project Loom Carrier-Thread Pinning & Lock Contention:** Wrapping blocking I/O and cache lookups in a coarse `synchronized` monitor under 5,000 concurrent virtual threads vs migrating to `ReentrantLock` / `StampedLock`.
     - **Incident 4: ZGC Allocation Stall Under Burst Traffic:** Flooding 2 GB/s of boxed wrapper objects (`Map<Long, Double>`) during a Bluesky firehose burst vs flattening with primitive arrays / **JOL-verified** compact records.
     - **Incident 5: Off-Heap / Native Memory Leak (Container `OOMKilled` with 50% Free Heap):** Leaking unclosed `GZIPInputStream` (`Inflater` C-heap buffers) or `DirectByteBuffer` slices, caught via **Native Memory Tracking (`jcmd VM.native_memory detail.diff`)**.
  2. **Part B — The Agentic JVM Forensics Co-Pilot (Pure Java + JFR Consumer API + MCP):**
     Build an automated diagnostic engine that uses JDK's built-in **`jdk.jfr.consumer.RecordingFile` API**, a **Unified GC Log Parser**, and a **`jcmd` Thread/NMT Parser** to expose deterministic diagnostic tools (**MCP Server / Java Tool Registry**):
     - `analyzeGcLog(path)` $\rightarrow$ returns allocation rate (MB/s), promotion rate, humongous count, pause p50/p99/max, and allocation stalls.
     - `inspectJfrRecording(path)` $\rightarrow$ extracts top hot CPU methods, top allocating stack traces by bytes, worst lock-contention monitors (`jdk.JavaMonitorEnter`), and `jdk.VirtualThreadPinned` stack traces.
     - `inspectThreadDump(path)` $\rightarrow$ detects deadlocks, lock cycles, and thread pool saturation.
     - `diffNativeMemory(baseline, current)` $\rightarrow$ highlights off-heap NMT growth categories.
     - **Agentic Workflow:** When a latency SLA alert fires, the Java Agent autonomously invokes these diagnostic tools, correlates the exact offending stack frames in your source code, and outputs an actionable **SRE Incident Postmortem + Exact JVM Tuning Flags & Code Fix**.
- **Senior Check & Deliverable:**
  - Publish a **G1GC vs Generational ZGC Benchmark Report** in your README comparing throughput, p50/p99/p99.9 latency, max GC pause, and container RSS across the 5 incidents (before and after tuning), complete with real `async-profiler` flamegraphs!

---

## Design Pattern & LLD Concept → Project Map

Use this table during **LLD and Behavioral interviews** to cite concrete production implementations from your own repositories (pairs directly with your [LLD notes](../LLD/LLD.md) and [HLD notes](../HLD/HLD.md)):

| OOP / LLD / Concurrency Pattern | Why & Where You Implemented It | Target Project |
|---|---|---|
| **Strategy Pattern** | Swappable rate-limiting algorithms (Token Bucket vs Sliding Window) & cost/latency LLM routing strategies | ⭐ 1.1 `nexus-gateway` |
| **State Pattern (with `sealed interface`)** | Thread-safe Circuit Breaker state machine (`Closed` $\rightarrow$ `Open` $\rightarrow$ `HalfOpen`) & Multi-step Agent execution FSM | ⭐ 1.1 `nexus-gateway`, ⭐ 2.1 `loom-orchestrator` |
| **Chain of Responsibility** | Pluggable request filter & middleware pipeline (Auth $\rightarrow$ RateLimit $\rightarrow$ Cache $\rightarrow$ CircuitBreaker $\rightarrow$ Retry) | ⭐ 1.1 `nexus-gateway` |
| **Decorator Pattern** | Composable caching, telemetry, retry, and quota wrappers around upstream HTTP/LLM clients | ⭐ 1.1 `nexus-gateway` |
| **Adapter & Abstract Factory** | Normalizing heterogeneous REST/SSE APIs (GitHub, OpenAlex, SEC EDGAR, OpenAI, Gemini) behind unified domain ports | ⭐ 1.1 `nexus-gateway` |
| **Builder Pattern (Immutable DTOs)** | Fluent builders for complex retry/circuit policies and multi-agent task specs | ⭐ 1.1 `nexus-gateway`, ⭐ 2.1 `loom-orchestrator` |
| **Specification / Composite Pattern** | Composable boolean rule trees (`AndRule`, `OrRule`, `NotRule`) for live event triage | 1.2 `triage-rules` |
| **Command Pattern** | Encapsulating agent tool invocations & workflow actions with execution audit logs, timeouts, and retries | 1.2 `triage-rules`, ⭐ 2.1 `loom-orchestrator` |
| **Observer / Pub-Sub Event Bus** | Decoupling live stream anomaly detection from background agentic root-cause investigation & telemetry alerts | ⭐ 1.1 `nexus-gateway`, ⭐ 3.1 `pulse-stream` |
| **Object Pool / Flyweight Pattern** | Reusing direct byte buffers and mutable streaming event holders to reduce GC allocation pressure | ⭐ 3.1 `pulse-stream`, ⭐ 4.1 `jvm-copilot` |
| **Structured Concurrency & Bulkhead** | `StructuredTaskScope` parallel API fan-out paired with per-domain `Semaphore` rate bulkheads & `ScopedValue` | ⭐ 2.1 `loom-orchestrator` |
| **Single-Writer Ring Buffer** | Key-partitioned lock-free stream window aggregation avoiding lock contention across worker threads | ⭐ 3.1 `pulse-stream` |
| **Optimistic Read Caching (`StampedLock`)** | High-throughput read-heavy metadata & sliding-window cache with zero CAS contention on cache hits | ⭐ 2.1 `loom-orchestrator`, ⭐ 3.1 `pulse-stream` |

---

## Senior Engineer's JVM Tuning & Production Debugging Playbook

Memorize and practice this table—it covers **90%+ of real-world Senior Java production incidents and JVM deep-dive interview questions**:

| Production Symptom | Exact Diagnostic Tool & Command | What's Happening Inside the JVM | Concrete Senior Fix (Code + Flags) |
|---|---|---|---|
| **Frequent 200ms–2s G1GC pauses (`to-space exhausted` / Evacuation Failure)** | `-Xlog:gc*,gc+humongous*=debug:file=gc.log` + JFR allocation profile | **Humongous Allocations** (arrays/buffers $\ge 50\%$ of `G1HeapRegionSize` bypass Eden and fragment Old Gen) or **Premature Promotion** filling Old Gen before concurrent marking finishes. | Stream large JSON/files instead of `byte[]` slurping; raise `-XX:G1HeapRegionSize=16m` (or `32m`); lower `-XX:InitiatingHeapOccupancyPercent=35` so marking starts earlier; or switch to **Generational ZGC**. |
| **Using Generational ZGC (`<1ms` GC pauses), yet API p99 latency spikes to `900ms`** | Check `gc.log` for `Allocation Stall` or JFR `ZAllocationStall` events; run `async-profiler -e alloc` | **ZGC Allocation Stall:** Application threads are allocating memory faster (e.g., 3+ GB/s during a traffic burst) than background ZGC worker threads can reclaim it. | Cut allocation rate in top JFR stack frames (avoid boxing, reuse buffers); increase heap headroom (`-Xmx`); raise `-XX:ConcGCThreads` (ensure container CPU limits aren't throttling GC threads!). |
| **Heap usage climbs steadily in a sawtooth pattern until `OutOfMemoryError: Java heap space`** | `-XX:+HeapDumpOnOutOfMemoryError` or `jcmd <pid> GC.heap_dump`; open in **Eclipse MAT** (Dominator Tree) or JFR `OldObjectSample` | **Heap Memory Leak:** Unbounded `ConcurrentHashMap`/cache without eviction, unremoved event listeners, mutable keys breaking `hashCode()`, or `ThreadLocal` variables never `.remove()`d on pooled threads. | Replace raw maps with bounded **Caffeine / segmented LRU+TTL cache**; call `ThreadLocal.remove()` in `finally` (or migrate to **`ScopedValue`**); use `WeakReference` where appropriate. |
| **Docker/K8s Pod `OOMKilled` (Exit Code 137) even though JVM Heap is steady at 4 GB / 6 GB** | `-XX:NativeMemoryTracking=detail` + `jcmd <pid> VM.native_memory detail.diff` | **Non-Heap / Off-Heap Growth:** Total RSS = Heap + Metaspace + CodeCache + Thread Stacks (`1MB * threads`) + DirectByteBuffers + G1 Remembered Sets (5–15% of heap) + unclosed `GZIPInputStream`/`Deflater` native C buffers. | Set `-Xmx` to **65–75% of container RAM** (`-XX:MaxRAMPercentage=70.0`); always close `Deflater`/`Inflater` streams in `try-with-resources`; cap `-XX:MaxDirectMemorySize`; use Virtual Threads instead of 2,000 OS threads. |
| **Project Loom Virtual Threads service freezes or drops to 50 RPS under concurrent load** | `-Djdk.tracePinnedThreads=full` or inspect JFR `jdk.VirtualThreadPinned` events | **Carrier Thread Pinning:** Virtual threads are blocking on network/DB I/O while inside a `synchronized` method/block (JDK 21–23), pinning all OS carrier threads (`ForkJoinPool` default size = CPU cores). | Replace `synchronized` around blocking calls with **`ReentrantLock`** (or upgrade to **JDK 24+ JEP 491**); never run long CPU-bound loops directly on virtual threads. |
| **High CPU usage (90%+), low throughput, or completely frozen endpoints** | `jcmd <pid> Thread.print` (take 3 dumps 5s apart) + `async-profiler -e cpu` & `-e lock` | Either (a) **Deadlock / Lock Contention** (`BLOCKED` threads waiting on the same monitor), (b) infinite loop / catastrophic regex backtracking, or (c) **GC Thrashing** (JVM spending 85% of CPU running back-to-back Full GCs). | Fix lock ordering or replace coarse lock with `ConcurrentHashMap` / `StampedLock` / single-writer partition; check `gc.log` to rule out GC thrashing first! |
| **Setting `-Xmx34g` made memory usage *worse* and throughput *slower* than `-Xmx31g`** | `java -XX:+PrintFlagsFinal -version \| grep CompressedOops` or inspect with **JOL** | **CompressedOops Threshold Crossed:** At $\ge 32\text{ GB}$ heap, 32-bit shifted object references turn off and pointers become 64-bit, bloating object sizes by 15–25% and increasing CPU cache misses. | Keep heap at **`-Xmx30g` or `-Xmx31g`** unless you go up to `42g+` to break even (or enable Project Lilliput Compact Object Headers in JDK 24/25+). |

---

## Real-World Live APIs & Data Firehoses Used in This Plan

All APIs below are **free, public, and carry real schemas, rate limits, and high-volume event streams**:

| Live API / Firehose | Endpoint | Protocol & Auth | Why We Use It in This Java Plan | Used In |
|---|---|---|---|---|
| **Bluesky Jetstream Firehose** | `wss://jetstream2.us-east.bsky.network/subscribe` | WebSocket (JSON), No Auth | 500–2,000+ live global events/sec; stresses single-writer ring buffers, concurrent caches & ZGC pacing | ⭐ 3.1, ⭐ 4.1 |
| **Wikimedia EventStreams** | `https://stream.wikimedia.org/v2/stream/recentchange` | SSE (JSON), No Auth (`User-Agent` required) | Continuous live global Wikipedia edits/sec; ideal for sliding-window anomaly detection & agentic diff triage | ⭐ 3.1, ⭐ 4.1 |
| **Binance Public Market Streams** | `wss://stream.binance.com:9443/ws/btcusdt@aggTrade` & `/api/v3/ticker/24hr` | WebSocket + REST, No Auth | Thousands of live trade ticks/sec; high-frequency stress test for lock-free partitioning & tail latency | 2.2, ⭐ 3.1 |
| **GitHub REST & GraphQL APIs** | `https://api.github.com` | REST / GraphQL, Free PAT (5k req/hr) | Real repo dependency graphs, issues, PR diffs, and live event streams for LLD rules & Loom agents | ⭐ 1.1, 1.2, ⭐ 2.1 |
| **OSV.dev Vulnerability API** | `https://api.osv.dev/v1/querybatch` | REST (JSON), No Auth | Live open-source CVE database; powers the Loom Structured-Concurrency dependency security scanner | 1.2, ⭐ 2.1 |
| **SEC EDGAR CompanyFacts API** | `https://data.sec.gov/api/xbrl/companyfacts/CIK0000320193.json` | REST (JSON), No Auth (Strict `User-Agent`, 10 req/s) | 15–30 MB deeply nested JSON filings; tests rate-limiter shaping (10 req/s) & triggers G1 Humongous GC spikes | ⭐ 1.1, ⭐ 4.1 |
| **Hacker News Firebase & Algolia** | `https://hacker-news.firebaseio.com/v0/` & `hn.algolia.com/api/v1/` | REST (JSON), No Auth | Deep comment/story trees requiring 1,000+ concurrent HTTP fetches; ultimate Project Loom fan-out benchmark | ⭐ 2.1 |
| **OpenAlex Scholarly Works API** | `https://api.openalex.org/works` | REST (JSON), No Auth (`mailto=` polite pool) | 250M+ research papers & citation graphs; used for resilient gateway routing & parallel research agents | ⭐ 1.1, ⭐ 2.1 |

---

## Curated Resources (Fast-Track for 3–4 Months)

Don't try to read ten 800-page books in 3 months. Focus on these highest-signal chapters and guides:

1. **OOP, LLD & Design Patterns (Month 1):**
   - *Effective Java* (3rd Ed., Joshua Bloch) — Items on Builders, Immutability, Composition over Inheritance, Generics (`PECS`), and Serialization/Enums.
   - *Data-Oriented Programming in Java* (Brian Goetz's articles on InfoQ / Inside.java covering Records, Sealed Types, and Pattern Matching).
   - Your existing [LLD Notes](../LLD/LLD.md) & [Refactoring.Guru Design Patterns in Java](https://refactoring.guru/design-patterns/java).
2. **Java Concurrency & Project Loom (Months 2–3):**
   - *Java Concurrency in Action* (Brian Goetz) — Read **Chapters 1–8, 10–11, 15–16** (Thread safety, sharing objects, JMM happens-before, thread pools, locks, atomic variables).
   - **OpenJDK Project Loom Guides:** **JEP 444 (Virtual Threads)**, **JEP 480 (Structured Concurrency)**, **JEP 487 (Scoped Values)**, and **JEP 491 (Synchronize Virtual Threads without Pinning)**.
   - Martin Thompson's *Mechanical Sympathy* blog / LMAX Disruptor whitepaper (for Month 3 single-writer ring buffers).
3. **JVM Internals & Fine-Tuning (Month 4):**
   - *Java Performance: The Definitive Guide* (2nd Ed., Scott Oaks) — Read **Chapters 2–8** (JFR/JMC, JIT Compiler essentials, GC fundamentals, G1GC, ZGC, Heap & Native Memory).
   - Aleksey Shipilëv's *[JVM Anatomy Quarks](https://shipilev.net/jvm/anatomy-quarks/)* (Read the posts on TLABs, CompressedOops, Escape Analysis, Safepoints, and Lock Elision).

---

## Senior Engineer Interview & Resume Alignment

### How This 4-Month Plan Covers Every Senior Interview Round

| Interview Round | What Senior Interviewers Test | Your Direct Preparation from This Plan |
|---|---|---|
| **1. LLD / Machine Coding (60–90 min)** | Designing extensible, thread-safe in-memory systems (Rate Limiter, Circuit Breaker, Rule Engine, Job Scheduler, In-Memory Cache) with clean SOLID boundaries and design patterns. | **Month 1 (`nexus-gateway`, `triage-rules`)** & **Month 2 (`chrono-scheduler`)** + **Month 3 (Segmented LRU/TTL Cache)**. |
| **2. Java Concurrency & Multithreading Deep Dive** | JMM happens-before, `volatile` vs `Atomic` vs `LongAdder`, `ConcurrentHashMap` internals, `ReentrantLock` + `Condition`, `StampedLock`, thread pool sizing, deadlocks, and **Project Loom Virtual Threads / Pinning / `StructuredTaskScope`**. | **Month 2 (`loom-orchestrator`, `chrono-scheduler`)** & **Month 3 (`pulse-stream` single-writer ring buffers)**. |
| **3. JVM Internals & Production Troubleshooting** | Heap vs Non-Heap memory (`OOMKilled` vs Heap OOM), CompressedOops 32GB boundary, JIT inlining & Escape Analysis, **G1GC vs Generational ZGC** trade-offs, Humongous allocations, ZGC allocation stalls, reading GC logs, JFR & flamegraphs. | **Month 4 (`jvm-copilot` 5-Pathology Lab & G1/ZGC benchmark matrix)** + the **Playbook Table** above. |
| **4. System Design (HLD) & AI-Systems Architecture** | High-throughput event streaming, API gateways, resilient LLM/Agent orchestration, caching, backpressure, observability. | Connects all 4 flagship projects directly with your [HLD Notes](../HLD/HLD.md). |

### Ready-to-Use Senior Resume Bullet Templates (Quantified After You Build Them)

- **Flagship 1 + 2 (`nexus-gateway` + `loom-orchestrator`):**
  > *"Architected a high-concurrency Java 21 API & Agentic LLM Resilience Gateway and MCP Code-Intelligence Orchestrator using **Project Loom Virtual Threads**, **`StructuredTaskScope`**, **`ScopedValue`**, and explicit GoF state machines (Circuit Breaker, Strategy Cost/Latency Router, Per-Host Semaphore Bulkheads); benchmarked 10x higher fan-out throughput and 65% lower memory footprint vs traditional OS thread pools across 2,000+ concurrent live GitHub/OSV/HN API calls after eliminating carrier-thread pinning via JFR."*
- **Flagship 3 + 4 (`pulse-stream` + `jvm-copilot`):**
  > *"Built a real-time event streaming & anomaly detection engine ingesting 1,500+ live events/sec from Bluesky Jetstream, Wikimedia EventStreams, and Binance WebSockets using lock-free key-partitioned single-writer ring buffers and a `StampedLock` segmented LRU cache; profiled with **JFR, `async-profiler`, and JOL** and tuned **Generational ZGC vs G1GC** (eliminating 20 MB SEC EDGAR humongous allocation stalls) to cut p99.9 latency from 140 ms to <2.5 ms, paired with an agentic JFR/GC-log forensics co-pilot."*

---

## 16-Week Execution Schedule & Checklist

| Timeframe | Focus Area | Projects to Ship | Key Milestone |
|---|---|---|---|
| **Weeks 1–2** | Advanced OOP, SOLID, Java 21+ (`record`, `sealed`, pattern matching, generics) & GoF Patterns | Start ⭐ **1.1 `nexus-gateway`** (core filters, Circuit Breaker State Machine, Strategy Rate Limiters) | Clean Hexagonal LLD + UML diagrams |
| **Weeks 3–4** | Decorator, Adapter, Observer, Command & Agentic Model Router | Finish ⭐ **1.1 `nexus-gateway`** (live SEC EDGAR / GitHub / LLM routing) + **1.2 `triage-rules`** | Zero 429s under 200 concurrent callers; Machine-Coding ready |
| **Weeks 5–6** | JMM (`volatile`, CAS, `LongAdder`), Locks (`ReentrantLock`, `Condition`, `StampedLock`), `ConcurrentHashMap` | Build **2.2 `chrono-scheduler`** (custom Bounded BlockingQueue + Delayed Task Scheduler) | Can code thread-safe queues & schedulers from scratch |
| **Weeks 7–8** | Project Loom (`VirtualThread`, Pinning, `StructuredTaskScope`, `ScopedValue`) & MCP Agent State Machine | Ship ⭐ **2.1 `loom-orchestrator`** (live GitHub + OSV + HN + OpenAlex fan-out & JFR pinning benchmark) | 3-way ThreadPool vs Async vs Loom benchmark published |
| **Weeks 9–10** | Low-Contention Concurrency, False Sharing, Single-Writer Ring Buffers, Backpressure & Segmented Caches | Build core of ⭐ **3.1 `pulse-stream`** (live Bluesky / Wikimedia / Binance WebSocket + SSE ingestion) | Lock-free partitioned windowing & `StampedLock` LRU cache |
| **Weeks 11–12** | Group-Commit WAL Checkpointing, JMH + HdrHistogram Benchmarking & Event-Driven Anomaly Agent | Finish ⭐ **3.1 `pulse-stream`** (wire live z-score anomalies to background Virtual-Thread Investigator Agent) | 60-min live firehose soak test + JMH/HdrHistogram numbers |
| **Weeks 13–14** | JVM Internals (ClassLoading, JIT Inlining/Escape Analysis, Heap/Off-Heap Layout, JOL, CompressedOops) & G1GC vs Generational ZGC | Build Part A of ⭐ **4.1 `jvm-copilot`** (reproduce & fix the 5 JVM production pathologies on live traffic) | Before/After G1GC vs ZGC latency & RSS table + flamegraphs |
| **Weeks 15–16** | JFR Consumer API, Unified GC Log & Thread/NMT Parsing, Agentic SRE Forensics Tooling & Portfolio Polish | Finish Part B of ⭐ **4.1 `jvm-copilot`** (Agentic JFR/GC-log/Thread-dump MCP co-pilot) + polish GitHub READMEs | 4 Flagship Resume Projects live with metrics & architecture docs |

### Progress Checklist

**Month 1: Advanced OOP, LLD & Design Patterns**
- [ ] ⭐ **1.1 `nexus-gateway`**: Multi-Provider API & Agentic LLM Resilience Gateway (Chain of Responsibility, Decorator, Sealed State Circuit Breaker, Strategy Rate Limiter & Model Router, Observer Bus) tested against live **SEC EDGAR, GitHub, OpenAlex & LLM APIs**
- [ ] **1.2 `triage-rules`**: Extensible Event Rule Engine & Workflow Processor (Specification/Composite, Command with Undo, Open-Closed Registry) on live **GitHub Events & OSV.dev**

**Month 2: Multithreading, Core Concurrency & Project Loom**
- [ ] **2.2 `chrono-scheduler`**: Custom Bounded Blocking Queue (`ReentrantLock` + `Condition`) & Delayed/Cron Job Scheduler polling live **Binance & Wikimedia APIs**
- [ ] ⭐ **2.1 `loom-orchestrator`**: Project Loom Virtual-Thread & `StructuredTaskScope` Agentic Research / CVE Code-Intelligence Engine (`ScopedValue`, `StampedLock` cache, `Semaphore` bulkheads, MCP tool server) + **ThreadPool vs CompletableFuture vs Loom JFR Pinning Benchmark**

**Month 3: High-Throughput Streaming & Low-Contention Systems**
- [ ] ⭐ **3.1 `pulse-stream`**: Real-Time **Bluesky Jetstream / Wikimedia / Binance** Stream Processor (Single-Writer Partitioned Ring Buffers, Tumbling/Sliding Windows, Segmented `StampedLock` LRU Cache, WAL Snapshotter, and Event-Driven Virtual-Thread Anomaly Investigator Agent)

**Month 4: JVM Internals, GC Fine-Tuning & Production Forensics**
- [ ] ⭐ **4.1A `jvm-copilot` Pathology Lab**: Reproduce, profile (`JFR`, `async-profiler`, `JOL`, `Eclipse MAT`, `NMT`), and fix all 5 production incidents (G1 Humongous Storm, Heap Leak, Loom Pinning/Lock Contention, ZGC Allocation Stall, Off-Heap Native Leak)
- [ ] ⭐ **4.1B `jvm-copilot` Agentic SRE Co-Pilot**: Automated Java JFR / GC-Log / Thread-Dump / NMT analyzer exposed as MCP tools that generates root-cause postmortems & JVM tuning prescriptions
- [ ] Publish final **G1GC vs Generational ZGC Benchmark Matrix** and update Resume bullets

---

[back to previous page](../README.md)
