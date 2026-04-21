# Systems Design Interview Cheat Sheet

## Framework for Any System Design Question

1. **Clarify requirements** (5 min)
   - Functional requirements: what does the system do?
   - Non-functional requirements: scale, latency, availability, consistency
   - Out of scope: what are we NOT building?

2. **Estimate scale** (5 min)
   - DAU / QPS (reads vs writes)
   - Storage: data size × users × retention
   - Bandwidth: QPS × payload size

3. **High-level design** (10 min)
   - Draw the main components (clients, load balancer, servers, DB, cache)
   - Walk through a core use case end-to-end

4. **Deep dive** (15 min)
   - Pick 1–2 bottlenecks and go deep
   - Interviewer often steers you here

5. **Wrap up** (5 min)
   - Identify failure points / SPOFs
   - Monitoring & alerting strategy
   - Future improvements

---

## Back-of-the-Envelope Numbers

| Metric | Value |
|--------|-------|
| 1 million requests/day | ~12 QPS |
| 1 billion requests/day | ~12,000 QPS |
| 1 KB × 1M users | 1 GB |
| 1 KB × 1B users | 1 TB |
| SSD read | ~0.1 ms |
| HDD read | ~10 ms |
| Network (same DC) | ~0.5 ms |
| Network (cross-region) | ~100–150 ms |

---

## Core Components

### Load Balancers
- L4 (TCP) vs L7 (HTTP) — L7 can route by path/header
- Algorithms: round-robin, least connections, consistent hashing
- Handle via: Nginx, AWS ALB/NLB, HAProxy

### Databases

| Type | Use case | Examples |
|------|----------|---------|
| Relational (SQL) | Strong consistency, complex queries | PostgreSQL, MySQL |
| Document | Flexible schema, nested data | MongoDB, DynamoDB |
| Key-Value | High-speed lookups, sessions, cache | Redis, DynamoDB |
| Wide-Column | Time-series, high write throughput | Cassandra, HBase |
| Search | Full-text, fuzzy search | Elasticsearch |

### Caching
- **Where**: CDN → reverse proxy → app cache → DB cache
- **Strategies**: cache-aside, write-through, write-behind, read-through
- **Eviction**: LRU, LFU, TTL
- **Tools**: Redis, Memcached, CDN (CloudFront, Cloudflare)
- Watch out for: cache stampede, stale data, thundering herd

### Message Queues
- Decouple producers from consumers, handle spikes, async processing
- **At-most-once** (fire and forget), **at-least-once** (ack-based), **exactly-once** (idempotent consumers)
- Tools: Kafka (high throughput, replay), RabbitMQ, SQS, Pub/Sub

### CDN
- Cache static assets (images, JS, CSS) at edge nodes
- Reduce latency and origin load
- Push vs pull CDN

---

## CAP Theorem

> A distributed system can only guarantee **2 of 3**: Consistency, Availability, Partition Tolerance.
> Since partition tolerance is required in practice, choose between **CP** or **AP**.

- **CP** (consistent + partition tolerant): HBase, Zookeeper, MongoDB (default config)
- **AP** (available + partition tolerant): Cassandra, DynamoDB, CouchDB

---

## SQL vs NoSQL

| | SQL | NoSQL |
|--|-----|-------|
| Schema | Fixed | Flexible |
| Scaling | Vertical (+ read replicas) | Horizontal (sharding) |
| Transactions | ACID | BASE (eventual consistency) |
| Best for | Complex queries, joins | High scale, simple lookups |

---

## Scaling Patterns

### Horizontal vs Vertical
- **Vertical**: bigger machine — simpler but limited
- **Horizontal**: more machines — preferred for web services

### Database Scaling
- **Read replicas**: offload reads; replication lag is a tradeoff
- **Sharding**: partition data by key (user_id, geo); watch for hotspots
- **Federation**: split by function (users DB, products DB)

### Stateless Services
- Store session in Redis/cookie, not in-memory — enables horizontal scaling

---

## Reliability Patterns

- **Circuit breaker**: stop calling a failing service, fail fast
- **Retry with backoff**: exponential backoff + jitter
- **Rate limiting**: token bucket, leaky bucket, fixed window, sliding window
- **Bulkhead**: isolate failures to one pool of resources
- **Idempotency keys**: safe retries for payments/mutations

---

## Common System Design Topics

- [ ] URL Shortener (TinyURL)
- [ ] Rate Limiter
- [ ] Key-Value Store
- [ ] Unique ID Generator (Snowflake)
- [ ] Web Crawler
- [ ] Notification System
- [ ] News Feed (Twitter/Facebook)
- [ ] Chat System (WhatsApp)
- [ ] Search Autocomplete
- [ ] YouTube / Video Streaming
- [ ] Google Drive / File Storage
- [ ] Ride-sharing (Uber)
- [ ] Ad Click Aggregation
- [ ] Distributed Message Queue
- [ ] Payment System

---

## Useful APIs to Know

- **Pagination**: cursor-based (stable) vs offset (simple)
- **Long polling vs WebSocket vs SSE**: use WS for bidirectional, SSE for server push
- **REST vs gRPC**: REST for public APIs, gRPC for internal (proto, binary, fast)

---

## Questions to Ask the Interviewer

- Expected QPS / peak traffic?
- Read-heavy or write-heavy?
- Global or single region?
- Strong consistency required, or eventual is OK?
- How long is data retained?
- What's the acceptable latency (p99)?
