# SWE Interview Prep — Notes

A complete set of notes covering the core **software engineering systems** topics asked in SWE interviews: Operating Systems, Databases, Object-Oriented Programming, Computer Networks, and System Design. Each topic is a self-contained Markdown file with detailed explanations, diagrams, code, and (where relevant) LaTeX-formatted math.

> **Tip:** These render best in any Markdown viewer with LaTeX support (VS Code with a math preview extension, Obsidian, or GitHub).

---

## Reading Order

The first four files are CS fundamentals; the fifth synthesizes them into building systems at scale.

| # | Topic | What it covers |
|---|---|---|
| 01 | [Operating Systems](01_Operating_Systems.md) | Kernel, interrupts, DMA, storage hierarchy & caches, SMP/multicore, dual-mode, **processes** (states, PCB, `fork`/`exec`, zombies/orphans), **IPC** (shared memory, message passing, pipes/FIFOs), **threads** (models, Amdahl's Law, Pthreads), **synchronization** (critical section, Peterson's, `test_and_set`/`compare_and_swap`, semaphores, monitors, bounded-buffer/readers-writers/dining-philosophers), **CPU scheduling** (FCFS/SJF/SRTF/RR/priority/MLFQ + metrics), **memory** (paging, TLB, segmentation, virtual memory), **page replacement** (FIFO/Belady/LRU/Optimal/Clock), **thrashing**, **deadlocks** (4 conditions, RAG, **Banker's**), **file systems** (allocation, inode, journaling) |
| 02 | [DBMS](02_DBMS.md) | DBMS vs files, 3-level abstraction & data independence, **ER/EER** (entities, relationships, cardinality, weak entities, aggregation, specialization/generalization, lattice/category), **EER→relational mapping**, **relational algebra** (select/project/join/division/aggregates), **FDs** (Armstrong's axioms, closure, minimal cover), **normalization** (1NF→BCNF→4NF worked + lossless/dependency-preserving), **SQL** (joins, subqueries, CTEs, window functions), **transactions** (ACID, isolation levels), **concurrency** (serializability, precedence graph, **2PL**, timestamp ordering, **MVCC**), **indexing** (clustered/non-clustered, **B-tree vs B+ tree**, hash/bitmap/composite), **NoSQL & CAP/BASE** |
| 03 | [OOP](03_OOP.md) | Objects & memory layout (padding/alignment, `sizeof`), headers/include guards, access modifiers & encapsulation, dynamic allocation (`new`/`delete`), **constructors** (copy, initializer lists), `const`, **shallow vs deep copy**, destructors, `static`, **composition vs inheritance**, **vtable/vptr internals**, friends, **inheritance** (types, access control, order), **polymorphism** (overloading vs overriding), **diamond problem & virtual inheritance**, **abstraction** (abstract classes vs interfaces), **SOLID**, interview Q&A (RAII, object slicing, invariants, aggregation vs composition), **LLD design patterns** (Decorator/Adapter/Strategy/Singleton/Observer/Factory/Command/Builder + more), **move semantics & Rule of Five**, casting |
| 04 | [Computer Networks](04_Computer_Networks.md) | **OSI & TCP/IP** models + encapsulation, **physical** (Nyquist/Shannon, media, encoding, multiplexing), **data link** (framing, CRC/Hamming, stop-and-wait/GBN/SR, ALOHA/CSMA/CD/CA, Ethernet, switches/STP/VLAN), **network** (IPv4/IPv6, CIDR/subnetting, NAT, ARP, ICMP, **RIP/OSPF/BGP**), **transport** (ports, UDP, **TCP** handshake/teardown, flow & **congestion control AIMD**, RTT/RTO), **application** (HTTP/1-2-3, status codes, cookies, REST/GraphQL/gRPC, **TLS 1.2/1.3**, DNS, DHCP, email, WebSockets), **sockets** (`select`/`poll`/`epoll`), **CDN/load balancing/caching/pooling**, **security** (crypto, firewalls, attacks, VPN, zero trust), **wireless/5G/SDN/NFV** |
| 05 | [System Design](05_System_Design.md) | **Framework** + back-of-envelope (latency numbers, powers of two), **trade-offs** (CAP/PACELC, availability math), **consistency & availability patterns**, **DNS/CDN**, **LB/proxy/API-gateway**, **microservices** (service discovery, circuit breaker, saga, service mesh), **DBs at scale** (replication, federation, **sharding**, NoSQL, B-tree vs LSM), **caching** (all strategies + 4 failure modes), **async/messaging** (Kafka, RabbitMQ, SQS/SNS, CQRS, event sourcing), **rate limiting**, **consistent hashing**, **Snowflake IDs**, **distributed systems** (Raft/Paxos, 2PC/3PC/Saga, idempotency, locks, vector clocks, quorums), **observability** (logs/metrics/traces, SLI/SLO), **12 canonical designs** (URL shortener, Twitter feed, YouTube, WhatsApp, Drive, rate limiter, crawler, notifications, autocomplete, proximity, Uber, scheduler) |

---

## Conceptual Map

```
                    CS Fundamentals
   ┌──────────────┬──────────────┬──────────────┐
   OS            DBMS           OOP           Networks
   │              │              │              │
 processes     ACID +         classes +     OSI/TCP-IP
 threads       indexing       inheritance   TCP/UDP, HTTP
 scheduling    transactions   polymorphism  DNS/TLS/CDN
 memory/VM     concurrency    SOLID +       routing/LB
 deadlocks     normalization  patterns      security
   │              │              │              │
   └──────────────┴──────┬───────┴──────────────┘
                         ▼
                  System Design
        (synthesize everything to build at scale)
   concurrency → caching, locks, queues
   DBMS        → sharding, replication, CAP
   OOP         → service/LLD design, patterns
   Networks    → DNS, LB, CDN, TLS, protocols
        + distributed systems (consensus, sagas)
        + observability + canonical designs
```

---

## Recurring Themes

- **Everything is a trade-off.** OS scheduling (throughput vs. response time), DB normalization (redundancy vs. join cost), CAP (consistency vs. availability), and caching (freshness vs. latency) are all balancing acts — always state assumptions and justify choices.
- **Concurrency is the hard part.** Race conditions, deadlocks, and the four deadlock conditions appear in OS synchronization, DB 2PL/MVCC, and distributed locks/sagas.
- **Caching and locality are everywhere.** CPU caches → TLB → DB buffer pool/indexes → CDN → Redis. The same ideas (hit/miss, eviction, invalidation) recur at every layer.
- **Layering and abstraction.** OSI layers, DB abstraction levels, and OOP encapsulation all hide complexity behind clean interfaces.
- **Scale changes everything.** A single-node design (one DB, one server) becomes sharding, replication, queues, and consistent hashing once load grows — System Design is where the other four topics meet reality.
- **Know the internals.** vtable/vptr, B+ tree leaf links, TCP congestion control, and Snowflake ID bit layout are the kind of "how does it actually work" details that separate strong answers.
