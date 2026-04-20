# NETWORKS (HLD QUICK REFERENCE)

## 1. END-TO-END REQUEST FLOW

Client (browser/app):

* URL entered → check browser cache
* DNS resolution (local cache → OS → resolver)

DNS:

* resolver → root → TLD → authoritative
* returns IP (CDN / LB)

Connection setup:

* TCP 3-way handshake (SYN, SYN-ACK, ACK)
* TLS handshake (cert verify, key exchange)

Request:

* HTTP request sent
* passes through:

  * CDN (optional)
  * Load Balancer
  * Reverse proxy
  * Application server

Response:

* same path back
* connection reused (keep-alive / HTTP2 multiplexing)

---

## 2. APPLICATION LAYER (L7)

### HTTP/1.1

* text-based, request-response
* one request per connection (keep-alive reuse)
* head-of-line blocking

### HTTP/2

* binary protocol
* multiplexing (multiple streams on one TCP)
* header compression (HPACK)

### HTTP/3

* built on QUIC (UDP)
* no TCP head-of-line blocking
* faster connection setup

---

### HTTPS

* HTTP over TLS
* encryption + integrity
* certificate-based authentication

---

### WebSocket

* full-duplex persistent connection
* upgrade from HTTP
* low latency communication

---

### gRPC

* uses HTTP/2
* binary (Protocol Buffers)
* supports streaming (bi-directional)
* low latency, internal services

---

### DNS

* domain → IP resolution
* hierarchical lookup
* caching (TTL based)

**Core DNS flow:**

* recursive resolver
* root → TLD → authoritative

---

### MQTT

* lightweight pub-sub
* broker-based
* low bandwidth (IoT)

---

### AMQP

* message protocol (queues, routing)
* used by RabbitMQ
* supports acknowledgments, routing keys

---

### Kafka Protocol

* binary TCP protocol
* producer → broker → consumer
* batching, partition-based

Used by Apache Kafka

---

### Email Protocols

* SMTP → sending
* POP3 → download
* IMAP → sync mailbox

---

### FTP / SFTP

* FTP → plaintext file transfer
* SFTP → over SSH (secure)

---

## 3. TRANSPORT LAYER (L4)

### TCP

* connection-oriented
* reliable (ACKs, retransmissions)
* ordered delivery
* congestion control (slow start, AIMD)

**Flow:**

* 3-way handshake
* sequence numbers
* sliding window (flow control)

---

### UDP

* connectionless
* no guarantee (no retry, no ordering)
* low latency

Use:

* streaming, gaming, DNS

---

### QUIC

* runs over UDP
* built-in TLS (no separate handshake)
* multiplexed streams (no HOL blocking)
* faster reconnect (connection ID)

Used by:

* HTTP/3

---

## 4. NETWORK LAYER (L3)

### IP (IPv4 / IPv6)

* logical addressing
* packet routing across networks

---

### ICMP

* error reporting
* diagnostics (ping, traceroute)

---

### Routing Protocols

#### BGP (Border Gateway Protocol)

* internet-wide routing (between ISPs)
* path-vector protocol
* chooses best path based on policies

#### OSPF

* intra-network routing (within org/DC)
* shortest path first (Dijkstra)

---

## 5. LINK LAYER (L2)

### Ethernet

* frame-based communication
* MAC addressing

---

### ARP

* maps IP → MAC address
* broadcast within local network

---

## 6. SECURITY (TLS)

### TLS

* encryption protocol
* used in HTTPS, gRPC

**Handshake:**

* client hello (cipher suites)
* server cert + public key
* key exchange (session key)
* symmetric encryption after

Provides:

* confidentiality
* integrity
* authentication

---

## 7. INFRA COMPONENT BEHAVIOR

### CDN

* caches static content at edge
* reduces latency
* serves from nearest location

**Tech:**

* Cloudflare
* Akamai
* Amazon CloudFront

---

### Load Balancer

#### L4 (Transport level)

* routes based on IP:port
* fast, no content inspection

#### L7 (Application level)

* routes based on headers, path
* supports SSL termination

**Tech:**

* NGINX
* HAProxy
* AWS Elastic Load Balancing

---

### Reverse Proxy

* sits before backend
* handles:

  * SSL termination
  * routing
  * caching (optional)

---

### NAT (Network Address Translation)

* private IP → public IP mapping
* enables many devices to share one IP

Types:

* SNAT (source NAT)
* DNAT (destination NAT)

---

### DHCP

* assigns IP automatically
* provides:

  * IP
  * gateway
  * DNS server

---

## 8. CONNECTION OPTIMIZATIONS

* Keep-alive (reuse TCP connection)
* HTTP/2 multiplexing
* TLS session reuse
* CDN edge caching

---

## 9. FAILURE / EDGE CASES

* DNS failure (resolution fails)
* packet loss → TCP retransmission
* congestion → latency spikes
* TLS handshake failure
* connection timeout
* NAT port exhaustion
* head-of-line blocking (HTTP/1.1, TCP)

---
