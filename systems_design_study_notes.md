# Systems Design Study Notes

> Fill this in as you go through Grokking. After each section, **close the material** and write from memory first.
> Use `systems_design_cheat_sheet.md` as your answer key to check gaps afterward.

---

## Part 1: Foundations

### Consistent Hashing

- What problem does it solve?

- How does it work? (diagram in words)

- What happens when a node is added/removed?

- Real-world use:

---

### Load Balancing

- L4 vs L7 — what's the difference and when do you use each?

- Algorithms (name + one-line description each):
  1.
  2.
  3.

- Where does it sit in an architecture?

- Tools:

---

### Caching

- Where can caches live? (list all layers)

- Strategies — what's the difference:

- Eviction policies:

- Failure modes to watch for:

- Tools:

---

### Data Partitioning (Sharding)

- Why shard?

- Strategies (name + tradeoff each):
  1.
  2.
  3.

- What is a hotspot? How do you prevent it?

- What makes sharding hard?

---

### Indexes

- Why do indexes help reads?

- What's the tradeoff?

- When would you NOT add an index?

---

### Proxies

- Forward proxy vs reverse proxy — difference?

- What does a reverse proxy give you?

---

### Redundancy & Replication

- Active-active vs active-passive:

- Replication lag — what is it and why does it matter?

- Leader election — what triggers it?

---

### SQL vs NoSQL

- When to choose SQL:

- When to choose NoSQL:

- ACID vs BASE — define each acronym:
  - A:
  - C:
  - I:
  - D:
  - BASE:

---

### CAP Theorem

- State the theorem in your own words:

- Why is P (partition tolerance) non-negotiable in practice?

- CP examples + why:

- AP examples + why:

---

### Long Polling vs WebSockets vs SSE

- Long polling — how it works + when to use:

- WebSocket — how it works + when to use:

- SSE — how it works + when to use:

---

## Part 2: System Design Deep Dives

> For each system, use this structure:
>
> 1. Functional requirements
> 2. Non-functional requirements (scale, latency, availability)
> 3. Back-of-envelope estimates
> 4. High-level design
> 5. Core component deep dive
> 6. Key tradeoffs / decisions

---

### URL Shortener (TinyURL)

**Functional requirements:**

**Non-functional requirements:**

**Scale estimates:**

- Expected QPS (read vs write):
- Storage estimate:

**High-level design:** _(describe components)_

**Core questions to answer:**

- How do you generate unique short IDs?

- How do you handle redirects efficiently?

- How do you handle hotlinks (popular URLs)?

**Key tradeoffs:**

---

### Pastebin

**Functional requirements:**

**Non-functional requirements:**

**How is this different from TinyURL?**

**High-level design:**

**Where does the content live?**

**Key tradeoffs:**

---

### Instagram (Photo Sharing)

**Functional requirements:**

**Non-functional requirements:**

**Scale estimates:**

- Photo uploads/day:
- Storage/year:

**High-level design:**

**Core questions:**

- How do you store and serve photos at scale?

- How do you generate a user's feed?

- How do you handle follow graph lookups?

**Key tradeoffs:**

---

### Dropbox / Google Drive (File Storage)

**Functional requirements:**

**Non-functional requirements:**

**High-level design:**

**Core questions:**

- How do you handle large file uploads efficiently?

- What is chunking and why does it matter?

- How do you sync across devices?

- How do you handle conflicts?

**Key tradeoffs:**

---

### Facebook Messenger / Chat System

**Functional requirements:**

**Non-functional requirements:**

**High-level design:**

**Core questions:**

- How do you deliver messages in real time?

- How do you store message history?

- How do you handle offline users?

- 1:1 vs group chat — what changes?

**Key tradeoffs:**

---

### Twitter (News Feed / Timeline)

**Functional requirements:**

**Non-functional requirements:**

**Scale estimates:**

- Tweets/day:
- Fanout problem — what is it?

**High-level design:**

**Core questions:**

- Push vs pull model for feed generation — tradeoffs?

- How do you handle celebrity accounts (hotspots)?

- How do you rank/sort the feed?

