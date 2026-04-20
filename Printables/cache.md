# CACHE (HLD QUICK REFERENCE)

## CORE PURPOSE

* reduce latency, reduce DB load
* serve frequently accessed data from fast storage (memory)

---

## TYPES (BY LOCATION)

### Client-side

* browser cache
* controlled via HTTP headers (Cache-Control, ETag)

---

### CDN / Edge Cache

* caches static content at edge locations
* reduces latency globally

**Tech:**

* Cloudflare
* Akamai
* Amazon CloudFront

---

### Application Cache (in-memory)

* inside app process
* fastest, but not shared across instances

---

### Distributed Cache

* shared cache across services
* external system (network call)

**Tech:**

* Redis
* Memcached

---

## CORE PATTERNS (EXHAUSTIVE)

### Cache-Aside (Lazy Loading)

* flow:

  * read → cache miss → DB → populate cache
* write:

  * DB update → invalidate cache

Traits:

* simple, most common
* stale data possible

---

### Write-Through

* write goes to cache → cache writes to DB
* read always from cache

Traits:

* cache always consistent
* higher write latency

---

### Write-Back (Write-Behind)

* write to cache only
* async flush to DB

Traits:

* very fast writes
* risk of data loss on crash

---

### Read-Through

* cache itself fetches from DB on miss
* app only talks to cache

Traits:

* abstraction, cleaner app code

---

### Refresh-Ahead

* cache proactively refreshes before expiry

Traits:

* avoids cache miss spikes

---

## DATA MANAGEMENT

### TTL (Time To Live)

* auto expiry after time
* prevents stale data

---

### Eviction Policies

* LRU (Least Recently Used)
* LFU (Least Frequently Used)
* FIFO
* Random

Used in:

* Redis
* Memcached

---

## INTERNAL WORKING (IMPORTANT)

### Redis

* in-memory storage
* single-threaded event loop
* uses:

  * hash table for key lookup
  * data structures (skip list for sorted set)
* persistence:

  * RDB (snapshot)
  * AOF (log)
* clustering:

  * hash slots (16384)
* replication:

  * async leader-follower

---

### Memcached

* pure KV (string → blob)
* multi-threaded
* slab allocator (memory chunks)
* no persistence
* client-side sharding

Traits:

* simpler, faster for basic use
* no advanced data structures

---

## DISTRIBUTION

### Sharding

* Client-side hashing
* Consistent hashing

  * minimal rebalancing
  * used widely in cache clusters

---

### Replication

* master-replica (Redis)
* improves read throughput
* provides failover

---

## CONSISTENCY

* eventual consistency (common)
* strong consistency only with write-through / sync patterns

---

## CACHE KEYS

* must be:

  * unique
  * predictable
  * small

Common strategy:

* user:123:profile
* product:456

---

## CACHE INVALIDATION (CRITICAL)

### Strategies

* Time-based (TTL)
* Event-based (on write/delete)
* Versioning (key version change)

---

## FAILURE PATTERNS (IMPORTANT)

* Cache miss storm

  * many requests hit DB simultaneously

* Cache stampede

  * popular key expires → spike

* Thundering herd

  * concurrent rebuild of same key

* Stale data

  * delayed invalidation

* Hot keys

  * single key overloads node

* Cache penetration

  * repeated requests for non-existent keys

---

## OPTIMIZATION TECHNIQUES

* request coalescing (single fetch per key)
* jitter in TTL (avoid simultaneous expiry)
* warm-up cache (preload)
* use Bloom filter (prevent penetration)

---

## WHEN TO USE WHAT (FAST RECALL)

* Redis → advanced caching, counters, distributed systems
* Memcached → simple, high-speed KV cache
* CDN → static + global distribution
* in-process cache → ultra low latency, local only

---
