# System Design

A complete interview reference for system design, synthesized from the leading open-source resources ([donnemartin/system-design-primer](https://github.com/donnemartin/system-design-primer), [ByteByteGoHq/system-design-101](https://github.com/ByteByteGoHq/system-design-101)) and *Designing Data-Intensive Applications* (Kleppmann). Every section ties **theory → trade-offs → real examples**. The recurring mantra: **everything is a trade-off**.

> Builds on `04_Computer_Networks.md` (DNS, TCP/TLS, HTTP, CDN, load balancing) and `02_DBMS.md` (ACID, indexing, CAP). This note focuses on building systems at scale.

---

## 1. The Interview Framework

A system design interview is an **open-ended conversation you are expected to lead**. Use these steps:

1. **Clarify requirements** — separate **functional** (what it does) from **non-functional** (latency, throughput, availability, consistency, scalability, durability). Ask: Who uses it? How? How many users / QPS? Read:write ratio? Data volume? SLAs?
2. **Estimate (back-of-the-envelope)** — size traffic, storage, bandwidth, and memory (see below).
3. **High-level design** — sketch the main components and data flows; justify each choice.
4. **Deep dive** — data model, API contracts, algorithms, storage schema.
5. **Scale and find bottlenecks** — where does it break? What needs horizontal scaling, caching, async, sharding?
6. **Operations** — monitoring, alerting, failure modes, cost.

### Back-of-the-Envelope Numbers

**Powers of two:** $2^{10}$ = 1 KB, $2^{20}$ = 1 MB, $2^{30}$ = 1 GB, $2^{40}$ = 1 TB, $2^{50}$ = 1 PB.

**Latency numbers every programmer should know (orders of magnitude):**
| Operation | Latency |
|---|---|
| L1 cache reference | ~1 ns |
| Branch mispredict | ~3 ns |
| L2 cache reference | ~4 ns |
| Mutex lock/unlock | ~17 ns |
| Main memory reference | ~100 ns |
| Compress 1 KB (Zippy) | ~2 µs |
| Send 1 KB over 1 Gbps | ~10 µs |
| SSD random read | ~16–100 µs |
| Read 1 MB sequentially from memory | ~25 µs |
| Round trip within a datacenter | ~500 µs |
| Read 1 MB from SSD | ~1 ms |
| Disk seek (HDD) | ~10 ms |
| Read 1 MB from disk | ~20 ms |
| Round trip across continents | ~150 ms |

**Estimation worked example (Twitter-like):** 300 M users, 50% daily active = 150 M DAU. Each posts 2 tweets/day → 300 M tweets/day ÷ 86,400 s ≈ **3.5 K writes/s** (peak ~2× ≈ 7 K/s). At ~300 bytes/tweet → ~90 GB/day → ~33 TB/year (text only; media dwarfs this). Reads at ~100:1 → **~350 K reads/s** → caching and read replicas are mandatory.

---

## 2. Core Trade-offs

- **Performance vs. scalability** — *performance* = fast for a single user; *scalability* = stays fast under growing load. Slow for one user → performance problem; fast for one but slow under load → scalability problem.
- **Latency vs. throughput** — latency = time per operation; throughput = operations per unit time. Aim for **maximum throughput with acceptable latency**. (Little's Law: $L = \lambda W$.)
- **Availability vs. consistency (CAP)** — under a network **partition**, you can keep only **C** or **A**:
  - **CP** — consistency + partition tolerance (may reject/timeout): MongoDB, HBase, Zookeeper.
  - **AP** — availability + partition tolerance (eventual consistency): Cassandra, DynamoDB.
  - Partition tolerance is mandatory because networks fail.
- **PACELC** — extends CAP: if **P**artition, trade **A** vs **C**; **E**lse (normal operation), trade **L**atency vs **C**onsistency.

### Availability in Numbers
| Availability | Downtime/year |
|---|---|
| 99% (two 9s) | ~3.65 days |
| 99.9% (three 9s) | ~8.77 hours |
| 99.99% (four 9s) | ~52.6 minutes |
| 99.999% (five 9s) | ~5.26 minutes |

- **In series:** $A_{total} = A_1 \times A_2$ (lower than either).
- **In parallel:** $A_{total} = 1 - (1 - A_1)(1 - A_2)$ (higher than either — redundancy improves availability).

---

## 3. Consistency Patterns

- **Weak consistency** — after a write, reads may or may not see it (best-effort). Memcached, VoIP, live video.
- **Eventual consistency** — reads will see the write eventually (typically ms); async replication. DNS, email, DynamoDB (default).
- **Strong consistency** — after a write, all reads see it; synchronous replication. RDBMS, file systems, Zookeeper.

**Session guarantees:** read-your-writes, monotonic reads (never see older data after newer), monotonic writes, consistent prefix reads (never see writes out of causal order).

---

## 4. Availability Patterns

### Failover
- **Active-passive** — heartbeats between active and standby; on failure, the passive takes over the active's IP. Standby is **hot** (running) or **cold** (must boot). Only the active serves traffic. (Aka master-slave failover.)
- **Active-active** — both serve traffic, spreading load; DNS/app must know both; needs conflict resolution. (Aka master-master.)
- Downsides: extra hardware/complexity; risk of data loss if the active fails before replicating.

### Replication
- **Master-slave** — one write master, N read replicas (read scaling; replicas are eventually consistent; promote a replica on master failure).
- **Master-master** — multiple write masters (write scaling; needs conflict resolution; risk of inconsistency).
- **Synchronous** (strong, slower) vs. **asynchronous** (fast, may lose recent writes).

### Disaster Recovery
- **RTO (Recovery Time Objective)** — how quickly you must recover.
- **RPO (Recovery Point Objective)** — how much data loss is tolerable.
- Tiers (increasing cost/readiness): backup & restore → pilot light → warm standby → multi-site active-active.

---

## 5. DNS and CDN

### DNS Routing
Managed DNS (Route 53, Cloudflare) can route by: **weighted round-robin**, **latency-based**, **geo-based (GeoDNS)**, and **failover**. (See `04_Computer_Networks.md` for record types and resolution.)

### CDN
- **Push CDN** — you proactively upload content to the edge; good for known static assets; higher storage cost; you manage updates.
- **Pull CDN** — the CDN fetches from origin on first request and caches by TTL; good for high-traffic/large catalogs; lower storage; first request is slow.
- Metrics: **cache-hit ratio**; technique: **stale-while-revalidate** (serve stale while refreshing).
- **Dynamic content at the edge** — Edge Side Includes (ESI), Cloudflare Workers, Lambda@Edge.

---

## 6. Load Balancers and Proxies

- **L4 (TCP/UDP)** — fast, routes by IP/port, no content inspection.
- **L7 (HTTP)** — content-aware (route by path/header/cookie), SSL termination, compression.
- **Algorithms:** round-robin, weighted round-robin, **least connections**, least response time, IP hash, **consistent hashing** (cache affinity / stateful).
- **Health checks** — *passive* (observe real responses) and *active* (probe endpoints).
- **Sticky sessions** pin a user to a backend (complicates horizontal scaling and rebalancing).
- **HA for the LB itself** — active-passive or active-active (avoid the LB being a single point of failure).

### Reverse Proxy vs. API Gateway vs. Load Balancer
- **Reverse proxy** — single entry point: SSL termination, caching, compression, rate limiting.
- **API gateway** — reverse proxy **plus** auth (JWT/OAuth), routing, request aggregation, protocol translation (REST↔gRPC), API versioning, analytics, rate limiting.
- **Load balancer** — distributes traffic across backends. (A gateway often sits in front of, or includes, an LB.)

---

## 7. Application Architecture

### Monolith vs. Microservices vs. Serverless
| | Monolith | Microservices | Serverless |
|---|---|---|---|
| Deploy | Single unit | Independent per service | Per function |
| Scaling | Whole app | Per service | Automatic |
| Coupling | Tight | Loose (network calls) | Event-driven, stateless |
| Complexity | Low | High (distributed system) | Medium (cold starts, limits) |
| Best for | Small teams/early stage | Large orgs, independent scaling | Spiky/event workloads |

### Service Discovery
- **Client-side** (Netflix Eureka) — client queries the registry and load-balances itself (caches the list).
- **Server-side** (AWS ALB, Nginx) — the LB queries the registry; clients just hit the LB.
- Health checking and automatic de-registration keep the registry accurate.

### Resilience Patterns
- **Circuit breaker** — states **closed** (pass-through) → **open** (fast-fail for a window) → **half-open** (probe a few requests). Prevents cascading failures. (Hystrix, Resilience4j.)
- **Retries with exponential backoff + jitter** — avoid retry storms.
- **Bulkheads** — isolate resource pools so one failure doesn't sink everything.
- **Timeouts** — never wait indefinitely on a dependency.
- **Idempotency** — make retries safe (see §14).

### Coordination
- **Orchestration** — a central orchestrator drives the workflow (easier to trace; a potential single point of failure).
- **Choreography** — services react to events independently (decoupled; harder to trace).
- **Sidecar / service mesh** (Istio, Envoy) — proxies handle mTLS, retries, and observability without changing app code.

### Architectural Patterns (named)
Layered (n-tier), event-driven, microkernel/plugin, CQRS, event sourcing, hexagonal (ports & adapters), and the **12-Factor App** for cloud-native apps.

---

## 8. Databases at Scale

### RDBMS Scaling
- **Master-slave replication** — read scaling; replication lag; promote a replica on failure. (Read-heavy workloads.)
- **Master-master** — write scaling; conflict resolution (last-write-wins, app-level, CRDTs); geo-distributed writes.
- **Federation (functional partitioning)** — split by function (users DB, orders DB, products DB); smaller, independently scalable DBs; cross-DB joins/transactions become app-level.
- **Sharding (horizontal partitioning)** — split rows across nodes by a **shard key**:
  - **Range-based** — simple; risk of **hotspots**.
  - **Hash-based** — even distribution; harder range queries.
  - **Directory-based** — a lookup service maps keys → shards; flexible but the directory is a bottleneck.
  - **Consistent hashing** — preferred for dynamic shard counts; minimal rebalancing.
  - Challenges: cross-shard joins/transactions, rebalancing, choosing a good shard key (avoid hotspots and "celebrity" skew).
- **Denormalization** — duplicate data to avoid expensive joins in a distributed setup; trades write overhead/consistency for read speed.
- **SQL tuning** — `EXPLAIN` plans, covering indexes, slow-query log, **connection pooling** (PgBouncer), read replicas for analytics, partitioned tables.

### Vertical vs. Horizontal Partitioning
- **Vertical** — split columns (hot vs. cold columns) into separate tables.
- **Horizontal** — split rows (sharding).

### NoSQL Deep Dive
| Type | Examples | Model & Strengths |
|---|---|---|
| **Key-value** | Redis, DynamoDB | O(1) by key; sessions, caching, leaderboards, counters. Redis types: string, hash, list, set, sorted-set, bitmap, HyperLogLog, stream |
| **Document** | MongoDB, CouchDB | Schema-flexible JSON; nested objects; catalogs, profiles; aggregation pipeline |
| **Wide-column** | Cassandra, HBase | Row-key + sorted columns; write-heavy, time-series, huge scale. Cassandra: tunable consistency (quorum), ring + gossip, no SPOF, SSTables + compaction + bloom filters |
| **Graph** | Neo4j, Neptune | Nodes/edges; traversals O(1) per hop; social graphs, fraud, recommendations; Cypher |
| **Search** | Elasticsearch | Inverted index (Lucene); full-text, faceting, geo; shards + replicas; ELK stack |
| **Time-series** | InfluxDB, TimescaleDB | Columnar, time-partitioned, heavy compression; metrics/IoT |
| **NewSQL** | CockroachDB, Spanner | Distributed SQL with ACID; Raft/Paxos; geo-distributed strong consistency |

**Storage engines:** **B-Tree** (read-optimized, in-place updates — RDBMS) vs. **LSM-Tree** (write-optimized, append + compaction — Cassandra, RocksDB, LevelDB).

### Choosing SQL vs. NoSQL
- **SQL** when: complex queries/joins, transactions (ACID), stable schema, relational data.
- **NoSQL** when: massive scale, flexible schema, simple access patterns, eventual consistency acceptable, or a specific model fits (graph/document/time-series).

---

## 9. Caching (complete treatment)

### Where to Cache
Client (browser) → CDN → reverse proxy / web server (Nginx) → application (in-process, e.g. Caffeine) → distributed (Redis/Memcached) → database query cache.

### Write/Read Strategies
| Strategy | How | Pros | Cons |
|---|---|---|---|
| **Cache-aside (lazy)** | App checks cache; on miss reads DB, populates cache | Only caches what's used; resilient | First read is slow; can serve stale |
| **Write-through** | Write to cache **and** DB synchronously | Cache always fresh | Higher write latency |
| **Write-behind (write-back)** | Write to cache, async flush to DB | Low write latency | Risk of data loss on crash |
| **Refresh-ahead** | Proactively refresh hot keys before expiry | Low latency for hot data | Wasted refreshes if prediction is wrong |

### Eviction Policies
**LRU** (least recently used — linked hashmap), **LFU** (least frequently used — better for skewed access), **FIFO**, **TTL**, **Random**. Choose by access pattern.

### Cache Failure Modes (ByteByteGo)
- **Cache stampede / thundering herd** — a popular key expires and many requests miss at once → DB overload. *Fix:* mutex/lock on cache fill, probabilistic early expiration, background refresh.
- **Cache penetration** — requests for keys that never exist always hit the DB. *Fix:* **bloom filter** for existence, cache null results with short TTL.
- **Cache avalanche** — many keys expire simultaneously → DB overload. *Fix:* **TTL jitter**, circuit breaker, persistent cache.
- **Hot key / big key** — one key overwhelms a shard. *Fix:* local replicas, split big keys, add a key suffix to spread load.

### Redis vs. Memcached
- **Redis** — persistence, rich data structures, pub/sub, Lua scripting, clustering, replication.
- **Memcached** — simpler, multi-threaded, pure LRU cache.
- Prefer Redis unless you specifically need Memcached's multi-threaded simplicity.

---

## 10. Asynchronism and Messaging

**Why async:** decouple producers/consumers, absorb traffic spikes, enable retries, improve availability.

- **Delivery semantics:** at-most-once, at-least-once (most common; needs idempotent consumers), exactly-once (hard; usually at-least-once + dedup). **Dead-letter queue (DLQ)** holds poison messages.
- **Kafka (log-based)** — topics → **partitions** → replicas; producers append; **consumer groups** track offsets independently; **retention-based** (not deleted on ack). Extremely high throughput via sequential disk writes, OS page cache, **zero-copy (`sendfile`)**, and batching. Uses: event streaming, CDC, activity feeds, log aggregation.
- **RabbitMQ (broker-based)** — exchanges (direct, topic, fanout, headers) → queues; **ack-based** deletion; rich routing; lower throughput. Uses: task queues, RPC, complex routing.
- **SQS/SNS (AWS)** — SQS = managed queue (standard/FIFO, **visibility timeout**, long polling); SNS = pub/sub fan-out.
- **Back pressure** — when consumers can't keep up, slow/shed producers; bounded queues, reactive streams.
- **Event-driven patterns** — **event sourcing** (store events, not state), **CQRS** (separate read/write models), **saga** (distributed transactions via local steps + compensations).
- **Task queues** (Celery, Sidekiq) — background jobs, scheduled tasks, retries with exponential backoff.

---

## 11. Rate Limiting

| Algorithm | Idea | Notes |
|---|---|---|
| **Token bucket** | Tokens refill at rate $r$, capacity $b$; each request consumes one | Allows bursts up to $b$; common in API gateways |
| **Leaky bucket** | Requests enter a queue drained at a constant rate | Smooths bursts; queue can overflow |
| **Fixed window** | Count per fixed window | Simple; **boundary burst** (up to 2× at window edges) |
| **Sliding window log** | Store timestamps of requests | Accurate; high memory |
| **Sliding window counter** | Interpolate previous + current window | Memory-efficient and accurate (most used) |

**Distributed rate limiting** — share state across nodes via **Redis** (atomic `INCR` + TTL, or a Lua script for atomic check-and-increment). Respond with **429 Too Many Requests** and a **`Retry-After`** header; expose `X-RateLimit-Limit/Remaining/Reset`.

---

## 12. Consistent Hashing

**Problem with `hash(key) % N`:** when $N$ changes, nearly all keys remap → cache misses/rebalancing storms.

**Consistent hashing** maps both nodes and keys onto a ring; a key belongs to the next node clockwise. Adding/removing a node only remaps keys in one arc → **minimal movement** (~$K/N$ keys). **Virtual nodes (vnodes)** — each physical node owns many ring points → better load balance and smoother rebalancing.

**Applications:** distributed cache sharding, load balancing, Cassandra/DynamoDB node assignment, CDN request routing.

---

## 13. Unique ID Generation

| Approach | Pros | Cons |
|---|---|---|
| **UUID v4** (128-bit random) | No coordination, globally unique | Not sortable; poor DB-index locality |
| **DB auto-increment** | Simple, monotonic | SPOF; multi-master needs coordination |
| **Ticket server** (Flickr) | Centralized, simple | SPOF/bottleneck |
| **Snowflake** (Twitter) | Sortable by time, distributed, high throughput | Clock-skew sensitive |
| **ULID / KSUID** | Lexicographically sortable, URL-safe | 128-bit |

**Snowflake layout (64-bit):** 1 sign bit + **41-bit timestamp (ms)** + **10-bit machine ID** + **12-bit sequence** → 4096 IDs/ms/machine, time-sortable. Handle backward clock movement by waiting or rejecting; keep clocks synced with NTP.

---

## 14. Distributed Systems Fundamentals

### Consensus
- **Raft** — understandable consensus: randomized-timeout **leader election**, **log replication** via AppendEntries (commit when a majority acks), and safety rules. Used in etcd, Consul, CockroachDB, Kafka (KRaft).
- **Paxos** — Prepare/Promise/Accept/Accepted; multi-Paxos for a stream of values; powerful but hard to implement. Used in Chubby, Spanner.
- **Leader election** — bully algorithm; Zookeeper ephemeral nodes; etcd TTL leases.

### Distributed Transactions
- **2PC (two-phase commit)** — coordinator sends **prepare** → participants vote → **commit** if all yes. Problems: coordinator is a SPOF; participants **block** if the coordinator crashes after prepare.
- **3PC** — adds a pre-commit phase to reduce blocking; still not fully fault-tolerant.
- **Saga** — a sequence of local transactions with **compensating transactions** to undo on failure; *choreography* (event-driven) or *orchestration* (a saga orchestrator); eventual consistency. Preferred in microservices.

### Other Essentials
- **Idempotency** — same result if applied multiple times; use **idempotency keys** for safe retries (critical for payments).
- **Distributed locks** — Redis `SET key val NX PX ttl` (Redlock uses a majority of N nodes); Zookeeper ephemeral sequential nodes; DynamoDB conditional writes. Beware lock expiry vs. long operations.
- **Vector clocks** — track causality without a global clock; $V_i[j]$ = events node $i$ knows from node $j$; detect concurrent vs. causally-ordered events (used in Dynamo).
- **Time** — NTP (ms accuracy); **Lamport timestamps** (logical clocks; total order, no causality); **Google TrueTime** (GPS + atomic clocks with an uncertainty bound — Spanner waits out the bound for external consistency); **Hybrid Logical Clocks (HLC)**.
- **Quorums** — with $N$ replicas, reads $R$ and writes $W$: strong consistency when $R + W > N$ (e.g., $N=3, W=2, R=2$).
- **Failure detection** — heartbeats, gossip protocols, phi-accrual detectors.

---

## 15. Observability (Logging, Metrics, Tracing)

The **three pillars**:
- **Logs** — structured (JSON) vs. unstructured; log levels; aggregation via **ELK** (Elasticsearch, Logstash, Kibana) or Loki+Grafana; sample high-volume logs.
- **Metrics** — counters, gauges, histograms, summaries; **Prometheus** (pull-based, PromQL, time-series TSDB) + **Grafana**; push-based (StatsD, InfluxDB). **SLI** (e.g., p99 latency, error rate, throughput) → **SLO** (target) → **SLA** (contractual).
- **Distributed tracing** — a **span** is a unit of work; a **trace** is a tree of spans; trace IDs propagate via headers (W3C TraceContext). Jaeger, Zipkin, **OpenTelemetry**. Diagnoses latency across microservices; tune sampling.

**Alerting** — threshold vs. anomaly detection; avoid alert fatigue; runbooks; PagerDuty. **SRE concepts** — **error budgets**, toil reduction, blameless post-mortems, **SLO burn-rate alerts**, **canary/blue-green** deployments.

---

## 16. Canonical System Design Problems

Each sketch covers: requirements → estimates → high-level design → data model → key algorithms → bottlenecks → scale-up.

### 1. URL Shortener (Bit.ly)
- **Approach:** Base62-encode an auto-increment ID (or hash the long URL with MD5/SHA-256 and take 7 chars; handle collisions). 7 Base62 chars → $62^7 \approx 3.5$ trillion URLs.
- **Schema:** `short_code (PK) → long_url, created_at, expiry, user_id`.
- **Read-heavy (~100:1)** → cache-aside in Redis; Zipfian access keeps hot codes cached.
- **Extras:** custom aliases, expiration, analytics (async via Kafka). KGS (key-generation service) can pre-generate unused keys.

### 2. Twitter / News Feed
- **Fan-out on write (push)** — precompute each follower's timeline on tweet (fast reads, expensive for celebrities).
- **Fan-out on read (pull)** — assemble the timeline at read time (cheap writes, slow reads).
- **Hybrid** — push for normal users; pull for celebrities (high follower counts).
- **Storage:** tweets in Cassandra (hot) + object store for media + Elasticsearch for search. **Timeline cache** in a Redis sorted set keyed by timestamp. Shard by `user_id`.

### 3. YouTube / Video Streaming
- **Upload:** client → API → blob storage → **transcoding workers** (FFmpeg → **HLS/DASH adaptive bitrate**: 360p/720p/1080p/4K).
- **Delivery:** push CDN for popular videos; pull for the long tail.
- **Metadata:** sharded MySQL (video info, comments, likes). **View count** via Redis `INCR` batched to the DB. Recommendation engine offline.

### 4. WhatsApp / Chat
- **WebSocket** connections (stateful, one per client) via a connection/gateway service.
- **Chat service** routes messages; store in Cassandra keyed by `(chat_id, created_at)`.
- **Delivery status** (sent/delivered/read), **group chat** fan-out, **offline** queueing.
- **Presence** via heartbeats + Redis TTL. **E2E encryption** (Signal protocol).

### 5. Google Drive / File Storage
- **Chunked, resumable uploads** (e.g., 5 MB chunks); **deduplication** by hashing chunks.
- **Delta sync** (only changed chunks). Object storage (S3) for blobs; metadata DB (files, folders, **versions**, soft delete).
- **Conflict resolution** (last-write-wins or operational transform). Notification service for sync.

### 6. Rate Limiter
- Redis **sliding-window counter** with a Lua script for atomicity; per-user/IP/API-key buckets.
- Headers `X-RateLimit-*`; distributed token bucket synced via Redis. (See §11.)

### 7. Web Crawler
- **URL frontier** (priority queue) → fetchers → DNS resolver → HTML parser → content/link extractors → revisit policy.
- **Politeness** (robots.txt, crawl delay); **dedup** via bloom filter + URL fingerprints; distributed via Kafka + consistent hashing; **trap avoidance** (max depth).

### 8. Notification System
- Channels: push (APNs/FCM), SMS (Twilio), email (SES/SendGrid).
- Async via Kafka by notification type → per-channel workers; retries with backoff; user-preference service; rate limiting; templating; analytics (open/click rates).

### 9. Search Autocomplete (Typeahead)
- **Trie** of top-k completions per prefix, precomputed from query-frequency logs (batch).
- Distributed trie sharded by prefix; serve from edge with short TTL; client debounce; ~100 ms latency budget.

### 10. Proximity Service (Yelp / nearby)
- **Geohash** (encode lat/lon to a base-32 string; adjacent cells share a prefix) or **S2** (Google spherical cells / **quadtree**).
- Index geohash in the DB; expand radius by checking neighbor cells; cache popular geohashes. Read-heavy.

### 11. Ride-Sharing (Uber)
- Driver location via WebSocket (~every 4 s) → Location Service → Redis geo (sorted set by geohash).
- **Matching service** finds nearby drivers within a radius; trip **state machine** (requested → accepted → en_route → arrived → in_trip → completed).
- **Surge pricing** from supply/demand per grid cell; routing via a road graph + Dijkstra/A-star.

### 12. Distributed Job Scheduler
- Submit jobs to a queue; a scheduler assigns to workers; track state in a DB; retries with backoff; cron-style triggers; leader election (Zookeeper/etcd) for the scheduler; idempotent execution.

---

## 17. "What Happens When You Type google.com?"

A single question that exercises the whole stack:
1. **DNS** — browser cache → OS cache → recursive resolver → root → `.com` TLD → authoritative → IP.
2. **TCP three-way handshake** + **TLS handshake** (certificate validation, session key).
3. **HTTP GET /** — over HTTP/2 or HTTP/3 (QUIC).
4. **Anycast** routes to the nearest datacenter; **L7 load balancer** / reverse proxy.
5. **Application servers** + caches (Memcache, Bigtable) + the serving index.
6. **Response HTML** → browser renders (DOM → CSSOM → render tree → layout → paint), runs JS, fetches CSS/JS/images from the CDN.

It spans **DNS, TCP/IP, TLS, HTTP, CDN, load balancing, and rendering** — the reason it's a perennial favorite.

---

## 18. Quick Interview Checklist

- **Always start with requirements + estimates** before drawing boxes.
- **CAP:** partition tolerance is mandatory → choose CP or AP per use case; PACELC adds the latency-vs-consistency trade in normal operation.
- **Scale reads** with caching + read replicas; **scale writes** with sharding (consistent hashing) and async/queues.
- **Cache strategies:** cache-aside is the default; know the four failure modes (stampede, penetration, avalanche, hot key).
- **Kafka** = high-throughput log (retention-based); **RabbitMQ** = flexible routing (ack-based).
- **Rate limiting:** sliding-window counter in Redis; return 429 + Retry-After.
- **Consistent hashing + vnodes** minimize rebalancing.
- **Snowflake IDs** are time-sortable and distributed.
- **Consensus** (Raft/Paxos) for leader election & replicated logs; **Saga** for distributed transactions.
- **Observability** = logs + metrics + traces; define SLIs/SLOs and an error budget.
- **Everything is a trade-off** — state assumptions and justify each choice.
