# RATE LIMITER (HLD QUICK REFERENCE)

## DEFINITION

* Controls number of requests per client (user/IP/API key) over time
* Goals:

  * prevent abuse
  * protect backend
  * ensure fairness

---

## WHERE APPLIED

* API Gateway
* Load Balancer layer
* Application layer (middleware)
* Distributed edge (CDN)

---

## KEY DIMENSIONS

* Identifier: user_id / IP / token
* Limit: requests per window
* Window: time unit (sec/min/hour)
* Scope: per-user / per-API / global

---

## CORE ALGORITHMS (EXHAUSTIVE)

### Fixed Window Counter

* count requests in fixed interval
* reset counter every window

Flow:

* key = user + window_timestamp
* increment counter
* reject if > limit

Traits:

* simple, fast
* burst allowed at boundary (double spike)

---

### Sliding Window Log

* store timestamps of each request
* remove timestamps older than window

Flow:

* list of timestamps per user
* on request:

  * delete old entries
  * check size

Traits:

* accurate
* high memory (stores all requests)

---

### Sliding Window Counter

* approximate sliding window using 2 buckets

Flow:

* current window count + previous window count
* weighted sum based on time overlap

Traits:

* lower memory than log
* slight inaccuracy

---

### Token Bucket

* tokens added at fixed rate
* request consumes 1 token

Flow:

* bucket has capacity C
* refill continuously at rate R
* if tokens available → allow

Traits:

* allows bursts up to capacity
* smooth rate control

---

### Leaky Bucket

* requests processed at fixed rate

Flow:

* queue incoming requests
* process at constant rate
* overflow → drop

Traits:

* no bursts
* steady output rate

---

## COMPARISON (INTUITION)

* Fixed window → simplest, inaccurate at edges
* Sliding log → exact, heavy memory
* Sliding counter → balanced
* Token bucket → best for burst + control
* Leaky bucket → strict smoothing

---

## DISTRIBUTED RATE LIMITING

### Centralized

* single store (Redis)
* simple but bottleneck

---

### Distributed (common in prod)

* multiple nodes share state via KV store

Approaches:

* shared Redis cluster
* sharded counters
* local + global hybrid

Problems:

* race conditions
* network latency
* consistency vs availability

---

## DATA STORAGE

### Redis (most common)

* atomic ops (INCR, EXPIRE)
* sorted sets (for sliding log)
* Lua scripts for atomic multi-step logic

### In-memory (local)

* fastest
* not globally consistent

### Distributed KV

* DynamoDB / Cassandra
* scalable but higher latency

---

## IMPLEMENTATION PATTERNS (EXHAUSTIVE)

### Per-key Counter

* key: user_id
* value: count

---

### Time-bucketed Keys

* key: user_id:timestamp
* auto-expire

---

### Sorted Set (Redis)

* score = timestamp
* remove old entries

---

### Token State Storage

* key: user_id
* value: tokens + last_refill_time

---

### Hybrid (edge + central)

* local limiter (fast reject)
* global limiter (accuracy)

---

## HARD PROBLEMS

* clock synchronization (distributed nodes)
* double counting (race conditions)
* hot keys (popular users)
* burst handling vs fairness
* consistency vs availability

---

## FAILURE CASES

* Redis down → no limiting or fail closed
* network partition → inconsistent limits
* stale counters → over/under limiting
* thundering herd on reset (fixed window)

---

## INDUSTRY SYSTEMS / TOOLS

### NGINX

* built-in rate limiting (leaky bucket)
* config-based
* per-IP limiting

---

### Envoy Proxy

* distributed rate limiting support
* integrates with external rate limit service

---

### Kong API Gateway

* plugins for rate limiting
* supports Redis / DB backend

---

### AWS API Gateway

* built-in throttling
* burst + steady rate
* integrates with usage plans

---

### Cloudflare

* edge rate limiting
* global enforcement at CDN layer

---

## DESIGN CHOICES (FAST RECALL)

* need burst → Token Bucket
* need strict rate → Leaky Bucket
* need accuracy → Sliding Log
* need scale → Redis + sharding
* low latency → local + global hybrid

---
