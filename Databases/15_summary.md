# Databases — Quick Mapping (Tight + Interview Memory)

> Category → Core idea → Then concrete DBs with **distinct internals + traits**

---

## 1. Relational DB (SQL)

* **Core Idea:** Tables + schema, ACID, joins
* **Internals:** B-Tree indexes, WAL, query planner
* **Use Cases:** Transactions, strong consistency

### MySQL

* **Internals:** InnoDB (B+Tree, row-level locking, WAL)
* **Trait:** High read performance, widely used OLTP

### PostgreSQL

* **Internals:** MVCC (no read locks), advanced planner
* **Trait:** Better concurrency + complex queries

---

## 2. Key-Value Store

* **Core Idea:** Simple GET/SET, key → value
* **Internals:** Hash maps, in-memory, optional persistence
* **Use Cases:** Cache, sessions, rate limiting

### Redis

* **Internals:** Single-threaded event loop, in-memory + AOF/RDB
* **Trait:** Ultra-low latency, rich data structures

---

## 3. Document DB

* **Core Idea:** JSON-like flexible schema
* **Internals:** Document storage + secondary indexes
* **Use Cases:** Evolving schema, user/content data

### MongoDB

* **Internals:** BSON docs, replication, sharding
* **Trait:** Schema flexibility + horizontal scaling

---

## 4. Wide-Column DB

* **Core Idea:** Column families, sparse rows
* **Internals:** LSM Trees, SSTables, write-optimized
* **Use Cases:** High write throughput, large-scale data

### Apache Cassandra

* **Internals:** LSM + consistent hashing + no master
* **Trait:** Linear scalability, eventual consistency

### Apache HBase

* **Internals:** HDFS-backed, LSM + region servers
* **Trait:** Strong consistency (per row), Hadoop ecosystem

---

## 5. Search Engine

* **Core Idea:** Full-text search
* **Internals:** Inverted index
* **Use Cases:** Search, log analytics

### Elasticsearch

* **Internals:** Distributed inverted index + shards
* **Trait:** Fast search + aggregation

---

## 6. Graph DB

* **Core Idea:** Nodes + edges (relationships first-class)
* **Internals:** Adjacency list, index-free traversal
* **Use Cases:** Social graphs, recommendations

### Neo4j

* **Internals:** Native graph storage + traversal engine
* **Trait:** Efficient deep relationship queries

---

## 7. Time-Series DB

* **Core Idea:** Time-indexed data
* **Internals:** Append-heavy, compression, LSM-like
* **Use Cases:** Metrics, monitoring, IoT

### InfluxDB

* **Internals:** Time-structured storage + compression
* **Trait:** Optimized for high-ingest + time queries

---

## 🔥 Ultra-Short Mapping

* SQL → MySQL/Postgres → transactions
* KV → Redis → speed
* Doc → Mongo → flexibility
* Wide-column → Cassandra/HBase → scale writes
* Search → Elasticsearch → text queries
* Graph → Neo4j → relationships
* Time-series → InfluxDB → metrics

---

## One-Line Summary

> Pick DB based on access pattern: transactions, speed, flexibility, write scale, search, relationships, or time-based data.

---
