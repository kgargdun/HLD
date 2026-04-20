# CAPACITY ESTIMATION (HLD – MAKE OR BREAK)

---

## 1. WHAT THIS IS

* Back-of-the-envelope calculation (BOTEC)
* Goal: estimate **RPS, storage, bandwidth, servers**
* Not exact → order of magnitude correct is enough 

---

## 2. STANDARD UNITS (USE CONSISTENTLY)

* 1 KB = 10³ bytes

* 1 MB = 10⁶ bytes

* 1 GB = 10⁹ bytes

* 1 TB = 10¹² bytes

* 1 ms = 10⁻³ s

* 1 μs = 10⁻⁶ s

* 1 ns = 10⁻⁹ s

* 1 Gbps = 10⁹ bits/sec ≈ 125 MB/s

---

## 3. TYPICAL SERVER BASELINE

| Component | Value      |
| --------- | ---------- |
| CPU       | 64 cores   |
| RAM       | 128–256 GB |
| Disk      | 1–16 TB    |
| Network   | 1–10 Gbps  |

---

## 4. LATENCY NUMBERS (MEMORIZE ORDER, NOT EXACT)

| Operation        | Time       |
| ---------------- | ---------- |
| L1 cache         | ~1 ns      |
| L2 cache         | ~3 ns      |
| L3 cache         | ~10 ns     |
| RAM              | ~100 ns    |
| Read 1 MB RAM    | ~10 μs     |
| SSD read         | ~200 μs    |
| Disk seek        | ~4 ms      |
| Datacenter RTT   | ~0.5 ms    |
| Inter-region RTT | ~50–100 ms |

👉 Key intuition:

* Memory ~100x slower than CPU
* Disk/Network ~1000x slower

---

## 5. THROUGHPUT (VERY IMPORTANT)

| System         | QPS per node    |
| -------------- | --------------- |
| Cache (Redis)  | 100K – 1M       |
| KV store       | ~10K            |
| SQL DB         | ~1K             |
| Web/App server | ~10K–50K        |
| Load Balancer  | ~100K+          |
| Kafka broker   | ~100K+ msgs/sec |

---

## 6. REQUEST TYPES (IMP FOR BOTEC)

| Type         | Bottleneck   | Example        |
| ------------ | ------------ | -------------- |
| CPU-bound    | compute      | encryption     |
| Memory-bound | RAM          | in-memory read |
| IO-bound     | disk/network | DB query       |

Approx:

* CPU = X
* Memory = 10X
* IO = 100X 

---

## 7. RPS ESTIMATION

### Step 1: Users → Requests

* Daily Active Users (DAU)
* Requests per user per day

```
Total Requests / day = DAU × req/user
RPS = total / (24 × 3600)
Peak RPS = avg × 2–5
```

---

### Step 2: Example

* DAU = 10M
* 10 req/user/day

→ 100M req/day
→ ~1.2K RPS avg
→ ~3–5K RPS peak

---

## 8. CPU BASED ESTIMATION (ADVANCED)

From reference 

* ~1 ms per request
  → 1 core = ~1000 RPS
  → 64 cores = ~64K RPS

Reality:

* network + IO reduces this heavily

---

## 9. STORAGE ESTIMATION

### Per record

* Example: user = 1 KB

### Total

```
Total storage = users × size
```

### With growth

```
Daily data = write RPS × size × 86400
```

### With replication

```
Total = raw × replication_factor (usually 3)
```

### With index overhead

* +20–50%

---

### Example

* 10M users × 1 KB = 10 GB
* replication (×3) → 30 GB

---

## 10. BANDWIDTH ESTIMATION

### Formula

```
Bandwidth = RPS × request_size
```

### Example

* 5K RPS × 1 KB
  → 5 MB/s
  → ~40 Mbps

Include response size separately

---

## 11. COMPONENT CAPACITY (VERY IMPORTANT)

---

### LOAD BALANCER

* Handles: 100K–1M RPS
* Mostly network bound
* Stateless

---

### WEB / APP SERVER

* ~10K–50K RPS
* CPU + memory bound
* Scale horizontally

---

### DATABASE (SQL)

* ~1K QPS
* IO bound
* Scaling:

  * read replicas
  * sharding for writes

---

### CACHE (Redis)

* 100K–1M QPS
* memory bound
* used to reduce DB load

---

### MESSAGE QUEUE

**Kafka**

* partitioned log
* throughput: 100K–1M msgs/sec
* sequential disk writes

**RabbitMQ**

* lower throughput (~10K–50K)
* flexible routing

**SQS**

* managed
* virtually unlimited scale

---

### BLOB STORAGE

* large objects (images/videos)
* throughput limited by network
* scalable (S3-like)

---

### NETWORK

* 1 Gbps → 125 MB/s
* often bottleneck at scale

---

## 12. SCALING CALCULATION

### Servers needed

```
servers = total_RPS / per_server_RPS
```

### Example

* 50K RPS / 10K per server
  → 5 servers

Always add:

* 20–30% buffer

---

## 13. READ vs WRITE SPLIT

Typical:

* 80–90% reads
* 10–20% writes

Impacts:

* DB scaling
* cache effectiveness

---

## 14. CACHE IMPACT

If cache hit rate = 90%:

```
DB load = total × (1 - hit_rate)
```

Example:

* 10K RPS → DB sees only 1K

---

## 15. END-TO-END FLOW (INTERVIEW)

1. Estimate users (DAU/MAU)
2. Estimate requests/user
3. Compute RPS (avg + peak)
4. Estimate read/write split
5. Estimate storage
6. Estimate bandwidth
7. Compute per-component load:

   * LB
   * app servers
   * DB
   * cache
8. Decide scaling:

   * replicas
   * sharding
9. Add buffer (20–30%)

---

## 16. COMMON PITFALLS

* ignoring peak traffic
* forgetting replication factor
* ignoring cache hit ratio
* underestimating response size
* assuming DB scales linearly
* ignoring network bottleneck

---

## 17. QUICK MEMORY SNAPSHOT

* DB → ~1K QPS
* KV → ~10K QPS
* Cache → 100K–1M QPS
* 1 core → ~1K RPS
* 1 Gbps → ~125 MB/s
* Peak = 3–5× avg

---

## 18. REALITY

* Numbers vary by workload
* Always state assumptions
* Order-of-magnitude correctness > precision

---
