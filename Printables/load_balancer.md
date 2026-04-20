# LOAD BALANCERS (HLD QUICK REFERENCE)

## CORE PURPOSE

* distribute incoming traffic across multiple servers
* improve availability, scalability, latency
* prevent overload on single node

---

## TYPES

### L4 LOAD BALANCER (Transport Layer)

* operates on IP + port
* no visibility into request content
* forwards TCP/UDP packets

**Traits:**

* very fast (no parsing)
* limited routing logic
* supports any protocol (TCP/UDP)

---

### L7 LOAD BALANCER (Application Layer)

* understands HTTP/gRPC
* routes based on:

  * URL path
  * headers
  * cookies

**Traits:**

* intelligent routing
* supports SSL termination
* higher latency than L4

---

## ARCHITECTURE / FLOW

Client → DNS → LB → backend servers

Flow:

* request hits LB IP
* LB selects backend
* forwards request
* gets response → returns to client

---

## LOAD BALANCING ALGORITHMS (EXHAUSTIVE)

### Simple

* Round Robin

  * sequential distribution

* Weighted Round Robin

  * weight per server (capacity aware)

---

### Load-based

* Least Connections

  * server with fewest active connections

* Weighted Least Connections

  * combines weight + load

---

### Latency-based

* Least Response Time

  * lowest latency server

---

### Hash-based

* IP Hash

  * same client → same server

* Consistent Hashing

  * minimal re-distribution on scaling
  * used for sticky sessions / caching

---

### Advanced

* Random
* Power of Two Choices

  * pick 2 random → choose better

---

## KEY FEATURES

### Health Checks

* periodic checks (HTTP/TCP)
* remove unhealthy instances
* active + passive checks

---

### SSL TERMINATION

* TLS handled at LB
* backend receives plain HTTP
* reduces backend CPU usage

---

### STICKY SESSIONS

* same client → same server
* via:

  * cookies
  * IP hash

Tradeoff:

* uneven load distribution

---

### CONNECTION MANAGEMENT

* keep-alive
* connection pooling
* idle timeout handling

---

## SCALING PATTERNS

### Horizontal LB

* multiple LBs
* fronted by DNS or anycast IP

---

### Global Load Balancing

* DNS-based routing (geo, latency)
* directs to nearest region

---

## FAILURE HANDLING

* health check failure → remove node
* retry on another backend
* circuit breaking (stop sending to bad node)

---

## INTERNAL WORKING

### L4 LB

* maintains connection table
* maps client → backend
* forwards packets (NAT or direct routing)

---

### L7 LB

* parses request (HTTP headers)
* selects backend using rules
* may modify request (headers, cookies)

---

## TECH STACK

### Software LB

#### NGINX

* event-driven (non-blocking)
* supports L7 + limited L4
* features:

  * reverse proxy
  * SSL termination
  * caching
  * rate limiting
* widely used at edge + service layer

---

#### HAProxy

* high-performance L4 + L7
* optimized for TCP
* features:

  * advanced routing rules
  * health checks
  * stickiness
* used in high-throughput systems

---

### Cloud LB

#### AWS Elastic Load Balancing

* managed LB service

Types:

* ALB (Application LB)

  * L7 (HTTP/HTTPS)
  * path + host routing

* NLB (Network LB)

  * L4 (TCP/UDP)
  * ultra low latency

* CLB (Classic, legacy)

---

#### Google Cloud Load Balancing

* global anycast IP
* auto scaling
* L4 + L7

---

#### Azure Load Balancer

* L4 LB
* paired with Application Gateway (L7)

---

## RELATED COMPONENTS

### Reverse Proxy vs LB

* reverse proxy = single entry point
* LB = distributes across many nodes
* often same tool (NGINX/HAProxy)

---

### Anycast

* same IP across regions
* routed to nearest LB

---

### Service Mesh LB (internal)

#### Envoy

* sidecar proxy
* per-service load balancing
* retries, circuit breaking

---

#### Istio

* control plane over Envoy
* traffic policies, observability

---

## FAILURE / EDGE CASES

* LB single point of failure → use multiple + DNS/anycast
* uneven load (bad hashing, sticky sessions)
* slow backend → queue buildup
* connection exhaustion
* health check false positives/negatives
* SSL overhead at scale

---
