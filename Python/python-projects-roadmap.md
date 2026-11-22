[back to previous page](../README.md)

---

# Python Mastery Roadmap (4 Months / 4 Stages): Senior Backend, Agentic AI & ML Systems Engineering

> **Goal:** Go from **Python fundamentals to Senior AI / ML / Backend Systems Engineer depth** in **16 focused weeks (4 months)**—building a portfolio of **production-grade Agentic AI, Applied ML, and High-Concurrency Data/Backend projects** backed by **live, high-volume real-world APIs** and **rigorous evaluation harnesses**.
>
> Designed for engineers targeting **Senior SWE, AI Engineer, Agent Infrastructure, and ML Platform/Inference roles** at deep-tech companies (**OpenAI, Anthropic, Google DeepMind, Meta, Databricks, Modal, Anyscale, Cursor, Perplexity, Stripe, Uber**).
>
> Instead of 20 toy notebook wrappers or an 18-month PhD reading list, this plan is organized into **4 Progressive 1-Month Stages (Beginner $\rightarrow$ Intermediate $\rightarrow$ Advanced $\rightarrow$ Senior Deep-Tech)** that produce **3–4 connected, resume-defining flagship projects (⭐)**.

---

## Table of Contents

1. [How to Execute This 4-Month (16-Week) Plan](#1-how-to-execute-this-4-month-16-week-plan)
2. [What Makes a Python / AI Project "Senior Deep-Tech Grade"](#2-what-makes-a-python--ai-project-senior-deep-tech-grade)
3. [Stage 1 (Month 1): Beginner → Intermediate — Modern Python, Internals & High-Concurrency Asyncio](#stage-1-month-1-weeks-14-beginner--intermediate--modern-python-internals--high-concurrency-asyncio)
4. [Stage 2 (Month 2): Intermediate → Senior — Columnar Data, Hybrid Search & Eval-Driven RAG Systems](#stage-2-month-2-weeks-58-intermediate--senior--columnar-data-hybrid-search--eval-driven-rag-systems)
5. [Stage 3 (Month 3): Advanced — Autonomous Agentic Runtime, MCP Servers & Sandboxed Execution](#stage-3-month-3-weeks-912-advanced--autonomous-agentic-runtime-mcp-servers--sandboxed-execution)
6. [Stage 4 (Month 4): Senior Deep-Tech — PyTorch Internals, LoRA/RLVR Distillation & vLLM Inference Serving](#stage-4-month-4-weeks-1316-senior-deep-tech--pytorch-internals-lorarlvr-distillation--vllm-inference-serving)
7. [Senior Engineer's Python, Asyncio & AI/LLM Systems Debugging Playbook](#senior-engineers-python-asyncio--aillm-systems-debugging-playbook)
8. [Real-World Live APIs, Firehoses & Benchmarks Directory](#real-world-live-apis-firehoses--benchmarks-directory)
9. [Python Internals & AI/ML Concept → Project Map](#python-internals--aiml-concept--project-map)
10. [Curated Fast-Track Resources (4-Month Scope)](#curated-fast-track-resources-4-month-scope)
11. [Senior Deep-Tech Interview Prep & Resume Bullet Templates](#senior-deep-tech-interview-prep--resume-bullet-templates)
12. [16-Week Execution Schedule & Progress Checklist](#16-week-execution-schedule--progress-checklist)

---

## 1. How to Execute This 4-Month (16-Week) Plan

- **Time commitment:** **16 weeks (~4 months)** at **12–15 hours/week** (~25% concepts & internals, ~75% building, profiling, and running evaluation benchmarks).
- **4 Stages = 4 Connected Flagship Projects (⭐):**
  - **Stage 1 (Month 1):** High-concurrency `asyncio` ingestion, distributed worker queue & multi-provider LLM/API gateway (**`async-nexus`**).
  - **Stage 2 (Month 2):** High-speed columnar feature pipeline + hybrid BM25/Vector search & verifiable citation RAG with an automated CI Eval Harness (**`veritas-rag`**).
  - **Stage 3 (Month 3):** Pure-Python state-machine autonomous coding & security-ops agent with custom **MCP (Model Context Protocol)** servers and ephemeral Docker sandboxes (**`aegis-agent`**).
  - **Stage 4 (Month 4):** Verifiable **LoRA / RLVR (Reinforcement Learning with Verifiable Rewards)** model distillation pipeline + quantized **vLLM** serving stack (**`distill-serve`**).
- **No black-box frameworks for core logic:** You will write your own hybrid retriever, explicit agent state machine, MCP JSON-RPC servers, eval metrics, and PyTorch attention/KV-cache mechanics in clean Python first—so you can explain every layer from first principles in senior interviews.
- **Live, high-volume public APIs only:** Every project ingests real, messy, rate-limited streams (**GitHub REST/GraphQL, SEC EDGAR XBRL, OpenAlex, Hacker News Firebase, OSV.dev CVEs, Bluesky Jetstream, Wikimedia EventStreams, HuggingFace Hub**).

---

## 2. What Makes a Python / AI Project "Senior Deep-Tech Grade"

Hiring managers at deep-tech companies immediately reject Jupyter notebooks and thin LangChain wrappers. Apply this engineering standard to every project:

| Criterion | Senior Engineer Bar |
|---|---|
| **Modern Packaging & Strict Typing** | `pyproject.toml`, `src/` layout, reproducible lockfile via **`uv`**, **`ruff`** lint/format clean, and **`pyright` / `mypy` (`--strict`)** clean. |
| **Evaluation-Driven Development (EDD)** | Every AI/ML project ships with a **golden evaluation dataset (50–100+ real ground-truth test cases)** and an automated CLI/CI eval harness reporting **Recall@K, MRR, Faithfulness, Pass@1, p95 Latency, and $/1k queries**. |
| **Profiled & Memory-Audited** | Benchmarked with **`py-spy`** (CPU flamegraphs), **`memray`** (heap & native memory allocations), and **`locust` / `k6`** (async tail latency under concurrent load). |
| **Production Resilience & Observability** | Structured JSON logs (`structlog`), **OpenTelemetry** traces across async & tool boundaries, token-bucket rate shaping, retries with jitter, idempotency keys, and graceful `SIGTERM` draining. |
| **Live Real-World Data** | Connects to live open APIs and public firehoses (handling pagination cursors, HTTP 429 `Retry-After`, schema drift, and network faults), paired with deterministic recorded fixtures (`respx` + `pytest`) in CI. |

---

## Stage 1 (Month 1 / Weeks 1–4): Beginner → Intermediate — Modern Python, Internals & High-Concurrency Asyncio

> **Focus:** Master idiomatic Python 3.12+ from the ground up, understand how **CPython** works under the hood (data model, descriptors, memory management, GIL vs free-threading), and build a **production-grade `asyncio` task engine & multi-provider API/LLM gateway**.

### Core Concepts to Master (Weeks 1–2)
1. **Modern Python Foundations & Data Model (Python 3.12+):**
   - Built-in data structures (`dict` compact hash table internals, `set`, `list` over-allocation, `tuple`, `collections.deque`, `Counter`, `defaultdict`), comprehensions, slicing, unpacking, `pathlib`.
   - **Generators & Lazy Streaming:** `yield`, generator expressions, `itertools` (`islice`, `chain`, `batched`), processing multi-GB streams in $O(1)$ constant memory.
   - **OOP & Python Data Model:** Dunder methods (`__repr__`, `__eq__`, `__hash__`, `__call__`, `__enter__`/`__exit__`), `@dataclass(frozen=True, slots=True)` (`__slots__` memory savings!), **Descriptors** (`__get__`, `__set__`—how properties, methods, and Pydantic/ORMs work), decorators with `functools.wraps` & `ParamSpec`, `Protocol` (structural duck typing), `Generic[T]`, and **Pydantic v2**.
2. **CPython Internals & Memory Management:**
   - Everything is a `PyObject*` (reference count `ob_refcnt` + type pointer `ob_type`).
   - **Reference Counting + Cyclic Garbage Collector** (generations 0/1/2, reference cycles, `weakref`).
   - **The GIL (Global Interpreter Lock) & PEP 703 (Free-Threaded Python 3.13t/3.14t):** Why CPU-bound Python threads serialize on the GIL, when C-extensions release the GIL, and how `multiprocessing` / shared memory vs `asyncio` work.
3. **Deep `asyncio` & High-Concurrency Backend Engineering:**
   - Event loop mechanics (`epoll`/`kqueue` selectors, coroutines, `Task`, futures), **Structured Concurrency (`asyncio.TaskGroup`)**, cancellation propagation (`CancelledError` pitfalls), `contextvars` for request-scoped tracing.
   - **Rule #1 of Production `asyncio`:** Never block the event loop with synchronous I/O or CPU work (`loop.run_in_executor` / `asyncio.to_thread`).
   - Connection pooling (`httpx.AsyncClient`, `asyncpg`), backpressure (`asyncio.Semaphore`, bounded `asyncio.Queue`), and Redis atomic operations (Lua scripts & Streams).

---

### Warmup Drill 1.1 (Week 1): `stream-probe` — Constant-Memory Streaming Log & Live API Contract CLI
- **Real-world data sources:** Multi-GB hourly **[GH Archive](https://www.gharchive.org/) NDJSON.gz dumps** (`https://data.gharchive.org/YYYY-MM-DD-H.json.gz`) + live endpoints (**[PyPI JSON API](https://pypi.org/pypi/fastapi/json)**, **[OpenAlex API](https://api.openalex.org/works?per-page=5)**, **[GitHub REST API](https://api.github.com)**).
- **Build:** A packaged CLI (`uvx stream-probe`) that: (1) streams multi-GB compressed GH Archive dumps directly over HTTP using lazy generators to compute top-K active repos, event velocity, and payload p50/p95/p99 stats in **<50 MB constant RAM** (verified with `memray`), and (2) validates live API schemas & p95 latency SLAs via Pydantic v2.

### ⭐ Flagship Project 1.2 (Weeks 2–4): `async-nexus` — High-Concurrency Async Task Engine & Intelligent LLM/API Gateway
- **Real-world live APIs:**
  - **[Hacker News Firebase API](https://hacker-news.firebaseio.com/v0/)** (top stories fanning out into 2,000+ concurrent comment/item fetches)
  - **[SEC EDGAR CompanyFacts API](https://data.sec.gov/api/xbrl/companyfacts/CIK0000320193.json)** (strict 10 req/sec per User-Agent quota)
  - **[OpenAlex API](https://api.openalex.org/works)** & **LLM APIs** (OpenAI / Anthropic / Gemini / local Ollama or vLLM)
- **What you build:** A high-concurrency async platform (FastAPI + `httpx` + Redis + Postgres `asyncpg`) combining a **Distributed Async Worker Queue** with an **Intelligent API & LLM Resilience Gateway**:
  1. **Structured Concurrency Fan-Out & Distributed Task Queue:** Crawl 2,000+ concurrent Hacker News / OpenAlex items using `asyncio.TaskGroup` and a durable Redis Streams / Postgres `SKIP LOCKED` worker pool with visibility-timeout lease recovery, dead-letter queues, idempotency keys, and graceful `SIGTERM` draining.
  2. **Atomic Distributed Rate Shaping & Circuit Breaking:** Redis Lua sliding-window / token-bucket rate limiter that guarantees upstream quotas (like SEC EDGAR's strict 10 req/sec limit) are never violated across multiple worker processes, plus singleflight request coalescing and automatic circuit breaking.
  3. **Agentic Cost/Latency LLM Router & Semantic Cache:** Drop-in OpenAI-compatible streaming SSE endpoint (`/v1/chat/completions`) with token-aware rate limiting (TPM/RPM), exact + vector semantic caching, automatic provider failover on 429/5xx, and a **complexity classifier router** that sends simple extraction/classification prompts to fast small models and complex reasoning prompts to frontier models.
- **Senior Check & Deliverable:**
  - Load-test with `locust` at 500+ concurrent requests, profile with **`py-spy`** and **`memray`** to eliminate event-loop blocking and memory leaks, trace requests end-to-end in **OpenTelemetry**, and publish the router's **cost-vs-latency savings table** in your README.

---

## Stage 2 (Month 2 / Weeks 5–8): Intermediate → Senior — Columnar Data, Hybrid Search & Eval-Driven RAG Systems

> **Focus:** Master high-performance data processing in Python (**Polars, DuckDB, Apache Arrow, NumPy memory layout**), build a **Hybrid Lexical + Vector Search Engine** from first principles, and architect a **Verifiable Citation RAG System with an Automated CI Evaluation Harness** over live financial and scientific APIs.

### Core Concepts to Master (Weeks 5–6)
1. **High-Performance Data & Numerical Computing:**
   - Why pure Python loops over millions of rows are slow (boxing, pointer chasing, dynamic dispatch) vs **Vectorized Columnar Execution**.
   - **NumPy & Apache Arrow Memory Layout:** Contiguous C/Fortran buffers, strides, zero-copy views vs copies, SIMD execution, Parquet columnar compression, predicate & projection pushdown in **Polars** and **DuckDB**.
   - Profiling CPU vs memory vs I/O bottlenecks with **`scalene`** and **`memray`**.
2. **Information Retrieval & Hybrid Search Internals:**
   - **Lexical Search:** Tokenization, inverted indexes, **BM25** term-frequency saturation & document-length normalization.
   - **Dense Vector Search:** Bi-encoder embeddings, cosine similarity / inner product, how **HNSW (Hierarchical Navigable Small World)** and **IVF** indexes trade recall for sub-10ms latency, metadata pre-filtering.
   - **Hybrid Fusion & Reranking:** Combining BM25 + Dense Vector results via **Reciprocal Rank Fusion (RRF)**, followed by a **Cross-Encoder Reranker** (top-50 $\rightarrow$ top-5) and **AST / structure-aware chunking**.
3. **Evaluation-Driven Development (EDD) for AI Systems:**
   - Why "vibe-checking" prompts fails in production.
   - **Retrieval Metrics:** **Recall@K, Precision@K, MRR (Mean Reciprocal Rank), NDCG@K**.
   - **Generation & Grounding Metrics:** **Citation Precision/Recall** (verifying every inline `[doc_id:span]` citation against retrieved source text), **Faithfulness / Hallucination detection**, **Deterministic Numeric Verification**, and refusal calibration.

---

### Warmup Drill 2.1 (Week 5): `lake-feature` — High-Speed Columnar ETL & Point-in-Time Feature Pipeline
- **Real-world live data:** Pull 5M–20M real records from **[GH Archive](https://www.gharchive.org/)** or **[OpenSky Network Aircraft States](https://opensky-network.org/api/states/all)** joined temporally (`ASOF` join) with **[Open-Meteo Historical Weather API](https://open-meteo.com/en/docs/historical-weather-api)**.
- **Build:** An incremental ingestion & feature pipeline writing partitioned Parquet via **Polars** and **DuckDB** with zero-copy **Apache Arrow** interchange. Publish a benchmark table comparing Pandas vs Polars vs DuckDB (wall-clock speed + `memray` peak RSS).

### ⭐ Flagship Project 2.2 (Weeks 6–8): `veritas-rag` — Production Hybrid RAG, Verifiable Citation Engine & CI Eval Harness
- **Real-world live APIs (with deterministic ground-truth verification!):**
  - **Financial Intelligence Track:** Live 10-K/10-Q/8-K filings **AND** structured **XBRL CompanyFacts JSON** from the **[SEC EDGAR API](https://data.sec.gov/api/xbrl/companyfacts/CIK0000320193.json)** (lets you deterministically verify whether the RAG system's extracted revenue/EPS numbers match the official XBRL JSON!).
  - **Scientific & Code Track:** Live papers and citation graphs from **[OpenAlex API](https://api.openalex.org/works)** + **[arXiv API](https://export.arxiv.org/api/query)** + **[GitHub Repo Docs/Issues API](https://api.github.com)**.
- **What you build:**
  1. **Structure-Aware Ingestion & Incremental Sync:** Table-preserving SEC filing / Markdown section parser (or `tree-sitter` AST code chunker) with content-hash deduplication and incremental API cursor sync.
  2. **Hybrid Retrieval & Cross-Encoder Reranking Engine:** Combine **BM25** (exact tickers, financial line items, function names) + **Dense Vector Embeddings** (`pgvector` / Qdrant / memory-mapped HNSW) + **Metadata Filtering** (ticker, filing date, year) via **Reciprocal Rank Fusion (RRF)** and a **Cross-Encoder Reranker**, plus query decomposition for multi-hop questions.
  3. **Verifiable Inline Citation Generator:** Constrained structured generation (Pydantic schema) requiring every factual claim and number to carry an exact source span citation (`[CIK0000320193:10-K:2025:p42]`), with a post-processor that strips or flags any claim whose cited span does not entail the statement.
  4. **Automated Golden Eval Harness (Wired into GitHub Actions CI):**
     - Build a curated dataset of **100+ real multi-hop questions** paired with exact ground-truth document IDs and SEC XBRL numerical values.
     - Automatically compute **Recall@5, MRR, NDCG@10, Citation Faithfulness %, Numeric Exact-Match %, p95 Latency, and $/1k queries** on every code/prompt/chunking change.
- **Senior Check & Deliverable:**
  - Include an **Ablation Experiment Table** in your README showing the exact measurable jump from: `Naive Fixed-Chunk Vector Search` $\rightarrow$ `+ Structure-Aware Chunking` $\rightarrow$ `+ Hybrid BM25 + RRF` $\rightarrow$ `+ Cross-Encoder Reranker` $\rightarrow$ `+ Query Decomposition` (e.g., Recall@5 improving from 61% $\rightarrow$ 91%, Faithfulness from 74% $\rightarrow$ 97%).

---

## Stage 3 (Month 3 / Weeks 9–12): Advanced — Autonomous Agentic Runtime, MCP Servers & Sandboxed Execution

> **Focus:** Move beyond single-turn RAG to build a **fault-tolerant, sandboxed Autonomous Agent Runtime** in pure Python. You will implement the **Model Context Protocol (MCP)**, explicit finite-state-machine orchestration, ephemeral Docker execution sandboxes, context compaction, and evaluate your agent on **real software engineering & security tasks (SWE-bench Lite & Live GitHub/CVE Repositories)**.

### Core Concepts to Master (Weeks 9–10)
1. **Agentic Systems Architecture (First Principles — No Opaque Wrappers):**
   - Why production agent teams at Anthropic, OpenAI, Cursor, and Cognition build **explicit State Machines / Execution Loops** instead of deeply nested framework chains.
   - Core patterns: **ReAct (Reason + Act)**, **Plan-and-Execute**, **Parallel Tool Fan-Out (`asyncio.TaskGroup`)**, and **Generator-Verifier (Actor-Critic) loops** where a deterministic tool (compiler, linter, test runner, SQL/XBRL check) verifies the agent's work before finishing.
2. **Model Context Protocol (MCP) & Tool Engineering:**
   - MCP architecture (Host, Client, Server over stdio / SSE JSON-RPC), designing high-signal, token-efficient tools with strict Pydantic input/output schemas and actionable error messages that guide agent self-correction.
3. **Context Engineering, Memory & Reliability Guardrails:**
   - **Context Window Compaction:** Tool output truncation (head/tail slicing of huge logs), sliding-window trajectory summarization, and structured scratchpads so 30-turn runs don't blow up token costs or degrade attention.
   - **Durable Checkpointing & Human-in-the-Loop:** Persisting every state transition (`StateSnapshot`) to SQLite/Postgres so a crashed or paused agent can resume from turn $k$ without re-running tools, plus approval gates for destructive actions.
   - **Loop & Budget Guardrails:** Repeated tool-call cycle detection, max-turn & max-dollar per-task circuit breakers.
4. **Isolated Execution Sandboxing:**
   - Running untrusted agent-generated code, git patches, and shell commands safely inside **ephemeral Docker containers** (`--network=none`, `--memory=512m`, `--cpus=1`, read-only mounts, strict command timeouts).

---

### ⭐ Flagship Project 3.1 (Weeks 9–12): `aegis-agent` — Sandboxed Autonomous Coding, CVE Security-Ops & Research Agent (Pure Python FSM + MCP + Docker Sandbox)
- **Real-world live APIs & Benchmark Feeds:**
  - **[GitHub REST & GraphQL API](https://api.github.com)** (clone repos, read issues, inspect PR diffs, search code, open draft PRs)
  - **[OSV.dev Vulnerability API](https://api.osv.dev/v1/)** & **[PyPI JSON API](https://pypi.org/pypi/)** (live dependency CVE advisories & version compatibility)
  - **[SWE-bench Lite / Verified Dataset](https://huggingface.co/datasets/princeton-nlp/SWE-bench_Lite)** (streamed via **HuggingFace Datasets API**)
  - Your Stage 2 **`veritas-rag`** server (for documentation & paper lookup)
- **What you build:**
  1. **Custom MCP Tool Servers:** Build 3 clean MCP servers in Python:
     - `mcp-repo-sandbox`: AST code navigation (`tree-sitter` symbol/call-graph lookup, fast ripgrep search, surgical file patching) + **ephemeral Docker test runner** (runs `pytest`, `ruff`, `pyright` inside an isolated container and returns structured failure tracebacks).
     - `mcp-secops`: Queries **OSV.dev** and **PyPI** for live CVEs in a repo's `pyproject.toml`/`requirements.txt`, identifies the minimal safe upgrade version, and checks breaking API changes.
     - `mcp-knowledge`: Wraps your Stage 2 **`veritas-rag`** hybrid search engine over live docs/papers/filings.
  2. **Explicit Pure-Python Agent State Machine:**
     - States: `TriageAndLocalize` $\rightarrow$ `HypothesizeAndPlan` $\rightarrow$ `ApplyPatchInSandbox` $\rightarrow$ `RunDeterministicVerification (pytest/ruff/mypy)` $\rightarrow$ `SelfCorrectOrFinalize`.
     - Includes **automatic context compaction** (summarizing old tool outputs when token count exceeds threshold), **loop detection**, **durable SQLite/Postgres state checkpoints**, and full **OpenTelemetry** span trees for every turn, tool call, and LLM prompt.
- **Senior Check & Deliverable (The Ultimate Resume Differentiator):**
  - Run your agent on **30–50 real tasks from SWE-bench Lite** + **10 real live GitHub repo CVE upgrades / bug issues**.
  - Publish a rigorous **Engineering Evaluation Report** in your README: **Pass@1 Resolve Rate (%)**, **Average Cost ($) & Tokens per Resolved Issue**, **p50/p95 Turns to Resolution**, and a **Categorized Failure Taxonomy** (e.g., % localization failures vs % broken edge-case tests vs % context overflow)—plus how context compaction and AST symbol search improved pass@1.

---

## Stage 4 (Month 4 / Weeks 13–16): Senior Deep-Tech — PyTorch Internals, LoRA/RLVR Distillation & vLLM Inference Serving

> **Focus:** Bridge **Applied AI Engineering $\rightarrow$ Senior ML Systems & Inference Engineering**. Understand modern **Transformer & KV-Cache internals in PyTorch**, build an automated **LoRA / RLVR (Reinforcement Learning with Verifiable Rewards) Model Distillation Flywheel**, and deploy a high-throughput **quantized vLLM / SGLang inference stack** benchmarked under realistic concurrent traffic.

### Core Concepts to Master (Weeks 13–14)
1. **PyTorch & Modern Transformer Internals (Senior Systems Depth):**
   - Tensor strides, contiguous memory, `bf16` mixed precision, `torch.compile` operator fusion, GPU memory breakdown (model weights vs gradients vs optimizer states vs activation memory).
   - **Inside a Modern Decoder Transformer (Llama-3 / Qwen-2.5 architecture):**
     - **RMSNorm**, **RoPE (Rotary Position Embeddings)**, **SwiGLU** feed-forward networks, and **Grouped-Query Attention (GQA)** (why GQA shrinks KV-cache memory by 4–8x compared to Multi-Head Attention!).
2. **LLM Inference Serving Mechanics & Math (Asked in Every Deep-Tech AI Infra Interview):**
   - **Two Phases of Inference:** **Prefill** (parallel prompt processing $\rightarrow$ *compute-bound*) vs **Decode** (autoregressive 1-token-at-a-time generation $\rightarrow$ *memory-bandwidth-bound*).
   - **KV-Cache Sizing Formula:** Be able to calculate on a whiteboard: $\text{KV Cache Bytes} = 2 \times n_{\text{layers}} \times n_{\text{kv\_heads}} \times d_{\text{head}} \times \text{seq\_len} \times \text{batch\_size} \times \text{bytes\_per\_param}$.
   - **How Modern Engines (vLLM / SGLang) Achieve 10x Throughput:** **Continuous Batching** (iteration-level scheduling), **PagedAttention** (eliminating contiguous KV-cache fragmentation), **Radix / Prefix Caching** (reusing KV-cache for shared system prompts & RAG contexts), **Quantization (FP8 / AWQ / INT4)**, and **Speculative Decoding**.
3. **Post-Training, Fine-Tuning & Model Distillation (LoRA, DPO & RLVR):**
   - **LoRA / QLoRA:** Low-rank adapter math ($W_0 + \Delta W = W_0 + BA$), target modules, memory savings.
   - **Distillation & RL with Verifiable Rewards (RLVR / DPO / GRPO):** Using a frontier teacher model to generate reasoning/tool trajectories on real tasks, filtering them automatically with a **deterministic verifier** (unit test pass, SQL/JSON schema check, or exact SEC EDGAR XBRL number match), and training a fast 1.5B–7B student model to match teacher accuracy at 10x lower cost.

---

### ⭐ Flagship Project 4.1 (Weeks 13–16): `distill-serve` — Verifiable LoRA/RLVR Model Distillation Flywheel & High-Throughput vLLM Serving Stack
- **Real-world verifiable data sources (pick 1 domain from your Stage 2/3 projects):**
  - **Domain A (Financial Extraction & Reasoning):** Raw 10-K/10-Q text tables from **[SEC EDGAR API](https://data.sec.gov)** paired with **exact ground-truth numbers from EDGAR's XBRL CompanyFacts JSON API** as the automatic deterministic reward verifier!
  - **Domain B (Code Generation & Tool Calling):** Real Python bug-fix/function tasks + unit tests streamed from **[HuggingFace Hub API](https://huggingface.co/datasets)** (**MBPP / HumanEval / BigCodeBench**) or **GitHub PRs**, where **Docker `pytest` execution** acts as the deterministic reward verifier!
- **What you build (3 Connected Components):**
  1. **Part A — Clean PyTorch Transformer & KV-Cache Micro-Engine (`micro-infer`):**
     Write a concise, crystal-clear ~250-line pure PyTorch implementation of a modern Transformer block (**RMSNorm + RoPE + Grouped-Query Attention + SwiGLU**) with an explicit **KV-Cache incremental decode loop** and a benchmark showing prefill vs decode latency with and without KV caching. (This proves you understand the math and PyTorch tensors under the hood.)
  2. **Part B — Automated Verifiable Distillation & Fine-Tuning Pipeline (LoRA + DPO/GRPO):**
     - Generate 2,000–5,000 structured reasoning/tool trajectories on your live dataset, score every trajectory automatically with your **deterministic verifier** (XBRL exact numerical match or sandboxed `pytest` pass/fail).
     - Fine-tune an open-weight **Qwen-2.5-3B / 7B or Llama-3.2-3B** model using **QLoRA SFT + DPO or GRPO** (`peft` + `trl` / `unsloth` on a single rented A10G/L4/A100 GPU on Modal/RunPod for ~$10–$20 total).
  3. **Part C — Production vLLM Inference Serving & Intelligent Cascade Integration:**
     - Deploy your fine-tuned model on **vLLM** (testing FP16 vs AWQ/FP8 quantization and **Prefix Caching** on shared RAG system prompts) and wire it into your Stage 1 **`async-nexus` Gateway**.
     - Benchmark **Time-To-First-Token (TTFT)**, **Time-Per-Output-Token (TPOT / ITL)**, and **Total Tokens/sec** across concurrency levels ($1 \rightarrow 128$ concurrent users).
- **Senior Check & Deliverable:**
  - Publish the complete **Distillation & Serving Benchmark Matrix** in your README:
    1. **Accuracy Jump:** Base 3B/7B Model $\rightarrow$ + LoRA SFT $\rightarrow$ + Verifiable DPO/GRPO vs Frontier Teacher (GPT-4o / Claude Sonnet).
    2. **Serving Roofline:** TTFT (ms), TPOT (ms/token), Peak Throughput (tok/s), and GPU VRAM usage for `PyTorch Eager` vs `vLLM Continuous Batching` vs `vLLM + Prefix Caching + INT4/FP8`.
    3. **End-to-End Pareto Win:** Showing your **`async-nexus` + `distill-serve`** stack matches **>92% of frontier accuracy at 8–12x lower cost and 4x lower p95 latency**.

---

## Senior Engineer's Python, Asyncio & AI/LLM Systems Debugging Playbook

This table covers the exact production failure modes probed in Senior Backend, AI Engineer, and ML Systems interviews:

| Production Symptom | Exact Diagnostic Tool & Command | What's Happening Under the Hood | Concrete Senior Fix |
|---|---|---|---|
| **FastAPI / `asyncio` service throughput collapses and health checks time out even though CPU is at 100% on 1 core** | `py-spy top --pid <pid>` or `py-spy record -o profile.svg --pid <pid>`; enable `loop.set_debug(True)` | **Event-Loop Starvation:** A synchronous blocking call (`requests.get`, sync file/DB I/O, CPU-heavy JSON/PDF parsing, or `time.sleep`) is running inside an `async def` coroutine, freezing the entire single-threaded event loop. | Replace sync I/O with async (`httpx.AsyncClient`, `asyncpg`); offload CPU-bound parsing/hashing to `await asyncio.to_thread(...)` or a `ProcessPoolExecutor`. |
| **Python worker RSS memory climbs until container `OOMKilled`, even though batch results are deleted** | `memray run -o trace.bin app.py && memray flamegraph trace.bin`; `tracemalloc` | Either (a) holding references in global caches / `lru_cache` without `maxsize`, (b) Pandas/NumPy views keeping an entire 2 GB parent array alive in RAM, or (c) glibc `malloc` arena fragmentation / C-extension leaks. | Add bounded `maxsize` / TTL to caches; `.copy()` small slices before dropping huge parent DataFrames; stream chunks via generators / Polars lazy frames; use `MALLOC_TRIM_THRESHOLD_`. |
| **RAG system answers confidently with wrong numbers or hallucinated facts on multi-hop queries** | Run Stage 2 **CI Eval Harness**; inspect **Recall@5 vs Faithfulness** breakdown | **Retrieval vs Generation Split:** If Recall@5 is low (<70%), dense embeddings missed exact identifiers/tickers or table structure was destroyed during chunking. If Recall@5 is high (>90%) but Faithfulness is low, context is cluttered with noisy chunks ("lost in the middle"). | Add **Hybrid BM25 + RRF** for exact terms + **Table-preserving chunker** (fixes Recall); add a **Cross-Encoder Reranker** (top-50 $\rightarrow$ top-5) + strict span-citation verification (fixes Faithfulness). |
| **Autonomous agent gets stuck in infinite loops, blows through $5/task token budget, or degrades after turn 12** | Inspect **OpenTelemetry** agent trace tree & token growth per turn | **Context Pollution & Missing Verification:** Raw 50 KB tool outputs (full file dumps / stack traces) flood the prompt, degrading LLM instruction following, and the agent lacks a cycle detector. | Implement **Tool Output Truncation** + **Sliding-Window Trajectory Compaction**; add a deterministic **Cycle Detector** (block identical `(tool, args)` calls) and hard token/turn budget circuit breakers. |
| **vLLM / GPU inference server p99 Time-To-First-Token (TTFT) spikes to 8 seconds under concurrent bursts** | Inspect vLLM Prometheus metrics (`vllm:num_requests_waiting`, `vllm:gpu_cache_usage_perc`) | **KV-Cache Exhaustion / Prefill Contention:** Long-context requests exhaust GPU KV-cache blocks (causing request preemption/swapping) or massive prompt prefills stall ongoing token decode steps. | Enable **Prefix Caching** (`--enable-prefix-caching` for shared system/RAG prompts); enable **Chunked Prefill**; quantize weights/KV-cache (**FP8 / AWQ**); cap `--max-model-len`. |

---

## Real-World Live APIs, Firehoses & Benchmarks Directory

Every API below is **free, public, and provides real-world volume and verification signals**:

| Live API / Dataset | Endpoint / Access | Auth & Protocol | Why It's Ideal for Senior Python / AI Projects | Target Stage |
|---|---|---|---|---|
| **SEC EDGAR Filings & XBRL API** | `https://data.sec.gov/api/xbrl/companyfacts/CIK0000320193.json` | REST JSON/HTML (No Auth; strict `User-Agent`, 10 req/s) | 15–30 MB real financial filings + **exact ground-truth XBRL JSON numbers** for deterministic RAG & RLVR verification! | Stage 1, 2, 4 |
| **GitHub REST & GraphQL APIs** | `https://api.github.com` | REST / GraphQL (Free PAT, 5,000 req/hr) | Live repo code trees, issues, PR diffs, and CI logs for MCP coding agents & RAG | Stage 1, 2, 3, 4 |
| **OSV.dev & PyPI JSON APIs** | `https://api.osv.dev/v1/querybatch` & `https://pypi.org/pypi/<pkg>/json` | REST JSON (No Auth) | Live CVE vulnerability graph across 500k+ Python packages; powers the SecOps MCP Agent | Stage 1, 3 |
| **OpenAlex & arXiv APIs** | `https://api.openalex.org/works` & `export.arxiv.org/api/query` | REST JSON (No Auth; `mailto=` polite pool) | 250M+ scholarly papers, abstracts & citation graphs for hybrid BM25+Vector search | Stage 1, 2, 3 |
| **Hacker News Firebase API** | `https://hacker-news.firebaseio.com/v0/` | REST JSON (No Auth) | Deep recursive story/comment trees requiring 2,000+ concurrent async fetches | Stage 1 |
| **GH Archive** | `https://data.gharchive.org/YYYY-MM-DD-H.json.gz` | HTTP NDJSON.gz (No Auth) | Multi-GB hourly GitHub event dumps for `memray` constant-memory generators & Polars/DuckDB | Stage 1, 2 |
| **Bluesky Jetstream & Wikimedia SSE** | `wss://jetstream2.us-east.bsky.network/subscribe` & `stream.wikimedia.org` | Live WebSocket / SSE (No Auth) | 1,000+ live events/sec for testing async backpressure and streaming pipelines | Stage 1 |
| **HuggingFace Hub & Datasets API** | `https://huggingface.co/datasets` (`SWE-bench_Lite`, `MBPP`) | REST / Parquet / `datasets` SDK (Free token) | Official **SWE-bench Lite** coding agent tasks, open-weight **Qwen/Llama** models & RLVR code benchmarks | Stage 3, 4 |

---

## Python Internals & AI/ML Concept → Project Map

| Core Concept / Tool | Where You Implement & Prove It |
|---|---|
| `uv`, `ruff`, `pyright`/`mypy --strict`, `pytest`, `respx`, generators, `memray` | 1.1 `stream-probe` and every repo after |
| Descriptors, `@dataclass(slots=True)`, `Protocol`, `ParamSpec`, Pydantic v2, `contextvars` | 1.1 `stream-probe`, ⭐ 1.2 `async-nexus` |
| `asyncio.TaskGroup`, `Semaphore`, Redis Lua rate limiters, Postgres `SKIP LOCKED`, OpenTelemetry | ⭐ 1.2 `async-nexus` distributed task engine & LLM gateway |
| **Polars**, **DuckDB**, **Apache Arrow** zero-copy, Parquet partitioning, `py-spy`, `scalene` | 2.1 `lake-feature` columnar ETL pipeline |
| **BM25** inverted index, **Dense Vector HNSW**, **Reciprocal Rank Fusion (RRF)**, **Cross-Encoder Reranker** | ⭐ 2.2 `veritas-rag` hybrid search core |
| **Evaluation-Driven Development (EDD)** (Recall@K, MRR, NDCG, Citation Faithfulness, CI gates) | ⭐ 2.2 `veritas-rag` automated golden eval harness |
| **Model Context Protocol (MCP)** JSON-RPC servers, `tree-sitter` AST search, explicit Python FSM | ⭐ 3.1 `aegis-agent` autonomous agent runtime |
| **Ephemeral Docker Sandboxing**, context window compaction, durable SQLite/Postgres checkpoints, **SWE-bench** | ⭐ 3.1 `aegis-agent` sandboxed execution & eval report |
| **PyTorch** (`bf16`, `torch.compile`, **RMSNorm, RoPE, Grouped-Query Attention, SwiGLU, KV-Cache**) | ⭐ 4.1A `distill-serve` (`micro-infer` PyTorch core) |
| **QLoRA / LoRA**, **DPO / GRPO (RL with Verifiable Rewards)**, deterministic XBRL/`pytest` verifiers | ⭐ 4.1B `distill-serve` distillation flywheel |
| **vLLM** (Continuous Batching, PagedAttention, Prefix Caching, AWQ/FP8 Quantization), TTFT & TPOT rooflines | ⭐ 4.1C `distill-serve` high-throughput serving stack |

---

## Curated Fast-Track Resources (4-Month Scope)

Focus exclusively on these high-signal resources during your 16 weeks:

1. **Stage 1 (Python Internals & Asyncio):**
   - *Fluent Python* (2nd Ed., Luciano Ramalho) — Selected chapters: **Data Model (Ch 1–3), Functions/Decorators (Ch 7–9), Protocols/ABCs (Ch 11–13), Generators/Coroutines & `asyncio` (Ch 17, 19–21)**.
   - Hynek Schlawack's talks/posts on production Python packaging (`uv`), `structlog`, and async service reliability.
2. **Stage 2 & 3 (Applied AI, RAG, Evals & Autonomous Agents):**
   - *AI Engineering* (Chip Huyen, 2025) — Chapters on Evaluation-Driven Development, RAG & Hybrid Retrieval, Agent Patterns, and Latency/Cost Optimization.
   - **Anthropic Engineering Guides:** *[Building Effective Agents](https://www.anthropic.com/research/building-effective-agents)* and *[Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)*.
   - **Model Context Protocol (MCP) Specification & Python SDK Docs** (`modelcontextprotocol.io`).
   - Hamel Husain & Eugene Yan's field guides on **LLM Evals & Error Analysis**.
3. **Stage 4 (PyTorch Transformers, Post-Training & LLM Inference Internals):**
   - **Andrej Karpathy** — *Let's reproduce GPT-2 (124M)* (4-hour video walkthrough of PyTorch Transformer & GPU optimization).
   - **Stanford CS336 (Language Modeling from Scratch)** — Selected lectures on Transformer architecture (RoPE, GQA, SwiGLU), KV-cache inference math, and post-training (DPO/GRPO).
   - **vLLM Paper & Blog** (*Efficient Memory Management for Large Language Model Serving with PagedAttention*).

---

## Senior Deep-Tech Interview Prep & Resume Bullet Templates

### How This 4-Month Plan Covers Every Senior Interview Round

| Interview Round | What Deep-Tech Interviewers Probe | Your Direct Preparation from This Plan |
|---|---|---|
| **1. Python Internals & Practical Machine Coding (45–60 min)** | Implementing an async crawler with rate limits & retries, in-memory TTL cache, streaming generator pipeline, or tool-calling agent loop; GIL vs `asyncio` event-loop blocking, descriptors, refcounting + cyclic GC, memory leaks. | **Stage 1 (`stream-probe` + `async-nexus`)** + the **Debugging Playbook** above. |
| **2. AI / RAG & Search Systems Design** | Designing high-precision enterprise RAG: chunking strategies, BM25 + dense vector hybrid search, RRF, cross-encoder reranking, citation verification, and building an offline + online evaluation harness. | **Stage 2 (`veritas-rag` + Ablation Experiment Table)**. |
| **3. Agentic Architecture & Infrastructure Design** | Designing reliable multi-step coding/research agents: state machines vs raw loops, MCP tool design, container sandboxing, context compaction, loop prevention, durable checkpoint recovery, and SWE-bench evaluation. | **Stage 3 (`aegis-agent` + Failure Taxonomy Report)**. |
| **4. ML Systems & LLM Inference Deep Dive** | Prefill vs decode rooflines, exact KV-cache memory calculation, GQA vs MHA, Continuous Batching, PagedAttention, Prefix Caching, quantization (FP8/INT4), and distilling frontier models via LoRA / DPO / RLVR. | **Stage 4 (`distill-serve` + PyTorch `micro-infer` + vLLM Roofline Matrix)**. |

### Ready-to-Use Senior Resume Bullet Templates (Quantified After You Build Them)

- **Resume Project 1 (`veritas-rag` + `async-nexus` — Applied AI & High-Concurrency Backend):**
  > *"Engineered a high-concurrency Python `asyncio` LLM Gateway & Verifiable Citation RAG platform over live **SEC EDGAR 10-K/XBRL** and **OpenAlex** APIs (`FastAPI`, `TaskGroup`, Redis Lua rate limiters, **BM25 + HNSW Hybrid Search**, **RRF**, and **Cross-Encoder Reranking**); built an automated 120-query **CI Evaluation Harness** verifying LLM financial answers deterministically against EDGAR XBRL JSON, boosting Recall@5 from 62% to 92% and citation faithfulness to 96.5%."*
- **Resume Project 2 (`aegis-agent` — Agentic AI Infrastructure & Sandboxed Execution):**
  > *"Built a fault-tolerant autonomous coding & CVE remediation agent runtime in pure Python (explicit finite-state machine, custom **Model Context Protocol (MCP)** servers for `tree-sitter` AST search, **GitHub/OSV.dev APIs**, and **ephemeral Docker test sandboxes**) with sliding-window context compaction and durable Postgres state recovery; achieved **48% Pass@1** on a 50-task **SWE-bench Lite** subset at $0.16/task with full OpenTelemetry trace replay."*
- **Resume Project 3 (`distill-serve` — ML Systems, Fine-Tuning & GPU Inference Serving):**
  > *"Built an automated **QLoRA + GRPO (RL with Verifiable Rewards)** model distillation pipeline using deterministic SEC XBRL and `pytest` sandbox verifiers to distill frontier teacher trajectories into a **Qwen-2.5-3B/7B** student model; deployed on **vLLM** (continuous batching, prefix caching, AWQ INT4 quantization) behind a complexity router to retain **93% of frontier accuracy while cutting inference cost by 10x and p95 TTFT latency by 4.2x**."*

---

## 16-Week Execution Schedule & Progress Checklist

| Stage & Timeframe | Level & Theme | Projects to Ship | Key Senior Deliverable |
|---|---|---|---|
| **Stage 1: Weeks 1–4** | **Beginner $\rightarrow$ Intermediate** (Modern Python 3.12+, Internals & High-Concurrency `asyncio`) | **1.1 `stream-probe`** + ⭐ **1.2 `async-nexus`** | `<50 MB` `memray` streaming proof + `locust` & `py-spy` benchmarked async task queue & LLM router |
| **Stage 2: Weeks 5–8** | **Intermediate $\rightarrow$ Senior** (Columnar Data, Hybrid Search & Eval-Driven RAG) | **2.1 `lake-feature`** + ⭐ **2.2 `veritas-rag`** | Polars/DuckDB benchmark + **Ablation Eval Table** (Recall@5, MRR, XBRL-verified Faithfulness in CI) |
| **Stage 3: Weeks 9–12** | **Advanced** (Autonomous Agent Runtime, MCP Servers & Docker Sandboxes) | ⭐ **3.1 `aegis-agent`** | 3 custom MCP servers + Docker test sandbox + **SWE-bench Lite / Live GitHub CVE Pass@1 Report** |
| **Stage 4: Weeks 13–16** | **Senior Deep-Tech** (PyTorch Internals, LoRA/RLVR Distillation & vLLM Serving) | ⭐ **4.1 `distill-serve`** | 250-line PyTorch RoPE/GQA/KV-cache core + QLoRA/GRPO distilled model + **vLLM TTFT/TPOT Pareto Matrix** |

### Progress Checklist

**Stage 1 (Month 1): Modern Python, Internals & High-Concurrency Asyncio**
- [ ] **1.1 `stream-probe`**: Constant-memory (`<50 MB` verified with `memray`) streaming GH Archive CLI & live API schema/SLA contract checker (`uv`, `ruff`, `pyright --strict`, Pydantic v2)
- [ ] ⭐ **1.2 `async-nexus`**: Distributed `asyncio.TaskGroup` worker queue (2,000+ concurrent Hacker News / OpenAlex crawls) + Redis Lua rate-limited API & OpenAI-compatible LLM Router (semantic cache + complexity model routing, profiled with `py-spy` & `locust`)

**Stage 2 (Month 2): Columnar Data, Hybrid Search & Eval-Driven RAG**
- [ ] **2.1 `lake-feature`**: Incremental **Polars + DuckDB + Apache Arrow** Parquet feature pipeline on multi-million-row GH Archive / OpenSky + Open-Meteo data (with Pandas vs Polars vs DuckDB `memray` benchmark)
- [ ] ⭐ **2.2 `veritas-rag`**: Production Hybrid RAG (**BM25 + Dense Vector + RRF + Cross-Encoder Reranker**) over live **SEC EDGAR 10-K/XBRL & OpenAlex APIs** with verifiable inline citations + **100-Query Automated CI Eval Harness & Ablation Table**

**Stage 3 (Month 3): Autonomous Agentic Runtime, MCP & Sandboxed Execution**
- [ ] ⭐ **3.1A `aegis-agent` Core & MCP Servers**: Explicit pure-Python Agent State Machine + 3 custom **MCP JSON-RPC Servers** (`tree-sitter` AST repo tools, **OSV.dev/PyPI** CVE scanner, and `veritas-rag` knowledge tool) + **Ephemeral Docker Execution Sandbox**
- [ ] ⭐ **3.1B `aegis-agent` Reliability & Benchmark**: Context compaction, cycle detection, durable SQLite/Postgres checkpoint recovery, OpenTelemetry tracing, and published evaluation on **30–50 SWE-bench Lite tasks + 10 live GitHub issues**

**Stage 4 (Month 4): PyTorch Internals, Verifiable Distillation & vLLM Serving**
- [ ] ⭐ **4.1A `micro-infer` PyTorch Core**: Clean ~250-line PyTorch implementation of **RMSNorm, RoPE, Grouped-Query Attention (GQA), SwiGLU, and incremental KV-Cache decoding** with prefill vs decode benchmark
- [ ] ⭐ **4.1B `distill-serve` Verifiable LoRA/RLVR Flywheel**: Fine-tune a **Qwen-2.5 / Llama-3 (3B–7B)** student model via **QLoRA + DPO/GRPO** on trajectories filtered by deterministic **SEC XBRL / Docker `pytest` verifiers**
- [ ] ⭐ **4.1C `distill-serve` Production vLLM Stack**: Serve the quantized model on **vLLM** (continuous batching + prefix caching) behind your Stage 1 `async-nexus` router and publish the **Accuracy vs TTFT/TPOT vs $/1k-Queries Pareto Report**

---

[back to previous page](../README.md)
