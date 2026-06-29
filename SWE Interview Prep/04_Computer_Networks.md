# Computer Networks

A **computer network** is a set of interconnected devices that exchange data using shared protocols. This note builds the full networking interview surface from the ground up: the layered models, then each layer (physical, data link, network, transport, application), socket programming, reliability/scalability/performance (CDN, load balancing, caching), network security, and wireless/modern topics.

> Rendering note: all math uses `$...$`; superscripts use `\ast` where a star is intended (e.g., $2^{n}-1$, not literal asterisks).

---

## 1. Models and Architecture

### The OSI Model (7 layers)

| # | Layer | Function | PDU | Protocols/Standards | Device |
|---|---|---|---|---|---|
| 7 | **Application** | Network services to apps | Data | HTTP, FTP, DNS, SMTP | Gateway |
| 6 | **Presentation** | Translation, encryption, compression | Data | TLS/SSL, JPEG, ASCII | Gateway |
| 5 | **Session** | Establish/manage/terminate sessions | Data | NetBIOS, RPC, sockets | Gateway |
| 4 | **Transport** | End-to-end delivery, reliability, ports | Segment (TCP) / Datagram (UDP) | TCP, UDP | — |
| 3 | **Network** | Logical addressing, routing | Packet | IP, ICMP, OSPF, BGP | Router |
| 2 | **Data Link** | Framing, MAC, error detection on a link | Frame | Ethernet, PPP, 802.11 | Switch, bridge |
| 1 | **Physical** | Bits over the medium | Bit | Cables, signals, Ethernet PHY | Hub, repeater |

Mnemonic (top→bottom): **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing.

### The TCP/IP Model (4 layers)
| TCP/IP Layer | Maps to OSI | Protocols |
|---|---|---|
| **Application** | Application + Presentation + Session | HTTP, DNS, SMTP, FTP |
| **Transport** | Transport | TCP, UDP |
| **Internet** | Network | IP, ICMP, ARP |
| **Network Access / Link** | Data Link + Physical | Ethernet, WiFi |

TCP/IP **collapsed the top three OSI layers** into one application layer because, in practice, applications handle session/presentation concerns themselves (e.g., TLS, serialization).

### Encapsulation / Decapsulation
Data flows **down** the sender's stack and **up** the receiver's. Each layer adds its own **header** (and the data link layer adds a trailer/FCS):

```
Application:  [ Data ]
Transport:    [ TCP hdr | Data ]                  → Segment
Network:      [ IP hdr  | TCP hdr | Data ]        → Packet
Data Link:    [ Frame hdr | IP hdr | TCP hdr | Data | FCS ] → Frame
Physical:      bits on the wire
```

The receiver strips one header per layer on the way up (decapsulation).

---

## 2. Physical Layer

### Signals and Capacity
- **Analog** (continuous) vs. **digital** (discrete) signals.
- **Bandwidth (Hz)** is the frequency range of a channel; **data rate (bps)** is bits per second.

**Nyquist theorem (noiseless channel)** — maximum bit rate with $V$ signal levels and bandwidth $B$:

$$
C = 2B \log_2 V \quad \text{(bps)}
$$

**Shannon–Hartley theorem (noisy channel)** — capacity with signal-to-noise ratio $S/N$:

$$
C = B \log_2\!\left(1 + \frac{S}{N}\right) \quad \text{(bps)}
$$

SNR is often given in **decibels**: $\text{SNR}_{dB} = 10 \log_{10}(S/N)$.

### Transmission Media
| Medium | Notes |
|---|---|
| **Twisted pair (UTP/STP)** | Cheap, common (Cat5e/6); susceptible to interference; limited distance |
| **Coaxial** | Better shielding; cable TV, older Ethernet |
| **Fiber-optic** | Light over glass; huge bandwidth, immune to EMI; **single-mode** (long-haul) vs. **multi-mode** (short) |
| **Wireless** | No cabling; affected by attenuation, interference, distance |

Impairments: **attenuation** (signal weakens with distance), **noise/interference**, **distortion**.

### Encoding and Modulation
- **Line coding:** **NRZ** (Non-Return-to-Zero), **Manchester** (self-clocking, transition mid-bit), **4B/5B** (maps 4 data bits to 5 code bits to ensure transitions).
- **Modulation:** **AM/FM/PM** (amplitude/frequency/phase), **QAM** (combines amplitude + phase for many bits per symbol).

