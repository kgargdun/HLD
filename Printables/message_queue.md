# MESSAGE QUEUES (HLD QUICK REFERENCE)

## CORE PURPOSE

* decouple producers and consumers
* async processing
* absorb traffic spikes (buffering)
* improve reliability

---

## CORE MODELS

### Queue (Point-to-Point)

* producer → queue → one consumer processes message
* message consumed once

---

### Pub-Sub (Fan-out)

* producer → topic → multiple consumers
* each consumer gets a copy

---

### Stream (Log-based)

* append-only log
* consumers read at own pace (offset-based)
* message retained for time

---

## DELIVERY GUARANTEES

* At-most-once

  * no retries, possible loss

* At-least-once

  * retries, duplicates possible

* Exactly-once

  * deduplication + idempotency

---

## MESSAGE LIFECYCLE

Producer:

* creates message → sends to broker

Broker:

* stores message (memory/disk)
* routes to queue/topic

Consumer:

* pulls or receives message
* processes
* sends ACK

Failure:

* no ACK → message retried / requeued

---

## PATTERNS (EXHAUSTIVE)

### Work Queue

* distribute tasks among workers
* each task processed once

---

### Publish-Subscribe

* broadcast to multiple services

---

### Fan-out

* one message → multiple queues

---

### Dead Letter Queue (DLQ)

* failed messages after retries
* stored separately for debugging

---

### Retry Pattern

* retry with delay/backoff
* exponential backoff common

---

### Delayed Queue

* message processed after delay

---

### Priority Queue

* higher priority messages first

---

### Competing Consumers

* multiple consumers on same queue
* load balancing

---

### Idempotent Consumer

* handle duplicate messages safely

---

### Exactly-once Processing

* deduplication + transactional writes

---

## INTERNAL WORKING (IMPORTANT SYSTEMS)

### Apache Kafka

* log-based distributed system

**Core model:**

* topic → partitions → ordered log
* each partition = append-only file

**Write:**

* producer → partition (by key hashing)
* sequential disk write (fast)

**Read:**

* consumer reads by offset
* no deletion on consume

**Consumer groups:**

* each partition assigned to one consumer in group
* enables parallelism

**Replication:**

* leader + followers per partition
* ISR (in-sync replicas)

**Guarantees:**

* at-least-once (default)
* exactly-once with idempotent producers + transactions

**Traits:**

* high throughput (batching + sequential IO)
* durable storage
* replay capability

---

### RabbitMQ

* traditional message broker (queue-based)

**Core model:**

* producer → exchange → queue → consumer

**Exchange types:**

* direct → routing key match
* fanout → broadcast
* topic → pattern match
* headers → header-based

**Queue:**

* messages stored (memory/disk)

**Delivery:**

* push-based (broker pushes to consumer)
* ACK required

**Features:**

* DLQ
* priority queues
* message TTL
* routing flexibility

**Traits:**

* strong routing capabilities
* lower throughput than Kafka
* suited for task queues

---

### Amazon SQS

* fully managed queue service

**Types:**

* Standard:

  * at-least-once
  * best-effort ordering
* FIFO:

  * exactly-once (deduplication)
  * strict ordering

**Model:**

* pull-based (long polling)

**Features:**

* visibility timeout (lock message)
* DLQ support
* auto scaling

**Traits:**

* simple, highly scalable
* limited features vs RabbitMQ

---

### Apache Pulsar

* hybrid (queue + stream)

**Architecture:**

* brokers (serve requests)
* BookKeeper (storage layer)

**Storage:**

* segment-based log (offloaded to BookKeeper)

**Features:**

* multi-tenancy
* geo-replication
* separate compute + storage

**Traits:**

* Kafka-like but more flexible
* complex setup

---

## PUSH VS PULL

### Push

* broker sends messages
* low latency
* risk of overwhelming consumers

Used in:

* RabbitMQ

---

### Pull

* consumer fetches messages
* better control, batching

Used in:

* Kafka, SQS

---

## ORDERING

* global ordering → single partition (low scale)
* partition-level ordering (Kafka)
* no guarantee (SQS standard)

---

## SCALING

### Horizontal scaling

* multiple brokers
* partitioned topics (Kafka)

### Consumer scaling

* consumer groups
* parallel processing

---

## STORAGE

* memory (fast, volatile)
* disk (durable)
* log-based (Kafka, Pulsar)
* queue-based (RabbitMQ)

---

## FAILURE / EDGE CASES

* duplicate messages (at-least-once)
* message loss (at-most-once)
* consumer crash → reprocessing
* poison messages (always failing)
* backlog buildup (slow consumers)
* partition imbalance (Kafka)
* ordering break (multiple consumers)

---

## WHEN TO USE WHAT (FAST RECALL)

* Kafka → high throughput, streaming, logs
* RabbitMQ → complex routing, task queues
* SQS → managed, simple queueing
* Pulsar → hybrid + geo replication

---