**Key tradeoffs:**

---

### YouTube / Video Streaming

**Functional requirements:**

**Non-functional requirements:**

**Scale estimates:**

- Storage per video:
- Bandwidth estimate:

**High-level design:**

**Core questions:**

- How do you handle video upload and processing?

- What is adaptive bitrate streaming?

- How do you serve video at global scale?

**Key tradeoffs:**

---

### Typeahead / Search Autocomplete

**Functional requirements:**

**Non-functional requirements:**

**High-level design:**

**Core questions:**

- What data structure powers fast prefix lookups?

- How do you keep suggestions fresh / updated?

- How do you rank suggestions?

- How do you scale reads?

**Key tradeoffs:**

---

### API Rate Limiter

**Functional requirements:**

**Non-functional requirements:**

**Algorithms — describe each:**

- Token bucket:
- Leaky bucket:
- Fixed window counter:
- Sliding window log:
- Sliding window counter:

**High-level design:**

**Core questions:**

- Where does the rate limiter live (client, gateway, service)?

- How do you share state across distributed nodes?

**Key tradeoffs:**

---

### Web Crawler

**Functional requirements:**

**Non-functional requirements:**

**High-level design:**

**Core questions:**

- How do you avoid crawling the same URL twice?

- How do you prioritize which URLs to crawl next?

- How do you handle traps (infinite loops)?

- How do you scale the crawler?

**Key tradeoffs:**

---

### Proximity Server (Yelp / Find Nearby)

**Functional requirements:**

**Non-functional requirements:**

**High-level design:**

**Core questions:**

- How do you index and query by location?

- What is geohashing? How does it work?

- What are the tradeoffs of geohashing vs quadtrees?

**Key tradeoffs:**

---

### Ride-Sharing (Uber / Lyft)

**Functional requirements:**

**Non-functional requirements:**

**High-level design:**

**Core questions:**

- How do you match riders to nearby drivers in real time?

- How do you handle driver location updates at scale?

- How do you handle surge pricing?

**Key tradeoffs:**

---

### Notification System

**Functional requirements:**

**Non-functional requirements:**

**High-level design:**

**Core questions:**

- Push vs SMS vs email — how do you route?

- How do you handle delivery failures / retries?

- How do you avoid spamming a user across channels?

**Key tradeoffs:**

---

### Unique ID Generator (Snowflake)

**Functional requirements:**

**Non-functional requirements:**

**Options (describe + tradeoffs):**

1. UUID:
2. DB auto-increment:
3. Snowflake:

**Snowflake ID structure — what are the bit segments?**

**Key tradeoffs:**

---

### Distributed Message Queue (Kafka-style)

**Functional requirements:**

**Non-functional requirements:**

**High-level design:**

**Core questions:**

- Producer → broker → consumer — how does it flow?

- What is a partition? What is a consumer group?

- At-most-once vs at-least-once vs exactly-once — how do you achieve each?

- How do you handle message retention and replay?

**Key tradeoffs:**

---

## Part 3: Staff-Level Depth

> These topics come up in senior/staff deep dives. Come back to these after Part 2.

### Consensus Algorithms

- What problem does consensus solve?

- Raft — describe leader election and log replication in one paragraph:

- Paxos — why is it harder to implement than Raft?

---

### Distributed Transactions

- What is the two-phase commit (2PC) problem?

- What is SAGA pattern and when do you use it?

---

### Observability

- The 3 pillars — define each:
  - Metrics:
  - Logs:
  - Traces:

- What's the difference between monitoring and observability?

---

### Data Consistency Models

- Strong consistency:
- Eventual consistency:
- Read-your-writes:
- Monotonic reads:

---

### Back-of-Envelope Quick Reference

> Fill these in without looking — good to have memorized cold.

| Metric                | Value |
| --------------------- | ----- |
| 1M requests/day → QPS |       |
| 1B requests/day → QPS |       |
| 1 KB × 1M users       |       |
| 1 KB × 1B users       |       |
| SSD read latency      |       |
| HDD read latency      |       |
| Same-DC network       |       |
| Cross-region network  |       |