### Multiplexing
- **FDM** (Frequency-Division) — different frequency bands.
- **TDM** (Time-Division) — time slots.
- **WDM** (Wavelength-Division) — fiber wavelengths.
- **OFDM** (Orthogonal FDM) — overlapping orthogonal subcarriers (WiFi, LTE).

---

## 3. Data Link Layer

Provides node-to-node delivery over a single link. Sublayers: **LLC** (logical link control) and **MAC** (media access control).

### Framing
- **Character count** — a field states the frame length (fragile if corrupted).
- **Flag bytes with byte stuffing** — delimit frames with a flag byte; escape the flag inside data.
- **Bit stuffing** — flag `01111110`; insert a `0` after five consecutive `1`s in the data.

### Error Detection
- **Parity** — even/odd parity bit; **2D parity** detects and can locate some errors.
- **Checksum** — sum of segments (used by IP/TCP/UDP).
- **CRC (Cyclic Redundancy Check)** — treat the message as a polynomial, divide by a **generator polynomial** $G(x)$; append the remainder. The receiver re-divides; a non-zero remainder means an error. Powerful for burst errors.

### Error Correction — Hamming Code
- **Minimum Hamming distance** $d_{min}$ between valid codewords determines power: detect up to $d_{min}-1$ errors, correct up to $\lfloor (d_{min}-1)/2 \rfloor$.
- For **single-error correction**, parity bits occupy positions $1, 2, 4, 8, \dots$ (powers of two); each parity bit checks positions whose index has a particular bit set. The receiver computes the syndrome; a non-zero syndrome gives the position of the flipped bit. **SECDED** adds an overall parity bit for double-error detection.

### Flow Control
- **Stop-and-Wait** — send one frame, wait for ACK. With $a = T_p / T_t$ (propagation/transmission time):

$$
\text{efficiency} = \frac{1}{1 + 2a}
$$

- **Sliding window** — allow multiple unacknowledged frames (window size $W$); efficiency $= \min(1, W/(1+2a))$.
  - **Go-Back-N (GBN)** — receiver discards out-of-order frames; sender retransmits from the lost frame. Sender window $\le 2^{n}-1$ (with $n$-bit sequence numbers).
  - **Selective Repeat (SR)** — receiver buffers out-of-order frames; sender retransmits only the errored frame. Window $\le 2^{\,n-1}$.

