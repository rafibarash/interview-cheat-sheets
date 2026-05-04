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

| Metric                 | Value       |
| ---------------------- | ----------- |
| 1 million requests/day | ~12 QPS     |
| 1 billion requests/day | ~12,000 QPS |
| 1 KB × 1M users        | 1 GB        |
| 1 KB × 1B users        | 1 TB        |
| SSD read               | ~0.1 ms     |
| HDD read               | ~10 ms      |
| Network (same DC)      | ~0.5 ms     |
| Network (cross-region) | ~100–150 ms |

---

## Core Components

### Load Balancers

- L4 (TCP, network/IP) vs L7 (HTTP, headers/url)
- Algorithms:
  - (weighted) round-robin: round robin but adjusted by capacity, default goog application, good for web services and dbs.
  - (weighted) least connections: takes into account current active connections and capacity. good for variable/high traffic apps and dbs.
  - IP hash (consistent hashing): consistent routing
  - Least response time: good for low latency (e.g. video streaming, gaming, trading)
  - Least bandwidth: CDNs, video streaming, large data transfer
- Goes in front of API gateway typically, can also go in front of individual microservices or db clusters

### API Gateway

- Reverse proxy to route to right backend/microservice/api
- Also handles rate limiting, middleware like authentication and authorization

### Databases

| Type             | Use case                            | Examples          |
| ---------------- | ----------------------------------- | ----------------- |
| Relational (SQL) | Strong consistency, complex queries | PostgreSQL, MySQL |
| Document         | Flexible schema, nested data        | MongoDB, DynamoDB |
| Key-Value        | High-speed lookups, sessions, cache | Redis, DynamoDB   |
| Wide-Column      | Time-series, high write throughput  | Cassandra, HBase  |
| Search           | Full-text, fuzzy search             | Elasticsearch     |

### Caching

- **Where**: Browser -> CDN → reverse proxy → app cache → DB cache
- **Strategies**:
  - write-through: higher latency but data consistency
  - write-around: write to db, don't write to cache until subsequent read
  - write-back: write to cache, async write to DB
  - read-through: app reads from cache, cache fetches from data store on miss. great if cache misses are infrequent
  - read-aside: app reads from cache, on miss it reads from data store and updates the cache
- **Eviction**: LRU, LFU, TTL
- **Tools**: Redis, Memcached, CDN (CloudFront, Cloudflare)
- Watch out for: cache stampede, stale data, thundering herd

### Partitioning

- Methods:
  - Horizontal (sharding): dividing table into shards, each shard contains a range of rows or specific keys.
    - Strategies:
      - range based (but hot spots occur - all writes go to last shard which could be problematic)
      - geographic based
      - hash based with consistent hashing: hash range of keys to partition ring
      - lookup service: dedicated service maps keys to shards. ex Vitess
  - Vertical: divide table into shards containing a subset of columns, based on access pattern
- Spanner does a mix of this: shards by primary key range, and dynamically creates splits (contiguous ranges of primary key space).
- Don't shard until you have to, it is the highest cost / least reversible scaling decision. Caches, read replicas, better indexes, query optimization, vertical scaling, and connection pooling are all cheaper, faster to deploy, and easier to undo. Maybe write-heavy workloads should be in different DB altogether.

### Message Queues

- Decouple producers from consumers, handle spikes, async processing
- **At-most-once** (fire and forget), **at-least-once** (ack-based), **exactly-once** (idempotent consumers)
- Tools: Kafka (high throughput, replay), RabbitMQ, SQS, Pub/Sub

## CAP Theorem

> A distributed system can only guarantee **2 of 3**: Consistency, Availability, Partition Tolerance.
> Since partition tolerance is required in practice, choose between **CP** or **AP**.

- **CP** (consistent + partition tolerant): HBase, Zookeeper, MongoDB (default config)
- **AP** (available + partition tolerant): Cassandra, DynamoDB, CouchDB

## SQL vs NoSQL

|              | SQL                        | NoSQL                       |
| ------------ | -------------------------- | --------------------------- |
| Schema       | Fixed                      | Flexible                    |
| Scaling      | Vertical (+ read replicas) | Horizontal (sharding)       |
| Transactions | ACID                       | BASE (eventual consistency) |
| Best for     | Complex queries, joins     | High scale, simple lookups  |

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

## Reliability Patterns

- **Circuit breaker**: stop calling a failing service, fail fast
- **Retry with backoff**: exponential backoff + jitter
- **Rate limiting**: token bucket, leaky bucket, fixed window, sliding window
- **Bulkhead**: isolate failures to one pool of resources
- **Idempotency keys**: safe retries for payments/mutations

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

## Useful APIs to Know

- **Pagination**: cursor-based (stable) vs offset (simple)
- **Long polling vs WebSocket vs SSE**: use WS for bidirectional, SSE for server push
- **REST vs gRPC**: REST for public APIs, gRPC for internal (proto, binary, fast)

## Questions to Ask the Interviewer

- Expected QPS / peak traffic?
- Read-heavy or write-heavy?
- Global or single region?
- Strong consistency required, or eventual is OK?
- How long is data retained?
- What's the acceptable latency (p99)?
