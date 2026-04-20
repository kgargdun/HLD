# DATABASES (HLD QUICK REFERENCE)

## RELATIONAL (RDBMS)

### MySQL (InnoDB)

* Row-oriented relational DB, strict schema, SQL
* Storage layout:

  * Clustered index → table physically ordered by primary key
  * Secondary indexes store PK reference (extra lookup)
* Engine:

  * InnoDB (default), uses B+ Trees
  * Buffer pool (memory cache for pages)
* Write path:

  * write → redo log (WAL) → buffer pool → flush to disk
* MVCC:

  * old versions via undo logs
  * snapshot reads (non-blocking)
* Locking:

  * row-level + gap locks (phantom protection)
* Replication:

  * binlog (logical log) → async replicas
* Strengths:

  * strong consistency, mature ecosystem
  * transactional workloads (OLTP)
* Weakness:

  * horizontal scaling hard (manual sharding)

---

### PostgreSQL

* Advanced relational DB, strict schema, SQL + extensions
* Storage:

  * heap table (no clustering by default)
  * multiple versions stored inline (MVCC)
* MVCC:

  * each row has xmin/xmax (visibility control)
  * vacuum cleans old versions
* Write path:

  * WAL (Write Ahead Log) → durability guarantee
* Index:

  * B-Tree (default), GIN (inverted), GiST (spatial)
* Query engine:

  * powerful optimizer (cost-based)
* Extensions:

  * TimescaleDB (time-series), PostGIS (geo)
* Strengths:

  * complex queries, analytics + OLTP hybrid
* Weakness:

  * write amplification due to MVCC + vacuum

---

## KEY-VALUE STORES

### Redis

* In-memory data structure store (not just KV)
* Core model:

  * key → value (value = rich structure)
* Execution:

  * single-threaded event loop (no locks, predictable latency)
* Data structures:

  * string, list, set, sorted set (skip list), hash
* Persistence:

  * RDB → periodic snapshot
  * AOF → append-only log (replay)
* Replication:

  * async leader-follower
* Clustering:

  * 16384 hash slots
  * client decides node (no proxy)
* Strengths:

  * extremely low latency (sub-ms)
  * atomic operations (INCR, etc.)
* Weakness:

  * memory bound
  * not durable by default

---

### DynamoDB

* Fully managed distributed KV + document DB
* Data model:

  * table → items (JSON-like)
  * primary key = partition key (+ optional sort key)
* Partitioning:

  * hash(partition key) → partition
  * auto split on scale
* Replication:

  * multi-AZ, internally leaderless
* Consistency:

  * eventual (default)
  * strong (quorum read)
* Index:

  * GSI (different partition key)
  * LSI (same partition key, diff sort key)
* Throughput:

  * provisioned (RCU/WCU)
  * on-demand auto scaling
* Internal traits:

  * consistent hashing + replication (Dynamo paper)
  * quorum reads/writes
* Strengths:

  * infinite scale, no ops
* Weakness:

  * access pattern must be predefined

---

## DOCUMENT STORE

### MongoDB

* Document DB (JSON/BSON), flexible schema
* Storage:

  * BSON documents
  * B-Tree indexes on fields
* Query:

  * supports nested fields, arrays
* Replication:

  * replica set (primary + secondaries)
  * oplog replication
* Sharding:

  * shard key determines distribution
  * mongos acts as router
* Write path:

  * journal (WAL) → memory → disk
* Read:

  * primary (strong) or secondary (eventual)
* Aggregation:

  * pipeline (map-reduce alternative)
* Strengths:

  * flexible schema, developer friendly
* Weakness:

  * joins limited, consistency weaker vs RDBMS

---

## WIDE COLUMN STORES

### Cassandra

* Distributed wide-column store (AP system)
* Data model:

  * partition key → determines node
  * clustering columns → sort within partition
* Storage engine:

  * LSM Tree
  * write → commit log → memtable → SSTables
* Compaction:

  * merges SSTables (removes duplicates)
* Replication:

  * peer-to-peer (no leader)
* Consistency:

  * tunable (ONE, QUORUM, ALL)
* Partitioning:

  * consistent hashing ring
* Read:

  * merge data from multiple SSTables
* Strengths:

  * extremely high write throughput
  * no single point of failure
* Weakness:

  * complex data modeling
  * read amplification

---

### HBase

* Column-family DB on top of HDFS
* Data model:

  * row key → column family → columns
* Storage:

  * LSM (HFiles)
* Architecture:

  * HMaster (coordination)
  * RegionServers (serve partitions)
* Strong consistency (per row)
* Optimized for:

  * large scans, batch workloads
* Strengths:

  * integrates with Hadoop ecosystem
* Weakness:

  * operational complexity

---

## GRAPH DB

### Neo4j

* Native graph DB
* Data model:

  * nodes, relationships, properties
* Storage:

  * direct pointers between nodes (no joins needed)
* Query:

  * Cypher (pattern matching)
* Execution:

  * index-free adjacency (fast traversal)
* Strengths:

  * deep relationship queries (O(k) traversal)
* Weakness:

  * not suited for large-scale distributed writes

---

## TIME-SERIES DB

### InfluxDB

* Time-series optimized DB
* Data model:

  * measurement + tags + timestamp
* Storage:

  * append-only, compressed
* Query:

  * aggregation over time windows
* Features:

  * retention policies
  * continuous queries
* Strengths:

  * high ingestion rate
* Weakness:

  * limited general querying

---

### TimescaleDB

* PostgreSQL extension
* Data model:

  * hypertables (auto partition by time)
* Uses:

  * Postgres engine (MVCC, WAL)
* Benefits:

  * SQL + time-series optimization
* Strength:

  * combines relational + time-series

---

## SEARCH ENGINE

### Elasticsearch

* Distributed search + analytics engine
* Core:

  * inverted index (term → documents)
* Write path:

  * document → analyzer → tokens → index
* Sharding:

  * index split into shards
* Replication:

  * primary + replica shards
* Query:

  * full-text, filters, aggregations
* Ranking:

  * BM25 (term frequency based)
* Refresh:

  * near real-time (~1s delay)
* Strengths:

  * powerful search, analytics
* Weakness:

  * not for transactional workloads

---

## STORAGE ENGINES

### B+ Tree

* sorted tree, balanced
* good for range + point queries

### LSM Tree

* write optimized
* compaction heavy
* read requires merge

---

## DISTRIBUTION

### Replication

* Leader-Follower
* Multi-Leader
* Leaderless (quorum)

### Sharding

* Range
* Hash
* Directory

---

## CONSISTENCY

* Strong
* Eventual
* Tunable (quorum)

CAP:

* CP / AP tradeoff

---

## TRANSACTIONS

### ACID

* Atomicity, Consistency, Isolation, Durability

### BASE

* Eventually consistent systems

---

## INDEXING

* Primary
* Secondary
* Composite
* Inverted

---

## FAILURE PATTERNS

* replication lag
* split brain
* hot partitions
* compaction stalls
* read/write amplification

---
