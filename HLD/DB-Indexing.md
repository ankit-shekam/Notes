[back to HLD index](./HLD.md)
---

# Database Indexing

> *By maintaining separate data structures optimized for searching, indexes let databases quickly locate exact records without examining every row—trading extra disk space and write overhead for dramatically faster reads.*

## Table of Contents
1. [How Database Indexes Work](#1-how-database-indexes-work)
2. [B-Tree Indexes](#2-b-tree-indexes)
3. [LSM Trees (Log-Structured Merge Trees)](#3-lsm-trees-log-structured-merge-trees)
4. [Hash Indexes](#4-hash-indexes)
5. [Geospatial Indexes](#5-geospatial-indexes)
6. [Inverted Indexes](#6-inverted-indexes)
7. [Index Optimization Patterns](#7-index-optimization-patterns)
8. [Interview Wrap-Up & Decision Guide](#8-interview-wrap-up--decision-guide)

---

## 1. How Database Indexes Work

> **Interview Scope:** For full-stack and product-focused roles, knowing *when* and *why* to use indexes is usually enough. Unless you are interviewing for a database internals role, storage-engine mechanics won't be quizzed directly—but understanding the fundamentals helps you make strong architectural trade-offs.

### Physical Storage & Access Patterns
Modern databases store vast amounts of data on **disk** (typically SSDs), yet can only process records once loaded into **RAM**. While databases use prefetching and buffer-pool caching to speed up disk access, sequentially scanning every page of a large table on disk is far too slow. Indexes provide compact, structured lookup paths so the database reads only the few disk pages it actually needs.

### The Cost of Indexes
Indexes are never free—every index introduces clear trade-offs:

| Trade-Off | Why It Happens | Practical Implication |
| :--- | :--- | :--- |
| **Extra Disk Space** | Each index stores its own sorted/structured copy of keys and pointers | Can consume nearly as much disk space as the original table |
| **Slower Writes** | Every `INSERT`, `UPDATE`, or `DELETE` must update both the main table **and** every index on it | A single write on a heavily indexed table can trigger multiple disk writes |
| **Memory Pressure** | Hot index pages compete for space in the buffer pool | Modern buffer pools mitigate this well, so memory impact is often overstated—still, avoid unused indexes |

**When Indexes Hurt More Than They Help:**
* **Write-heavy, rarely read tables:** E.g., an append-heavy logging table where new rows stream in constantly and old rows are seldom queried.
* **Tiny tables:** On tables with only a few hundred rows, traversing an index structure can cost more than a simple sequential table scan.

---

## 2. B-Tree Indexes

**B-tree indexes** are the most common database index type. A B-tree is a **self-balancing tree** that keeps keys in sorted order and supports fast searches, insertions, and deletions while minimizing disk reads.

Unlike binary trees (at most 2 children per node), B-tree nodes have a high branching factor—typically **hundreds of children** in practice. Each node holds an ordered array of keys and pointers sized to fit on a **single disk page** (typically `8 KB`), maximizing I/O efficiency.

```mermaid
flowchart TD
    ROOT["20  |  50  |  90"]
    L1["5  |  8  |  10"]
    L2["35"]
    L3["55  |  67"]
    L4["91  |  100  |  102"]
    NOTE["Each node is sized to fit on\na single disk page (typically 8 KB)"]

    ROOT -->|"< 20"| L1
    ROOT -->|"20 – 50"| L2
    ROOT -->|"50 – 90"| L3
    ROOT -->|"> 90"| L4
    L4 -.- NOTE

    style ROOT fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#0f172a
    style L1 fill:#e2e8f0,stroke:#334155,stroke-width:1.5px,color:#0f172a
    style L2 fill:#e2e8f0,stroke:#334155,stroke-width:1.5px,color:#0f172a
    style L3 fill:#e2e8f0,stroke:#334155,stroke-width:1.5px,color:#0f172a
    style L4 fill:#e2e8f0,stroke:#334155,stroke-width:1.5px,color:#0f172a
    style NOTE fill:#fef3c7,stroke:#d97706,stroke-width:1.5px,color:#78350f
```

### Core B-Tree Invariants
Every node in a B-tree of order `m` enforces four strict rules:
1. All leaf nodes sit at the **exact same depth**.
2. Each non-root node contains between `m/2` and `m` keys.
3. An internal node with `k` keys has exactly `k + 1` children.
4. Keys within every node are stored in **sorted order**.

### B-Trees Across Modern Databases
* **PostgreSQL:** Uses B-trees for almost everything—primary keys, `UNIQUE` constraints, and default indexes:
  ```sql
  CREATE TABLE users (
      id    SERIAL PRIMARY KEY,
      email VARCHAR(255) UNIQUE
  );
  -- Automatically creates two B-tree indexes (id and email)
  ```
* **MongoDB:** Uses B-trees (specifically **B+ trees**, where record pointers live in the leaf nodes) for its indexes:
  ```javascript
  db.users.createIndex({ "email": 1 });
  ```
* **DynamoDB Note:** Organizes items within a partition in sort-key order for fast range queries, though its underlying storage engine is widely understood to use an **LSM-style** architecture rather than a B-tree.

### Why B-Trees Are the Default Choice
* **Sorted order:** Makes range queries (`BETWEEN`, `>`) and `ORDER BY` operations naturally fast.
* **Self-balancing:** Guarantees predictable `O(log n)` depth even as data grows or receives random inserts and deletes.
* **Disk-friendly I/O:** High fan-out + disk-page-aligned nodes minimize random disk reads.
* **Versatile:** Handles both equality lookups (`email = 'x'`) and range scans (`age > 25`) well.

> **Interview Default:** If you need to pick an index in an interview and aren't sure, **B-tree is almost always the right default**.

---

## 3. LSM Trees (Log-Structured Merge Trees)

B-trees shine on balanced and read-heavy workloads, but what if you're building a system like **Datadog** ingesting **100,000+ writes per second**? Updating a B-tree in place requires finding the target leaf page, reading it into memory, modifying it, and writing it back—turning heavy write traffic into a storm of random disk seeks (like constantly hunting down scattered folders in a filing cabinet).

**LSM trees** solve this with an **append-only** design that turns many small random writes into fewer large **sequential disk writes**.

> **Important Distinction vs. B-Trees:** With B-trees, each index is an independent structure you can attach to any column. An **LSM tree** is the **storage format for the entire table**, sorted by primary key. Primary-key lookups are fast, while secondary indexes require extra structures (such as Cassandra secondary indexes or DynamoDB GSIs/LSIs) with different performance characteristics.

### How LSM Trees Work

```mermaid
flowchart TD
    W["Incoming Write"]

    subgraph LEFT["Disk — 1. Durability Log"]
        WAL(("WAL\nWrite-Ahead Log\n(Sequential Append)"))
    end

    subgraph RIGHT["RAM → Disk — 2 & 3. Buffer & Flush Path"]
        direction TB
        subgraph RAM["RAM (In-Memory)"]
            MEM["Memtable\n(Sorted Red-Black Tree / Skip List)"]
        end
        subgraph SST["Disk — SSTable (Immutable Sorted Segments)"]
            direction TB
            S1["Segment 1:  evan:2  |  joe:4  |  sam:8  |  tom:12"]
            S2["Segment 2:  jan:3   |  jen:9  |  john:10 |  sri:12"]
        end
        MEM -->|"3. Flush when full\n(Large sequential write)"| SST
    end

    W -->|"1. Sequential append\n(Durability)"| WAL
    W -->|"2. Fast in-memory insert"| MEM

    style WAL fill:#fee2e2,stroke:#dc2626,stroke-width:2px,color:#7f1d1d
    style MEM fill:#dcfce7,stroke:#16a34a,stroke-width:2px,color:#14532d
    style S1 fill:#dbeafe,stroke:#2563eb,stroke-width:1.5px,color:#0f172a
    style S2 fill:#dbeafe,stroke:#2563eb,stroke-width:1.5px,color:#0f172a
```

1. **Memtable (RAM):** Incoming writes go straight into an in-memory sorted structure (typically a red-black tree or skip list)—ultra-fast because it stays entirely in memory.
2. **Write-Ahead Log / WAL (Disk):** Simultaneously, every write is sequentially appended to an on-disk WAL for crash durability—far faster than random page updates.
3. **Flush to SSTable (Disk):** Once the memtable reaches a size threshold (e.g., a few MBs), it is frozen and flushed in one sequential write as an immutable **Sorted String Table (SSTable)**.
4. **Compaction (Background):** Over time, many SSTable segments accumulate. Background compaction periodically merges segments, discarding overwritten values and tombstoned deletes to keep file counts low and reads fast.

### Read Overhead & Optimizations
Because data may live in any layer, a point lookup must check in order:
1. Active **Memtable**
2. Any **immutable memtables** waiting to flush
3. On-disk **SSTables** from newest to oldest

To prevent point queries from reading dozens of files on disk, LSM engines rely on three key optimizations:
* **Bloom Filters:** A compact probabilistic structure attached to each SSTable that answers *"definitely NOT here"* (allowing instant skips) or *"maybe here"* (triggering a read).
* **Sparse Indexes:** Because SSTables are sorted, a sparse index tracks key ranges per block—so searching for `user_id = 500` completely skips an SSTable covering keys `1000–2000`.
* **Compaction Strategies:**
  * **Size-Tiered Compaction:** Minimizes write amplification, but leaves more overlapping SSTables to check on reads.
  * **Leveled Compaction:** Keeps fewer non-overlapping files (faster reads), at the cost of more frequent background rewrites.

### B-Tree vs. LSM Tree

| Dimension | B-Tree | LSM Tree |
| :--- | :--- | :--- |
| **Write Pattern** | In-place random page updates | Append-only sequential writes (WAL + Memtable flush) |
| **Write Throughput** | Moderate (random disk seeks at extreme scale) | **Very High** (sequential I/O) |
| **Read Latency** | **Predictable & Fast** (single tree traversal) | Slightly higher / variable (may check multiple SSTables) |
| **Best Use Cases** | User-facing OLTP apps, read-heavy or balanced workloads | Time-series, metrics (Datadog), IoT, audit logs, write-heavy ingestion |
| **Real-World Engines** | PostgreSQL, MySQL (InnoDB), MongoDB | **Cassandra** (Netflix viewing events), **RocksDB** (Facebook social interactions) |

---

## 4. Hash Indexes

**Hash indexes** are persistent hashmaps that map indexed keys directly to disk row locations, trading query flexibility for **`O(1)` exact-match lookups**.

```mermaid
flowchart LR
    K1["john@test.com"]
    K2["tim@test.com"]

    subgraph HF["Hash Function"]
        direction TB
        H2["h('tim@test.com')"]
        H1["h('john@test.com')"]
    end

    subgraph HT["Hash Table (Buckets)"]
        direction TB
        B1["Key: d5f43...  |  Value: ptr"]
        B2["Key: 2e2bd...  |  Value: ptr"]
    end

    D1["Disk page with full\nrecord of tim"]
    D2["Disk page with full\nrecord of john"]

    K2 --> H2 --> B1 --> D1
    K1 --> H1 --> B2 --> D2

    style K1 fill:#e2e8f0,stroke:#334155,stroke-width:1.5px,color:#0f172a
    style K2 fill:#e2e8f0,stroke:#334155,stroke-width:1.5px,color:#0f172a
    style HF fill:none,stroke:#64748b,stroke-width:1.5px,stroke-dasharray: 5 5
    style H1 fill:#fef3c7,stroke:#d97706,stroke-width:1.5px,color:#78350f
    style H2 fill:#fef3c7,stroke:#d97706,stroke-width:1.5px,color:#78350f
    style B1 fill:#dbeafe,stroke:#2563eb,stroke-width:1.5px,color:#0f172a
    style B2 fill:#dbeafe,stroke:#2563eb,stroke-width:1.5px,color:#0f172a
    style D1 fill:#dcfce7,stroke:#16a34a,stroke-width:1.5px,color:#14532d
    style D2 fill:#dcfce7,stroke:#16a34a,stroke-width:1.5px,color:#14532d
```

* **How it works:** Hash the indexed value to locate its bucket, then follow the pointer to the row's disk page. Hash collisions are typically handled via chaining or overflow buckets.
* **Major limitation:** Completely **useless for range queries or sorting**, because similar keys hash to scattered buckets.
* **Real-world usage:** Rare on disk—PostgreSQL supports hash indexes, yet defaults to B-trees because B-trees handle equality nearly as fast while also supporting ranges and ordering. Hash indexes shine in **in-memory** engines such as **Redis** and MySQL's `MEMORY` storage engine.
* **When to choose in interviews:** Only when you need ultra-fast **in-memory exact-match lookups**, will *never* need range queries or sorting, and have ample RAM.

---

## 5. Geospatial Indexes

### The Challenge With Location Data
Suppose you are building a restaurant discovery app like **Yelp** and need to answer: *"Find restaurants within 5 miles of me."*

A naive approach creates two separate B-tree indexes on `latitude` and `longitude`:

```sql
CREATE TABLE restaurants (
    id        SERIAL PRIMARY KEY,
    name      VARCHAR(255),
    latitude  DECIMAL(10, 8),
    longitude DECIMAL(11, 8)
);

CREATE INDEX idx_lat ON restaurants(latitude);
CREATE INDEX idx_lng ON restaurants(longitude);
```

This breaks down immediately in 2D space:

```mermaid
flowchart TD
    Q["Query: Restaurants within 5 miles\n(lat BETWEEN a AND b) AND (lng BETWEEN c AND d)"]
    Q --> LAT["idx_lat Scan\nHuge horizontal strip spanning\nthe entire globe at that latitude"]
    Q --> LNG["idx_lng Scan\nHuge vertical strip spanning\npole-to-pole at that longitude"]
    LAT --> INT["Expensive Index Intersection\n(or point-by-point filtering on 1st set)"]
    LNG --> INT
    INT --> RECT["Rectangular Bounding Box\n(Still larger than circular 5-mile radius —\nrequires extra distance filtering!)"]

    style LAT fill:#fee2e2,stroke:#dc2626,stroke-width:1.5px,color:#7f1d1d
    style LNG fill:#dbeafe,stroke:#2563eb,stroke-width:1.5px,color:#1e3a8a
    style RECT fill:#fef3c7,stroke:#d97706,stroke-width:1.5px,color:#78350f
```

1. **Single-index scan:** Scanning `idx_lat` returns every restaurant across the entire globe in that latitude band; checking longitude for each row becomes expensive point filtering instead of a range scan.
2. **Index intersection:** Merging two massive sets (`idx_lat` ∩ `idx_lng`) is expensive, produces a rectangle larger than your circular radius, and still requires filtering out corner points outside 5 miles.

To query proximity efficiently, you need **2D spatial indexes**: **Geohash**, **Quadtree**, or **R-Tree**.

---

### Geohash
**Geohash** converts a 2D `(latitude, longitude)` point into a **1D Base32 string** that preserves spatial proximity. Recursively subdividing the map grid adds characters—longer strings mean higher precision:

![Geohash spatial grid subdivision](./images/geohash-spatial-index.jpeg)

**Step-by-step mental model:**

```mermaid
flowchart TD
    PT["Input Location: 2D Point (lat, lng)\ne.g., Mission District, San Francisco"]

    subgraph S1["Step 1: Divide World into 4 Cells  →  Pick Cell '2'"]
        direction LR
        G1A["0 (NW)"]
        G1B["1 (NE)"]
        G1C["2 (SW) ★\nPoint is inside!"]
        G1D["3 (SE)"]
    end

    subgraph S2["Step 2: Split Cell '2' into 4 Sub-Cells  →  Append '1' → '21'"]
        direction LR
        G2A["20"]
        G2B["21 ★\nNarrowed down!"]
        G2C["22"]
        G2D["23"]
    end

    subgraph S3["Step 3: Split Cell '21' Again  →  Append '2' → '212'"]
        direction LR
        G3A["210"]
        G3B["211"]
        G3C["212 ★\nNeighborhood level!"]
        G3D["213"]
    end

    BASE32["Real Geohash Uses Base32 (each extra char zooms in further):\n'9q8y' (San Francisco)  →  '9q8yy' (Mission District)  →  '9q8yyk' (City Block)\ne.g., Los Angeles = '9qh16'"]
    BTREE["Why Databases Love This (Standard B-Tree!):\nNearby locations share the same prefix → WHERE geohash LIKE '9q8yyk%'"]

    PT --> S1
    G1C --> S2
    G2B --> S3
    G3C --> BASE32 --> BTREE

    style PT fill:#e2e8f0,stroke:#334155,stroke-width:1.5px,color:#0f172a
    style G1A fill:#e2e8f0,stroke:#64748b,stroke-width:1px,color:#0f172a
    style G1B fill:#e2e8f0,stroke:#64748b,stroke-width:1px,color:#0f172a
    style G1D fill:#e2e8f0,stroke:#64748b,stroke-width:1px,color:#0f172a
    style G1C fill:#fee2e2,stroke:#dc2626,stroke-width:2px,color:#7f1d1d
    style G2A fill:#e2e8f0,stroke:#64748b,stroke-width:1px,color:#0f172a
    style G2C fill:#e2e8f0,stroke:#64748b,stroke-width:1px,color:#0f172a
    style G2D fill:#e2e8f0,stroke:#64748b,stroke-width:1px,color:#0f172a
    style G2B fill:#fee2e2,stroke:#dc2626,stroke-width:2px,color:#7f1d1d
    style G3A fill:#e2e8f0,stroke:#64748b,stroke-width:1px,color:#0f172a
    style G3B fill:#e2e8f0,stroke:#64748b,stroke-width:1px,color:#0f172a
    style G3D fill:#e2e8f0,stroke:#64748b,stroke-width:1px,color:#0f172a
    style G3C fill:#fee2e2,stroke:#dc2626,stroke-width:2px,color:#7f1d1d
    style BASE32 fill:#fef3c7,stroke:#d97706,stroke-width:1.5px,color:#78350f
    style BTREE fill:#dcfce7,stroke:#16a34a,stroke-width:2px,color:#14532d
```

* **Why Geohash is so popular:** Nearby points usually share the same prefix (`9q8yyk...` vs `9q8yym...`). Once encoded as strings, you can index them with a **standard B-tree**—turning 2D proximity search into fast string prefix range scans (plus querying the 8 neighboring cells for radius searches spanning cell borders).
* **Boundary limitation:** Two places right across a street that falls on a major grid boundary can have completely different prefixes. Querying adjacent cells handles most cases, and for most applications this edge case is minor.

---

### Quadtree
Instead of encoding coordinates into strings, a **Quadtree** directly partitions 2D space by recursively splitting any region into **four equal quadrants** whenever it exceeds a point-count threshold `k` (typically `4–8`; `k = 5` below).

![Quadtree 2D space partitioning and tree structure](./images/quadtree-spatial-index.jpeg)

**How the 2D splits map to the tree (`k = 5`):**

```mermaid
flowchart TD
    ROOT["Full Map Region\nRule: Max k = 5 points per quadrant — if > 5, split into 4 equal quarters!"]

    NW["NW Quadrant (Sparse)\n3 points ≤ 5\n✓ STOP (Leaf)"]
    SW["SW Quadrant (Sparse)\n2 points ≤ 5\n✓ STOP (Leaf)"]
    NE["NE Quadrant (Dense City!)\n17 points > 5\n⚡ SPLIT INTO 4 QUARTERS"]
    SE["SE Quadrant (Sparse)\n4 points ≤ 5\n✓ STOP (Leaf)"]

    NE_NW["NE-NW\n3 pts ≤ 5\n✓ Leaf"]
    NE_SW["NE-SW (Downtown!)\n7 pts > 5\n⚡ SPLIT AGAIN"]
    NE_NE["NE-NE\n4 pts ≤ 5\n✓ Leaf"]
    NE_SE["NE-SE\n3 pts ≤ 5\n✓ Leaf"]

    L1["2 pts\n✓ Leaf"]
    L2["2 pts\n✓ Leaf"]
    L3["1 pt\n✓ Leaf"]
    L4["2 pts\n✓ Leaf"]

    ROOT --> NW
    ROOT --> SW
    ROOT --> NE
    ROOT --> SE

    NE --> NE_NW
    NE --> NE_SW
    NE --> NE_NE
    NE --> NE_SE

    NE_SW --> L1
    NE_SW --> L2
    NE_SW --> L3
    NE_SW --> L4

    style ROOT fill:#e2e8f0,stroke:#334155,stroke-width:2px,color:#0f172a
    style NW fill:#fee2e2,stroke:#dc2626,stroke-width:1.5px,color:#7f1d1d
    style SW fill:#dcfce7,stroke:#16a34a,stroke-width:1.5px,color:#14532d
    style SE fill:#fef3c7,stroke:#d97706,stroke-width:1.5px,color:#78350f
    style NE fill:#bfdbfe,stroke:#2563eb,stroke-width:2px,color:#0f172a
    style NE_SW fill:#bfdbfe,stroke:#2563eb,stroke-width:2px,color:#0f172a
    style NE_NW fill:#dbeafe,stroke:#2563eb,stroke-width:1.5px,color:#0f172a
    style NE_NE fill:#dbeafe,stroke:#2563eb,stroke-width:1.5px,color:#0f172a
    style NE_SE fill:#dbeafe,stroke:#2563eb,stroke-width:1.5px,color:#0f172a
    style L1 fill:#dbeafe,stroke:#2563eb,stroke-width:1.5px,color:#0f172a
    style L2 fill:#dbeafe,stroke:#2563eb,stroke-width:1.5px,color:#0f172a
    style L3 fill:#dbeafe,stroke:#2563eb,stroke-width:1.5px,color:#0f172a
    style L4 fill:#dbeafe,stroke:#2563eb,stroke-width:1.5px,color:#0f172a
```

* **Adaptive resolution:** Dense urban areas subdivide finely while sparse rural regions remain large quadrants—similar to how **Google Maps** organizes map tiles across zoom levels.
* **Proximity search:** Traverse down to the target quadrant, inspect sibling/neighbor quadrants at the same depth, and walk up or down levels to adjust radius.
* **Trade-off:** Unlike Geohash, Quadtrees require a specialized tree structure rather than reusing standard database B-trees, making them less common inside general-purpose databases today.

---

### R-Tree
**R-Trees** are the **default spatial index in modern production databases** (including **PostgreSQL / PostGIS** and **MySQL**). Instead of splitting space into rigid equal quarters like a Quadtree, R-Trees group nearby objects into **flexible, overlapping Minimum Bounding Rectangles (MBRs)** that adapt to actual data clusters.

![R-Tree overlapping bounding rectangles and tree hierarchy](./images/r-tree-spatial-index.jpeg)

**How the overlapping bounding boxes map to the tree:**

```mermaid
flowchart TD
    ROOT["Root: Top-Level Bounding Boxes  [ Box M  |  Box N ]\nFlexible rectangles wrapping data clusters (M & N may overlap in 2D space!)"]

    IL["Inside Box M — Tighter Bounding Boxes\n[ Box I  |  Box L ]"]
    JK["Inside Box N — Tighter Bounding Boxes\n[ Box J  |  Box K ]"]

    FEG["Leaf Objects: F, E, G\n(Clustered nearby shapes)"]
    H["Leaf Object: H\n(Standalone shape)"]
    BC["Leaf Objects: B, C\n(Overlapping shapes)"]
    AD["Leaf Objects: A, D\n(Points / polygons / roads)"]

    TIP["How a Search Works:\nTest which bounding boxes contain your query area →\nonly descend into matching branches (skip non-overlapping boxes!)"]

    ROOT -->|"Region M"| IL
    ROOT -->|"Region N"| JK
    IL -->|"Box I"| FEG
    IL -->|"Box L"| H
    JK -->|"Box J"| BC
    JK -->|"Box K"| AD
    AD -.- TIP

    style ROOT fill:#fee2e2,stroke:#dc2626,stroke-width:2px,color:#7f1d1d
    style IL fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#0f172a
    style JK fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#0f172a
    style FEG fill:#e2e8f0,stroke:#334155,stroke-width:1.5px,color:#0f172a
    style H fill:#e2e8f0,stroke:#334155,stroke-width:1.5px,color:#0f172a
    style BC fill:#e2e8f0,stroke:#334155,stroke-width:1.5px,color:#0f172a
    style AD fill:#e2e8f0,stroke:#334155,stroke-width:1.5px,color:#0f172a
    style TIP fill:#fef3c7,stroke:#d97706,stroke-width:1.5px,color:#78350f
```

* **Supports points AND complex shapes:** A single R-Tree can index restaurant coordinates, delivery-zone polygons, and road networks together—each bounding rectangle expands to fit whatever shapes it holds.
* **Trade-off:** Because bounding rectangles can overlap (`M` and `N` may share spatial area), a query sometimes has to search multiple branches. Modern R-Tree variants optimize overlap vs. tree depth specifically for disk page reads.

### Geospatial Indexes Comparison

| Approach | How Space Is Divided | Works on Standard B-Tree? | Strengths | Weaknesses |
| :--- | :--- | :--- | :--- | :--- |
| **Geohash** | 1D Base32 prefix strings over recursive grid | **Yes** (prefix range scans) | Simple to implement & explain; leverages existing B-trees | Grid boundary edge cases; points only |
| **Quadtree** | Rigid recursive 4-way quadrant splits | No (custom tree) | Adaptive resolution for uneven density; great for map tiles | Specialized structure; less native DB support |
| **R-Tree** | Flexible, overlapping bounding rectangles | No (native in PostGIS/MySQL) | **Production default**; indexes points, polygons, and lines | Overlapping boxes can trigger multi-branch scans |

---

## 6. Inverted Indexes

B-trees fail when you need **full-text search** matching words anywhere inside a text column:

```sql
SELECT * FROM posts
WHERE content LIKE '%database%';
```

A B-tree can only help with prefix matches (`'database%'`) or suffix matches (on a reversed column). Searching for a substring anywhere in the text forces a full scan checking every character of every document.

An **inverted index** solves this by flipping the mapping from **Document → Words** to **Word → List of Documents** (just like the index at the back of a textbook).

**Example Documents:**
```text
doc1: "B-trees are fast and reliable"
doc2: "Hash tables are fast but limited"
doc3: "B-trees handle range queries well"
```

**Resulting Inverted Index:**
```text
b-trees  → [doc1, doc3]
fast     → [doc1, doc2]
reliable → [doc1]
hash     → [doc2]
tables   → [doc2]
limited  → [doc2]
handle   → [doc3]
range    → [doc3]
queries  → [doc3]
```

### Production Analysis Pipeline (Elasticsearch / Lucene), Full-Text-Search (FTS) index pipeline in Couchbase is similar

```mermaid
flowchart TD
    DOC["Raw Document Text\n'The Databases are fast!'"]
    TOK["1. Tokenization (Split into words)\n['The', 'Databases', 'are', 'fast']"]
    LOW["2. Lowercasing (Normalize case)\n['the', 'databases', 'are', 'fast']"]
    STOP["3. Stop-Word Removal (Drop filler words)\n['databases', 'fast']"]
    STEM["4. Stemming (Reduce to root form)\n['databas', 'fast']"]
    INV["Inverted Index (Term → Matching Documents)\ndatabas  →  [doc1, doc4]\nfast     →  [doc1, doc2]"]

    DOC --> TOK --> LOW --> STOP --> STEM --> INV

    style DOC fill:#e2e8f0,stroke:#334155,stroke-width:1.5px,color:#0f172a
    style TOK fill:#f1f5f9,stroke:#475569,stroke-width:1.5px,color:#0f172a
    style LOW fill:#f1f5f9,stroke:#475569,stroke-width:1.5px,color:#0f172a
    style STOP fill:#f1f5f9,stroke:#475569,stroke-width:1.5px,color:#0f172a
    style STEM fill:#fef3c7,stroke:#d97706,stroke-width:1.5px,color:#78350f
    style INV fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#0f172a
```

Because of this analysis pipeline, searching `"Databases"` seamlessly matches `"database"`, `"DATABASE"`, and `"database's"`. Production search engines (**Elasticsearch**, **Apache Lucene**) layer additional capabilities on top:
* **Term frequency analysis** (how often words appear)
* **Relevance scoring** (ranking the best matching documents, e.g., BM25)
* **Fuzzy matching** (handling typos like `"databas"`)
* **Phrase queries** (matching exact ordered word sequences)

**Real-world usage:** GitHub repository search, Slack message history search, documentation search.

> **Trade-Offs:** Inverted indexes carry substantial storage overhead and expensive write updates—whenever a document is edited, postings lists must be updated for **every term** inside that document.

---

## 7. Index Optimization Patterns

### Composite Indexes
The most common real-world optimization pattern: combining **multiple columns into a single ordered index** to match multi-condition queries.

Consider a classic social media feed query:
```sql
SELECT * FROM posts
WHERE user_id = 123
  AND created_at > '2024-01-01'
ORDER BY created_at DESC;
```

Two separate single-column indexes (`idx_user` and `idx_time`) force the database to scan both indexes, intersect the two result sets, and then sort by `created_at`. A single **composite index** handles filtering **and** sorting in one contiguous scan:

```sql
CREATE INDEX idx_user_time ON posts(user_id, created_at);
```

```mermaid
flowchart TD
    ROOT["Composite B-Tree Root\n(120, 130)"]

    L1["Leaf 1 (user_id < 120)\n(101, 2024-01-01)\n(101, 2024-01-02)\n(119, 2024-01-01)\n(119, 2024-01-03)"]

    subgraph L2["Leaf 2 (user_id >= 120)"]
        direction TB
        E1["(122, 2024-01-01)"]
        MATCH["MATCHING RANGE SCAN:\n(123, 2024-01-02)\n(123, 2024-01-03)"]
        E2["(125, 2024-01-01)"]
    end

    ROOT -->|"< 120"| L1
    ROOT -->|">= 120"| L2

    style ROOT fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#1e3a8a
    style L1 fill:#dcfce7,stroke:#16a34a,stroke-width:1.5px,color:#14532d
    style MATCH fill:#fef08a,stroke:#ca8a04,stroke-width:2px,stroke-dasharray: 5 5,color:#713f12
    style E1 fill:#dcfce7,stroke:#16a34a,stroke-width:1px,color:#14532d
    style E2 fill:#dcfce7,stroke:#16a34a,stroke-width:1px,color:#14532d
```

* Entries are sorted by `user_id` first, then by `created_at` within each `user_id`.
* For `user_id = 123`, the engine jumps directly to the highlighted slice and scans sequentially—**both filtering and sorting happen in a single index scan**.

> **Column Order Matters (Leftmost Prefix Rule):** An index on `(user_id, created_at)` accelerates queries filtering on `user_id` alone or `(user_id, created_at)`, **not** queries filtering on `created_at` alone. A common rule of thumb is to **order columns from most selective to least selective** (and equality columns before range columns).

---

### Covering Indexes
A **covering index** includes all columns required to satisfy a query—not just the filter/sort keys—so the database can answer the query purely from the index (**index-only scan**) without fetching full rows from the main table on disk.

```sql
CREATE TABLE posts (
    id         SERIAL PRIMARY KEY,
    user_id    INT,
    title      TEXT,
    content    TEXT,
    likes      INT,
    created_at TIMESTAMP
);

-- Regular composite index (must still fetch table pages to read 'likes')
CREATE INDEX idx_user_time ON posts(user_id, created_at);

-- Covering index: stores 'likes' in the index leaf so table lookups are skipped
CREATE INDEX idx_user_time_likes ON posts(user_id, created_at) INCLUDE (likes);
```

*(Shown in SQL here, though the exact same principle applies across NoSQL databases as well.)*

* **When it pays off:** High-traffic, read-heavy queries fetching a small subset of columns from wide tables (e.g., social feeds showing timestamps + like counts, or leaderboards).
* **Trade-off & interview reality:** Covering indexes are larger and add write/maintenance overhead, making them a **niche optimization** rather than a default choice. In interviews, prioritize clean primary and composite indexes first—reach for covering indexes only when you can clearly justify avoiding main-table lookups.

---

## 8. Interview Wrap-Up & Decision Guide

Use this decision flow whenever an interviewer asks how you would index a table or query pattern:

```mermaid
flowchart TD
    START{"Need efficient\ndata access?"}
    START -->|"No"| FTS["Full Table Scan"]
    START -->|"Yes"| SIZE{"Table size\n> 10k rows?"}
    SIZE -->|"No"| FTS
    SIZE -->|"Yes"| TYPE{"What type of data\nare you querying?"}

    TYPE -->|"Full-text search"| INV["Inverted Index"]
    TYPE -->|"Location data"| GEO["Geospatial Index"]
    TYPE -->|"In-memory exact matches"| HASH["Hash Index"]
    TYPE -->|"Everything else"| BTREE["B-Tree"]

    BTREE --> COMP{"Multiple columns\nqueried together?"}
    BTREE --> COV{"Heavy reads on\nfew columns?"}

    COMP -->|"Yes"| USE_COMP["Consider Composite Index"]
    COV -->|"Yes"| USE_COV["Consider Covering Index"]

    style FTS fill:#fee2e2,stroke:#dc2626,stroke-width:2px,color:#7f1d1d
    style INV fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#1e3a8a
    style GEO fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#1e3a8a
    style HASH fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#1e3a8a
    style BTREE fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#1e3a8a
    style USE_COMP fill:#dcfce7,stroke:#16a34a,stroke-width:2px,color:#14532d
    style USE_COV fill:#dcfce7,stroke:#16a34a,stroke-width:2px,color:#14532d
```

### Key Takeaways to Remember
* **When in doubt, pick B-Trees:** They are the Swiss Army knife of indexes, handling equality lookups, range scans, and sorting reliably.
* **Extreme write throughput:** Call out **LSM-tree** storage engines (Cassandra, RocksDB) when ingestion volume makes random B-tree page updates a bottleneck.
* **Location / proximity queries:** Reach for a **Geospatial Index**—at minimum know **Geohash** (and even better, contrast it with **Quadtrees** and **R-Trees**).
* **Full-text search:** Reach for an **Inverted Index** (e.g., Elasticsearch / Lucene).

---

**Previous:** [← Data Modeling](./Data-Modeling.md) | **Index:** [HLD Index](./HLD.md)