### MAC Sublayer — Multiple Access Protocols
- **ALOHA** — pure (transmit anytime; max efficiency $\approx 18\%$), slotted (aligned slots; $\approx 37\%$).
- **CSMA** (Carrier Sense Multiple Access) — listen before transmit: **1-persistent**, **p-persistent**, **non-persistent**.
- **CSMA/CD** (Collision Detection) — detect collisions while sending; **binary exponential backoff**; used by classic wired **Ethernet (802.3)**.
- **CSMA/CA** (Collision Avoidance) — avoid collisions with **RTS/CTS** handshakes; used by **WiFi (802.11)** (can't detect collisions while transmitting on wireless).

### Ethernet
**Frame structure:** Preamble (7B) | SFD (1B) | Dest MAC (6B) | Src MAC (6B) | Type/Length (2B) | Payload (46–1500B) | FCS/CRC (4B). **MAC address** = 6 bytes; the first 3 bytes are the **OUI** (vendor).

### Switches vs. Hubs
- **Hub** — physical-layer repeater; broadcasts to all ports (one collision domain).
- **Switch** — data-link device; learns a **MAC address table**, then **forwards/filters** frames to the right port; **floods** unknown destinations. Each port is its own collision domain.
- **STP (Spanning Tree Protocol)** — prevents loops by disabling redundant links to form a loop-free tree.
- **VLANs (802.1Q)** — partition a switch into logical broadcast domains via a VLAN tag.

### PPP (Point-to-Point Protocol)
Used on DSL and WAN links; phases include **LCP** (link control) and **NCP** (network control), with optional authentication (PAP/CHAP).

---

## 4. Network Layer

### IPv4
- **32-bit** address in dotted-decimal (e.g., `192.168.1.10`).
- **Header fields:** version, IHL (header length), DSCP/ECN, total length, identification, flags (DF/MF), fragment offset, **TTL**, protocol, header checksum, source IP, destination IP, options.
- **Fragmentation/reassembly** — routers fragment packets larger than the link MTU; the destination reassembles using ID + offset + MF flag.

### Address Classes and CIDR
- Historical classes: **A** (`/8`), **B** (`/16`), **C** (`/24`), **D** (multicast), **E** (reserved) — largely replaced by **CIDR**.
- **CIDR** — prefix notation `/n`; number of host addresses $= 2^{\,32-n} - 2$ (subtract network + broadcast).

> **Subnetting example.** `192.168.1.0/24` split into 4 subnets → borrow 2 bits → `/26`, each with $2^{6}-2 = 62$ hosts: `.0/26`, `.64/26`, `.128/26`, `.192/26`. Network address = all host bits 0; broadcast = all host bits 1.

### IPv6
- **128-bit**, colon-hex (e.g., `2001:db8::1`).
- Advantages: vast address space (no NAT needed), stateless auto-configuration (SLAAC), simpler fixed header, built-in IPsec.
- **Transition:** dual-stack, tunneling (6to4), NAT64/DNS64.

### NAT (Network Address Translation)
Maps private addresses to public ones.
- **Static NAT** — 1:1 fixed mapping.
- **Dynamic NAT** — pool of public addresses.
- **PAT / NAPT** — many private hosts share one public IP, distinguished by **port**.
- Breaks end-to-end connectivity; **NAT traversal** uses **STUN/TURN** (see §10).

### ARP (Address Resolution Protocol)
Maps **IP → MAC** on a local link. Request is **broadcast** ("who has 192.168.1.1?"), reply is **unicast**. Results are cached (**ARP cache**). **Gratuitous ARP** announces one's own mapping. **ARP poisoning/spoofing** is a MITM attack (forge mappings).

### ICMP
Error/diagnostic messaging: destination unreachable, **time exceeded** (TTL=0), redirect, **echo request/reply** (`ping`). **`traceroute`** sends packets with increasing TTL, eliciting "time exceeded" from each hop.

### Routing
- **Forwarding table** + **longest-prefix match** select the next hop.
- **Autonomous System (AS)** — a network under one administrative control.

**Distance-Vector (RIP)** — Bellman-Ford ("routing by rumor"): each router shares its distance vector with neighbors. Hop-count metric (**max 15**); periodic 30s updates; suffers the **count-to-infinity** problem, mitigated by **split-horizon** and **poison-reverse**.

**Link-State (OSPF)** — each router floods **LSAs** describing its links; all build a complete topology map and run **Dijkstra** for shortest paths. Uses **areas** (backbone **area 0**, ABR, ASBR); cost metric; fast convergence.

| | Distance-Vector | Link-State |
|---|---|---|
| Knowledge | Neighbors only | Full topology |
| Algorithm | Bellman-Ford | Dijkstra |
| Convergence | Slow (count-to-infinity) | Fast |
| Overhead | Lower CPU/memory | Higher CPU/memory |
| Example | RIP | OSPF |

**BGP (path-vector)** — the inter-AS routing protocol of the Internet; **policy-based**; **eBGP** (between ASes) and **iBGP** (within); uses the **AS-PATH** attribute for loop prevention and route selection; supports route aggregation. **BGP hijacking** (announcing routes you don't own) is a real-world threat.

---

## 5. Transport Layer

### Ports and (De)multiplexing
- **Well-known** 0–1023, **registered** 1024–49151, **ephemeral** 49152–65535.
- Common ports: HTTP **80**, HTTPS **443**, DNS **53**, DHCP **67/68**, FTP **20/21**, SSH **22**, SMTP **25/587**, POP3 **110**, IMAP **143**.
- A connection is uniquely identified by the **4-tuple** (src IP, src port, dst IP, dst port) — this is how a server demultiplexes many clients.

### UDP
- **8-byte header:** source port, dest port, length, checksum.
- Connectionless, no ordering, no reliability, no congestion control → low overhead, low latency.
- Use cases: DNS, DHCP, streaming, gaming, VoIP, **QUIC**.

### TCP
- **Header:** source/dest port, **sequence number**, **acknowledgment number**, data offset, **flags** (SYN, ACK, FIN, RST, PSH, URG), **window size**, checksum, urgent pointer, options. Provides a reliable, ordered, **byte-stream** abstraction.

**Three-way handshake (connection setup):**
```
Client ── SYN(seq=x) ───────────►  Server
Client ◄── SYN(seq=y), ACK(x+1) ── Server
Client ── ACK(y+1) ─────────────►  Server   → ESTABLISHED
```
Initial sequence numbers (**ISN**) are randomized; **SYN cookies** defend against SYN floods.

**Four-way teardown:**
```
A ── FIN ──► B
A ◄── ACK ── B
A ◄── FIN ── B
A ── ACK ──► B   → A enters TIME_WAIT (2×MSL) then CLOSED
```
**TIME_WAIT** (2×Maximum Segment Lifetime) ensures stray segments die out and the final ACK is delivered. **RST** aborts abruptly; **half-close** allows one direction to close.

**State diagram (abridged):** `CLOSED → LISTEN → SYN_RCVD / SYN_SENT → ESTABLISHED → FIN_WAIT_1/2 → TIME_WAIT → CLOSED`.

**Flow control** — the receiver advertises a **receive window (rwnd)** in each segment; the sender never sends more unacked data than `rwnd`. **Zero-window probes** restart a stalled flow. **Silly window syndrome** (tiny segments) is mitigated by **Nagle's algorithm** (coalesce small writes) and delayed ACKs.

**Congestion control (TCP Reno):**
- **Slow start** — `cwnd` starts at 1 MSS and **doubles each RTT** (exponential) until it reaches **ssthresh**.
- **Congestion avoidance** — additive increase: `cwnd += 1 MSS` per RTT (linear) — this is **AIMD** (additive-increase, multiplicative-decrease).
- **Fast retransmit** — **3 duplicate ACKs** trigger retransmission without waiting for a timeout.
- **Fast recovery** — set `ssthresh = cwnd/2`, `cwnd = ssthresh + 3`, continue (Reno). **TCP Tahoe** instead drops `cwnd` to 1 on any loss.
- Modern variants: **CUBIC** (default on Linux; window grows as a cubic function of time since last loss — good for high-bandwidth/high-latency), **BBR** (models bottleneck bandwidth and RTT instead of reacting to loss).

**RTT estimation (Jacobson/Karn):**

$$
\text{SRTT} = (1-\alpha)\,\text{SRTT} + \alpha\,\text{RTT}_{sample}, \qquad
\text{RTTVAR} = (1-\beta)\,\text{RTTVAR} + \beta\,\lvert \text{SRTT} - \text{RTT}_{sample}\rvert
$$

$$
\text{RTO} = \text{SRTT} + 4\cdot\text{RTTVAR}
$$

**Karn's algorithm** — don't update RTT estimates from retransmitted segments (ambiguous ACKs); back off RTO exponentially on timeout.

| | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented | Connectionless |
| Reliability | Guaranteed, ordered | Best-effort |
| Overhead | Higher (20-byte header, handshakes) | Lower (8-byte header) |
| Control | Flow + congestion control | None |
| Use cases | Web, email, file transfer | DNS, streaming, gaming, VoIP |

---

## 6. Application Layer

### HTTP Evolution
- **HTTP/1.0** — non-persistent (one request per TCP connection).
- **HTTP/1.1** — persistent connections (keep-alive), pipelining, chunked transfer, mandatory **Host** header (virtual hosting).
- **HTTP/2** — binary framing, **multiplexing** many streams over one TCP connection, **HPACK** header compression, server push. Still suffers **TCP head-of-line blocking**.
- **HTTP/3** — runs over **QUIC** (UDP); eliminates transport-level head-of-line blocking (independent streams), integrates TLS 1.3, **0-RTT** connection setup, connection migration across IP changes.

### HTTP Methods and Semantics
`GET` (safe, idempotent), `POST` (not idempotent), `PUT` (idempotent — replace), `PATCH` (partial update), `DELETE` (idempotent), `HEAD`, `OPTIONS`, `CONNECT`. **Safe** = no side effects; **idempotent** = repeating yields the same result.

### Status Codes
- **1xx** informational.
- **2xx** success: 200 OK, 201 Created, 204 No Content.
- **3xx** redirection: 301 Moved Permanently, 302 Found, 304 Not Modified.
- **4xx** client error: 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 429 Too Many Requests.
- **5xx** server error: 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable, 504 Gateway Timeout.

### Cookies and Sessions
`Set-Cookie` / `Cookie` headers; flags **HttpOnly** (no JS access), **Secure** (HTTPS only), **SameSite** (CSRF mitigation). **Session** state stored server-side (cookie holds a session ID) vs. **JWT** (stateless, signed token holds claims).

### API Styles
| | REST | GraphQL | gRPC |
|---|---|---|---|
| Style | Resource-based over HTTP | Query language | Binary RPC |
| Data | JSON | JSON (client-shaped) | Protocol Buffers |
| Transport | HTTP/1.1+ | HTTP | HTTP/2 |
| Strength | Simple, cacheable | No over/under-fetching | Fast, strongly typed, streaming |

### HTTPS + TLS
TLS provides confidentiality, integrity, and authentication. **Asymmetric** crypto (RSA / **ECDHE**) negotiates a shared **symmetric** session key (e.g., **AES-GCM**); bulk data uses the fast symmetric cipher.

**TLS 1.2 handshake:** `ClientHello → ServerHello → Certificate → ServerHelloDone → ClientKeyExchange → ChangeCipherSpec → Finished` (2 round trips).
**TLS 1.3 improvements:** 1-RTT (and **0-RTT** resumption), removed static RSA key exchange, **mandatory perfect forward secrecy** (ephemeral DH), fewer/cleaner cipher suites.

**Certificate chain:** root CA → intermediate CA → leaf (server) certificate, verified up the chain. **OCSP stapling** proves the cert isn't revoked; **SNI** lets one IP host many TLS sites.

### DNS
Translates **name → IP**. Record types: **A** (IPv4), **AAAA** (IPv6), **CNAME** (alias), **MX** (mail), **NS** (name server), **TXT**, **PTR** (reverse), **SOA**. Resolution chain: browser cache → OS cache → recursive resolver → **root** → **TLD** (`.com`) → **authoritative** server. **TTL** controls caching; **negative caching** stores NXDOMAIN. Privacy: **DoH** (DNS over HTTPS) and **DoT** (DNS over TLS). Attacks: **cache poisoning** (fix: DNSSEC) and **DNS amplification** DDoS.

### DHCP
Dynamic host configuration via **DORA**: **Discover → Offer → Request → Acknowledge**. Provides IP, subnet mask, gateway, DNS; has **lease time**, renewal, and rebinding; a **relay agent** forwards across subnets.

### Email Protocols
- **SMTP** (send, ports 25/587), **POP3** (download-and-delete, 110), **IMAP** (server-side storage/sync, 143).
- **MIME** encodes attachments; **SPF/DKIM/DMARC** authenticate senders to fight spoofing.

### WebSockets, SSE, Long-Polling
- **WebSocket** — upgrade from HTTP (`101 Switching Protocols`), then **full-duplex** over one TCP connection; for chat, live updates, gaming.
- **SSE (Server-Sent Events)** — one-way server→client stream over HTTP.
- **Long-polling** — client holds a request open until the server has data; a fallback before WebSockets.

### FTP
**Active** vs. **passive** mode; control (port 21) + data (port 20) connections. Secure variants: **SFTP** (over SSH) and **FTPS** (FTP over TLS).

---

## 7. Socket Programming

### TCP Server / Client
```
Server: socket() → bind() → listen() → accept() → read()/write() → close()
Client: socket() → connect() → read()/write() → close()
```
`accept()` blocks and returns a **new** socket fd for the connection (the listening socket keeps accepting).

### UDP
```
Server: socket() → bind() → recvfrom()/sendto()
Client: socket() → sendto()/recvfrom()   (no connect needed)
```

### Socket Options and I/O Multiplexing
- `SO_REUSEADDR` (reuse a port in TIME_WAIT), `SO_KEEPALIVE` (detect dead peers), `TCP_NODELAY` (disable Nagle for low latency).
- **Non-blocking sockets** + an event loop scale to many connections:
  - **`select`** — O(n) scan of fd sets, limited by `FD_SETSIZE`.
  - **`poll`** — O(n) but no fixed limit.
  - **`epoll`** (Linux) / **`kqueue`** (BSD) — O(1) readiness notification; scales to tens of thousands of connections. **Edge-triggered** (notify only on state change — must drain) vs. **level-triggered** (notify while ready).
- The **reactor pattern** (an event loop dispatching ready events to handlers) underlies Nginx, Node.js, Redis.

---

## 8. Reliability, Scalability, and Performance

### CDN (Content Delivery Network)
Geographically distributed caches.
- **PoP (Point of Presence)** — edge locations close to users; multi-tier **cache hierarchy** (edge → regional → origin shield).
- **Anycast routing** — the same IP is advertised from many PoPs; BGP routes users to the nearest.
- **Push vs. Pull:** *push* CDN — you upload content ahead of time (good for known assets); *pull* CDN — the CDN fetches from the origin on first request and caches by TTL (good for large/changing catalogs).
- Uses: static assets, video streaming, **DDoS mitigation**, TLS termination at the edge.

### Load Balancing
- **L4 (transport)** — routes by IP/port; fast, no content inspection.
- **L7 (application)** — routes by URL/header/cookie; can do SSL termination, compression, content-based routing.
- **Algorithms:** round-robin, weighted round-robin, **least connections**, least response time, **IP hash**, **consistent hashing** (virtual nodes → minimal disruption when servers scale).
- **Health checks** remove unhealthy backends; **sticky sessions** pin a client to a server (complicates scaling).
- **Active-passive** vs. **active-active** HA for the balancers themselves.

### Proxies
- **Forward proxy** — sits in front of clients (corporate egress, caching, anonymity).
- **Reverse proxy** (Nginx, HAProxy) — sits in front of servers; SSL termination, compression, **rate limiting**, auth, **canary routing**, caching.
- **Transparent proxy** — intercepts without client configuration.

### HTTP Caching
- **`Cache-Control: max-age=…`**, `no-cache`, `private`/`public`.
- **Validation:** `ETag` / `If-None-Match` (entity tag), `Last-Modified` / `If-Modified-Since` → `304 Not Modified` (no body re-sent).
- **`Vary`** caches different responses per header (e.g., `Accept-Encoding`).
- Layers: browser cache → CDN cache → reverse-proxy cache → application cache (Redis/Memcached). **Cache invalidation is one of the hard problems.** Strategies: write-through, write-behind, cache-aside (see the System Design note).

### Connection Pooling
Establishing a TCP connection costs a three-way handshake (+ TLS handshake) round trips. A **pool** of keep-alive connections amortizes this cost — essential for DB and service clients.

### Performance Quantities
- **Bandwidth** (capacity, bps), **latency** (delay, s), **throughput** (achieved rate). 
- **Bandwidth-Delay Product (BDP)** $= \text{bandwidth} \times \text{RTT}$ — the amount of in-flight data needed to keep a pipe full (sizes the TCP window).
- **Little's Law:** $L = \lambda W$ — average number in the system = arrival rate × average time in system (sizing queues/connections).

---

## 9. Network Security

### Cryptography
- **Symmetric** — one shared key; **AES** (block cipher, modes CBC/**GCM**), **ChaCha20**. Fast, but key distribution is the challenge.
- **Asymmetric** — public/private key pair; **RSA**, **Diffie-Hellman / ECDH** key exchange. **Digital signatures** — sign with the private key, verify with the public key. **X.509 certificates** bind a public key to an identity (issued by CAs).
- **Hashing** for integrity — **MD5** (broken), **SHA-1** (broken), **SHA-256 / SHA-3**; **HMAC** for keyed integrity.
- Hybrid systems (TLS) use asymmetric crypto to exchange a symmetric session key, then symmetric crypto for bulk data.

### Firewalls
- **Packet-filter (stateless)** — rules on src/dst IP, port, protocol.
- **Stateful inspection** — tracks connection state, allows return traffic of established connections.
- **Application-layer / WAF** — deep packet inspection, L7 rules (block SQLi/XSS patterns).

### Common Attacks and Defenses
| Attack | Mechanism | Defense |
|---|---|---|
| **SYN flood** | Many half-open connections exhaust resources | SYN cookies, rate limiting |
| **DDoS** | Volumetric / protocol / application-layer flooding | CDN/scrubbing centers, anycast, rate limiting |
| **MITM** | Intercept/alter traffic | TLS + certificate pinning |
| **DNS cache poisoning** | Inject fake records | DNSSEC (signed records) |
| **SQL injection / XSS / CSRF** | App-layer input attacks | Parameterized queries, output encoding/CSP, CSRF tokens, WAF |
| **ARP poisoning** | Forge IP↔MAC mappings | Dynamic ARP inspection on switches |

### VPN
Tunnels encrypted traffic over a public network — **IPsec**, **WireGuard**, **OpenVPN**. **Site-to-site** (connect networks) vs. **remote-access** (a user to a network); **split tunneling** routes only some traffic through the VPN.

### Zero Trust
"Never trust, always verify" — authenticate/authorize every request regardless of network location; **micro-segmentation**; **mTLS** (mutual TLS) for service-to-service authentication.

---

## 10. Wireless and Modern Topics

### WiFi (802.11 a/b/g/n/ac/ax)
Uses **CSMA/CA**. The **hidden-node problem** (two stations can't hear each other but both reach the AP) is mitigated by **RTS/CTS**. **SSID** identifies a network; security: **WPA2** (AES-CCMP), **WPA3** (SAE handshake). Bands: **2.4 GHz** (longer range, more interference) vs. **5 GHz** (more bandwidth, shorter range); 802.11ax (WiFi 6) adds OFDMA and MU-MIMO.

### Cellular
- **4G LTE** — **OFDMA** downlink, **SC-FDMA** uplink; all-IP core.
- **5G** — **mmWave** (very high bandwidth, short range) and **sub-6 GHz**; **network slicing** (logical networks per use case), **massive MIMO**, ultra-low latency (URLLC).

### NAT Traversal for P2P
- **STUN** — discover your public IP/port behind NAT.
- **TURN** — relay traffic when direct connection fails.
- **ICE** — try candidate paths (host, STUN, TURN) to find the best — used by **WebRTC**.

### Software-Defined Networking (SDN)
Separate the **control plane** (decision logic) from the **data plane** (forwarding). A central controller programs switches via **OpenFlow**, enabling network programmability and centralized policy.

### Network Function Virtualization (NFV)
Run network functions (routers, firewalls, load balancers) as software on VMs/containers instead of dedicated hardware appliances — elastic, cheaper, and faster to deploy.

---

## 11. "What Happens When You Type google.com?" (ties it all together)

1. **DNS resolution** — browser cache → OS cache → recursive resolver → root → `.com` TLD → authoritative → IP returned.
2. **TCP handshake** (SYN/SYN-ACK/ACK), then **TLS handshake** (certificate validation, session-key negotiation).
3. **HTTP request** — `GET /` (HTTP/2 or HTTP/3 over QUIC).
4. **Routing** — anycast directs to the nearest datacenter; an **L7 load balancer** / reverse proxy receives the request.
5. **Application servers** serve the response, hitting caches (Memcache) and storage; CDN serves static assets.
6. **Rendering** — browser parses HTML → DOM, CSS → CSSOM → render tree → layout → paint; executes JS; fetches additional resources (CSS/JS/images) from the CDN.

This single question spans **DNS, TCP/IP, TLS, HTTP, CDN, load balancing, and rendering** — a favorite because it exercises the whole stack.

---

## 12. Quick Interview Checklist

- **OSI 7 layers** vs. **TCP/IP 4 layers**; know each layer's PDU and devices.
- **TCP vs. UDP:** reliability/ordering/congestion control vs. low overhead.
- **3-way handshake** (SYN/SYN-ACK/ACK), **4-way teardown**, **TIME_WAIT** = 2×MSL.
- **Congestion control** = AIMD: slow start (exponential) → congestion avoidance (linear); 3 dup ACKs → fast retransmit.
- **CIDR hosts** $= 2^{\,32-n} - 2$; **NAT/PAT** shares one public IP by port.
- **DV (RIP, Bellman-Ford)** vs. **LS (OSPF, Dijkstra)**; **BGP** routes between ASes.
- **TLS 1.3** = 1-RTT, mandatory forward secrecy; certificate chain to a root CA.
- **DNS** record types + resolution chain; **DoH/DoT** for privacy.
- **L4 vs. L7 LB**; **consistent hashing** minimizes reshuffling.
- **HTTP/3 over QUIC** removes TCP head-of-line blocking.
