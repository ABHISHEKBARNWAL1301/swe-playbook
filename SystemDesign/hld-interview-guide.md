# HLD Interview Guide (SDE2)

Everything needed for the system design round, in one page: how the round works → estimation → every core concept in depth → 26 problems with full solutions → how to present a system you built.

Related: [syllabus](../test/INTERVIEW_SYLLABUS.md) · [LLD guide](lld-interview-guide.md) · [concurrency guide](concurrency-interview-guide.md) · [GenAI guide](genai.md) (LLM apps, RAG, agents, LLM serving)

## Index

**Part A — How to run the round**
- [1. What the SDE2 bar looks like](#1-what-the-sde2-bar-looks-like)
- [2. The 45-minute framework](#2-the-45-minute-framework)
- [3. Back-of-the-envelope estimation](#3-back-of-the-envelope-estimation)

**Part B — Concepts**
- [4. Networking fundamentals](#4-networking-fundamentals)
- [5. API design](#5-api-design)
- [6. Load balancing, proxies, gateways, service discovery](#6-load-balancing-proxies-gateways-service-discovery)
- [7. CDN and edge](#7-cdn-and-edge)
- [8. Caching](#8-caching)
- [9. Databases: relational internals](#9-databases-relational-internals)
- [10. Databases: NoSQL and data modeling](#10-databases-nosql-and-data-modeling)
- [11. Replication](#11-replication)
- [12. Partitioning and sharding](#12-partitioning-and-sharding)
- [13. Distributed systems core](#13-distributed-systems-core)
- [14. Messaging and stream processing](#14-messaging-and-stream-processing)
- [15. Storage: block, file, object, data lake, warehouse](#15-storage-block-file-object-data-lake-warehouse)
- [16. Search](#16-search)
- [17. Special data structures](#17-special-data-structures)
- [18. Reliability and resilience](#18-reliability-and-resilience)
- [19. Security](#19-security)
- [20. Observability](#20-observability)
- [21. Architecture styles and deployment](#21-architecture-styles-and-deployment)

**Part C — Problems**
- [22. Tier 1 problems](#22-tier-1-problems)
- [23. Tier 2 problems](#23-tier-2-problems)
- [24. Tier 3 problems](#24-tier-3-problems)

**Part D**
- [25. Presenting a system you built](#25-presenting-a-system-you-built)
- [26. Common mistakes and phrases that help](#26-common-mistakes-and-phrases-that-help)

---

# Part A — How to run the round

## 1. What the SDE2 bar looks like

| Signal | SDE1 | **SDE2 (target)** | Senior |
|---|---|---|---|
| Requirements | Needs prompting | **Drives it, picks a scope, states non-functional needs** | Finds hidden requirements |
| Estimation | Skips | **Rough numbers that change design choices** | Uses numbers to compare options |
| Design | Boxes connected | **Working end-to-end design; every box has a reason** | Several options compared deeply |
| Data model | Vague | **Tables/keys/partition keys + DB choice with reasons** | Access-pattern driven, migrations |
| Deep dive | Shallow | **Goes deep on 1–2 components (cache, queue, sharding, consistency)** | Goes deep anywhere |
| Tradeoffs | Rare | **States the tradeoff of each major choice** | Quantifies them |
| Failure | Ignored | **Retries, idempotency, replication, single points of failure** | Multi-region, ops, cost |

**Why SDE2 candidates fail:** no numbers, no data model, picking technologies by name ("use Kafka") without saying why, never discussing failure, and monologuing without checking what the interviewer wants to dig into.

## 2. The 45-minute framework

| Step | Time | Output |
|---|---|---|
| 1. Functional requirements | 3–5 min | 3–5 core features. Say what's **out of scope**. |
| 2. Non-functional requirements | 2–3 min | Scale (DAU, data size), latency target (p99), availability vs consistency, durability, read/write ratio. |
| 3. Estimates | 3–5 min | QPS (avg + peak), storage/year, bandwidth. Only numbers that affect the design. |
| 4. API | 3–5 min | 3–6 endpoints: method, path, params, response. |
| 5. Data model | 5 min | Entities, keys, partition key, indexes. DB choice + why. |
| 6. High-level design | 10 min | Diagram. Walk one write path and one read path end to end. |
| 7. Deep dives | 10–15 min | The hard part of *this* problem. Ask the interviewer which part to go deep on. |
| 8. Wrap up | 2–3 min | Bottlenecks, failure modes, monitoring, next steps. |

**Requirements checklist (ask these out loud):**
- Who are the users and how many (DAU, peak concurrent)?
- Read-heavy or write-heavy? Ratio?
- Latency target? Real-time or eventual?
- Can data be stale? For how long? Can we lose data?
- Global or one region? Mobile, web, or both?
- Any hard rule (no double booking, no double charge)?

**Walk-through example (first 2 minutes of "design Instagram"):**
> "Core features: upload photo, follow users, home feed, like/comment. Out of scope: stories, DMs, ads, search. Non-functional: 500M DAU, feed p99 under 300 ms, very read-heavy, feed can be a few seconds stale, uploads must never be lost. I'll estimate next."

## 3. Back-of-the-envelope estimation

### Numbers to memorize

**Time conversions**
- 1 day = 86,400 s ≈ **10^5 s** (use 100K for quick math)
- 1 month ≈ 2.5 × 10^6 s · 1 year ≈ 3 × 10^7 s
- **1M/day ≈ 12/s** · 10M/day ≈ 120/s · 100M/day ≈ 1.2K/s · 1B/day ≈ 12K/s
- Peak ≈ 2–3× average (5–10× for events like sales, New Year)

**Powers of two**
- 2^10 ≈ 1 thousand (KB) · 2^20 ≈ 1 million (MB) · 2^30 ≈ 1 billion (GB) · 2^40 ≈ 1 trillion (TB) · 2^50 = PB

**Sizes**
- char (ASCII) 1 B · int32 4 B · int64/timestamp 8 B · UUID 16 B
- Short text row (tweet + metadata) ≈ 0.5–1 KB · user profile ≈ 1 KB
- Compressed photo ≈ 200 KB–2 MB · 1 min 1080p video ≈ 50–100 MB · 1 min audio ≈ 1 MB

**Latency (order of magnitude)**

| Operation | Time |
|---|---|
| L1 cache reference | ~1 ns |
| Main memory reference | ~100 ns |
| Compress 1 KB (fast codec) | ~2 µs |
| Read 1 MB sequentially from memory | ~10 µs |
| SSD random read | ~100 µs |
| Read 1 MB sequentially from SSD | ~1 ms |
| Round trip within a data center | ~0.5 ms |
| HDD seek | ~10 ms |
| Round trip US ↔ Europe | ~80–150 ms |

Takeaways: memory ≫ SSD ≫ network ≫ disk seek. Avoid cross-region calls in the request path. Batch small network calls.

**Single-machine capacity (ballpark — for deciding whether to scale out)**

| Component | Rough capacity |
|---|---|
| Stateless app server | 1K–10K req/s (depends on work per request) |
| Postgres/MySQL (good hardware) | ~10K simple reads/s, ~1–5K writes/s; a few TB comfortably |
| Redis | ~100K ops/s per node (single-threaded command execution) |
| Cassandra node | ~10K+ writes/s per node, scales linearly |
| Kafka broker | hundreds of MB/s |
| Elasticsearch node | hundreds to a few thousand queries/s (query-dependent) |
| WebSocket server | 50K–100K+ concurrent connections |
| Network card | 10 Gbps ≈ 1.25 GB/s |

### Method

1. **Traffic:** DAU × actions per user per day ÷ 10^5 = average QPS. × 2–3 = peak.
2. **Read/write split:** separate the two; they scale differently.
3. **Storage:** writes per day × size × retention. Add replication factor (×3) if asked.
4. **Bandwidth:** QPS × response size.
5. **Memory for cache:** 80/20 rule — cache 20% of daily read data.
6. **Servers:** peak QPS ÷ per-server capacity (with headroom).

### Worked example 1: social feed

- 200M DAU, each opens feed 10×/day → 2B reads/day ≈ **23K QPS**, peak ~70K.
- Each user posts 0.5×/day → 100M posts/day ≈ **1.2K writes/s**.
- Post = 1 KB text/metadata → 100 GB/day → **~36 TB/year** (before replication) → needs sharding.
- Images: 20% of posts have a 500 KB image → 20M × 0.5 MB = 10 TB/day → object storage + CDN.
- Feed cache: 200M users × 800 post IDs × 8 B ≈ 1.3 TB → Redis cluster (≈ 20–30 nodes with headroom).

### Worked example 2: chat

- 100M DAU × 50 messages/day = 5B/day ≈ **58K msg/s**, peak ~150K.
- 5B × 200 B = 1 TB/day → wide-column store.
- 20M concurrent connections ÷ 50K per gateway = **400 gateway servers** (+ headroom).

### Worked example 3: URL shortener

- 100M new URLs/day ≈ 1.2K writes/s; 100:1 reads ≈ 120K reads/s peak.
- 5 years ≈ 180B URLs × 500 B ≈ **90 TB**.
- 62^7 ≈ 3.5 × 10^12 > 180 × 10^9 → **7-char base62 codes**.

**Tip:** round aggressively, say the numbers out loud, and state the *consequence* ("70K reads/s → one DB can't do it → cache + replicas").

---

# Part B — Concepts

Each topic: **what it is → how it works → when to use it → tradeoffs → what to say.**

## 4. Networking fundamentals

### 4.1 OSI vs TCP/IP models and how data flows end to end

**Why two models?**
- **OSI (7 layers)** is a teaching/reference model — great for naming *where* something happens ("that's an L7 load balancer").
- **TCP/IP (4 layers)** is what the internet actually runs. Its layers map onto groups of OSI layers.

| OSI layer | Job | Data unit (PDU) | Examples | Devices / components | TCP/IP layer |
|---|---|---|---|---|---|
| 7 Application | What the app wants to say | Message / data | HTTP, gRPC, DNS, SMTP, WebSocket | L7 load balancer, API gateway, WAF, CDN | **Application** |
| 6 Presentation | Format, encode, encrypt | Data | TLS (encryption), JSON/Protobuf, compression | — | Application |
| 5 Session | Open/keep/close conversations | Data | Sessions, TLS session resumption, RPC sessions | — | Application |
| 4 Transport | Process-to-process delivery: ports, reliability, ordering, flow/congestion control | **Segment** (TCP) / **datagram** (UDP) | TCP, UDP, QUIC | L4 load balancer, stateful firewall | **Transport** |
| 3 Network | Host-to-host delivery across networks: IP addressing, routing | **Packet** | IPv4, IPv6, ICMP | Router, L3 switch, NAT gateway | **Internet** |
| 2 Data link | Delivery to the next device on the same local network: MAC addresses | **Frame** | Ethernet, Wi-Fi (802.11), ARP | Switch, Wi-Fi access point | **Link** (network access) |
| 1 Physical | Bits as signals | **Bits** | Copper, fiber, radio | NIC, cables, hubs | Link |

**Memory trick (L7 → L1):** "All People Seem To Need Data Processing."

**Gotchas interviewers like**
- TLS doesn't fit neatly: it encrypts application data (presentation-like) but runs on top of TCP; in practice "between L7 and L4".
- QUIC is a transport protocol built on UDP, with TLS 1.3 built in; HTTP/3 runs on it.
- DNS is application layer; normally UDP port 53 (TCP for large responses and zone transfers; DNS over HTTPS/TLS exists).
- ARP (IP → MAC lookup on a local network) sits between L2 and L3.
- "L4 vs L7 load balancer" literally means which header it reads: ports/IPs vs HTTP paths/headers.

**Encapsulation: each layer wraps the one above it**

```text
Application   [ HTTP request: GET /orders/42 Host: api.example.com ]
TLS           [ TLS record header | encrypted(HTTP request) ]
Transport     [ TCP header: src port 51544 → dst port 443, seq/ack | TLS record ]           = segment
Network       [ IP header: src 192.168.1.20 → dst 93.184.216.34, TTL 64 | TCP segment ]     = packet
Data link     [ Ethernet header: src MAC laptop → dst MAC router | IP packet | FCS ]        = frame
Physical      0101100111010...                                                              = bits
```
The receiver does the reverse (**decapsulation**): each layer strips its header and hands the payload up.

**End-to-end: what happens to one request (laptop → server)**

```text
Laptop ─Wi-Fi─► Home router (NAT) ─► ISP routers ─► … internet backbone … ─► Cloud edge ─► Load balancer ─► App server
 L7–L1            L3 + NAT              L3 hops                                    L4 or L7        L7
```

1. **Application:** the browser builds `GET /orders/42`. It already resolved `api.example.com` → `93.184.216.34` via DNS (4.5).
2. **Transport:** TCP connection to port 443 exists (3-way handshake done once), plus TLS handshake (4.4). The HTTP bytes are encrypted, split into segments sized to fit the path (**MSS** — usually ~1,460 bytes on Ethernet's 1,500-byte **MTU**), numbered with sequence numbers.
3. **Network:** each segment goes into an IP packet: source = laptop's private IP, destination = server's public IP, TTL = 64.
4. **Data link:** the destination isn't on the local network, so the laptop sends the frame to its **default gateway** (the home router). It finds the router's MAC address with **ARP**, then sends the frame over Wi-Fi.
5. **Home router (NAT):** rewrites the source private IP:port to its public IP and a new port, remembers the mapping (so replies come back to the right laptop), and forwards.
6. **Routers across the internet:** each hop reads only the **IP header**, looks up the destination in its routing table (routes learned via **BGP** between networks), decrements TTL (TTL 0 → dropped, which is how `traceroute` works), and **rewrites the L2 header** for the next link. IP addresses stay the same end to end; MAC addresses change every hop.
7. **Cloud edge / load balancer:**
   - An **L4 LB** looks at IP + port and forwards the TCP connection to a backend (often without decrypting).
   - An **L7 LB** terminates TCP and TLS, reads the HTTP request (path, headers, cookies), picks a backend, and opens/reuses a separate connection to it.
8. **Server:** NIC receives the frame → kernel strips Ethernet and IP headers → TCP reassembles segments in order (requests retransmission of missing ones, sends ACKs) → delivers bytes to the process listening on port 443 (or the LB's backend port) → TLS decrypts → the web framework parses HTTP → your handler runs.
9. **Response:** travels back the same way with the roles reversed; the home router uses its NAT table to send it to the laptop.

**Why this matters in design discussions**

| Situation | Layer reasoning |
|---|---|
| Routing `/api` vs `/static` to different services | Needs L7 (HTTP path) → L7 LB or API gateway |
| Millions of long-lived WebSocket/TCP connections, raw throughput | L4 LB is cheaper per connection |
| Real-time video/gaming tolerating some loss | UDP (or QUIC) instead of TCP to avoid head-of-line blocking |
| Slow first request from far away | TCP + TLS handshakes cost round trips → keep-alive, connection pooling, TLS resumption, HTTP/3, CDN edge termination |
| Packet loss on mobile | TCP retransmits stall the whole stream; QUIC isolates streams and survives network changes |
| "Large requests fail but small ones work" | MTU/fragmentation issues (e.g., blocked ICMP breaking path MTU discovery) |
| Blocking an attacker IP vs a malicious payload | Firewall/security group (L3/L4) vs WAF (L7) |

### 4.2 TCP vs UDP

| | TCP | UDP |
|---|---|---|
| Connection | 3-way handshake (SYN → SYN-ACK → ACK) | None |
| Delivery | Reliable, ordered, retransmits lost packets | Best effort, may drop/reorder |
| Flow/congestion control | Yes (slows down when network is congested) | No |
| Overhead | Higher (handshake, headers, ACKs) | Lower |
| Use for | Web, APIs, DBs, file transfer | Video calls, gaming, DNS, live streaming, QUIC |

- **Head-of-line blocking:** in TCP one lost packet delays everything behind it in that connection.
- Connection setup costs a round trip (plus TLS). That's why we reuse connections (keep-alive, pooling).

### 4.3 HTTP versions

- **HTTP/1.1:** one request at a time per connection (pipelining unused in practice) → browsers open ~6 connections per host. Text headers.
- **HTTP/2:** many streams **multiplexed** over one TCP connection, binary framing, header compression (HPACK), server push (deprecated). Still TCP head-of-line blocking.
- **HTTP/3:** runs over **QUIC (UDP)**. Streams are independent (no TCP HOL blocking), faster handshakes (0-RTT/1-RTT), connection migration (phone switches Wi-Fi → 4G without reconnecting).

**Status codes to use correctly:** 200 OK, 201 Created, 202 Accepted (async), 204 No Content, 301/302 redirects, 304 Not Modified, 400 Bad Request, 401 Unauthenticated, 403 Forbidden, 404, 409 Conflict, 422 Validation error, 429 Too Many Requests, 500, 502 Bad Gateway, 503 Unavailable, 504 Gateway Timeout.

### 4.4 TLS (HTTPS)

#### What TLS gives you

- **Encryption:** eavesdroppers on Wi-Fi/ISP see random bytes, not your request.
- **Integrity:** if anyone changes a byte in transit, the receiver detects it and drops the connection.
- **Authentication:** you're really talking to `api.example.com`, not an impostor — proven with a **certificate**.

#### Three pieces, three jobs

| Piece | Job | Speed |
|---|---|---|
| **Certificate** (signed by a Certificate Authority) | Proves the server's identity. Contains the domain name and the server's **public key**, signed by a CA your device already trusts | Used once per connection |
| **Key exchange** (Diffie-Hellman, ECDHE) | Lets both sides create the **same secret key** without ever sending it | Used once per connection |
| **Symmetric cipher** (AES-GCM / ChaCha20) | Encrypts and decrypts all actual data with that shared key, and detects tampering | Fast — used for every byte |

The certificate does **not** decrypt your data. It only proves who the server is. The data key is created fresh for every connection.

#### The TLS 1.3 handshake, step by step

```text
 Browser (client)                                          Server (api.example.com)
 has: trusted root CAs built into OS/browser               has: certificate + its private key
                                                           (cert = "api.example.com's public key,
                                                            signed by an intermediate CA, signed by a root CA")

 1. ClientHello ─────────────────────────────────────────────►
    "TLS 1.3; I support AES-GCM, ChaCha20;
     here's my temporary DH public value A"

 ◄───────────────────────────────────────────── 2. ServerHello
                       "Use AES-GCM. Here's my temporary DH public value B"

 ── both compute the SAME shared secret from A and B → derive handshake keys ──
 ── from here on, the handshake itself is encrypted ──

 ◄───────────────────────────────────────────── 3. Certificate chain
 ◄───────────────────────────────────────────── 4. CertificateVerify:
                       a signature over the whole handshake so far, made with the server's PRIVATE key
 ◄───────────────────────────────────────────── 5. Finished

 6. Client checks:
    ✓ the chain ends at a root CA it trusts
    ✓ the certificate is for "api.example.com", not expired, not revoked
    ✓ the signature from step 4 verifies with the PUBLIC key inside the certificate
      → only the real owner of the private key could have produced it

 7. Finished ────────────────────────────────────────────────►
 ── both derive application keys (symmetric) from the same shared secret ──

 8. GET /orders/42 encrypted with AES-GCM ═══════════════════►  decrypts with the same key
 ◄═══════════════════════════════  response encrypted with AES-GCM
```
One round trip (steps 1–2) before encrypted application data can flow (plus the TCP handshake before it, unless using QUIC/HTTP/3, which combines them).

#### How both sides get the same key without sending it (Diffie-Hellman, toy numbers)

```text
Public, everyone can see:   p = 23, g = 5

Client picks secret a = 6                   Server picks secret b = 15
A = 5^6 mod 23 = 8    ── sends 8 ──►
                      ◄── sends 19 ──       B = 5^15 mod 23 = 19

Client: 19^6 mod 23 = 2                     Server: 8^15 mod 23 = 2
→ both have the secret 2. It never crossed the network.
  An eavesdropper sees 23, 5, 8 and 19 but can't compute 2 without a or b
  (with real-size numbers / elliptic curves this is infeasible).
```
The shared secret goes through a key-derivation function (HKDF) to produce the actual AES keys.

```python
p, g = 23, 5
a, b = 6, 15                                   # each side's private random number
A, B = pow(g, a, p), pow(g, b, p)              # public values sent over the network
assert (A, B) == (8, 19)
assert pow(B, a, p) == pow(A, b, p) == 2       # same shared secret on both sides
```

#### How data is then encrypted

```text
Client:  "GET /orders/42"  ──AES-GCM(key K, nonce)──►  8f3a91c7e2…  + auth tag   (on the wire)
Server:  8f3a91c7e2… + tag ──AES-GCM(key K, nonce)──►  "GET /orders/42"          (tag checked first)
```
- **Same key on both sides** = symmetric encryption → fast (hardware-accelerated AES).
- The **auth tag** detects any modification; a tampered record fails decryption.
- Each direction uses its own key, and every record its own nonce.

#### Things interviewers ask

- **"Doesn't the server encrypt the session key with its public key?"** That was TLS 1.2's RSA key exchange; TLS 1.3 removed it. With RSA key exchange, anyone who recorded your traffic and later stole the server's private key could decrypt **all past sessions**. With temporary DH keys, past sessions stay safe — **forward secrecy**.
- **Why not encrypt everything with public-key crypto?** It's ~1000× slower than AES. So: asymmetric for identity and key setup, symmetric for data.
- **How does the browser trust the CA?** Root CA certificates ship with the OS/browser. The server sends its own cert + intermediates; the browser builds the chain up to a trusted root.
- **TLS termination:** a load balancer/CDN holds the certificate and private key, does the handshake with the user, and forwards plain HTTP (or a new TLS connection) to backends. Pros: offloads crypto, enables L7 routing and caching. Cons: traffic behind it is unencrypted unless you re-encrypt.
- **mTLS (mutual TLS):** the client also presents a certificate and the server verifies it → used for service-to-service authentication in a service mesh.
- **Session resumption / 0-RTT:** a returning client reuses keys from an earlier session and can send data in its first message. Catch: 0-RTT data can be **replayed** by an attacker, so only allow it for idempotent requests (GET).
- **Certificate expiry** is a classic outage cause → automate renewal (ACME / Let's Encrypt) and alert well before expiry.

### 4.5 DNS

- Turns `api.example.com` into an IP.
- Resolution: browser cache → OS cache → **recursive resolver** (ISP/8.8.8.8) → root server → TLD server (`.com`) → **authoritative** server for `example.com` → answer cached per **TTL**.
- Records: A (IPv4), AAAA (IPv6), CNAME (alias), MX (mail), TXT, NS.
- **DNS-based load balancing / GeoDNS:** return different IPs per region or by health (Route 53 latency routing).
- Low TTL = faster failover but more DNS queries. Clients and resolvers sometimes ignore TTLs → DNS failover is slow (minutes).

### 4.6 "What happens when you type a URL?"

Short version below; the packet-level version is in [4.1](#41-osi-vs-tcpip-models-and-how-data-flows-end-to-end).

1. DNS lookup → IP.
2. TCP handshake (or QUIC) + TLS handshake.
3. HTTP request → CDN edge (cache hit → return) → load balancer → app server.
4. App server → cache/DB/other services → builds response.
5. Response → browser parses HTML → fetches CSS/JS/images (mostly from CDN) → renders.

### 4.7 IP, ports, NAT, proxies

- **Private IPs** (10.x, 172.16–31.x, 192.168.x) inside networks; **NAT** maps many private IPs to one public IP.
- A **port** identifies a process on a host (443 HTTPS, 5432 Postgres, 6379 Redis).
- **Forward proxy:** sits in front of *clients* (corporate proxy, hides client identity).
- **Reverse proxy:** sits in front of *servers* (Nginx/Envoy): TLS termination, routing, caching, compression, hides backend topology.

## 5. API design

An API is a contract between a client and a server. Interviewers check three things: can you design clean endpoints, do you know what each protocol is good and bad at, and do you handle the real-world problems (retries, caching, large lists, real-time updates).

Running example for this whole section: a **food-ordering app** with users, restaurants, menus and orders.

### 5.1 REST

#### What REST actually is

REST is a style of using HTTP the way it was designed:
- **Resources** are things, identified by URLs: `/orders/42` is order 42.
- You act on them with **HTTP methods**: `GET /orders/42` reads it, `DELETE /orders/42` cancels it.
- The server sends a **representation** (usually JSON) of the resource.
- **Stateless:** each request carries everything needed (auth token, IDs). The server doesn't remember earlier requests from this client → any server behind the load balancer can handle any request.

#### Designing the endpoints

| Action | Endpoint | Notes |
|---|---|---|
| List restaurants near me | `GET /v1/restaurants?lat=12.97&lng=77.59&cuisine=south-indian&sort=-rating` | Filters and sort are query params, not new URLs |
| Get one restaurant | `GET /v1/restaurants/7` | |
| Get its menu | `GET /v1/restaurants/7/menu-items` | Nested because menu items belong to a restaurant |
| Place an order | `POST /v1/orders` | Body has restaurant_id + items. Not `/restaurants/7/orders/create` — no verbs in URLs |
| Get my order | `GET /v1/orders/42` | Top-level, not `/users/9/restaurants/7/orders/42` — keep nesting to one level |
| Cancel an order | `POST /v1/orders/42/cancel` or `PATCH /v1/orders/42 {"status": "CANCELLED"}` | Actions that aren't plain CRUD are fine as a sub-resource "command" |
| My order history | `GET /v1/users/me/orders?status=DELIVERED&limit=20&cursor=…` | |

Rules of thumb: plural nouns, no verbs in paths (except command sub-resources), lowercase with hyphens, IDs in the path, everything optional in the query string.

#### HTTP methods: safe, idempotent, cacheable

- **Safe** = doesn't change anything on the server.
- **Idempotent** = doing it twice leaves the server in the same state as doing it once. This decides whether a client can **retry blindly** after a timeout.

| Method | Use | Safe | Idempotent | Example of "twice" |
|---|---|---|---|---|
| GET | Read | Yes | Yes | Reading order 42 twice changes nothing |
| PUT | Replace the whole resource | No | **Yes** | `PUT /users/9/address {...}` twice → same address stored |
| PATCH | Change some fields | No | Depends | `{"status": "CANCELLED"}` twice → same; `{"op": "increment"}` twice → not |
| DELETE | Remove | No | **Yes** | Second delete finds nothing; end state is the same (may return 404) |
| POST | Create / run a command | No | **No** | `POST /orders` twice → **two orders**. Needs an idempotency key ([5.6](#56-idempotency)) |

#### Status codes, in context

```text
POST /v1/orders            → 201 Created, Location: /v1/orders/42
POST /v1/orders (async)    → 202 Accepted (work queued; poll /v1/orders/42 for status)
GET /v1/orders/42          → 200 OK  |  404 Not Found  |  403 Forbidden (not your order)
GET with valid ETag        → 304 Not Modified (see caching below)
Missing/expired token      → 401 Unauthorized (really "unauthenticated")
Body fails validation      → 400 Bad Request or 422 Unprocessable Entity
Seat/stock already taken   → 409 Conflict
Version check failed       → 412 Precondition Failed
Too many requests          → 429 Too Many Requests + Retry-After: 30
Bug on our side            → 500 ;  upstream down → 502/503 ;  upstream too slow → 504
```

#### A full request and response

```http
POST /v1/orders HTTP/1.1
Host: api.food.example
Authorization: Bearer eyJhbGciOi...
Content-Type: application/json
Idempotency-Key: 6f1c2a9e-4b7d-4f0e-9a51-3c0d2e7b8a10

{"restaurant_id": 7, "items": [{"menu_item_id": 301, "qty": 2}], "address_id": 5}
```

```http
HTTP/1.1 201 Created
Location: /v1/orders/42
Content-Type: application/json

{"id": 42, "status": "PLACED", "total": 49800, "currency": "INR", "created_at": "2025-05-01T12:30:00Z"}
```

#### Errors that clients can act on

```json
{
  "error": {
    "code": "ITEM_UNAVAILABLE",
    "message": "Masala Dosa is no longer available.",
    "details": {"menu_item_id": 301},
    "request_id": "req_8f2c..."
  }
}
```
- `code` is a stable machine-readable string → the app can show the right screen ("remove item from cart?") without parsing English.
- `request_id` lets support find the exact request in logs.

#### Versioning and backward compatibility

- **Non-breaking changes** (no new version needed): add a new endpoint, add an optional request field, add a new response field. Clients must ignore unknown fields.
- **Breaking changes** (need `/v2` or a new field name): remove/rename a field, change a field's type or meaning, make an optional field required, change error codes.
- Path versioning (`/v1/...`) is the simplest to see and route; header versioning keeps URLs clean. Run old and new versions side by side; announce deprecation; measure who still calls v1 before removing it.

#### HTTP caching: how it really works

The fastest request is the one that never reaches your server. HTTP has caching built in, controlled by **response headers**. Caches exist in the browser, in CDNs, and in reverse proxies.

There are two separate ideas:

**1. Freshness: "you may reuse this without asking me for N seconds"**

```http
HTTP/1.1 200 OK
Cache-Control: public, max-age=60
ETag: "a1b2c3"
```

- For 60 seconds after this response, any cache may serve this copy **without contacting the server at all**. Zero network cost.
- `public` = shared caches (CDN) may store it. `private` = only the user's browser may (use for anything user-specific like "my orders").
- `s-maxage=300` = a different lifetime just for shared caches (CDN keeps it 5 min, browser 1 min).
- `no-cache` = you *may* store it, but must **revalidate** before every reuse (doesn't mean "don't cache").
- `no-store` = never store it anywhere (bank statements, tokens).
- `stale-while-revalidate=30` = after expiry, serve the stale copy instantly for up to 30 s while fetching a fresh one in the background.

**2. Validation: "my copy expired — is it still correct?"**

The server tagged the response with an **ETag** — a fingerprint of the content (e.g., a hash of the JSON, or a version number). When the cached copy is stale, the client asks *conditionally*:

```text
Client                                              Server
  │  GET /v1/restaurants/7/menu-items                 │
  │  If-None-Match: "a1b2c3"   ───────────────────►   │  computes current ETag
  │                                                   │  same "a1b2c3"? → nothing changed
  │  ◄───────────────────  304 Not Modified (no body) │
  │  reuse cached body, treat as fresh for another 60 s
  │
  │  …later the menu changes (price update)…
  │  GET … If-None-Match: "a1b2c3"  ───────────────►  │  current ETag is now "d4e5f6"
  │  ◄──────────── 200 OK + new body + ETag "d4e5f6"  │
```

- A **304** still costs a round trip but **no body** → saves bandwidth and server work (no JSON serialization of a big menu).
- `Last-Modified` + `If-Modified-Since` is the older, timestamp-based version of the same idea.
- `Vary: Accept-Language` tells caches to store separate copies per language (otherwise a Hindi user might get the English cached copy).

**ETags also prevent lost updates (optimistic concurrency):**

```http
PUT /v1/restaurants/7/menu-items/301
If-Match: "a1b2c3"
```
If someone else changed the item since you read it, its ETag is different → server replies **412 Precondition Failed** instead of silently overwriting their change.

**What to cache in an API:** public, read-heavy data (restaurant details, menus, product pages) at the CDN with short `max-age` + ETags; user-specific data only with `private`; never cache responses to POST/PUT/DELETE.

Runnable simulation of a client cache talking to a server:

```python
import hashlib
import json

class MenuServer:
    def __init__(self):
        self.menu = {"restaurant": 7, "items": [{"id": 301, "name": "Masala Dosa", "price": 12000}]}
        self.bodies_sent = 0

    def get(self, if_none_match=None):
        body = json.dumps(self.menu, sort_keys=True)
        etag = '"' + hashlib.sha256(body.encode()).hexdigest()[:12] + '"'
        if if_none_match == etag:
            return 304, {"ETag": etag, "Cache-Control": "max-age=60"}, None   # no body
        self.bodies_sent += 1
        return 200, {"ETag": etag, "Cache-Control": "max-age=60"}, body

class CachingClient:
    def __init__(self, server, clock):
        self.server, self.clock, self.entry = server, clock, None     # (etag, body, fresh_until)

    def get_menu(self):
        now = self.clock()
        if self.entry and now < self.entry[2]:
            return self.entry[1], "served from cache, no request"
        status, headers, body = self.server.get(self.entry[0] if self.entry else None)
        max_age = int(headers["Cache-Control"].split("=")[1])
        if status == 304:
            self.entry = (self.entry[0], self.entry[1], now + max_age)
            return self.entry[1], "revalidated: 304, reused cached body"
        self.entry = (headers["ETag"], body, now + max_age)
        return body, "full 200 response"

now = [0]
server = MenuServer()
client = CachingClient(server, clock=lambda: now[0])
assert client.get_menu()[1] == "full 200 response"
now[0] = 30
assert client.get_menu()[1] == "served from cache, no request"
now[0] = 70                                               # expired, content unchanged
assert client.get_menu()[1] == "revalidated: 304, reused cached body"
server.menu["items"][0]["price"] = 13000                  # price changes
now[0] = 140
body, how = client.get_menu()
assert how == "full 200 response" and '"price": 13000' in body
assert server.bodies_sent == 2                            # 4 reads, only 2 bodies sent
```

### 5.2 gRPC

#### What it is

**RPC = remote procedure call:** calling a function on another server as if it were local: `order = orders.GetOrder(id=42)`. gRPC is Google's RPC framework.

1. You write the contract once in a `.proto` file:

```protobuf
syntax = "proto3";

service OrderService {
  rpc GetOrder (GetOrderRequest) returns (Order);                     // unary: 1 request → 1 response
  rpc TrackOrder (GetOrderRequest) returns (stream LocationUpdate);   // server streaming
}

message GetOrderRequest { int64 order_id = 1; }
message Order {
  int64 id = 1;
  string status = 2;
  int64 total = 3;
}
message LocationUpdate { double lat = 1; double lng = 2; }
```

2. A code generator creates a typed **client stub** and a **server interface** in each language (Go, Java, Python…).
3. The caller just calls `stub.GetOrder(GetOrderRequest(order_id=42), timeout=0.2)`. The stub serializes, sends over HTTP/2, waits, deserializes.

#### Why it's faster and smaller than JSON

JSON sends field **names** as text every time: `{"id":150,"status":"PLACED"}` = 28 bytes.
Protobuf sends a **field number + type** and a compact value. The field `id = 150` encodes as 3 bytes:

```text
08        → field number 1, wire type "varint"   (1 << 3 | 0)
96 01     → 150 as a varint (7 bits per byte, little-endian groups)
```
Smaller payloads + no text parsing → less CPU and bandwidth, which matters for high-volume service-to-service traffic.

#### The four call types

| Type | Example |
|---|---|
| Unary | `GetOrder(id) → Order` |
| Server streaming | `TrackOrder(id) → stream of locations` (live delivery tracking) |
| Client streaming | `UploadLogs(stream of lines) → summary` |
| Bidirectional | Chat or live collaboration: both sides send whenever they want |

All run over **one HTTP/2 connection** with many concurrent streams (multiplexing) → no per-request connection setup.

#### Things that come up in interviews

- **Deadlines:** every call carries a timeout that propagates downstream (A gives B 200 ms; B passes the remaining budget to C).
- **Schema evolution:** never reuse or renumber a field number; add new fields with new numbers; old clients ignore unknown fields → safe rolling upgrades.
- **Downsides:** browsers can't call gRPC directly (need gRPC-Web + a proxy); binary payloads are hard to inspect with curl; HTTP caching/CDNs don't apply.
- **Typical split:** REST/GraphQL for public and browser clients; gRPC between internal services.

### 5.3 GraphQL

#### The problem it solves

The mobile app's "order details" screen needs: the order, the restaurant's name, and each item's name. With REST:

```text
GET /v1/orders/42                 → order (items have only menu_item_id)
GET /v1/restaurants/7             → full restaurant object (40 fields; we need 1)  ← over-fetching
GET /v1/menu-items/301            → item name                                    ← one call per item
GET /v1/menu-items/305            →  …                                           ← under-fetching
```
4+ round trips on a slow mobile network, mostly downloading data the screen ignores.

With GraphQL the client sends **one query describing exactly the shape it wants**:

```graphql
query {
  order(id: 42) {
    status
    restaurant { name }
    items { qty  menuItem { name price } }
  }
}
```
and gets back exactly that shape:

```json
{"data": {"order": {"status": "PLACED",
  "restaurant": {"name": "Udupi Café"},
  "items": [{"qty": 2, "menuItem": {"name": "Masala Dosa", "price": 12000}}]}}}
```

#### How the server executes a query: resolvers

The server has a **schema** (types and fields) and a **resolver function per field** that knows how to fetch that field:

```text
Query.order(id)        → SELECT * FROM orders WHERE id = 42
Order.restaurant       → SELECT * FROM restaurants WHERE id = order.restaurant_id
Order.items            → SELECT * FROM order_items WHERE order_id = 42
OrderItem.menuItem     → SELECT * FROM menu_items WHERE id = item.menu_item_id
```
The engine walks the query tree and calls the resolver for each field it reaches. This is what makes GraphQL flexible — and also what causes its most famous problem.

#### The N+1 query problem — what actually happens

Query: "10 latest posts, each with its author's name":

```graphql
query { posts(limit: 10) { title author { name } } }
```

The engine runs `Query.posts` once, then runs the `Post.author` resolver **once per post**:

```text
SELECT * FROM posts ORDER BY created_at DESC LIMIT 10      ← 1 query
SELECT * FROM users WHERE id = 3                           ← post 1's author
SELECT * FROM users WHERE id = 8                           ← post 2's author
SELECT * FROM users WHERE id = 3                           ← post 3's author (same user again!)
... 10 author queries in total
```
**1 query for the list + N queries for the related items = N+1.** With 100 posts that's 101 round trips to the database, and the same user is fetched repeatedly. Each resolver is written independently and has no idea the others exist.

#### DataLoader: the fix — batching + per-request caching

Idea: instead of each `author` resolver querying immediately, it **asks a loader for user 3** and waits. The loader:
1. **Collects** every key requested during the current step of execution (all 10 author IDs).
2. **Removes duplicates** (user 3 asked twice → once).
3. Runs **one batched query**: `SELECT * FROM users WHERE id IN (3, 8, 5, …)`.
4. Hands each waiting resolver its own user.

```text
Without DataLoader:  1 + 10 queries      With DataLoader:  1 + 1 query
```
A new loader is created **per request**, so its cache never leaks data between users or serves stale data across requests.

Runnable simulation (asyncio; the "database" just records the SQL it receives):

```python
import asyncio

POSTS = [{"id": i, "title": f"post {i}", "author_id": a} for i, a in enumerate([3, 8, 3, 5, 8], 1)]
USERS = {3: "asha", 5: "ravi", 8: "meera"}

class FakeDB:
    def __init__(self):
        self.queries: list[str] = []

    async def posts(self, limit):
        self.queries.append(f"SELECT * FROM posts LIMIT {limit}")
        return POSTS[:limit]

    async def user(self, uid):
        self.queries.append(f"SELECT * FROM users WHERE id = {uid}")
        return {"id": uid, "name": USERS[uid]}

    async def users(self, ids):
        self.queries.append(f"SELECT * FROM users WHERE id IN ({', '.join(map(str, ids))})")
        return {uid: {"id": uid, "name": USERS[uid]} for uid in ids}

class DataLoader:
    """Collects keys requested in the same event-loop tick, then loads them in one batch."""
    def __init__(self, batch_fn):
        self.batch_fn = batch_fn
        self.cache: dict = {}              # key -> Future (dedupes repeated keys)
        self.pending: dict = {}
        self.scheduled = False

    def load(self, key):
        if key in self.cache:
            return self.cache[key]
        fut = asyncio.get_running_loop().create_future()
        self.cache[key] = self.pending[key] = fut
        if not self.scheduled:             # dispatch after all currently-running resolvers have asked
            self.scheduled = True
            asyncio.get_running_loop().call_soon(lambda: asyncio.ensure_future(self._dispatch()))
        return fut

    async def _dispatch(self):
        batch, self.pending, self.scheduled = self.pending, {}, False
        results = await self.batch_fn(sorted(batch))
        for key, fut in batch.items():
            fut.set_result(results[key])

async def resolve_naive(db):
    posts = await db.posts(5)
    async def author(p):                   # the Post.author resolver, run once per post
        return await db.user(p["author_id"])
    authors = await asyncio.gather(*(author(p) for p in posts))
    return [(p["title"], a["name"]) for p, a in zip(posts, authors)]

async def resolve_with_loader(db):
    loader = DataLoader(db.users)          # one loader per request
    posts = await db.posts(5)
    async def author(p):
        return await loader.load(p["author_id"])
    authors = await asyncio.gather(*(author(p) for p in posts))
    return [(p["title"], a["name"]) for p, a in zip(posts, authors)]

db1, db2 = FakeDB(), FakeDB()
r1 = asyncio.run(resolve_naive(db1))
r2 = asyncio.run(resolve_with_loader(db2))
assert r1 == r2 == [("post 1", "asha"), ("post 2", "meera"), ("post 3", "asha"), ("post 4", "ravi"), ("post 5", "meera")]
assert len(db1.queries) == 6                                   # 1 + 5  (N+1)
assert db2.queries == ["SELECT * FROM posts LIMIT 5",
                       "SELECT * FROM users WHERE id IN (3, 5, 8)"]   # 1 + 1, duplicates removed
```

The same N+1 problem appears in REST code too (an ORM loop that lazily loads each item's relation). The fixes are the same idea: batch with `IN (...)`, or join.

#### Why HTTP caching is harder with GraphQL

- REST: each resource has its own URL (`GET /restaurants/7`) → CDNs and browsers cache by URL, ETags per resource.
- GraphQL: usually every request is `POST /graphql` with a different query body → HTTP caches don't cache POSTs, and two queries for the same restaurant asking for different fields produce different responses.
- Fixes:
  - **Persisted queries:** the client sends a hash of a pre-registered query via GET (`/graphql?id=abc123&variables=…`) → cacheable URL again, and only known queries are allowed.
  - **Client-side normalized cache** (Apollo, Relay): stores objects by `type + id` and reuses them across queries.
  - **Server-side data caching** inside resolvers/loaders (Redis), since response-level caching doesn't fit.

#### Expensive queries (and how to stop them)

Clients can write any query, including very expensive ones:

```graphql
query { user(id: 1) { friends(first: 100) { friends(first: 100) { friends(first: 100) { name } } } } }
```
That's up to 100 × 100 × 100 = **1,000,000 user lookups** from one small request. Protections:
- **Depth limit** (reject queries nested deeper than, say, 7 levels).
- **Complexity/cost scoring:** each field has a cost; list fields multiply by their page size; reject queries over a budget.
- **Mandatory pagination** on list fields (`first ≤ 100`).
- **Timeouts** and per-client rate limits by cost, not by request count.
- **Persisted queries only** for public clients (an allowlist).

#### Field-level authorization

A user's `email` should be visible only to that user and admins. In REST you'd write a separate endpoint; in GraphQL the same `User` type is reachable from many paths (post author, friend list, comment author) → the check must live **in the field's resolver** (or a shared policy layer), not in one endpoint:

```text
User.email resolver:  if viewer.id == user.id or viewer.is_admin: return user.email  else: return null (or error)
```

#### Writes and live updates

- **Mutations** for writes: `mutation { placeOrder(input: {...}) { id status } }` — returns the fields the client wants back.
- **Subscriptions** for live updates (usually over WebSocket): `subscription { orderStatus(id: 42) { status } }`.

#### REST vs GraphQL vs gRPC

| | REST | GraphQL | gRPC |
|---|---|---|---|
| Best for | Public APIs, simple CRUD, cacheable reads | Many client screens needing different data shapes (mobile/web) | Internal service-to-service, low latency, streaming |
| Payload | JSON | JSON (shape chosen by client) | Binary protobuf |
| HTTP caching / CDN | Excellent | Hard (persisted queries help) | No |
| Main risks | Over/under-fetching, many round trips | N+1, expensive queries, field-level auth | Browser support, debugging |

### 5.4 Real-time communication

The question is: **how does the server tell the client that something changed** (new message, order status, LLM tokens)?

```text
Short polling:   C: any news? → S: no   … 5 s …  C: any news? → S: no  … 5 s … C: any news? → S: yes!
                 Simple, but mostly wasted requests, and updates arrive up to 5 s late.

Long polling:    C: any news? → S holds the request open … 20 s … (event!) → S: yes, here it is
                 C immediately asks again. Near-instant updates over plain HTTP; one held request per client.

SSE:             C: GET /events (Accept: text/event-stream)
                 S keeps the response open and writes:
                     id: 101
                     data: {"order": 42, "status": "PREPARING"}

                     id: 102
                     data: {"order": 42, "status": "PICKED_UP"}
                 If the connection drops, the browser reconnects automatically and sends
                 Last-Event-ID: 102 → server resumes from there. Server → client only.

WebSocket:       C: GET /chat  Upgrade: websocket  Connection: Upgrade  Sec-WebSocket-Key: …
                 S: 101 Switching Protocols
                 Now one TCP connection carries small frames in BOTH directions, anytime.

Webhooks:        Server-to-server. You give a provider your URL; when an event happens
                 (payment succeeded), the provider POSTs it to you.
```

| Option | Direction | Pros | Cons | Use |
|---|---|---|---|---|
| Short polling | Client pulls | Trivial | Wasteful, delayed | Rare updates, admin dashboards |
| Long polling | Client pulls, server holds | Works everywhere | Reconnect per message, held connections | Fallback when nothing else works |
| **SSE** | Server → client | Plain HTTP, auto-reconnect + resume, simple | One direction, text only | LLM token streaming, notifications, live scores, order tracking |
| **WebSocket** | Both ways | Low overhead per message, bidirectional | Stateful connections, harder to load balance | Chat, multiplayer games, collaborative editing |
| Webhooks | Server → your server | No polling between companies | You must verify, dedupe, and handle retries | Payments, GitHub, Shopify events |

**Scaling WebSockets (or SSE):** a connection lives on one specific server, so a message for user U must reach *that* server.

```text
User U ══ WebSocket ══► Gateway-3
                           ▲
Redis: conn:U → gateway-3  │ (registry, refreshed by heartbeats)
                           │
Chat service: "send to U" → look up gateway-3 → publish to channel gateway-3 → Gateway-3 writes to U's socket
```
Also: least-connections load balancing; drain servers gradually during deploys; clients reconnect with **jittered** backoff so 1M clients don't reconnect in the same second.

**Receiving webhooks safely:** verify a signature (so attackers can't fake "payment succeeded"), reject old timestamps (so a captured request can't be replayed), process idempotently by event ID (providers retry), and respond 2xx quickly (queue the real work).

```python
import hashlib
import hmac

SECRET = b"whsec_demo_secret"

def sign(body: bytes, timestamp: int) -> str:                 # what the provider does
    return hmac.new(SECRET, f"{timestamp}.".encode() + body, hashlib.sha256).hexdigest()

seen_event_ids: set[str] = set()

def handle_webhook(body: bytes, timestamp: int, signature: str, now: int, event_id: str) -> int:
    if abs(now - timestamp) > 300:
        return 400                                            # too old: possible replay
    if not hmac.compare_digest(sign(body, timestamp), signature):
        return 401                                            # not from the provider
    if event_id in seen_event_ids:
        return 200                                            # duplicate delivery: ack, do nothing
    seen_event_ids.add(event_id)
    # enqueue real processing here
    return 200

body = b'{"type": "payment.succeeded", "order_id": 42}'
sig = sign(body, 1_000)
assert handle_webhook(body, 1_000, sig, now=1_010, event_id="evt_1") == 200
assert handle_webhook(body, 1_000, sig, now=1_020, event_id="evt_1") == 200      # retry, ignored
assert handle_webhook(b'{"type": "payment.succeeded", "order_id": 99}', 1_000, sig, 1_010, "evt_2") == 401
assert handle_webhook(body, 1_000, sig, now=5_000, event_id="evt_3") == 400
```

### 5.5 Pagination

You never return 1 million orders in one response. Two common ways to page:

**Offset pagination:** `GET /orders?limit=20&offset=40` → `SELECT … ORDER BY created_at DESC LIMIT 20 OFFSET 40`.

Two problems:
1. **Slow for deep pages:** for `OFFSET 100000` the database still walks past 100,000 rows and throws them away.
2. **Duplicates/skips when data changes:** you load page 1 (posts 10, 9, 8). Someone creates post 11. Page 2 is now "skip 3" → skips 11, 10, 9 → returns 8, 7, 6 → **post 8 appears twice**.

**Cursor (keyset) pagination:** "give me 20 items *after this one*":

```sql
-- page 1
SELECT id, created_at FROM posts ORDER BY created_at DESC, id DESC LIMIT 3;
-- last row was (created_at=80, id=8) → cursor
-- page 2
SELECT id, created_at FROM posts
WHERE (created_at, id) < (80, 8)            -- strictly after the last row we showed
ORDER BY created_at DESC, id DESC LIMIT 3;
```
- Uses the index on `(created_at, id)` to **jump** straight to the right spot → same speed for page 1 and page 10,000.
- New rows at the top don't shift later pages → no duplicates.
- `id` is the **tie-breaker**: two posts with the same `created_at` still have a strict order.
- The cursor goes to the client as an **opaque token** (base64 of `{"created_at": 80, "id": 8}`) so clients don't build it themselves and you can change it later.
- Trade-off: no "jump to page 57" — fine for feeds and infinite scroll, which is most APIs.

```python
import base64
import json
import sqlite3

db = sqlite3.connect(":memory:")
db.execute("CREATE TABLE posts (id INTEGER PRIMARY KEY, created_at INTEGER)")
db.execute("CREATE INDEX idx_posts_feed ON posts (created_at DESC, id DESC)")
db.executemany("INSERT INTO posts VALUES (?, ?)", [(i, i * 10) for i in range(1, 11)])

def offset_page(limit, offset):
    return [r[0] for r in db.execute(
        "SELECT id FROM posts ORDER BY created_at DESC, id DESC LIMIT ? OFFSET ?", (limit, offset))]

def cursor_page(limit, cursor=None):
    if cursor is None:
        rows = db.execute("SELECT id, created_at FROM posts ORDER BY created_at DESC, id DESC LIMIT ?", (limit,))
    else:
        c = json.loads(base64.urlsafe_b64decode(cursor))
        rows = db.execute("SELECT id, created_at FROM posts WHERE (created_at, id) < (?, ?) "
                          "ORDER BY created_at DESC, id DESC LIMIT ?", (c["created_at"], c["id"], limit))
    rows = rows.fetchall()
    next_cursor = base64.urlsafe_b64encode(json.dumps(
        {"created_at": rows[-1][1], "id": rows[-1][0]}).encode()).decode() if rows else None
    return [r[0] for r in rows], next_cursor

page1 = offset_page(3, 0)
c_page1, cursor = cursor_page(3)
db.execute("INSERT INTO posts VALUES (11, 110)")          # a new post arrives between page loads
page2 = offset_page(3, 3)
c_page2, _ = cursor_page(3, cursor)
assert page1 == [10, 9, 8] and page2 == [8, 7, 6]          # offset: post 8 shown twice
assert c_page1 == [10, 9, 8] and c_page2 == [7, 6, 5]      # cursor: correct continuation
```

### 5.6 Idempotency

**The problem, concretely:**

```text
App ── POST /payments {amount: 500} ──► Server: charges the card ✔ … response lost (network drop / timeout)
App sees a timeout. Did it work? It retries:
App ── POST /payments {amount: 500} ──► Server: charges the card AGAIN ✘   → customer charged ₹1,000
```
Timeouts are normal on mobile networks, load balancers retry, users double-tap. Any POST that creates or charges something needs protection.

**The fix: an idempotency key**

1. The client generates a unique ID **per logical action** (one per "Pay" tap, not per HTTP attempt) and sends it as `Idempotency-Key: 6f1c…`. Retries reuse the same key.
2. The server records the key **atomically before doing the work** (unique constraint), does the work, then stores the response under that key.
3. A retry with the same key gets the **stored response** — the card is not charged again.

```text
idempotency_keys
  key (PRIMARY KEY) | request_hash | status (IN_PROGRESS / DONE) | response | created_at
```

Edge cases interviewers ask about:
- **Two retries arrive at the same time:** the unique constraint lets only one insert win; the other sees `IN_PROGRESS` → returns 409 ("still processing, retry later").
- **Same key, different body** (client bug): compare a hash of the request → 422.
- **Server crashes mid-way:** the row is stuck IN_PROGRESS → expire it after a timeout, or make the work itself safe to resume (e.g., pass the same key to the payment provider, which also dedupes).
- Keys expire (e.g., after 24 h) to bound storage.
- The same idea protects **consumers** of queues (dedupe by message ID) and **webhook handlers** (dedupe by event ID).

```python
import hashlib
import json
import sqlite3

db = sqlite3.connect(":memory:")
db.execute("CREATE TABLE idempotency_keys (key TEXT PRIMARY KEY, request_hash TEXT, status TEXT, response TEXT)")
charges = []

def create_payment(key: str, body: dict) -> tuple[int, dict]:
    req_hash = hashlib.sha256(json.dumps(body, sort_keys=True).encode()).hexdigest()
    try:
        db.execute("INSERT INTO idempotency_keys VALUES (?, ?, 'IN_PROGRESS', NULL)", (key, req_hash))
        db.commit()
    except sqlite3.IntegrityError:                               # key seen before
        stored_hash, status, response = db.execute(
            "SELECT request_hash, status, response FROM idempotency_keys WHERE key = ?", (key,)).fetchone()
        if stored_hash != req_hash:
            return 422, {"error": "idempotency key reused with a different request"}
        if status == "IN_PROGRESS":
            return 409, {"error": "request still in progress"}
        return 200, json.loads(response)                         # replay the original result
    charges.append(body["amount"])                               # the side effect, done once
    response = {"payment_id": len(charges), "amount": body["amount"], "status": "SUCCEEDED"}
    db.execute("UPDATE idempotency_keys SET status = 'DONE', response = ? WHERE key = ?",
               (json.dumps(response), key))
    db.commit()
    return 201, response

assert create_payment("k1", {"amount": 500})[0] == 201
status, resp = create_payment("k1", {"amount": 500})             # client retry after a timeout
assert status == 200 and resp["payment_id"] == 1 and charges == [500]   # charged once
assert create_payment("k1", {"amount": 900})[0] == 422          # same key, different request
```

### 5.7 Other API concerns

- **Authentication:** API keys for server-to-server partners; OAuth2 access tokens (often JWTs) for user-facing apps (details in [19](#19-security)).
- **Rate limiting** per API key/user, returning 429 + `Retry-After` (algorithms in [18.6](#186-rate-limiting)).
- **Validation at the edge:** reject oversized bodies, wrong types and missing fields before they reach business logic (schema validation from an OpenAPI spec).
- **Documentation and contracts:** OpenAPI (REST), `.proto` (gRPC), the schema (GraphQL) → generate clients and mock servers from them.
- **BFF (Backend for Frontend):** a thin API layer per client type that calls several internal services and returns exactly what that screen needs:

```text
Mobile app ─► Mobile BFF ─┬─► Order service
                          ├─► Restaurant service      one call from the phone,
                          └─► Delivery service        fan-out happens inside the data center
Web app    ─► Web BFF ────┘  (different shape, same services)
```
  Same benefit as GraphQL (fewer mobile round trips), but with server-defined responses per screen.
- **Long-running operations:** return `202 Accepted` + a status URL (`/v1/exports/77`); the client polls it, or gets a webhook/SSE event when done.

## 6. Load balancing, proxies, gateways, service discovery

### 6.1 Why

One server can handle maybe a few thousand requests per second and will eventually crash or be redeployed. So you run **many identical servers** and put a **load balancer (LB)** in front:

```text
                       ┌──► app-1  (healthy)
Users ──► Load balancer├──► app-2  (healthy)
     one public IP     ├──► app-3  (failing health checks → no traffic)
                       └──► app-4  (just added by autoscaling)
```

The LB gives you:
- **Scale:** add servers to handle more traffic.
- **Availability:** a dead server stops getting traffic within seconds.
- **Zero-downtime deploys:** take servers out one at a time, update, put back.
- **One stable address** for clients while servers come and go.

### 6.2 L4 vs L7

The difference is **how much of the request the LB reads** (layers from [4.1](#41-osi-vs-tcpip-models-and-how-data-flows-end-to-end)).

**L4 (transport) LB** sees only IPs and ports. It picks a backend when a TCP connection opens and then just shovels bytes back and forth.

```text
Client ──TCP SYN to 203.0.113.10:443──► L4 LB: "new connection → app-2" ──► app-2
       (all bytes of this connection, still encrypted, go to app-2)
```

**L7 (application) LB** terminates the connection, decrypts TLS, reads the HTTP request, then decides:

```text
Client ──HTTPS──► L7 LB: decrypt, read "GET /api/orders/42, Cookie: …, Host: api.example.com"
                    ├─ path /api/*    → api servers
                    ├─ path /static/* → static servers / CDN origin
                    └─ header X-Canary: 1 → canary pool
                  then opens (or reuses) its own connection to the chosen backend
```

| | L4 | L7 |
|---|---|---|
| Reads | IP, port | URL path, host, headers, cookies, body |
| Can do | Fast forwarding, any TCP/UDP protocol | Path/host routing, TLS termination, retries, header rewriting, auth, rate limits, compression, caching |
| Cost | Very cheap per connection | More CPU (TLS, HTTP parsing) |
| Balances | Connections | Individual requests (better for HTTP/2 and gRPC, where one connection carries many requests) |
| Examples | AWS NLB, LVS, Maglev | AWS ALB, Nginx, Envoy, HAProxy (can do both) |

**Common setup:** an L4 LB at the edge (huge throughput, DDoS absorption) in front of a fleet of L7 proxies that do the smart routing.

### 6.3 Algorithms

| Algorithm | How it picks | Good for | Weakness |
|---|---|---|---|
| **Round robin** | Next server in order | Identical servers, short similar requests | Ignores load |
| **Weighted round robin** | Bigger servers get more turns | Mixed machine sizes; canary (send 5%) | Static weights |
| **Least connections** | Server with fewest active connections | Long or uneven requests, WebSockets | Needs shared state across LB nodes |
| **Least response time** | Lowest recent latency | Mixed workloads | Can herd onto a server that's fast because it's failing fast |
| **Power of two choices** | Pick 2 random servers, send to the less loaded | Large fleets; almost as good as least-connections, no global state | — |
| **Hash (IP / consistent hashing)** | `hash(key) → server` | Cache locality (same user → same cache server), sticky behaviour | Uneven if keys are skewed |

Why least-connections matters — a runnable simulation where some requests are 50× slower than others:

```python
import random
from itertools import cycle

def simulate(pick, servers=5, requests=20_000, seed=7):
    rng = random.Random(seed)
    busy_until = [0.0] * servers          # time each server finishes its queued work
    active = [[] for _ in range(servers)]
    worst_wait = 0.0
    for t in range(requests):
        now = t * 0.01
        for s in range(servers):          # drop finished requests
            active[s] = [end for end in active[s] if end > now]
        s = pick(active, rng)
        duration = 2.0 if rng.random() < 0.02 else 0.04     # 2% of requests are slow
        start = max(now, busy_until[s])
        worst_wait = max(worst_wait, start - now)
        busy_until[s] = start + duration
        active[s].append(busy_until[s])
    return worst_wait

rr = cycle(range(5))
round_robin = lambda active, rng: next(rr)
least_conn = lambda active, rng: min(range(5), key=lambda s: len(active[s]))
def two_choices(active, rng):
    a, b = rng.sample(range(5), 2)
    return a if len(active[a]) <= len(active[b]) else b

w_rr, w_lc, w_p2 = simulate(round_robin), simulate(least_conn), simulate(two_choices)
assert w_lc < w_rr and w_p2 < w_rr       # load-aware picking avoids queueing behind slow requests
```

Round robin keeps sending requests to a server that's stuck on a slow request; load-aware algorithms route around it.

### 6.4 Health checks and high availability

**Active health checks:** the LB calls each backend every few seconds:

```text
LB → app-3: GET /health  (timeout 2 s)
   fail, fail, fail (3 in a row)  → mark DOWN, stop routing
   ok, ok (2 in a row)            → mark UP, resume routing
```

**Passive health checks:** the LB notices real requests failing (connection refused, 5xx) and ejects the server temporarily (Envoy's "outlier detection").

**Liveness vs readiness** (two different questions):
- **Liveness:** "is the process alive?" — if not, restart it.
- **Readiness:** "can it serve traffic right now?" — DB connected, caches warmed, not shutting down. Not ready → keep it alive but send no traffic.
- A bad readiness check that calls the database can take **every** server out of rotation when the database blips → keep checks cheap and local, or degrade instead of failing.

**Graceful shutdown during deploys:** mark not-ready → LB stops sending new requests → finish in-flight requests (e.g., up to 30 s) → exit. Otherwise every deploy drops requests.

**The LB itself must not be a single point of failure:**
- Managed LBs (AWS ALB/NLB, GCP LB) are redundant across zones by design.
- Self-hosted: two LB machines share a **virtual IP**; if the active one dies, the standby takes over the IP (keepalived/VRRP). Or run several LBs behind DNS / anycast.

### 6.5 Stateless servers and sessions

**Stateful (bad for scaling):** the user's session (logged-in user, cart) lives in app-2's memory.

```text
Request 1 → app-2 (session created in app-2 memory)
Request 2 → app-4 → "who are you?" → logged out   ✗
app-2 crashes → every session on it is lost       ✗
```

**Sticky sessions** (the LB always sends a user to the same server, via a cookie) patch this but cause uneven load and still lose sessions on crash.

**Stateless (good):** keep app servers free of per-user state:
- Session data in a shared store (Redis): the cookie holds a random session ID; any server looks it up.
- Or a signed token (JWT) that carries the claims itself — no lookup needed.
- Uploaded files go to object storage, not the server's disk.

Now any server can handle any request, autoscaling can add/remove servers freely, and a crash loses nothing.

### 6.6 API gateway

A reverse proxy specialised for APIs, sitting between clients and many microservices:

```text
Mobile/Web ──► API Gateway ──┬─► /users/*    → user-service
                             ├─► /orders/*   → order-service
                             └─► /payments/* → payment-service
   gateway handles for every request:
   TLS termination · authentication (verify JWT/API key) · rate limiting per client
   · request validation · routing · request/response transformation · metrics & logs
```

- Services don't each re-implement auth and rate limiting.
- Clients see one domain and one API surface; internal service boundaries can change freely.
- Risks: it's on every request's path → must be highly available and fast; don't put business logic in it (it becomes a bottleneck team-wise too).
- **Reverse proxy vs API gateway vs LB:** an LB spreads load; a reverse proxy adds TLS/caching/routing; an API gateway adds API concerns (auth, keys, quotas). Products like Envoy, Nginx and Kong blur the lines.

### 6.7 Service discovery

In a dynamic environment, service instances come and go and their IPs change. How does order-service find payment-service?

```text
payment-service instance starts → registers "payment-service = 10.0.3.17:8080" in the registry
                                  (and renews it with heartbeats; if it dies, the entry expires)

order-service wants payment-service:
  Client-side discovery:  ask registry → get [10.0.3.17, 10.0.5.2] → pick one itself (client-side LB)
  Server-side discovery:  call "payment-service" → DNS/LB resolves it → LB picks an instance
```

- **Kubernetes:** a `Service` gets a stable virtual IP and DNS name (`payment-service.default.svc`); kube-proxy/the service mesh routes to healthy pods. You rarely build discovery yourself.
- **Registries:** Consul, etcd, ZooKeeper, Eureka.
- **Service mesh** (Istio, Linkerd): a sidecar proxy runs next to every service instance and handles discovery, retries, timeouts, mTLS and metrics for outgoing calls — without code changes.

### 6.8 Autoscaling

Add or remove instances automatically based on load:

```text
target: average CPU 60%
current: 10 instances at 90% CPU  → desired = ceil(10 × 90 / 60) = 15 → add 5
later:   15 instances at 30% CPU  → desired = ceil(15 × 30 / 60) = 8  → remove 7 (gradually)
```

- **Signals:** CPU (simple), requests per second per instance, queue depth (for workers), p95 latency, custom metrics.
- **Horizontal scaling** (more machines) is preferred; **vertical** (bigger machine) is simpler but has a ceiling and usually needs a restart.
- **Cooldown / stabilization windows** prevent flapping (scale up fast, scale down slowly).
- New instances take time to boot and warm up (seconds to minutes) → keep headroom, or **pre-scale** before known spikes (sale at 12:00).
- Scaling app servers doesn't help if the **database** is the bottleneck — and can make it worse (more connections).

## 7. CDN and edge

A **CDN (content delivery network)** is a set of servers ("edges", "points of presence") in many cities that cache your content close to users.

```text
Without CDN:   User in Chennai ──── 180 ms round trip ────► Origin in Virginia (every image, every time)

With CDN:      User in Chennai ── 10 ms ──► CDN edge in Chennai
                                              ├─ cache HIT  → returns the file immediately
                                              └─ cache MISS → fetch from origin once (180 ms),
                                                              store it, serve the next 10,000 users locally
```

**Why it matters:** lower latency (fewer long round trips, TLS handshakes close to the user), much less load and bandwidth on your origin, and it absorbs traffic spikes and DDoS attacks.

**How a request flows (pull CDN, the common kind):**

```text
1. DNS: assets.example.com → CNAME to the CDN → resolves to the nearest edge (GeoDNS / anycast)
2. Edge looks up its cache key (usually host + path + selected query params/headers)
3. HIT and still fresh (per Cache-Control max-age) → respond
4. MISS → ask the origin shield (a regional mid-tier cache) → MISS → ask origin
5. Store according to Cache-Control / s-maxage, respond
```
- **Origin shield:** without it, 200 edges missing the same file means 200 requests to your origin. With a shield, edges ask the shield and only the shield asks the origin.

**Push CDN:** you upload content to the CDN ahead of time. Useful for big, predictable files (game patches, video libraries).

**What to cache:**
- Static assets: images, JS/CSS, fonts, downloads → long TTLs (days–a year).
- Video segments (HLS/DASH) → the CDN carries almost all video traffic.
- Public API responses (product page for logged-out users, restaurant menu) → short TTLs (seconds–minutes) + ETags.
- Never: anything personalized unless the cache key includes the user (usually don't).

**Invalidation — the hard part:**
- **Versioned URLs (best):** `app.3f9a2c.js`, `/img/v7/logo.png`. New content = new URL, so you can cache the old one "forever" (`max-age=31536000, immutable`) and never purge.
- **Purge API:** "delete /menu/7 from all edges" — takes seconds to minutes to propagate; use for emergencies and for content whose URL can't change.
- **Short TTL + stale-while-revalidate** for content that changes unpredictably.

**Security at the edge:**
- **Signed URLs/cookies:** `https://cdn.example.com/video.m3u8?expires=1735689600&sig=…` — the edge checks the signature, so only paying users can fetch paid content (code in [15](#15-storage-block-file-object-data-lake-warehouse)).
- **WAF + DDoS protection** run at the edge, before traffic reaches you.

**Edge compute** (Cloudflare Workers, Lambda@Edge): small functions run at the edge — redirects, A/B bucketing, auth checks, header rewrites, geo-blocking — with no round trip to origin.

**Key metric: cache hit ratio.** 95% hit ratio means the origin sees 5% of traffic; raising it to 99% cuts origin load by another 5×.

## 8. Caching

A cache keeps a copy of data somewhere faster than where it normally lives. Reading from memory takes ~100 ns; from Redis over the network ~0.5 ms; from a database query ~1–10 ms; from another service 10–100 ms.

### 8.1 Where caches live

```text
Browser cache ─► CDN ─► API gateway / reverse proxy cache ─► App server
                                                              ├─ in-process cache (dict/LRU in RAM)  ~100 ns
                                                              ├─ distributed cache (Redis/Memcached) ~0.5 ms
                                                              └─ database (+ its own buffer pool)     ~1–10 ms
```

| Layer | Shared by | Good for | Watch out |
|---|---|---|---|
| Browser | One user | Static assets, user's own data | You can't purge it — use versioned URLs |
| CDN | All users near an edge | Public content | Never cache personalized data |
| In-process | One server instance | Hottest small data (config, feature flags, top products) | Each instance has its own copy → inconsistent across instances; memory per instance |
| Distributed (Redis) | All app servers | Sessions, computed results, DB rows | Network hop; must handle Redis being down |

### 8.2 Read and write patterns

**Cache-aside (lazy loading) — the default:**

```text
READ                                                    WRITE
app: GET user:42 from Redis                             app: UPDATE users SET … WHERE id = 42   (DB first)
  hit  → return it                                      app: DEL user:42 from Redis             (then invalidate)
  miss → SELECT … FROM users WHERE id = 42
         SET user:42 <row> EX 300
         return it
```

**Why delete on write instead of updating the cache?** Two writers can update the cache in the wrong order:

```text
time →   Writer A: DB = 1 ............................. cache = 1   (A was slow)
         Writer B:        DB = 2 ── cache = 2
Result: DB = 2, cache = 1  → stale until TTL expires (or forever without a TTL)
```
Deleting is order-independent: the next reader loads whatever the DB has now.

A smaller race remains even with delete: a reader misses, reads the old value from the DB, the writer updates the DB and deletes the key, then the reader writes the old value into the cache. TTLs bound how long that lasts; "delayed double delete" (delete again ~1 s later) or version numbers shrink it further.

```python
class DB:
    def __init__(self): self.rows = {"user:42": "v1"}

class Cache:
    def __init__(self): self.data = {}

def run(update_cache_on_write: bool) -> tuple[str, str]:
    db, cache = DB(), Cache()
    # Two writers interleave: A writes DB, B writes DB + cache, then A writes cache.
    db.rows["user:42"] = "A"
    db.rows["user:42"] = "B"
    if update_cache_on_write:
        cache.data["user:42"] = "B"
        cache.data["user:42"] = "A"           # A's slow cache update lands last
    else:
        cache.data.pop("user:42", None)        # B invalidates
        cache.data.pop("user:42", None)        # A invalidates
    value = cache.data.get("user:42") or db.rows["user:42"]   # next reader (cache-aside)
    return db.rows["user:42"], value

assert run(update_cache_on_write=True) == ("B", "A")    # stale read
assert run(update_cache_on_write=False) == ("B", "B")   # correct
```

**Other patterns:**

| Pattern | How it works | When to use | Risk |
|---|---|---|---|
| Read-through | The cache library itself loads from DB on a miss | Same as cache-aside, cleaner code | — |
| Write-through | Every write goes to cache **and** DB synchronously | Data is read right after it's written | Slower writes; caches data nobody reads |
| Write-back (write-behind) | Write to cache only; a background job flushes to DB in batches | Very high write rates: view counters, likes, analytics | **Data loss** if the cache dies before flushing |
| Write-around | Write to DB only; cache fills on the next read | Data rarely re-read soon after writing (logs, archives) | First read is a miss |
| Refresh-ahead | Refresh hot keys shortly before they expire | Predictable hot data (home page) | Wasted refreshes for keys that went cold |

**Write-back example:** a video's view count gets 50,000 increments per second. Updating the DB row each time would melt it. Instead `INCR views:video9` in Redis, and every 10 s a job does `UPDATE videos SET views = views + <delta>`. Losing a few seconds of counts in a crash is acceptable; losing a payment is not — so never use write-back for money.

### 8.3 Eviction policies

A cache has limited memory. When it's full, something must go:

- **LRU (least recently used):** evict what nobody has touched for the longest time. Good default — recent popularity predicts near-future popularity.
- **LFU (least frequently used):** evict what's been used least often. Protects long-term favourites from being flushed by a one-time scan, but needs frequency decay so yesterday's viral item doesn't stay forever.
- **FIFO / random:** cheap; random eviction is surprisingly decent.
- **TTL (time to live):** entries expire after a fixed time regardless of use. Always set one — it's your safety net against stale data and bugs.

```text
LRU with capacity 3:   get A, get B, get C        → [A, B, C]        (left = least recent)
                       get A                       → [B, C, A]
                       put D (full)                → evict B → [C, A, D]
```

Redis offers `allkeys-lru`, `allkeys-lfu`, `volatile-ttl` and others; they're approximate (sample a few keys, evict the best candidate) because exact LRU across millions of keys costs too much memory.

### 8.4 Cache problems you must know

**1. Cache stampede (thundering herd).** A very hot key (home page feed) expires. In the next 50 ms, 10,000 requests all miss and all query the database at once → database falls over.

Fixes:
- **Single-flight / request coalescing:** only the first request rebuilds the value; the others wait for its result (or serve the slightly stale old value).
- **Jittered TTLs:** `ttl = 300 + random(0, 60)` so many keys don't expire at the same moment.
- **Early (probabilistic) refresh:** refresh shortly before expiry.
- **Never expire the hottest keys;** refresh them in the background.

```python
import threading
import time

class SingleFlightCache:
    """Only one thread loads a missing key; concurrent requests for it wait for that result."""
    def __init__(self, loader):
        self.loader, self.data = loader, {}
        self.lock = threading.Lock()
        self.inflight: dict[str, threading.Event] = {}

    def get(self, key):
        while True:
            with self.lock:
                if key in self.data:
                    return self.data[key]
                event = self.inflight.get(key)
                if event is None:                      # I'm the leader for this key
                    event = self.inflight[key] = threading.Event()
                    leader = True
                else:
                    leader = False
            if not leader:
                event.wait()                           # follower: wait, then re-check the cache
                continue
            try:
                value = self.loader(key)
                with self.lock:
                    self.data[key] = value
                return value
            finally:
                with self.lock:
                    del self.inflight[key]
                event.set()

db_queries = []
def slow_db_load(key):
    db_queries.append(key)
    time.sleep(0.05)
    return f"value-of-{key}"

cache = SingleFlightCache(slow_db_load)
results = []
threads = [threading.Thread(target=lambda: results.append(cache.get("home_feed"))) for _ in range(50)]
for t in threads: t.start()
for t in threads: t.join()
assert len(results) == 50 and set(results) == {"value-of-home_feed"}
assert db_queries == ["home_feed"]                   # 50 concurrent misses → 1 database query
```

**2. Cache penetration.** Requests for keys that **don't exist** (`GET /users/99999999`, often an attack) always miss the cache and always hit the DB.
Fixes: cache the "not found" result with a short TTL; put a **Bloom filter** of existing IDs in front ([17](#17-special-data-structures)); validate inputs.

**3. Cache avalanche.** Many keys expire together (they were all loaded at startup with the same TTL), or the whole cache cluster restarts → a flood of misses.
Fixes: jittered TTLs; replicated cache; warm the cache before taking traffic; rate-limit and circuit-break DB access so the DB survives.

**4. Hot key.** One key gets a huge share of traffic (a celebrity's profile, the flash-sale item) → the one Redis shard that owns it saturates while others idle.
Fixes: keep a copy in each app server's **in-process cache** for a few seconds; or replicate it under several keys (`item:9#1 … item:9#8`) and read a random copy.

**5. Big key.** A 20 MB list in one key → every read moves 20 MB and blocks single-threaded Redis.
Fixes: split it (paginate, hash by sub-key), compress, store blobs in object storage and cache only metadata.

**6. Stale data.** The cache disagrees with the DB.
Fixes: TTLs sized to how stale each data type may be; event-driven invalidation (CDC → delete keys); critical reads (balance, checkout price, inventory at purchase) go to the DB.

### 8.5 Redis in depth

**Why it's fast:** all data in memory; a single thread executes commands one at a time (no locks, no context switches); efficient data structures; I/O threads handle networking in newer versions.

**Consequence of single-threaded execution:** every single command is **atomic** — two clients' `INCR counter` never interleave. But one slow command (`KEYS *` over 10M keys, `SMEMBERS` of a huge set) blocks everyone → use `SCAN`, paginate.

**Data structures and what they're for:**

| Type | Example | Use |
|---|---|---|
| String | `SET session:abc {...} EX 3600`, `INCR views:9` | Cached objects, counters, simple locks (`SET lock:x token NX PX 30000`) |
| Hash | `HSET user:42 name Asha city Pune` | Objects you update field by field |
| List | `LPUSH recent:42 item9`, `LTRIM recent:42 0 99` | Recent items, simple queues |
| Set | `SADD online_users 42` | Unique members, tags, "who liked" |
| **Sorted set** | `ZADD leaderboard 1500 asha`, `ZREVRANGE leaderboard 0 9` | Leaderboards, timelines, delay queues (score = run time), sliding-window rate limits |
| Bitmap | `SETBIT active:2025-05-01 42 1` | Daily active flags for millions of users in a few MB |
| HyperLogLog | `PFADD visitors:home ip1 ip2` → `PFCOUNT` | Approximate unique counts in 12 KB |
| Geo | `GEOADD drivers 77.59 12.97 d1` → `GEOSEARCH …` | Nearby search |
| Stream | `XADD orders * id 42` + consumer groups | Lightweight event log / queue |
| Pub/Sub | `PUBLISH chat:room1 "hi"` | Fire-and-forget fan-out (messages lost if nobody listens) |

**Multi-step atomic logic:** a Lua script (runs without interleaving) or `MULTI/EXEC`. Example: "decrement stock only if > 0" must be one script, otherwise two clients can both see stock = 1.

**Persistence (if you need data to survive restarts):**
- **RDB snapshots:** fork and write the whole dataset every N minutes. Compact, fast restarts, but you lose everything since the last snapshot.
- **AOF (append-only file):** log every write; `fsync` every second → lose at most ~1 s. Bigger files; rewritten in the background.
- Pure caches often disable both — the DB is the source of truth.

**High availability:**
- **Replication:** a primary streams writes to replicas **asynchronously** → a failover can lose the last writes.
- **Sentinel:** monitors primaries and promotes a replica on failure.
- **Redis Cluster:** data split into **16,384 hash slots** (`CRC16(key) mod 16384`), each owned by one primary (+ replicas). Clients cache the slot map and follow `MOVED` redirects when slots move. Multi-key operations need all keys in one slot → **hash tags**: `{user:42}:cart` and `{user:42}:profile` hash only the `user:42` part.

**Memcached vs Redis:** Memcached is a simpler, multi-threaded key → string cache (great at pure caching); Redis adds data structures, persistence, replication, scripting, pub/sub.

### 8.6 Cache consistency strategies (pick per data type)

Ask for each piece of data: **how stale may it be, and what does a wrong value cost?**

| Data | Staleness allowed | Strategy |
|---|---|---|
| Restaurant description, product details | Minutes | Cache-aside, TTL 5–60 min, delete on update |
| Prices, stock shown on listing pages | Seconds | Short TTL (10–30 s) or event-driven invalidation (DB change → CDC → delete key) |
| Price/stock at checkout, account balance | None | **Read the database.** Cache is only for display |
| User's own profile right after editing | None for that user | Read-your-writes: update/delete the cache in the same request, or bypass cache for that user briefly |
| Feed, likes count, view count | Seconds–minutes | Long TTL, write-back counters |
| Feature flags, config | Seconds | In-process cache refreshed every N seconds |

**Event-driven invalidation pipeline:**

```text
App writes Postgres ──► WAL ──► CDC (Debezium) ──► Kafka "products" ──► invalidator ──► DEL product:42
```
No dual-write in application code, and every change (including ones made by scripts/admin tools) invalidates the cache.

## 9. Databases: relational internals

### 9.1 ACID

A **transaction** groups several statements into one unit. ACID describes what the database promises about it. Example: transfer ₹100 from account A to B:

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 'A';
UPDATE accounts SET balance = balance + 100 WHERE id = 'B';
COMMIT;
```

- **Atomicity:** both updates happen, or neither. If the server crashes after the first `UPDATE`, recovery undoes it — money never disappears.
- **Consistency:** constraints hold before and after (`CHECK (balance >= 0)`, foreign keys, unique keys). A transaction that would break one is rejected.
- **Isolation:** another transaction never sees the half-done state (A debited, B not yet credited). How strictly this is enforced is the **isolation level** ([9.4](#94-isolation-levels-and-anomalies)).
- **Durability:** once `COMMIT` returns, the change survives a power cut — because it's in the WAL on disk ([9.2](#92-how-a-write-is-made-durable-wal)).

### 9.2 How a write is made durable: WAL

Writing data pages in place on every commit would mean many random disk writes (slow). Instead databases use a **write-ahead log (WAL)**:

```text
COMMIT of "UPDATE accounts … id='A'":
1. Append a log record "page 17, account A: 500 → 400" to the WAL      (sequential write, fast)
2. fsync the WAL                                                         ← durability point; COMMIT returns now
3. Change page 17 in memory (buffer pool); mark it dirty
4. Later, a checkpoint writes dirty pages to the data files in bulk

Crash after step 2 but before step 4?
→ On restart, replay the WAL from the last checkpoint → page 17 is rebuilt → nothing lost.
```

- Sequential appends are much faster than random page writes, and many commits can share one fsync (**group commit**).
- The same WAL stream feeds **replication** (replicas replay it) and **CDC** (Debezium reads it to publish change events).

Toy version of crash recovery:

```python
class ToyDB:
    def __init__(self):
        self.wal: list[tuple] = []       # durable (survives crash)
        self.pages: dict = {}            # data files on disk (updated lazily)
        self.memory: dict = {}           # buffer pool (lost on crash)

    def commit(self, txn_id, changes: dict):
        for key, value in changes.items():
            self.wal.append((txn_id, key, value))
        self.wal.append((txn_id, "COMMIT", None))    # durability point
        self.memory.update(changes)                  # pages flushed later

    def checkpoint(self):
        self.pages.update(self.memory)

    def crash_and_recover(self):
        self.memory = dict(self.pages)               # RAM is gone; start from disk pages
        committed = {t for t, k, _ in self.wal if k == "COMMIT"}
        for txn_id, key, value in self.wal:          # redo committed changes
            if txn_id in committed and key != "COMMIT":
                self.memory[key] = value

db = ToyDB()
db.commit(1, {"A": 500, "B": 0})
db.checkpoint()
db.commit(2, {"A": 400, "B": 100})                   # committed, not yet checkpointed
db.wal.append((3, "A", 0))                           # txn 3 crashed before COMMIT
db.crash_and_recover()
assert db.memory == {"A": 400, "B": 100}             # txn 2 recovered, txn 3 ignored
```

### 9.3 MVCC (multi-version concurrency control)

Problem: a long report reads 1M rows while other transactions update them. With plain locks, either the report blocks writers for minutes, or writers block the report.

MVCC's answer: **keep several versions of each row**, and give each transaction a **snapshot** — it sees the database as of the moment it started.

```text
Row "account A" versions:   [balance=500, created by txn 10]  [balance=400, created by txn 25]

Txn 20 (started before txn 25 committed) reads A → sees 500   (its snapshot)
Txn 30 (started after)                    reads A → sees 400
Txn 25 wrote A without waiting for readers; readers never waited for txn 25.
```

- **Readers don't block writers, writers don't block readers.** Two writers on the **same row** still conflict — the second waits (or fails, at stricter isolation levels).
- **Postgres:** old row versions stay in the table until **VACUUM** removes them. If vacuum falls behind (e.g., a transaction left open for hours), tables bloat and queries slow down.
- **MySQL InnoDB:** keeps the latest version in the table and reconstructs older ones from the **undo log**.

### 9.4 Isolation levels and anomalies

Isolation levels trade correctness for concurrency. To choose one, know what can go wrong:

| Anomaly | What happens (example) |
|---|---|
| **Dirty read** | T1 sees T2's **uncommitted** change; T2 rolls back → T1 acted on data that never existed |
| **Non-repeatable read** | T1 reads balance = 500; T2 commits 400; T1 reads again → 400 within the same transaction |
| **Phantom read** | T1: `COUNT(*) WHERE status='PENDING'` = 5; T2 inserts a pending row; T1 counts again = 6 |
| **Lost update** | T1 and T2 both read stock = 10, both write 10 − 1 = 9 → one sale lost (should be 8) |
| **Write skew** | Rule: ≥ 1 doctor on call. Alice and Bob are on call. Both check "is someone else on call?" → yes → both go off call → nobody on call. Each wrote a *different* row, so no row conflict was detected |

| Level | Prevents | Still allows | Used by |
|---|---|---|---|
| Read uncommitted | — | Everything | Rarely |
| **Read committed** | Dirty reads | Non-repeatable, phantoms, lost updates (read-then-write), write skew | Postgres/Oracle default |
| Repeatable read / snapshot | + non-repeatable (Postgres: + phantoms) | Write skew; (MySQL: some lost-update cases) | MySQL InnoDB default |
| **Serializable** | Everything — as if transactions ran one at a time | — | Postgres SSI aborts conflicting txns → **app must retry** |

**Lost update, the most common interview follow-up — four fixes:**

```sql
-- 1. Atomic update (best when possible): the DB does read+write in one step
UPDATE products SET stock = stock - 1 WHERE id = 9 AND stock > 0;      -- check rows affected

-- 2. Pessimistic lock: lock the row while deciding
BEGIN;
SELECT stock FROM products WHERE id = 9 FOR UPDATE;                    -- others wait here
UPDATE products SET stock = 9 WHERE id = 9;
COMMIT;

-- 3. Optimistic lock: version column, retry on conflict
UPDATE products SET stock = 9, version = version + 1 WHERE id = 9 AND version = 4;   -- 0 rows → someone else won

-- 4. SERIALIZABLE isolation + retry the transaction on serialization failure
```

```python
import sqlite3

db = sqlite3.connect(":memory:")
db.execute("CREATE TABLE products (id INTEGER PRIMARY KEY, stock INTEGER, version INTEGER)")
db.execute("INSERT INTO products VALUES (9, 10, 0)")

# Lost update: two "requests" both read stock = 10, then both write 10 - 1.
s1 = db.execute("SELECT stock FROM products WHERE id = 9").fetchone()[0]
s2 = db.execute("SELECT stock FROM products WHERE id = 9").fetchone()[0]
db.execute("UPDATE products SET stock = ? WHERE id = 9", (s1 - 1,))
db.execute("UPDATE products SET stock = ? WHERE id = 9", (s2 - 1,))
assert db.execute("SELECT stock FROM products WHERE id = 9").fetchone()[0] == 9      # should be 8

# Fix 1: atomic conditional update
db.execute("UPDATE products SET stock = 10 WHERE id = 9")
for _ in range(2):
    db.execute("UPDATE products SET stock = stock - 1 WHERE id = 9 AND stock > 0")
assert db.execute("SELECT stock FROM products WHERE id = 9").fetchone()[0] == 8

# Fix 3: optimistic locking — the second writer's stale version updates 0 rows
_, v = db.execute("SELECT stock, version FROM products WHERE id = 9").fetchone()
first = db.execute("UPDATE products SET stock = 7, version = version + 1 WHERE id = 9 AND version = ?", (v,)).rowcount
second = db.execute("UPDATE products SET stock = 7, version = version + 1 WHERE id = 9 AND version = ?", (v,)).rowcount
assert (first, second) == (1, 0)                     # second must re-read and retry
```

**Job queue in SQL** — many workers taking different rows without blocking each other:

```sql
SELECT id FROM jobs WHERE status = 'PENDING' ORDER BY id LIMIT 10 FOR UPDATE SKIP LOCKED;
```
`SKIP LOCKED` skips rows another worker already locked instead of waiting for them.

### 9.5 Indexes

Without an index, `SELECT * FROM orders WHERE user_id = 42` reads every row (**full table scan**) — fine for 1,000 rows, terrible for 100 million.

**B+ tree index** (the default): a sorted, balanced tree. Each node holds hundreds of keys, so even a billion rows need only ~3–4 levels:

```text
                        [ 1000 | 5000 | 9000 ]                      ← root (in memory)
              ┌───────────┼──────────┼──────────┐
       [100|400|700]  [2000|3000]  [6000|7500]  [9500|…]           ← internal nodes
          │ … │                                                     
   [leaf: 400 → row ptr, 401 → row ptr, …] ⇄ [leaf: 700 …] ⇄ …       ← leaves linked for range scans
```
Lookup `user_id = 401`: root → internal → leaf → row = 3–4 page reads instead of scanning millions. **Range queries** (`created_at BETWEEN …`) find the start leaf and walk the linked leaves.

**Composite indexes and the leftmost-prefix rule.** An index on `(user_id, status, created_at)` is sorted by user_id, then status, then created_at — like a phone book sorted by last name, then first name:

| Query | Uses the index? |
|---|---|
| `WHERE user_id = 42` | Yes (leftmost column) |
| `WHERE user_id = 42 AND status = 'PAID'` | Yes |
| `WHERE user_id = 42 AND status = 'PAID' ORDER BY created_at DESC LIMIT 20` | Yes — fully: no sort needed |
| `WHERE status = 'PAID'` | **No** (skips the first column — like finding everyone named "Asha" in a phone book sorted by last name) |
| `WHERE user_id = 42 AND created_at > …` | Partly (user_id only, then filters) |

Rule of thumb: **equality columns first, then the range/sort column.**

**Other index types and tricks**
- **Covering index:** contains every column the query needs → answered from the index alone (no table lookup).
- **Partial index:** `CREATE INDEX … WHERE status = 'PENDING'` → tiny index for a hot subset.
- **Hash index:** equality only, no ranges.
- **GIN/GiST** (Postgres): full-text search, JSONB, arrays, geospatial.
- **Clustered (InnoDB):** rows are stored inside the primary-key B+ tree; secondary indexes store the primary key. Random UUIDv4 primary keys insert all over the tree (page splits) → prefer time-ordered keys (auto-increment, UUIDv7, Snowflake).

**Costs:** every index must be updated on every insert/update (slower writes, more disk). Indexes on low-selectivity columns (a boolean) rarely help. Functions on a column (`WHERE LOWER(email) = …`) can't use a plain index on `email` → use an expression index.

Watch the query plan change from a full scan to an index search:

```python
import sqlite3

db = sqlite3.connect(":memory:")
db.execute("CREATE TABLE orders (id INTEGER PRIMARY KEY, user_id INT, status TEXT, created_at INT)")
db.executemany("INSERT INTO orders (user_id, status, created_at) VALUES (?, ?, ?)",
               [(i % 1000, "PAID" if i % 3 else "PENDING", i) for i in range(20_000)])

def plan(sql):
    return " ".join(row[-1] for row in db.execute("EXPLAIN QUERY PLAN " + sql))

q = "SELECT id FROM orders WHERE user_id = 42 AND status = 'PAID' ORDER BY created_at DESC LIMIT 20"
assert "SCAN" in plan(q)                                        # no index: read every row
db.execute("CREATE INDEX idx_user_status_time ON orders (user_id, status, created_at)")
assert "SEARCH" in plan(q) and "idx_user_status_time" in plan(q)          # index seek
assert "TEMP B-TREE" not in plan(q)                                        # no separate sort step
assert plan("SELECT id FROM orders WHERE status = 'PAID'").startswith("SCAN")   # leftmost column missing → scan
```

### 9.6 B-tree vs LSM-tree storage engines

Two families of storage engines optimize for different workloads.

**B-tree engines (Postgres, MySQL InnoDB):** data lives in pages of a B-tree; an update modifies the page in place (after writing the WAL). Reads are one tree walk. Great for read-heavy and mixed workloads with transactions.

**LSM-tree engines (Cassandra, RocksDB, ScyllaDB, HBase, LevelDB):** never modify in place; turn all writes into sequential appends.

```text
WRITE "user:9 = Pune"
  1. append to commit log (durability)
  2. insert into the memtable (sorted, in memory)
  3. memtable full → flush as an immutable sorted file: SSTable-7

READ "user:9"
  memtable → SSTable-7 (newest) → SSTable-6 → … stop at the first match
  (a Bloom filter per SSTable says "definitely not here" → skip most files without disk reads)

COMPACTION (background)
  merge SSTables, keep only the newest value per key, drop deleted keys (tombstones)
```

| | B-tree | LSM-tree |
|---|---|---|
| Writes | Random page writes | Sequential appends → **very high write throughput** |
| Reads | One tree lookup → fast, predictable | May check several files (Bloom filters help) |
| Deletes | In place | Tombstone markers until compaction (deleted data still costs reads) |
| Background work | Vacuum/checkpoints | Compaction (uses I/O, can cause latency spikes) |
| Best for | OLTP with reads + transactions | Write-heavy: events, messages, time series, logs |

Toy LSM to see the mechanics:

```python
import bisect

class ToyLSM:
    TOMBSTONE = object()

    def __init__(self, memtable_limit=3):
        self.memtable: dict = {}
        self.sstables: list[list[tuple]] = []      # newest first; each is sorted (key, value)
        self.limit = memtable_limit
        self.files_read = 0

    def put(self, key, value):
        self.memtable[key] = value
        if len(self.memtable) >= self.limit:
            self.sstables.insert(0, sorted(self.memtable.items()))   # flush
            self.memtable = {}

    def delete(self, key):
        self.put(key, self.TOMBSTONE)

    def get(self, key):
        if key in self.memtable:
            v = self.memtable[key]
            return None if v is self.TOMBSTONE else v
        for table in self.sstables:                     # newest to oldest
            self.files_read += 1
            keys = [k for k, _ in table]
            i = bisect.bisect_left(keys, key)
            if i < len(keys) and keys[i] == key:
                v = table[i][1]
                return None if v is self.TOMBSTONE else v
        return None

    def compact(self):
        merged = {}
        for table in reversed(self.sstables):           # oldest first, newer overwrite
            merged.update(table)
        self.sstables = [sorted((k, v) for k, v in merged.items() if v is not self.TOMBSTONE)]

lsm = ToyLSM()
for k, v in [("a", 1), ("b", 2), ("c", 3), ("a", 10), ("d", 4), ("e", 5)]:
    lsm.put(k, v)
lsm.delete("b")
assert lsm.get("a") == 10 and lsm.get("b") is None and len(lsm.sstables) == 2
lsm.put("f", 6); lsm.put("g", 7)                       # third flush (includes b's tombstone)
lsm.compact()
assert len(lsm.sstables) == 1 and lsm.get("b") is None and lsm.get("c") == 3
assert [k for k, _ in lsm.sstables[0]] == ["a", "c", "d", "e", "f", "g"]   # tombstoned b dropped
```

### 9.7 Normalization vs denormalization

**Normalized** (each fact stored once):

```text
orders(id, user_id, restaurant_id, total)      restaurants(id, name, city)
→ "show order with restaurant name" = JOIN; renaming a restaurant = update 1 row
```

**Denormalized** (copy data where it's read):

```text
orders(id, user_id, restaurant_id, restaurant_name, total)
→ no JOIN on read; renaming a restaurant = update it in every order (or accept old names on old orders)
```

| | Normalized | Denormalized |
|---|---|---|
| Reads | Joins (slower at scale, impossible across shards) | One lookup |
| Writes | One place | Fan-out to every copy |
| Consistency | Automatic | Copies can drift until updated |

**Typical production answer:** keep a normalized **source of truth** for writes, and build **denormalized read models** (cache entries, search documents, materialized views, per-query Cassandra tables) updated by events/CDC. This split of write model and read model is called **CQRS**.

Sometimes the copy is *correct* to freeze: an order should keep the **price and name at purchase time**, not today's.

### 9.8 Connection pooling

Opening a database connection is expensive: TCP handshake + TLS + authentication, and in Postgres a whole **process** per connection (~5–10 MB of memory). So applications keep a **pool** of open connections and reuse them:

```text
request → borrow connection from pool (instant) → run queries → return it to the pool
pool empty? → wait up to N ms → still none → fail fast (timeout) instead of piling up
```

**Sizing — the classic mistake is "more is better":**
- The database has limited CPU cores and disk; beyond a point, more concurrent queries just contend and get slower.
- Total connections = **app instances × pool size per instance**. 50 pods × 20 = 1,000 connections → Postgres struggles.
- Use **Little's law**: needed connections ≈ queries per second × average query time. 2,000 q/s × 5 ms = 10 busy connections on average → a pool of ~20–30 total is plenty.
- **PgBouncer** (transaction pooling) sits between apps and Postgres: thousands of client connections share a few dozen real ones. It doesn't make the database faster — it stops connection overhead from killing it.

## 10. Databases: NoSQL and data modeling

### 10.1 Choosing a database

Pick by **access pattern** (how data is written and read), **consistency needs**, and **scale** — not by brand.

| Need | Pick | Why |
|---|---|---|
| Transactions across rows/tables, joins, constraints: money, orders, inventory, bookings | **PostgreSQL / MySQL** | ACID, flexible queries, mature tooling |
| Each entity read and written as a whole document; schema changes often | **MongoDB, DynamoDB, Firestore** | One read gets everything; flexible fields |
| Huge write volume, time-ordered data, queries known up front: messages, events, IoT | **Cassandra, ScyllaDB, DynamoDB** | LSM writes, add nodes for linear scale |
| Microsecond lookups, counters, sessions, queues, leaderboards | **Redis** | In memory, rich data structures |
| Full-text search with relevance, filters, facets | **Elasticsearch / OpenSearch** | Inverted index |
| Multi-hop relationship queries: friends-of-friends, fraud rings, knowledge graphs | **Neo4j, Neptune** | Traversals follow pointers |
| Metrics over time | **Prometheus, InfluxDB, TimescaleDB** | Compression, time functions, downsampling |
| Analytics over billions of rows | **ClickHouse, BigQuery, Snowflake, Redshift** | Columnar storage |
| Similarity search on embeddings | **pgvector, Elasticsearch kNN, Pinecone, Milvus** | Approximate nearest-neighbour indexes |
| Files, images, video, backups | **S3 / GCS / R2** | Cheap, durable blob storage |

**Interview line:** "I'll start with Postgres as the source of truth. If one part outgrows it — e.g., chat messages at 200K writes/s — I'll move that part to Cassandra, because the access pattern is 'append and read recent by conversation'."

Many real systems use several: Postgres for orders, Redis for sessions and caching, Elasticsearch for search, S3 for images, Kafka to move changes between them (**polyglot persistence**).

### 10.2 Wide-column (Cassandra) modeling

**How Cassandra stores data:**
- Each row has a **partition key** → hashed → decides which nodes store it. All rows with the same partition key live together on the same nodes.
- Inside a partition, rows are sorted by **clustering columns**.
- So the fast query is always: "give me rows of **one partition**, in clustering order, maybe a range of it."

**Model by query, not by entity.** In SQL you design tables around entities and join at read time. In Cassandra you design **one table per query** and duplicate data across tables.

Example — a chat app needs two queries:
1. "Latest 50 messages in conversation X" (scroll up for older).
2. "My conversations, most recently active first."

```sql
-- Query 1
CREATE TABLE messages_by_conversation (
  conversation_id uuid,
  month           int,          -- bucket: keeps one partition from growing forever
  message_id      timeuuid,     -- time-ordered unique id
  sender_id       uuid,
  body            text,
  PRIMARY KEY ((conversation_id, month), message_id)
) WITH CLUSTERING ORDER BY (message_id DESC);

SELECT * FROM messages_by_conversation
WHERE conversation_id = ? AND month = 202505 LIMIT 50;                      -- one partition, already sorted

-- Query 2: a second table holding a copy of what that screen needs
CREATE TABLE conversations_by_user (
  user_id         uuid,
  last_activity   timestamp,
  conversation_id uuid,
  last_message    text,
  PRIMARY KEY ((user_id), last_activity, conversation_id)
) WITH CLUSTERING ORDER BY (last_activity DESC, conversation_id ASC);
```
Every new message writes to **both** tables. Writes are cheap in Cassandra; reads that touch many partitions are what's expensive.

**Rules and gotchas:**
- No joins, no ad-hoc `WHERE` on non-key columns (secondary indexes exist but are limited) → know your queries first.
- Keep partitions bounded (rule of thumb < ~100 MB / ~100K rows) → time buckets like `month`.
- **Hot partitions:** a partition key with huge traffic (a celebrity's feed) overloads its nodes → add a bucket/salt to spread it.
- **Tunable consistency per query:** `ONE` (fast), `QUORUM` (majority of replicas), `ALL`. `QUORUM` writes + `QUORUM` reads → reads see the latest write ([13.4](#134-quorums)).
- Updates of the same row resolve by **last-write-wins** using timestamps.

### 10.3 DynamoDB modeling

- Each item has a **partition key (PK)** and optionally a **sort key (SK)**. Items with the same PK form a collection sorted by SK.
- Queries: get one item by PK+SK, or **query a PK** with conditions on SK (`begins_with`, `between`).
- **Single-table design:** store several entity types in one table with structured keys so one query returns related data:

```text
PK              SK                          attributes
USER#42         PROFILE                     name=Asha, city=Pune
USER#42         ORDER#2025-05-01#9001       total=498, status=DELIVERED
USER#42         ORDER#2025-05-03#9007       total=120, status=PLACED
REST#7          PROFILE                     name=Udupi Café
REST#7          MENU#301                    name=Masala Dosa, price=120

"User 42's profile + orders"   → Query PK = USER#42
"User 42's orders in May"      → Query PK = USER#42 AND SK BETWEEN "ORDER#2025-05" AND "ORDER#2025-05~"
"Restaurant 7's menu"          → Query PK = REST#7 AND SK begins_with "MENU#"
```

- **GSI (global secondary index):** a second PK/SK on the same data for another access pattern (e.g., "orders by status" → GSI with PK = status). Eventually consistent, extra write cost.
- **Capacity:** throughput is split across partitions → a low-cardinality or skewed PK (e.g., PK = today's date) creates a **hot partition** that gets throttled.
- Transactions exist (`TransactWriteItems`, up to 100 items) but are slower; design so most writes touch one item.

Runnable sketch of how "query a partition by sort-key prefix" works (sorted keys + binary search):

```python
import bisect
from collections import defaultdict

class SingleTable:
    def __init__(self):
        self.partitions = defaultdict(list)          # PK -> sorted list of (SK, item)

    def put(self, pk, sk, item):
        rows = self.partitions[pk]
        i = bisect.bisect_left(rows, (sk,))
        if i < len(rows) and rows[i][0] == sk:
            rows[i] = (sk, item)
        else:
            rows.insert(i, (sk, item))

    def query(self, pk, sk_prefix="", reverse=False, limit=None):
        rows = self.partitions.get(pk, [])
        lo = bisect.bisect_left(rows, (sk_prefix,))
        hi = bisect.bisect_left(rows, (sk_prefix + "￿",))
        out = rows[lo:hi][::-1] if reverse else rows[lo:hi]
        return [sk for sk, _ in out[:limit]]

t = SingleTable()
t.put("USER#42", "PROFILE", {"name": "Asha"})
t.put("USER#42", "ORDER#2025-05-03#9007", {"total": 120})
t.put("USER#42", "ORDER#2025-04-28#8800", {"total": 300})
t.put("USER#42", "ORDER#2025-05-01#9001", {"total": 498})
assert t.query("USER#42", "ORDER#2025-05") == ["ORDER#2025-05-01#9001", "ORDER#2025-05-03#9007"]
assert t.query("USER#42", "ORDER#", reverse=True, limit=1) == ["ORDER#2025-05-03#9007"]   # latest order
assert len(t.query("USER#42")) == 4                                                     # whole collection
```

### 10.4 Document (MongoDB) modeling

A document holds a whole entity as nested JSON. The key decision is **embed vs reference**:

```js
// EMBED: order lines live inside the order — always read together, bounded size
{ _id: 9001, user_id: 42, status: "DELIVERED",
  items: [ {menu_item_id: 301, name: "Masala Dosa", qty: 2, price: 120} ],
  delivery: { address: "…", rider_id: 77 } }

// REFERENCE: comments are unbounded and shared → separate collection, point by id
{ _id: "c1", post_id: 500, user_id: 42, text: "Nice!" }
```

| Embed when | Reference when |
|---|---|
| Data is read together with the parent | Data is read on its own too |
| Child count is small and bounded | Child count is unbounded (comments, events) |
| Child belongs to one parent | Shared by many (a user referenced by many orders) |

- A single-document update is atomic → design so one business operation touches one document. Multi-document transactions exist but cost more.
- 16 MB document limit → unbounded arrays inside a document are a classic bug.
- Index the fields you filter and sort by; compound index rules are the same leftmost-prefix rules as SQL ([9.5](#95-indexes)).

### 10.5 Graph databases

In a graph database, **relationships are stored as first-class records**. Each node keeps a pointer to its first relationship, and relationships link to each other — so "who are X's friends" is following pointers, not searching an index.

Query "friends of my friends that I don't know yet" in a relational DB = self-joins on a 1-billion-row `friendships` table, once per hop. In a graph DB it's a traversal:

```cypher
MATCH (me:Person {id: 42})-[:FRIENDS_WITH]->(f)-[:FRIENDS_WITH]->(fof)
WHERE NOT (me)-[:FRIENDS_WITH]->(fof) AND fof <> me
RETURN fof.name, count(*) AS mutual ORDER BY mutual DESC LIMIT 10
```
The cost depends on how many edges you walk, **not** on the total size of the graph.

- **Use for:** recommendations ("people you may know"), fraud rings (accounts sharing devices/cards), knowledge graphs, network/dependency analysis, access-control graphs.
- **Native graph storage (Neo4j):** fixed-size node and relationship records linked by pointers, so traversing a node's edges needs no index lookup ("index-free adjacency").
- **Watch out for supernodes:** a node with millions of edges (a celebrity, a "country = India" node) makes traversals through it explode → cap fan-out, filter by edge type, or model it differently.
- Many teams store the core data in Postgres and only the relationship-heavy part in a graph DB.

![Social network as a graph, with typical Cypher queries](../static/images/graph-social-network-usecase.png)

![Neo4j native storage: node, relationship and property records](../static/images/graph-neo4j-native-storage.png)

![Relationship store: per-node linked lists of relationships](../static/images/graph-relationship-storage.png)

### 10.6 Time-series and columnar

**Time-series databases** store `(series, timestamp, value)`, e.g. `cpu{host="web-3", region="ap-south"} @ 12:00:05 = 71.2`.
- Writes are append-only and arrive in time order; queries are "aggregate this series over a time range".
- **Compression:** consecutive timestamps are nearly evenly spaced and values change slowly → delta-of-delta and XOR encoding get ~1–2 bytes per point instead of 16.
- **Downsampling + retention:** keep raw per-second data for 15 days, per-minute averages for 90 days, hourly for years.
- **Cardinality** (number of unique series) is the scaling limit: tagging metrics with `user_id` creates millions of series → don't.

**Columnar storage (OLAP):** store each column separately instead of each row together.

```text
Row store (Postgres):      [id=1, user=42, city=Pune, amount=120, …30 more columns]   [id=2, …]
Column store (ClickHouse): id: [1, 2, 3, …]   city: [Pune, Pune, Delhi, …]   amount: [120, 300, 80, …]

"SUM(amount) GROUP BY city" over 1B rows:
  row store reads all 34 columns of every row
  column store reads only `city` and `amount`, which also compress extremely well (Pune, Pune, Pune…)
```
- Fast scans and aggregations, vectorized execution.
- Bad at single-row updates and point lookups → not a source of truth for an app.

```python
import random

random.seed(1)
cities = ["Pune", "Delhi", "Chennai"]
rows = [{"id": i, "city": random.choice(cities), "amount": random.randint(50, 500),
         **{f"extra_{c}": "x" * 20 for c in range(30)}} for i in range(5_000)]

# Row store: to aggregate, every field of every row is read.
row_fields_read = sum(len(r) for r in rows)

# Column store: only the two needed columns are read.
columns = {name: [r[name] for r in rows] for name in rows[0]}
totals: dict[str, int] = {}
for city, amount in zip(columns["city"], columns["amount"]):
    totals[city] = totals.get(city, 0) + amount
col_fields_read = len(columns["city"]) + len(columns["amount"])

assert totals == {c: sum(r["amount"] for r in rows if r["city"] == c) for c in cities}
assert row_fields_read == 33 * col_fields_read // 2       # 33 columns per row vs 2 columns read
```

### 10.7 OLTP vs OLAP

| | OLTP (online transaction processing) | OLAP (online analytical processing) |
|---|---|---|
| Example query | "Insert order 9001", "get user 42's cart" | "Revenue by city by week for the last 2 years" |
| Rows touched | Few | Millions–billions |
| Storage | Row-oriented (Postgres, MySQL, DynamoDB) | Column-oriented (ClickHouse, BigQuery, Snowflake) |
| Latency target | Milliseconds | Seconds |
| Concurrency | Thousands of small transactions | Fewer, heavy queries |

**Never run heavy analytics on the production OLTP database** — one big query can starve user traffic. Copy data to an OLAP store:

```text
Postgres (OLTP) ──CDC / nightly ETL──► Kafka / S3 ──► ClickHouse / BigQuery (OLAP) ──► dashboards
```

## 11. Replication

### 11.1 Why

**Replication** = keeping copies of the same data on several machines.
- **Availability:** if the primary dies, a replica takes over.
- **Read scaling:** spread reads across replicas.
- **Latency:** a replica close to users (Mumbai, Frankfurt).
- **Isolation:** run backups and reports on a replica without slowing the primary.

### 11.2 Leader-follower (primary-replica)

```text
writes ──► Leader (primary) ──WAL stream──► Follower 1 ──► reads
                             └─WAL stream──► Follower 2 ──► reads
```
All writes go to the leader; it streams its log to followers, which replay it. Reads can go to followers.

**Synchronous vs asynchronous — when does the leader say "committed"?**

```text
Synchronous:  client → leader writes → waits for follower's "got it" → OK to client
              + no data loss if the leader dies     − every write waits for the slowest follower; follower down = writes stall

Asynchronous: client → leader writes → OK to client → (follower catches up a bit later)
              + fast writes                          − leader dies before shipping → last writes LOST on failover

Semi-sync:    wait for at least ONE follower; others async      ← common compromise
```

### 11.3 Replication lag problems

Async followers are usually milliseconds behind, but under load it can be seconds. Three classic bugs:

**1. Read-your-writes violation**

```text
12:00:00.000  User saves new profile photo → leader
12:00:00.050  User's page reloads → read from follower (still 300 ms behind) → OLD photo
              "My change didn't save!" → user saves again
```
Fixes: read the user's **own** data from the leader for a short window after they write (e.g., 5 s, tracked in their session); or remember the write's log position and read only from a replica that has reached it.

**2. Monotonic reads violation** — a user refreshes twice; the first read hits a fresh replica (sees the new comment), the second hits a lagging one (comment gone). Fix: pin each user's reads to one replica (hash user ID → replica).

**3. Consistent-prefix violation** — replies arrive before the question because they were written to different partitions/replicas. Fix: write causally related data through the same partition/sequence.

```python
class Replicated:
    """Leader + one async follower that applies writes after `lag` seconds."""
    def __init__(self, lag: float):
        self.leader, self.follower, self.pending, self.lag = {}, {}, [], lag
        self.last_write_at: dict[str, float] = {}

    def write(self, user, key, value, now):
        self.leader[key] = value
        self.pending.append((now + self.lag, key, value))
        self.last_write_at[user] = now

    def _replicate(self, now):
        due = [p for p in self.pending if p[0] <= now]
        for _, k, v in due:
            self.follower[k] = v
        self.pending = [p for p in self.pending if p[0] > now]

    def read(self, user, key, now, read_your_writes=False):
        self._replicate(now)
        recent_writer = now - self.last_write_at.get(user, -1e9) < 5
        source = self.leader if (read_your_writes and recent_writer) else self.follower
        return source.get(key)

db = Replicated(lag=0.3)
db.write("asha", "photo:asha", "new.jpg", now=0.0)
assert db.read("asha", "photo:asha", now=0.05) is None                              # stale: replica lag
assert db.read("asha", "photo:asha", now=0.05, read_your_writes=True) == "new.jpg"   # routed to leader
assert db.read("ravi", "photo:asha", now=0.05, read_your_writes=True) is None        # others may see old data briefly
assert db.read("ravi", "photo:asha", now=0.5) == "new.jpg"                           # replica caught up
```

### 11.4 Failover

When the leader dies, a follower must become the new leader:

```text
1. Detect: no heartbeat from leader for N seconds
2. Choose: the follower with the most up-to-date log (fewest lost writes)
3. Promote it; reconfigure other followers to replicate from it
4. Redirect writes (DNS / proxy / client config) to the new leader
5. When the old leader comes back, it must rejoin as a follower — NOT keep acting as leader
```

**What goes wrong:**
- **Lost writes:** with async replication, writes the old leader acknowledged but never shipped are gone. If those IDs were already used elsewhere (e.g., in a cache), data becomes inconsistent.
- **Split brain:** a network partition makes the old leader *look* dead; a new leader is promoted; both accept writes → conflicting data. Prevent with a consensus system (etcd/ZooKeeper) deciding who is leader, **fencing** (the old leader's writes are rejected by token, [13.9](#139-distributed-locks)), or STONITH ("shoot the other node in the head" — power it off).
- **Timeout too short:** a GC pause or brief network blip triggers a needless failover (which itself causes disruption). Too long: minutes of downtime.

Managed databases (RDS Multi-AZ, Cloud SQL, Aurora) automate this with a synchronous standby → failover in ~30–60 s with no data loss.

### 11.5 Multi-leader

Several leaders (typically one per region) each accept writes and replicate to each other.

```text
Mumbai users ──► Leader-Mumbai  ⇄  Leader-Frankfurt ◄── Europe users
                   (both accept writes; changes replicate asynchronously both ways)
```

- **Why:** low write latency in every region; each region keeps working if the link between regions breaks; offline-capable apps (calendar on your phone = a "leader" that syncs later).
- **The cost — write conflicts:** a user edits the same document title in both regions at the same time. Which wins?
  - **Last-write-wins** (by timestamp): simple, silently drops one edit.
  - **Merge** (e.g., union of shopping-cart items).
  - **CRDTs:** data types designed so concurrent updates always merge deterministically (counters, sets, text).
  - **Ask the user** (Git-style conflict).
- Avoid conflicts by design: route each user's writes to their "home" region.

### 11.6 Leaderless (Dynamo-style)

No leader: the client (or any node acting as coordinator) sends each write to **N** replicas and each read to several replicas.

```text
N = 3 replicas, W = 2, R = 2

write(x = 5) ──► replica A ✓, replica B ✓, replica C ✗ (down)   → 2 acks ≥ W → success
read(x)      ──► replica B (x=5, v2), replica C (x=3, v1)        → 2 replies ≥ R → newest wins → 5
                 (and C gets fixed: "read repair")
```
Because **R + W > N** (2 + 2 > 3), every read set overlaps every write set in at least one replica → the read sees the latest acknowledged write. Details and code in [13.4](#134-quorums).

Keeping replicas in sync over time:
- **Read repair:** fix stale replicas noticed during reads.
- **Hinted handoff:** if C is down, another node holds C's writes ("hints") and delivers them when C returns.
- **Anti-entropy:** replicas periodically compare **Merkle trees** of their data to find and sync only the differing ranges ([17](#17-special-data-structures)).

Used by Cassandra, ScyllaDB, Riak, and DynamoDB's internals.

## 12. Partitioning and sharding

### 12.1 Terms

- **Partitioning:** splitting one dataset into pieces.
- **Sharding:** putting those pieces on **different machines** (horizontal partitioning across nodes). Each shard holds a subset of rows and handles their reads and writes.
- **Vertical partitioning:** splitting by columns or by feature — move big blob columns out, or move the `orders` tables to their own database.
- Postgres "declarative partitioning" (`orders_2025_01`, `orders_2025_02`) splits a table **within one server** — great for dropping old data and pruning scans, but it's not sharding.

### 12.2 When to shard

Sharding adds a lot of complexity (cross-shard queries, transactions, rebalancing). Shard only when one machine truly can't cope:
- Data doesn't fit (many TBs), or
- Write throughput exceeds what one primary can handle.

Try first: better indexes and queries → caching → read replicas → bigger machine → archiving old data → splitting by feature (vertical). Many large companies run on a well-tuned single primary + replicas for a long time.

### 12.3 Strategies

```text
Range:   user_id 1–1M → shard A,  1M–2M → shard B,  2M–3M → shard C
Hash:    shard = hash(user_id) % 4
Directory: lookup table  user_id 42 → shard C   (stored in a small, highly available service + cache)
Geo:     users in India → shard IN, EU → shard EU
```

| Strategy | Pros | Cons |
|---|---|---|
| **Range** | Range scans are efficient ("all orders in May") | **Hotspots**: with time- or auto-increment keys, all new writes go to the last shard |
| **Hash** | Even spread of data and load | Range queries hit every shard; with `% N`, changing N moves almost every key |
| **Consistent hashing** | Adding/removing a node moves only ~1/N of keys | Slightly more complex; needs virtual nodes for balance |
| **Directory** | Full control: move one big tenant to its own shard | The lookup service must be fast and highly available |
| **Geo** | Data residency rules, low latency | Unequal shard sizes |

### 12.4 Choosing the shard key (most important sharding decision)

A good shard key:
1. Has **high cardinality** (many distinct values) and spreads **load** evenly — not just data.
2. Is present in **most queries**, so a query goes to one shard instead of all of them.
3. **Never changes** for a row (changing it = moving the row between shards).

| System | Good key | Why | Bad key |
|---|---|---|---|
| Chat | `conversation_id` | All messages of a chat together; every chat query includes it | `sender_id` (a conversation spread over shards) |
| E-commerce orders | `user_id` | "My orders" is one shard | `order_date` (today's shard gets all writes) |
| Multi-tenant SaaS | `tenant_id` | Tenant isolation; tenant queries on one shard | — but giant tenants need their own shard |
| URL shortener | `short_code` | Every redirect looks up by code | — |

### 12.5 Consistent hashing in detail

**The problem with `hash(key) % N`:** going from 4 to 5 servers changes the result for ~80% of keys → a cache cluster suddenly misses on almost everything, or a database must move most of its data.

**Consistent hashing:**
1. Imagine a ring of numbers 0 … 2³² − 1.
2. Hash each **server** to several points on the ring.
3. Hash each **key** to a point; walk clockwise; the first server you meet owns the key.

```text
              0
         S1 ●───────● S2
        /               \
   key k1 ●               ● S3           k1 walks clockwise → owned by S2
        \               /
         S3 ●───────● S1
             (each server appears several times = virtual nodes)

Add S4 at one point → it takes over only the keys between its predecessor and itself.
Every other key stays where it was.
```
- **Virtual nodes:** each physical server gets ~100–200 ring positions → load evens out, a bigger machine can get more positions, and a failed server's keys spread across many servers instead of all landing on one neighbour.
- **Replication:** store each key on the next R **distinct** servers clockwise.

```python
import bisect
import hashlib

def h(s: str) -> int:
    return int(hashlib.md5(s.encode()).hexdigest()[:8], 16)

class Ring:
    def __init__(self, nodes, vnodes=150):
        self.vnodes, self.ring = vnodes, []
        for n in nodes:
            self.add(n)

    def add(self, node):
        for i in range(self.vnodes):
            bisect.insort(self.ring, (h(f"{node}#{i}"), node))

    def owner(self, key):
        i = bisect.bisect(self.ring, (h(key), ""))
        return self.ring[i % len(self.ring)][1]

keys = [f"user:{i}" for i in range(20_000)]

modulo_before = {k: h(k) % 4 for k in keys}
modulo_after = {k: h(k) % 5 for k in keys}
moved_modulo = sum(modulo_before[k] != modulo_after[k] for k in keys) / len(keys)

ring = Ring(["s1", "s2", "s3", "s4"])
before = {k: ring.owner(k) for k in keys}
ring.add("s5")
after = {k: ring.owner(k) for k in keys}
moved_ring = sum(before[k] != after[k] for k in keys) / len(keys)

load = {n: list(after.values()).count(n) / len(keys) for n in ["s1", "s2", "s3", "s4", "s5"]}
assert moved_modulo > 0.7                       # ~80% of keys move with % N
assert 0.12 < moved_ring < 0.28                 # ~1/5 move with consistent hashing
assert all(0.12 < share < 0.28 for share in load.values())   # vnodes keep load roughly even
assert all(after[k] == "s5" for k in keys if before[k] != after[k])   # moved keys only go to the new node
```

Redis Cluster uses a fixed set of **16,384 hash slots** instead of a ring; nodes own ranges of slots, and rebalancing moves whole slots. Same goal, simpler bookkeeping.

### 12.6 Problems after sharding

**1. Cross-shard queries.** "Top 10 orders by amount across all users" with orders sharded by `user_id` → ask every shard for its top 10, merge (**scatter-gather**). Slow and fragile at 100 shards. Fix: maintain a separate read model for that query (a search index, an analytics store, a table keyed differently).

**2. Cross-shard transactions.** Moving money between users on different shards can't use one local transaction → design to avoid it, or use a saga / 2PC ([13.8](#138-distributed-transactions)).

**3. Unique IDs.** Each shard's auto-increment would collide → use Snowflake IDs or UUIDv7 ([13.7](#137-unique-id-generation)).

**4. Hot partitions.** One key gets disproportionate traffic (a celebrity's posts, a viral product). Fix: **salt** the key — write to `post_likes:9#0 … #9` (10 sub-keys on different shards), read all 10 and sum; or give that key a dedicated shard/cache.

**5. Resharding (adding capacity).**
- **Pre-split into many logical shards** (e.g., 4,096) mapped onto a few physical servers. To scale, move whole logical shards to new servers — no row-level rehashing. This is how Redis Cluster slots and many sharded MySQL setups work.
- **Online migration:** start double-writing to old and new layouts → backfill existing data → verify → switch reads → stop old writes.

## 13. Distributed systems core

### 13.1 Why distributed is hard

On one machine, a function call either returns or throws. Across a network, a request can:

```text
Client ──request──► Server
  1. lost on the way               → server never saw it
  2. server crashed while working  → maybe half done
  3. server did it, reply lost     → work done, client thinks it failed
  4. server is just slow (GC pause, overload) → reply comes after the client gave up
```
From the client's side, **all four look identical: a timeout.** You can't tell "slow" from "dead", and you don't know whether the action happened. Add clocks that disagree between machines and nodes that pause unpredictably — that's why distributed systems need retries **plus** idempotency, timeouts, replication, consensus, and careful ordering.

### 13.2 CAP and PACELC

**CAP:** when a **network partition** splits your replicas (they can't talk to each other), each side must choose:
- **C (consistency):** refuse requests that might return stale or conflicting data, or
- **A (availability):** keep answering, accepting that sides may disagree for a while.

```text
            ┌──── partition ────┐
 Mumbai replica      ✂       Frankfurt replica
 user buys the last seat     another user buys the SAME last seat?

 CP system: Frankfurt refuses to sell until it can talk to Mumbai (unavailable, but correct)
 AP system: both sell (available) → double-booked seat must be fixed later
```

- **CP choices:** bank balances, seat booking, inventory decrement, leader election, configuration (etcd, ZooKeeper, Spanner, most SQL setups).
- **AP choices:** social feeds, likes, product catalog, shopping cart, DNS, presence (Cassandra/Dynamo-style stores, caches).
- Without a partition you can have both — CAP only describes behaviour **during** one.

**PACELC** adds the everyday trade-off: **if P**artition → A or C; **E**lse (normal operation) → **L**atency or **C**onsistency. Even with a healthy network, strong consistency means waiting for other replicas (slower); eventual consistency answers from the nearest copy (faster).

Real systems mix both per feature: Amazon's cart is AP; its payment step is CP.

### 13.3 Consistency models (strong → weak)

Example: Asha updates her bio from "A" to "B". What may readers see?

| Model | Guarantee | In the example |
|---|---|---|
| **Linearizable (strong)** | Behaves like a single copy; once a write completes, every later read sees it | Everyone sees "B" right after Asha's save returns |
| **Sequential** | Everyone sees all writes in the same order (maybe delayed) | Readers may see "A" for a moment, but never "B" then "A" |
| **Causal** | If write 2 depended on write 1, everyone sees 1 before 2 | A reply to Asha's post never appears before the post |
| **Read-your-writes** | You always see your own writes | Asha sees "B"; others may still see "A" |
| **Monotonic reads** | You never see older data after newer | Once Ravi sees "B" he never sees "A" again |
| **Eventual** | If writes stop, all replicas converge | Some readers see "A" for a few seconds, then "B" |

Stronger = simpler to reason about, but slower and less available. Choose per feature: strong for money and inventory; read-your-writes for profile edits; eventual for likes and feeds.

### 13.4 Quorums

With **N** replicas, a write waits for **W** acknowledgements and a read asks **R** replicas.

**If R + W > N, every read overlaps every successful write** in at least one replica, so the read can return the newest value (picked by version number).

```text
N = 3: replicas {A, B, C}
W = 2: a write succeeded on some 2 of 3  → e.g., {A, B}
R = 2: a read asks some 2 of 3           → e.g., {B, C}
Any 2 of 3 and any other 2 of 3 must share at least one replica → the read sees the new version.
```

| Setting | Behaviour |
|---|---|
| N=3, W=2, R=2 | Balanced; survives 1 replica down for both reads and writes |
| N=3, W=3, R=1 | Fast reads; writes fail if any replica is down |
| N=3, W=1, R=1 | Fastest, always available, **may read stale data** (R+W = 2 ≤ 3) |

```python
import itertools

class QuorumStore:
    def __init__(self, n=3):
        self.replicas = [dict() for _ in range(n)]          # key -> (version, value)
        self.version = 0

    def write(self, key, value, replica_ids):               # replicas that accepted the write
        self.version += 1
        for i in replica_ids:
            self.replicas[i][key] = (self.version, value)

    def read(self, key, replica_ids):
        answers = [self.replicas[i].get(key, (0, None)) for i in replica_ids]
        return max(answers)[1]                              # newest version wins

n, w, r = 3, 2, 2
always_fresh = True
for write_set in itertools.combinations(range(n), w):
    for read_set in itertools.combinations(range(n), r):
        s = QuorumStore(n)
        s.write("x", "old", range(n))
        s.write("x", "new", write_set)
        always_fresh &= s.read("x", read_set) == "new"
assert always_fresh                                          # R + W > N: every combination reads "new"

s = QuorumStore(3)
s.write("x", "old", range(3))
s.write("x", "new", [0])                                     # W = 1
assert s.read("x", [1]) == "old"                             # R = 1: R + W = 2 ≤ 3 → stale read possible
```

Caveats: concurrent writes, failed partial writes and "sloppy" quorums (writes accepted by stand-in nodes during failures) weaken the guarantee in practice — quorums alone aren't full linearizability.

### 13.5 Consensus and leader election

**Consensus** = several nodes agreeing on a value (who is leader? what's the next log entry?) even if some crash or messages are lost. It's what makes etcd, ZooKeeper, Consul, Kafka's KRaft controller and CockroachDB/Spanner possible.

**Raft in plain words:**

```text
Roles: one LEADER, the rest FOLLOWERS. Time is divided into numbered TERMS.

Normal operation:
  client → leader: "set x = 5"
  leader appends to its log, sends AppendEntries to followers
  once a MAJORITY (3 of 5) have it → entry is COMMITTED → leader applies it and replies to client
  followers apply committed entries in the same order → identical state machines

Leader dies:
  followers stop receiving heartbeats → after a random timeout (150–300 ms) one becomes CANDIDATE
  candidate: term = term + 1, asks others "vote for me"
  a node votes once per term, and only for a candidate whose log is at least as up to date as its own
  majority of votes → new LEADER; random timeouts make split votes rare
```

- **Majority quorum:** 3 nodes tolerate 1 failure, 5 tolerate 2, 7 tolerate 3. Use odd numbers (4 nodes tolerate only 1, like 3).
- A minority side of a partition **cannot** elect a leader or commit → no split brain, but that side is unavailable (CP).
- **Leases:** a leader holds a time-limited lease and must renew it; if it can't reach a majority, it steps down.
- **In interviews:** don't implement Raft — say "use etcd/ZooKeeper for leader election, configuration and distributed locks", and know *why* they need a majority.

### 13.6 Time and ordering

**Why wall clocks can't order events across machines:** clocks drift (milliseconds per hour), NTP corrections make them jump backwards, VMs pause. Machine A writes at "12:00:00.010", machine B at "12:00:00.005", but B's write actually happened later — last-write-wins by timestamp would keep the wrong one.

**Lamport clocks — an order consistent with cause and effect:**

```text
Each node keeps a counter.
- before any local event: counter += 1
- when sending: attach counter
- when receiving a message with counter c: counter = max(counter, c) + 1
If event X caused Y, then L(X) < L(Y). (The reverse isn't guaranteed.)
```

**Vector clocks — detect concurrent updates:** each node keeps a counter **per node**. Comparing two vectors tells you whether one update happened before the other, or whether they're **concurrent** (a real conflict to merge).

```python
def compare(a: dict, b: dict) -> str:
    nodes = set(a) | set(b)
    a_le_b = all(a.get(n, 0) <= b.get(n, 0) for n in nodes)
    b_le_a = all(b.get(n, 0) <= a.get(n, 0) for n in nodes)
    if a_le_b and b_le_a:
        return "equal"
    if a_le_b:
        return "a happened before b"
    if b_le_a:
        return "b happened before a"
    return "concurrent (conflict)"

def bump(clock: dict, node: str) -> dict:
    return {**clock, node: clock.get(node, 0) + 1}

v0 = bump({}, "mumbai")                         # cart created in Mumbai       {mumbai: 1}
v1 = bump(v0, "mumbai")                         # item added in Mumbai          {mumbai: 2}
v2 = bump(v0, "frankfurt")                      # item added in Frankfurt from v0 {mumbai: 1, frankfurt: 1}
assert compare(v0, v1) == "a happened before b"
assert compare(v1, v2) == "concurrent (conflict)"   # neither saw the other → merge (e.g., union of items)
merged = bump({n: max(v1.get(n, 0), v2.get(n, 0)) for n in set(v1) | set(v2)}, "mumbai")
assert compare(v1, merged) == "a happened before b" and compare(v2, merged) == "a happened before b"
```

**Practical answer in system design:** don't rely on clocks for correctness. Get ordering from **one sequencer per entity**: a database sequence, a per-conversation counter, or a Kafka partition (all events for one key go through one ordered log). Google Spanner is the exception: it uses atomic clocks/GPS to bound clock error (TrueTime) and waits out that uncertainty at commit.

### 13.7 Unique ID generation

Requirements usually: unique across many servers, fast, no single bottleneck, ideally **sortable by time** (great for B-tree indexes and "latest first" queries), compact.

| Option | How | Pros | Cons |
|---|---|---|---|
| DB auto-increment | One counter | Simple, ordered, compact | One DB is a bottleneck/SPOF; exposes business volume |
| Auto-increment with step | Shard i issues i, i+N, i+2N | No coordination | Changing N is painful |
| **UUIDv4** | 122 random bits | No coordination at all | 16 bytes; random → scattered B-tree inserts; not sortable |
| **UUIDv7 / ULID** | 48-bit ms timestamp + random bits | No coordination, time-sortable | 16 bytes |
| **Snowflake** | 64-bit: timestamp + machine + sequence | Compact, time-sortable, no coordination per ID | Machine IDs must be unique; clock rollback handling |
| Range allocation (ticket server) | Each server leases a block of 10,000 IDs | Compact, mostly ordered | Extra service; gaps when a server dies |

**Snowflake layout (64 bits):**

```text
| 1 bit sign (0) | 41 bits: ms since custom epoch (~69 years) | 10 bits: machine id (1024) | 12 bits: sequence (4096/ms) |
```

```python
import threading

class Snowflake:
    EPOCH = 1_704_067_200_000          # 2024-01-01 in ms (custom epoch)

    def __init__(self, machine_id: int, clock_ms):
        if not 0 <= machine_id < 1024:
            raise ValueError("machine id must fit in 10 bits")
        self.machine_id, self.clock_ms = machine_id, clock_ms
        self.last_ms, self.sequence = -1, 0
        self.lock = threading.Lock()

    def next_id(self) -> int:
        with self.lock:
            now = self.clock_ms()
            if now < self.last_ms:
                raise RuntimeError("clock moved backwards; refusing to generate duplicate IDs")
            if now == self.last_ms:
                self.sequence = (self.sequence + 1) & 0xFFF
                if self.sequence == 0:                    # 4096 IDs used this ms → wait for next ms
                    while now <= self.last_ms:
                        now = self.clock_ms()
            else:
                self.sequence = 0
            self.last_ms = now
            return ((now - self.EPOCH) << 22) | (self.machine_id << 12) | self.sequence

    @staticmethod
    def parse(id_: int) -> dict:
        return {"ms": (id_ >> 22) + Snowflake.EPOCH, "machine": (id_ >> 12) & 0x3FF, "seq": id_ & 0xFFF}

t = [1_746_000_000_000]
gen_a, gen_b = Snowflake(1, lambda: t[0]), Snowflake(2, lambda: t[0])
ids = [gen_a.next_id() for _ in range(3)] + [gen_b.next_id()]
t[0] += 1
later = gen_a.next_id()
assert len(set(ids)) == 4 and ids[:3] == sorted(ids[:3]) and later > max(ids)   # unique + time-ordered
assert Snowflake.parse(ids[3]) == {"ms": 1_746_000_000_000, "machine": 2, "seq": 0}
t[0] -= 5
try:
    gen_a.next_id(); assert False
except RuntimeError:
    pass
```

### 13.8 Distributed transactions

**The problem:** placing an order touches three services, each with its own database:

```text
Order service: create order   →   Inventory service: reserve items   →   Payment service: charge card
```
There's no single database transaction spanning them. If payment fails after inventory was reserved, who un-reserves it?

**Option 1 — Two-phase commit (2PC):**

```text
Coordinator → all: "PREPARE"   each participant writes the change durably, locks rows, votes YES/NO
all YES     → all: "COMMIT"    (any NO → "ABORT")
```
- Atomic across participants.
- **Blocking:** if the coordinator crashes after PREPARE, participants hold locks until it recovers. Slow (multiple round trips, locks held across the network); every participant must support it.
- Used *inside* distributed databases (Spanner, CockroachDB), rarely between microservices.

**Option 2 — Saga:** a sequence of **local** transactions; if a step fails, run **compensating actions** for the completed steps in reverse.

```text
1. Order: create (PENDING)      compensate: mark CANCELLED
2. Inventory: reserve items     compensate: release items
3. Payment: charge card    ✗ fails
→ run compensations: release items → cancel order
```
- **Orchestration:** one orchestrator (a state machine — Temporal, AWS Step Functions, or your own service) calls each step and the compensations. Easy to see and monitor. **Prefer this in interviews.**
- **Choreography:** services react to each other's events (`OrderCreated` → inventory reserves → `ItemsReserved` → payment charges…). Loosely coupled, but the flow is spread across services and hard to follow.
- **No isolation:** others can see intermediate states (an order PENDING with items reserved) → model them explicitly with statuses.
- Compensations must be **idempotent** and should not fail permanently (refunds retry until they succeed).

```python
class SagaFailed(Exception): ...

def run_saga(steps):
    """steps: list of (name, action, compensation). Runs compensations in reverse on failure."""
    done, log = [], []
    for name, action, compensate in steps:
        try:
            action()
            log.append(f"done {name}")
            done.append((name, compensate))
        except Exception as exc:
            log.append(f"failed {name}: {exc}")
            for prev_name, comp in reversed(done):
                comp()
                log.append(f"compensated {prev_name}")
            raise SagaFailed(log) from exc
    return log

state = {"order": None, "reserved": 0, "charged": 0}

def charge_card():
    raise RuntimeError("card declined")

steps = [
    ("create_order", lambda: state.update(order="PENDING"), lambda: state.update(order="CANCELLED")),
    ("reserve_items", lambda: state.update(reserved=2), lambda: state.update(reserved=0)),
    ("charge_card", charge_card, lambda: state.update(charged=0)),
]
try:
    run_saga(steps); assert False
except SagaFailed as e:
    log = e.args[0]
assert log == ["done create_order", "done reserve_items", "failed charge_card: card declined",
               "compensated reserve_items", "compensated create_order"]
assert state == {"order": "CANCELLED", "reserved": 0, "charged": 0}
```

**Option 3 — Transactional outbox (fixes the "dual write" problem):**

The order service must save the order **and** publish `OrderCreated` to Kafka. Doing these as two separate steps breaks:

```text
save to DB ✓ → crash → publish never happens    → inventory never hears about the order
publish ✓   → DB write fails                     → ghost event for an order that doesn't exist
```

Fix: write the event into an **outbox table in the same database transaction** as the business change; a separate relay publishes outbox rows to Kafka.

```text
BEGIN
  INSERT INTO orders …
  INSERT INTO outbox (event_type, payload) VALUES ('OrderCreated', '{…}')
COMMIT                      ← both or neither

Relay (polls the outbox, or reads the WAL via CDC/Debezium) → publishes to Kafka → marks rows sent
```
The relay can crash after publishing but before marking a row sent → it publishes again → consumers must be **idempotent** (dedupe by event ID).

```python
import json
import sqlite3

db = sqlite3.connect(":memory:")
db.executescript("""
CREATE TABLE orders (id INTEGER PRIMARY KEY, status TEXT);
CREATE TABLE outbox (id INTEGER PRIMARY KEY, event TEXT, payload TEXT, published INTEGER DEFAULT 0);
""")
broker: list[dict] = []

def place_order(order_id: int, fail_midway=False):
    with db:                                            # one transaction
        db.execute("INSERT INTO orders VALUES (?, 'PLACED')", (order_id,))
        if fail_midway:
            raise RuntimeError("crash before commit")
        db.execute("INSERT INTO outbox (event, payload) VALUES ('OrderCreated', ?)",
                   (json.dumps({"order_id": order_id}),))

def relay_once():
    for row_id, event, payload in db.execute("SELECT id, event, payload FROM outbox WHERE published = 0").fetchall():
        broker.append({"event_id": row_id, "type": event, **json.loads(payload)})   # publish
        with db:
            db.execute("UPDATE outbox SET published = 1 WHERE id = ?", (row_id,))

place_order(1)
try:
    place_order(2, fail_midway=True)
except RuntimeError:
    pass
relay_once()
assert db.execute("SELECT id FROM orders").fetchall() == [(1,)]    # order 2 rolled back…
assert broker == [{"event_id": 1, "type": "OrderCreated", "order_id": 1}]   # …and no ghost event
```

**Change data capture (CDC):** instead of polling, read the database's replication log (Postgres logical decoding, MySQL binlog) and turn every committed row change into an event (Debezium → Kafka). Use it to update caches, search indexes and analytics without any dual writes in application code.

### 13.9 Distributed locks

**When:** only one worker across many machines may do something at a time — run tonight's billing job, rebuild one cache key, process one user's ledger.

**Redis lock, done right:**

```text
acquire:  SET lock:billing <random-token> NX PX 30000      (only if absent; auto-expires in 30 s)
work…
release:  delete the key ONLY IF its value is still my token   (Lua script, atomic)
          — otherwise I might delete a lock someone else acquired after mine expired
```

**The deep problem — a lock holder can pause:**

```text
Worker A acquires lock (expires at T+30s)
Worker A: long GC pause / VM freeze for 40 s ...........................
                         T+30s: lock expires → Worker B acquires it, writes data
Worker A wakes up, still believes it holds the lock, writes data  ✗ both wrote
```
No lock service can stop A from waking up late. The **storage** must reject A.

**Fencing tokens:** the lock service hands out a number that **increases** with every acquisition. Every write carries it; the storage remembers the highest token seen and rejects older ones.

```python
class LockService:
    def __init__(self):
        self.token, self.holder, self.expires = 0, None, 0.0

    def acquire(self, who, now, ttl=30):
        if self.holder is None or now >= self.expires:
            self.token += 1
            self.holder, self.expires = who, now + ttl
            return self.token
        return None

class Storage:
    def __init__(self):
        self.highest_token, self.value = 0, None

    def write(self, value, token):
        if token < self.highest_token:
            raise PermissionError(f"stale fencing token {token} < {self.highest_token}")
        self.highest_token, self.value = token, value

locks, storage = LockService(), Storage()
token_a = locks.acquire("A", now=0)          # token 1
# A pauses for 40 s; its lock expires; B takes over
token_b = locks.acquire("B", now=35)         # token 2
storage.write("written by B", token_b)
try:
    storage.write("written by A (late)", token_a)
    assert False
except PermissionError:
    pass
assert storage.value == "written by B"
```

- ZooKeeper (zxid) and etcd (revision) give you fencing-friendly numbers naturally; prefer them for correctness-critical locks.
- **Even better: avoid the lock.** Use a unique constraint, a conditional update (`UPDATE … WHERE status = 'PENDING'`), or route all work for a key to one consumer (a Kafka partition).

### 13.10 Failure detection and membership

**Heartbeats:** every node sends "I'm alive" every second; miss N in a row → considered dead.
- Timeout too short → false alarms (GC pause = "dead") → needless failovers and data movement.
- Too long → slow reaction to real failures.
- **Phi-accrual detectors** (Cassandra, Akka) adapt to observed heartbeat timing and output a suspicion level instead of a yes/no.

**Gossip:** in large clusters, nodes don't all ping each other (N² messages). Instead each node periodically swaps its membership list ("who's alive, with which heartbeat counter") with a few **random** peers. Information spreads like a rumour — the whole cluster learns about a change in O(log N) rounds. Used by Cassandra, Consul, Redis Cluster.

```python
import random

def gossip_rounds(n_nodes: int, fanout: int = 2, seed: int = 0) -> int:
    rng = random.Random(seed)
    informed = {0}                                   # node 0 notices "node 99 is down"
    rounds = 0
    while len(informed) < n_nodes:
        rounds += 1
        for node in list(informed):
            informed.update(rng.sample(range(n_nodes), fanout))
    return rounds

r1k, r100k = gossip_rounds(1_000), gossip_rounds(100_000)
assert r1k < 15 and r100k < 25 and r100k - r1k < 10     # 100× more nodes, only a few more rounds
```

### 13.11 Exactly-once — the honest answer

Messages and requests cross unreliable networks, so delivery can only be:
- **At-most-once:** send and never retry → may be **lost**.
- **At-least-once:** retry until acknowledged → may be **duplicated** (the ack was lost, not the message).

"Exactly-once delivery" over a network is impossible in general. What systems really provide is **exactly-once *effect***:

```text
at-least-once delivery  +  idempotent processing  =  effectively exactly once

Consumer receives "charge order 42" (event_id e-77):
  INSERT INTO processed_events(event_id) VALUES ('e-77')   ← unique constraint
    duplicate key? → already handled → ack and skip
    otherwise      → do the work in the SAME transaction → commit → ack
```
- Kafka's "exactly-once semantics" works **within Kafka**: an idempotent producer (dedups retries) plus transactions that atomically write output records and commit consumer offsets. The moment you call an external system (send an email, charge a card), you're back to at-least-once + idempotency.
- **Interview line:** "Delivery is at-least-once; I make every consumer idempotent with a dedup key, so the effect happens exactly once."

### 13.12 Backpressure

When a producer is faster than a consumer, work piles up somewhere:

```text
API (5,000 req/s) ──► queue ──► workers (3,000 jobs/s)
                       grows by 2,000/s → after an hour: 7.2M jobs, memory full or hours of lag
```

Options — choose deliberately:
- **Buffer (bounded):** absorb short spikes; when full, apply one of the below.
- **Slow the producer:** block or return 429/503 so clients back off (with `Retry-After`).
- **Shed load:** drop low-priority work (analytics events) to protect high-priority work (payments).
- **Scale consumers:** autoscale workers on queue depth/lag (up to the partition count for Kafka).

An **unbounded** queue isn't a solution — it just moves the failure to memory exhaustion and turns a short spike into hours of latency.

## 14. Messaging and stream processing

### 14.1 Why asynchronous messaging

**Synchronous chain (fragile and slow):**

```text
POST /orders → Order service ──► Email service (800 ms) ──► Analytics (200 ms) ──► Loyalty points (300 ms)
               user waits 1.3 s+; if Email is down, placing an order fails
```

**With a message broker:**

```text
POST /orders → Order service: save order, publish "OrderPlaced" → reply 201 in 30 ms
                                        │
                     Broker (Kafka / RabbitMQ / SQS)
                ┌───────────────────────┼────────────────────────┐
           Email worker           Analytics consumer        Loyalty consumer
       (retries if SMTP down)     (batches events)          (catches up later if slow)
```

What you get:
- **Decoupling:** the order service doesn't know or care who listens. Adding a "fraud check" consumer needs no change to it.
- **Fast responses:** slow work happens after the user gets a reply.
- **Spike absorption:** 50,000 orders in a minute queue up; workers drain them at their own pace.
- **Failure isolation + retries:** email outage → messages wait in the queue instead of failing orders.
- **Fan-out:** one event, many independent consumers.

What it costs: eventual consistency (the email arrives seconds later), duplicate messages to handle, another system to run, harder debugging (follow an event through traces).

### 14.2 Queue vs log

Two different designs hide behind "message broker":

**Message queue (RabbitMQ, SQS) — a to-do list.** Each message is a task taken by **one** worker; once acknowledged, it's deleted.

```text
Producer → [ job1, job2, job3 ] → worker A takes job1, worker B takes job2 (competing consumers)
           job1 acked → deleted forever
```

**Log (Kafka, Kinesis, Pulsar) — an append-only journal.** Messages stay for a retention period (e.g., 7 days). Each consumer group tracks its own **offset** (how far it has read).

```text
Partition 0:  [ e0 | e1 | e2 | e3 | e4 | e5 | … ]   (nothing is deleted on read)
                           ▲              ▲
          analytics group offset=2   email group offset=5     ← independent readers
          Bug in analytics? Fix it, reset its offset to 0, replay history.
```

| | Queue (RabbitMQ, SQS) | Log (Kafka) |
|---|---|---|
| After processing | Message deleted | Message kept until retention expires |
| Replay old messages | No | Yes — move the offset back |
| Many independent consumers | Needs one queue copy per consumer | Built in: each consumer group reads everything |
| Ordering | Best effort (FIFO variants exist) | Strict **within a partition** |
| Per-message features | Routing rules, priorities, delays, per-message ack/visibility timeout | Minimal — patterns needed for delays/priorities |
| Throughput | High | Very high (sequential disk I/O, batching) |
| Typical use | Background jobs, task distribution | Event streams, CDC, analytics pipelines, event sourcing, audit trails |

### 14.3 Kafka internals

```text
Topic "orders" (3 partitions, replication factor 3)

Partition 0: leader on broker 1, followers on brokers 2, 3   [0][1][2][3][4]…
Partition 1: leader on broker 2, followers on brokers 3, 1   [0][1][2]…
Partition 2: leader on broker 3, followers on brokers 1, 2   [0][1][2][3]…
```

**Producing:**
- A message has an optional **key**. Kafka picks the partition with `hash(key) % partitions` → **all messages with the same key land in the same partition, in order.** Key by `order_id` → every event of order 42 is processed in sequence. No key → spread round-robin (no ordering).
- Producers batch and compress messages for throughput.

**Durability settings that matter:**

| Setting | Meaning |
|---|---|
| `replication.factor=3` | Each partition stored on 3 brokers |
| `acks=all` | The leader acknowledges only after all **in-sync replicas (ISR)** have the message |
| `min.insync.replicas=2` | Refuse writes if fewer than 2 replicas are in sync (don't silently become a single copy) |
| `enable.idempotence=true` | Producer retries don't create duplicates (sequence numbers per producer) |

With these, losing one broker loses no acknowledged data.

**Consuming — consumer groups:**

```text
Group "email-service" with 2 consumers reading a 3-partition topic:
  consumer A ← partitions 0, 1
  consumer B ← partition 2
Add consumer C → rebalance → A←0, B←1, C←2
Add consumer D → D sits idle: a partition is read by only ONE consumer in a group
→ max parallelism of a group = number of partitions (choose partitions with growth in mind)
```
A different group (e.g., "analytics") gets its own copy of every message.

**Offsets:** a consumer periodically **commits** "I've processed partition 0 up to offset 1042" (stored in an internal Kafka topic). After a crash or rebalance, the new owner resumes from the last committed offset.

**Why Kafka is fast:** appends to the end of files (sequential I/O), relies on the OS page cache, batches and compresses, and sends data from disk to network without copying it through the application (zero-copy).

**Retention & compaction:** delete data older than N days or beyond a size; or **log compaction** keeps only the **latest** message per key (a changelog of current state: "user 42's latest address").

Runnable model of keyed partitioning and consumer groups:

```python
import zlib
from collections import defaultdict

class MiniKafka:
    def __init__(self, partitions: int):
        self.logs = [[] for _ in range(partitions)]
        self.committed = defaultdict(int)            # (group, partition) -> next offset to read

    def produce(self, key: str, value):
        p = zlib.crc32(key.encode()) % len(self.logs)
        self.logs[p].append((key, value))
        return p

    def assign(self, consumers: list[str]) -> dict[str, list[int]]:
        assignment = {c: [] for c in consumers}
        for p in range(len(self.logs)):
            assignment[consumers[p % len(consumers)]].append(p)
        return assignment

    def poll(self, group, partition, max_records=100):
        start = self.committed[(group, partition)]
        return start, self.logs[partition][start:start + max_records]

    def commit(self, group, partition, next_offset):
        self.committed[(group, partition)] = next_offset

k = MiniKafka(partitions=3)
for event in ["created", "paid", "shipped"]:
    k.produce("order-42", event)
for i in range(30):
    k.produce(f"order-{i}", "created")

p42 = k.produce("order-42", "delivered")
assert [v for key, v in k.logs[p42] if key == "order-42"] == ["created", "paid", "shipped", "delivered"]

assert k.assign(["a", "b"]) == {"a": [0, 2], "b": [1]}
assert k.assign(["a", "b", "c", "d"])["d"] == []          # more consumers than partitions → idle

start, batch = k.poll("email", p42)
k.commit("email", p42, start + len(batch))
assert k.poll("email", p42)[1] == []                      # email group is caught up
assert len(k.poll("analytics", p42)[1]) == len(batch)     # analytics group reads independently
```

### 14.4 RabbitMQ and SQS

**RabbitMQ (classic message broker):**

```text
Producer → Exchange ──routing rules──► Queue(s) → Consumers
  direct exchange : route by exact routing key   ("email" → email_queue)
  topic exchange  : wildcard patterns            ("order.*.paid")
  fanout exchange : copy to every bound queue
```
- Consumers **ack** each message; unacked messages are redelivered if the consumer dies.
- **Prefetch** limits how many unacked messages one consumer holds (spreads work fairly).
- Supports priorities, per-message TTL, **dead-letter exchanges**.

**AWS SQS (managed queue):**
- **Visibility timeout:** when a worker receives a message, it becomes invisible to others for N seconds. Delete it when done; if the worker crashes, it reappears and another worker gets it.

```text
worker A receives msg (invisible for 30 s) ── processes ── deletes ✓
worker B receives msg (invisible for 30 s) ── crashes ── 30 s later msg is visible again → worker C retries
```
- Set the timeout longer than the processing time (or extend it while working) — otherwise a slow job is processed twice.
- **Standard queues:** nearly unlimited throughput, at-least-once, best-effort order. **FIFO queues:** ordering per *message group ID*, deduplication window, lower throughput.
- **Redrive policy:** after N failed receives, move the message to a DLQ.

### 14.5 Delivery semantics and ordering

Where you commit the offset/ack decides what a crash does:

```text
At-most-once:   commit offset → process   crash after commit, before processing → message LOST
At-least-once:  process → commit offset   crash after processing, before commit → processed TWICE
```
**Default to at-least-once + idempotent consumers** ([13.11](#1311-exactly-once--the-honest-answer)): losing data is worse than handling a duplicate, and duplicates are easy to neutralize.

```python
import sqlite3

def run_consumer(messages, crash_before_commit_at=None, idempotent=False, db=None, state=None):
    """Processes from the last committed offset; optionally crashes after processing one message."""
    offset = state["committed"]
    while offset < len(messages):
        msg_id, amount = messages[offset]
        if idempotent:
            try:
                db.execute("INSERT INTO processed VALUES (?)", (msg_id,))   # dedupe key
                state["balance"] += amount
            except sqlite3.IntegrityError:
                pass                                                        # duplicate → skip
        else:
            state["balance"] += amount
        if offset == crash_before_commit_at:
            crash_before_commit_at = None
            continue                        # "crash": offset not committed; restart re-reads this message
        offset += 1
        state["committed"] = offset

msgs = [("m1", 100), ("m2", 50), ("m3", 25)]

state = {"committed": 0, "balance": 0}
run_consumer(msgs, crash_before_commit_at=1, state=state)
assert state["balance"] == 225                        # m2 applied twice (should be 175)

db = sqlite3.connect(":memory:")
db.execute("CREATE TABLE processed (msg_id TEXT PRIMARY KEY)")
state = {"committed": 0, "balance": 0}
run_consumer(msgs, crash_before_commit_at=1, idempotent=True, db=db, state=state)
assert state["balance"] == 175                        # duplicate delivery, single effect
```
(In real code the dedupe insert and the business update share one DB transaction.)

**Ordering:**
- Kafka guarantees order **within a partition** only. Key messages by the entity whose order matters (`account_id`, `order_id`): order within an entity, parallelism across entities.
- Retries can reorder (message 1 fails and goes to a retry topic while message 2 succeeds). If strict per-key order matters, block that key until message 1 succeeds, or carry version numbers and let consumers ignore older versions.

### 14.6 Failure handling patterns

```text
main topic ──► consumer ──✓──► done
                  │ transient error (timeout, 503, rate limit)
                  ▼
           retry-1m topic ──(consumer waits until 1 min passed)──► retry
                  │ fails again
                  ▼
           retry-10m topic ──► retry
                  │ fails again / non-retryable error (bad payload)
                  ▼
           DLQ (dead-letter queue) + alert  ──► human fixes the bug/data ──► replay into main topic
```

- **Retry with exponential backoff + jitter** for transient failures, a few times.
- **Separate retry topics** so a failing message doesn't block the partition behind it (in-place retries stall every later message in that partition).
- **DLQ:** after N attempts, or immediately for non-retryable errors. Store the original message + error + attempt count + timestamps.
- **Poison pill:** a malformed message that crashes the consumer on every attempt → catch exceptions per message, DLQ it, keep going.
- **Replay:** after fixing, re-publish DLQ messages; idempotent consumers make this safe.
- **Monitor consumer lag** (latest offset − committed offset). Growing lag = consumers can't keep up → alert and scale.

### 14.7 Event-driven patterns

**Event notification** — a thin event; consumers fetch what they need:
```json
{"type": "OrderPlaced", "order_id": 42}
```
Small and decoupled, but consumers call back into the order service (load, coupling at read time).

**Event-carried state transfer** — the event contains the data:
```json
{"type": "OrderPlaced", "order_id": 42, "user_id": 9, "items": [...], "total": 498, "address": {...}}
```
Consumers keep their own local copy, no callbacks; events are bigger and the schema is a contract (version it, e.g., with a schema registry).

**Event sourcing** — store the **events** as the source of truth; current state is computed by replaying them.

```text
Account 42 events:  Opened(0) → Deposited(500) → Withdrew(120) → Deposited(50)
Current balance = replay = 430          (snapshots every N events make replay fast)
```
Perfect audit trail, time travel ("balance on May 1"), easy to build new views from history. Costs: querying current state needs read models; changing event schemas is hard; more complex than CRUD.

**CQRS (command query responsibility segregation)** — separate the write model from read models:

```text
Commands (writes) → validated, normalized write DB ──events/CDC──► read models:
                                                       • search index (Elasticsearch)
                                                       • denormalized "order summary" table
                                                       • cache entries
Queries (reads)   → whichever read model fits the screen
```
Reads and writes scale and evolve independently; read models are eventually consistent.

### 14.8 Stream processing

Batch processing runs on a finished dataset ("yesterday's orders"). **Stream processing** computes continuously as events arrive ("orders per city in the last minute, updated every minute"). Engines: Flink, Kafka Streams, Spark Structured Streaming.

**Windows** — slicing an infinite stream into finite chunks:

```text
Tumbling (1 min, no overlap):     [12:00–12:01) [12:01–12:02) [12:02–12:03)
Sliding/hopping (5 min every 1):  [12:00–12:05) [12:01–12:06) [12:02–12:07) …
Session (gap 30 min):             a user's clicks grouped until 30 min of inactivity
```

**Event time vs processing time:** a phone in a tunnel sends a click that happened at 12:00:50 but arrives at 12:03:10. Count it in the 12:00 window (event time — correct) or the 12:03 window (processing time — easy but wrong)?

**Watermarks** answer "when can I close the 12:00 window?" A watermark of 12:01:30 means "I believe I've now seen all events up to 12:01:30". Windows ending before the watermark are emitted; events arriving later than an **allowed lateness** go to a side output/correction path.

```python
from collections import defaultdict

def tumbling_counts(events, window=60, allowed_lateness=30):
    """events: (event_time, arrival_time, key) in arrival order. Watermark = max event time seen - lateness."""
    open_windows = defaultdict(lambda: defaultdict(int))
    results, late, watermark = {}, [], float("-inf")
    for event_time, _arrival, key in events:
        start = event_time - event_time % window
        if start + window <= watermark:                      # window already closed → too late
            late.append((event_time, key))
            continue
        open_windows[start][key] += 1
        watermark = max(watermark, event_time - allowed_lateness)
        for s in [s for s in open_windows if s + window <= watermark]:
            results[s] = dict(open_windows.pop(s))           # emit closed windows
    for s, counts in open_windows.items():                   # end of stream: flush
        results[s] = dict(counts)
    return results, late

events = [(5, 5, "pune"), (20, 21, "delhi"), (50, 95, "pune"),   # 50 arrives late but within lateness
          (70, 71, "pune"), (100, 101, "delhi"), (10, 130, "delhi")]   # event at t=10 arrives far too late
results, late = tumbling_counts(events)
assert results[0] == {"pune": 2, "delhi": 1}        # late-but-allowed event counted in the right window
assert results[60] == {"pune": 1, "delhi": 1}
assert late == [(10, "delhi")]                      # beyond allowed lateness → side output
```

**State and fault tolerance:** a stream job keeps state (counts per key per window) locally and **checkpoints** it to durable storage together with input offsets. After a crash it restores the checkpoint and re-reads from those offsets → state stays exactly right.

**Lambda vs Kappa:**
- **Lambda:** a batch layer recomputes accurate results from raw data periodically + a speed layer gives fast approximate results; merge at query time. Two codebases.
- **Kappa:** stream processing only; to recompute, replay the log through a new version of the job. One codebase.

## 15. Storage: block, file, object, data lake, warehouse

**Three kinds of storage:**

| Type | What it is | Accessed by | Examples | Use for |
|---|---|---|---|---|
| **Block** | A raw virtual disk attached to one machine | The OS (formatted with a filesystem) | EBS, local NVMe SSD | Databases, OS disks — low latency, random writes |
| **File** | A shared filesystem with folders, mounted by many machines | File paths (NFS/SMB) | EFS, NFS, Filestore | Shared files, legacy apps |
| **Object** | A flat namespace of objects: key → bytes + metadata | HTTP API (`PUT /bucket/key`) | S3, GCS, Azure Blob, R2 | Images, video, backups, logs, data lakes, static websites |

**Object storage in practice (S3):**
- **Durable and cheap:** data is replicated / erasure-coded across data centers (S3 advertises 11 nines of durability). Pennies per GB-month.
- **Whole-object operations:** you can't update byte 500 of an object in place; you upload a new version.
- **Strongly consistent** read-after-write on S3 today; listing millions of keys is slow → keep your own metadata index (DB table of files).
- **Storage classes + lifecycle rules:** hot (Standard) → infrequent access after 30 days → archive (Glacier) after a year → delete after 7 years.

**Uploads and downloads should bypass your app servers.** Streaming 2 GB videos through API servers wastes their bandwidth and memory. Instead the server hands out **pre-signed URLs**:

```text
1. App: POST /videos → API server checks auth/quota → creates a DB row (status=UPLOADING)
                     → signs a URL valid for 15 min: PUT https://bucket.s3…/raw/9001.mp4?X-Amz-Signature=…
2. App uploads the file DIRECTLY to S3 using that URL (multipart for big files: parallel 8 MB parts, retry only failed parts)
3. S3 emits "object created" event → processing pipeline (transcode, thumbnails)
4. Downloads: API returns a signed GET URL (or a signed CDN URL) that expires
```

The signature is an HMAC over the method, path and expiry using a secret only the server (and S3/CDN) knows — anyone can verify it, nobody can forge or extend it:

```python
import hashlib
import hmac
from urllib.parse import parse_qs, urlencode, urlparse

SECRET = b"storage-signing-key"

def sign_url(method: str, path: str, expires_at: int) -> str:
    sig = hmac.new(SECRET, f"{method}\n{path}\n{expires_at}".encode(), hashlib.sha256).hexdigest()
    return f"https://cdn.example.com{path}?" + urlencode({"expires": expires_at, "sig": sig})

def verify(method: str, url: str, now: int) -> bool:
    u = urlparse(url)
    q = {k: v[0] for k, v in parse_qs(u.query).items()}
    if now > int(q["expires"]):
        return False
    expected = hmac.new(SECRET, f"{method}\n{u.path}\n{q['expires']}".encode(), hashlib.sha256).hexdigest()
    return hmac.compare_digest(expected, q["sig"])

url = sign_url("GET", "/videos/9001/master.m3u8", expires_at=1_000)
assert verify("GET", url, now=900)
assert not verify("GET", url, now=1_001)                                        # expired
assert not verify("GET", url.replace("expires=1000", "expires=9999"), now=900)  # tampered expiry
assert not verify("GET", url.replace("/9001/", "/9002/"), now=900)              # different video
```

**Distributed file systems (HDFS):** files split into large blocks (128 MB) replicated 3× across data nodes; a name node keeps the metadata (which blocks make up which file, where they live). Designed for huge sequential batch reads, with compute scheduled next to the data.

**Data lake vs warehouse vs lakehouse:**

```text
Sources (OLTP DBs via CDC, events, logs, files)
        │
        ▼
Data lake (S3/GCS): raw + cleaned files in Parquet/JSON — cheap, any format, schema-on-read
        │  Spark / dbt transformations
        ▼
Warehouse (Snowflake/BigQuery/Redshift): modeled tables, fast SQL for BI — schema-on-write
```
- **Lakehouse** (Delta Lake, Apache Iceberg, Hudi): table formats that add ACID transactions, schema evolution and time travel *on top of* lake files → one copy of data serves both data science and SQL BI.
- **ETL vs ELT:** transform before loading (classic) vs load raw first and transform inside the warehouse (modern, with dbt).

![Databricks platform: Spark compute, lakehouse storage, Unity Catalog, SQL warehouse](../static/images/databricks-platform-sketch.png)

**Batch processing (MapReduce → Spark):** split the data into partitions, process them in parallel, regroup by key, combine:

```text
map:     each worker reads its chunk, emits (key, value) pairs     ("pune", 1), ("delhi", 1) …
shuffle: all pairs with the same key are sent to the same reducer   pune → reducer 1
reduce:  combine values per key                                      ("pune", 41_230)
```

```python
from collections import defaultdict
from concurrent.futures import ThreadPoolExecutor

orders = [("pune", 120), ("delhi", 300), ("pune", 80), ("chennai", 50), ("delhi", 70), ("pune", 10)]
chunks = [orders[0:2], orders[2:4], orders[4:6]]                 # data split across 3 "workers"

def map_phase(chunk):                                              # runs in parallel
    return [(city, amount) for city, amount in chunk]

def shuffle(mapped):                                               # group by key
    groups = defaultdict(list)
    for pairs in mapped:
        for k, v in pairs:
            groups[k].append(v)
    return groups

def reduce_phase(item):
    city, amounts = item
    return city, sum(amounts)

with ThreadPoolExecutor(3) as pool:
    mapped = list(pool.map(map_phase, chunks))
    totals = dict(pool.map(reduce_phase, shuffle(mapped).items()))
assert totals == {"pune": 210, "delhi": 370, "chennai": 50}
```
Spark keeps intermediate data in memory across steps (instead of writing to disk between map and reduce like classic MapReduce), which makes multi-step jobs much faster.

## 16. Search

### 16.1 Inverted index

`SELECT * FROM products WHERE description LIKE '%running shoes%'` scans every row and can't rank results. Search engines flip the data around: instead of document → words, they store **word → documents**.

```text
Documents:
  d1: "Nike running shoes for men"
  d2: "Waterproof trail running shoe"
  d3: "Leather office shoes"

Analysis (lowercase, split, stem "shoes"→"shoe", "running"→"run", drop "for"):
  d1 → [nike, run, shoe, men]     d2 → [waterproof, trail, run, shoe]     d3 → [leather, office, shoe]

Inverted index (term → posting list of doc ids):
  run   → [d1, d2]
  shoe  → [d1, d2, d3]
  trail → [d2]
  …

Query "running shoes" → analyzed to [run, shoe] → intersect posting lists → {d1, d2} → score → rank
```
- Lookups are by term → fast regardless of corpus size.
- The **same analyzer** must run on documents and queries (otherwise "Shoes" never matches "shoe").
- Analyzers can also add **synonyms** ("sneakers" ↔ "shoes"), handle languages, and split identifiers.

```python
import math
import re
from collections import Counter, defaultdict

STOP = {"for", "the", "a", "of"}

def stem(word: str) -> str:                  # toy stemmer; real engines use Porter/Snowball
    if word.endswith("ning"):
        return word[:-4]                         # running → run
    if word.endswith("s") and len(word) > 3:
        return word[:-1]                         # shoes → shoe
    return word

def analyze(text: str) -> list[str]:
    words = re.findall(r"[a-z0-9]+", text.lower())
    return [stem(w) for w in words if w not in STOP]

class InvertedIndex:
    def __init__(self, docs: dict[str, str]):
        self.docs = {d: analyze(t) for d, t in docs.items()}
        self.postings = defaultdict(set)
        for d, terms in self.docs.items():
            for term in terms:
                self.postings[term].add(d)

    def search(self, query: str) -> list[str]:
        terms = analyze(query)
        candidates = set.intersection(*(self.postings.get(t, set()) for t in terms)) if terms else set()
        n = len(self.docs)
        def score(d):                                    # TF-IDF: rare matching terms count more
            tf = Counter(self.docs[d])
            return sum(tf[t] / len(self.docs[d]) * math.log(n / len(self.postings[t])) for t in terms)
        return sorted(candidates, key=score, reverse=True)

idx = InvertedIndex({
    "d1": "Nike running shoes for men",
    "d2": "Waterproof trail running shoe",
    "d3": "Leather office shoes",
})
assert idx.postings["shoe"] == {"d1", "d2", "d3"} and idx.postings["run"] == {"d1", "d2"}
assert set(idx.search("Running Shoes")) == {"d1", "d2"}
assert idx.search("trail shoes") == ["d2"]
```

### 16.2 Relevance scoring

Finding matches is easy; **ranking** them is the real work.

- **TF (term frequency):** a document mentioning "dosa" 5 times is probably more about dosa than one mentioning it once.
- **IDF (inverse document frequency):** a word in every document ("the", "buy") says little; a rare word ("dosa") says a lot. `idf = log(N / docs_containing_term)`.
- **TF-IDF** = TF × IDF.
- **BM25** (the default in Elasticsearch/Lucene) improves TF-IDF:
  - **Saturation:** the 10th mention adds much less than the 2nd (TF can't be gamed by repeating words).
  - **Length normalization:** a match in a short title counts more than a match in a 5,000-word page.
- **Field boosts:** a match in `title` worth 3× a match in `description`.
- **Business signals:** combine text relevance with popularity, rating, recency, availability, margin (`function_score`), or a learned ranking model (learning-to-rank) trained on clicks.
- **Semantic (vector) search** matches meaning, not words ("cheap sneakers" ↔ "budget running shoes"). **Hybrid search** runs both and merges the rankings — see the [GenAI guide](genai.md).

### 16.3 Elasticsearch architecture

```text
Index "products" = 3 primary shards × 1 replica each, spread over 3 nodes

node-1: P0  R1          node-2: P1  R2          node-3: P2  R0
(P = primary shard, R = replica of another node's primary; each shard is a full Lucene index)
```

**Writing a document:**
```text
PUT /products/_doc/42  → shard = hash(42) % 3 = P1 (node-2)
P1: write to in-memory buffer + translog (durability) → forward to replica R1 → ack
every ~1 s "refresh": buffer becomes a new immutable SEGMENT → now searchable  (near real-time)
background "merge": small segments combined into bigger ones; deleted docs purged
```
- Because the shard is chosen with `% number_of_primaries`, **the primary shard count is fixed** at index creation. Changing it means reindexing into a new index (or split/shrink APIs).
- An update = mark the old version deleted + index a new version.

**Searching:**
```text
query → any node becomes the COORDINATOR
  scatter: send to one copy (primary or replica) of every shard
  each shard returns its top 10 doc ids + scores
  gather: coordinator merges to the global top 10, then fetches those 10 documents
```
- More replicas → more read throughput and failover. More shards → more parallelism but more scatter-gather overhead (too many tiny shards hurts; aim ~10–50 GB per shard).

**Mappings (schema):**
- `text` fields are analyzed for full-text search (`description`).
- `keyword` fields are exact values for filters, sorting and aggregations (`brand`, `category`, `sku`).
- Numeric/date fields for ranges.
- **Filter context** (`brand = Nike`, `price < 2000`) doesn't score and is cached → fast; **query context** (`match: "running shoes"`) is scored.
- **Aggregations** compute facets ("Nike (120), Adidas (95)…", price buckets).

**Elasticsearch is a secondary index, not the source of truth:**

```text
Postgres (source of truth) ──CDC/Kafka──► indexer ──bulk API──► Elasticsearch
Need a mapping change? Create products_v2, reindex from Postgres, catch up from Kafka,
then atomically switch the alias "products" → products_v2. Zero downtime.
```

### 16.4 Autocomplete approaches

As the user types "iph", show "iphone 15", "iphone 15 pro", … within ~50 ms.

**1. Trie with precomputed top-K at each node** — a prefix tree where every node stores its best completions, so a lookup is just walking the prefix:

```text
(root)
  └ i
     └ p
        └ h  top-3: [iphone 15 (9800), iphone 15 pro (7600), iphone charger (3100)]
           └ o …
```

```python
import heapq

class AutocompleteTrie:
    def __init__(self, k=3):
        self.k, self.root = k, {}

    def add(self, phrase: str, popularity: int):
        node = self.root
        for ch in phrase:
            node = node.setdefault(ch, {})
            top = node.setdefault("__top", [])
            top.append((popularity, phrase))
            node["__top"] = heapq.nlargest(self.k, top)       # keep only the best K per prefix

    def suggest(self, prefix: str) -> list[str]:
        node = self.root
        for ch in prefix:
            if ch not in node:
                return []
            node = node[ch]
        return [phrase for _, phrase in node["__top"]]

t = AutocompleteTrie()
for phrase, pop in [("iphone 15", 9800), ("iphone 15 pro", 7600), ("iphone charger", 3100),
                    ("ipad air", 5000), ("iphone case", 2000)]:
    t.add(phrase, pop)
assert t.suggest("iph") == ["iphone 15", "iphone 15 pro", "iphone charger"]
assert t.suggest("ip") == ["iphone 15", "iphone 15 pro", "ipad air"]
assert t.suggest("xyz") == []
```
Built offline from query logs (counts with time decay), shipped to serving nodes as a snapshot.

**2. Edge n-grams** at index time: "iphone" is indexed as "i", "ip", "iph", "ipho", … so a prefix query is a normal term lookup. Works inside Elasticsearch with filters.

**3. Completion suggester** (Elasticsearch): an in-memory FST built for very fast prefix lookups.

Full design (trending, personalization, CDN caching of short prefixes) in [22.10](#2210-typeahead--search-autocomplete).

## 17. Special data structures

These trade a little accuracy for huge savings in memory or time. Each solves a specific system-design problem.

### Bloom filter — "have I seen this before?" without storing everything

A bit array of m bits + k hash functions.
- **Add(x):** set the k bits at `hash_i(x) % m`.
- **Contains(x):** if any of the k bits is 0 → **definitely not present**. If all are 1 → **probably present** (could be a false positive: other items set those bits).

```text
m = 16 bits, k = 3
add "apple"  → bits 2, 7, 11  set
add "mango"  → bits 4, 7, 13  set
check "grape" → bits 2, 9, 13 → bit 9 is 0 → definitely NOT in set (no disk lookup needed)
check "kiwi"  → bits 4, 11, 13 → all 1 → "maybe" → false positive (never added)
```

**Used for:** LSM storage engines (skip SSTables that can't contain a key), guarding against cache penetration (reject IDs that don't exist), "has this URL already been crawled?", "has this user seen this recommendation?". No deletes (a counting Bloom filter supports them).

**Sizing:** false-positive rate ≈ (1 − e^(−kn/m))^k; best k ≈ (m/n)·ln 2. About **10 bits per item gives ~1% false positives** — 100M items in ~120 MB instead of GBs.

```python
import hashlib
import math

class BloomFilter:
    def __init__(self, expected_items: int, fp_rate: float):
        self.m = math.ceil(-expected_items * math.log(fp_rate) / math.log(2) ** 2)
        self.k = max(1, round(self.m / expected_items * math.log(2)))
        self.bits = bytearray(self.m // 8 + 1)

    def _positions(self, item: str):
        digest = hashlib.sha256(item.encode()).digest()
        h1, h2 = int.from_bytes(digest[:8], "big"), int.from_bytes(digest[8:16], "big")
        return [(h1 + i * h2) % self.m for i in range(self.k)]      # double hashing → k positions

    def add(self, item: str):
        for p in self._positions(item):
            self.bits[p // 8] |= 1 << (p % 8)

    def might_contain(self, item: str) -> bool:
        return all(self.bits[p // 8] & (1 << (p % 8)) for p in self._positions(item))

bf = BloomFilter(expected_items=10_000, fp_rate=0.01)
for i in range(10_000):
    bf.add(f"user:{i}")
assert all(bf.might_contain(f"user:{i}") for i in range(10_000))          # no false negatives, ever
fp = sum(bf.might_contain(f"other:{i}") for i in range(20_000)) / 20_000
assert fp < 0.02 and bf.m / 10_000 < 10                                   # ~1% FP with < 10 bits per item
```

### HyperLogLog — count unique items in ~12 KB

"How many unique visitors today?" A set of 100M user IDs needs GBs. HyperLogLog estimates it with **~1% error in 12 KB** (Redis `PFADD` / `PFCOUNT`).

**Intuition:** hash each item to random bits. Seeing a hash that starts with 20 zero bits is about a 1-in-a-million event — if you've seen one, you've probably seen around a million distinct items. HLL splits hashes into many buckets, keeps the longest zero-run per bucket, and averages them for a stable estimate. HLLs can be **merged** (union of today's and yesterday's visitors) by taking the max per bucket.

### Count-Min Sketch — approximate frequency of each item in a stream

"How many times has hashtag X been used in the last hour?" for millions of hashtags.
- A small 2D array: d rows × w counters, a hash function per row.
- **Add(x):** increment one counter in each row.
- **Count(x):** take the **minimum** of x's counters (other items may have added to them, never subtracted) → **never underestimates**, may overestimate slightly.
- With a small heap it tracks the **top-K heavy hitters** (trending topics) in fixed memory.

```python
import hashlib
import heapq

class CountMinSketch:
    def __init__(self, width=500, depth=4):
        self.w, self.d = width, depth
        self.table = [[0] * width for _ in range(depth)]

    def _cols(self, item):
        return [int(hashlib.md5(f"{row}:{item}".encode()).hexdigest(), 16) % self.w for row in range(self.d)]

    def add(self, item, count=1):
        for row, col in enumerate(self._cols(item)):
            self.table[row][col] += count

    def estimate(self, item):
        return min(self.table[row][col] for row, col in enumerate(self._cols(item)))

cms = CountMinSketch()
true_counts = {"#ipl": 5000, "#budget": 1200, "#monsoon": 800}
true_counts.update({f"#tag{i}": 3 for i in range(2000)})        # long tail of rare tags
for tag, n in true_counts.items():
    cms.add(tag, n)
assert all(cms.estimate(t) >= n for t, n in true_counts.items())          # never underestimates
assert abs(cms.estimate("#ipl") - 5000) / 5000 < 0.05
top3 = heapq.nlargest(3, true_counts, key=cms.estimate)
assert top3 == ["#ipl", "#budget", "#monsoon"]                            # heavy hitters found
```

### Geohash — turn a location into a string where nearby places share a prefix

Repeatedly halve the longitude and latitude ranges, writing 1 for "upper half" and 0 for "lower half" (alternating lng/lat), then encode every 5 bits as a base-32 character.

```text
Bangalore (12.97, 77.59) → "tdr1y…"      precision 5 ≈ 4.9 km × 4.9 km cell
                                          precision 6 ≈ 1.2 km × 0.6 km
                                          precision 7 ≈ 150 m × 150 m
"Restaurants within 1 km" → take the user's precision-6 cell + its 8 neighbours
→ WHERE geohash LIKE 'tdr1y4%' OR … (any DB index on a string works) → exact distance filter
```
- Why neighbours too: two places 10 m apart can sit on opposite sides of a cell border with different prefixes.

```python
BASE32 = "0123456789bcdefghjkmnpqrstuvwxyz"

def geohash(lat: float, lng: float, precision: int = 6) -> str:
    lat_rng, lng_rng = [-90.0, 90.0], [-180.0, 180.0]
    bits, out, even = [], [], True
    while len(out) < precision:
        rng, val = (lng_rng, lng) if even else (lat_rng, lat)
        mid = (rng[0] + rng[1]) / 2
        if val >= mid:
            bits.append(1); rng[0] = mid
        else:
            bits.append(0); rng[1] = mid
        even = not even
        if len(bits) == 5:
            out.append(BASE32[int("".join(map(str, bits)), 2)])
            bits = []
    return "".join(out)

mg_road, brigade_road, mumbai = geohash(12.9756, 77.6066), geohash(12.9719, 77.6070), geohash(19.0760, 72.8777)
assert mg_road[:5] == brigade_road[:5]                    # ~400 m apart → share a long prefix
assert mg_road[:2] != mumbai[:2]                          # different cities → differ early
assert geohash(57.64911, 10.40744, 11) == "u4pruydqqvj"   # standard reference example
```

**Quadtree:** recursively split a square into 4 until each cell has ≤ K points → small cells downtown, huge cells over the ocean (adapts to density). **H3 (Uber) / S2 (Google):** hierarchical hexagon/square cells on a sphere; hexagons have equidistant neighbours, nice for ride-hailing.

### Merkle tree — find which part of two huge datasets differs

Hash each data block, then hash pairs of hashes up to a single **root hash**. Two replicas compare roots: equal → identical, done. Different → compare the two children, and only descend into subtrees whose hashes differ.

```text
                root = H(H12 + H34)
              /                     \
       H12 = H(H1+H2)          H34 = H(H3+H4)
        /        \               /        \
    H(block1) H(block2)     H(block3)  H(block4)

1M blocks, 1 differs → ~20 hash comparisons (one per tree level) instead of comparing 1M blocks
```
**Used for:** replica anti-entropy in Cassandra/DynamoDB, Git, IPFS, blockchains, verifying downloads.

```python
import hashlib

def H(b: bytes) -> bytes:
    return hashlib.sha256(b).digest()

def build(blocks: list[bytes]) -> list[list[bytes]]:
    level = [H(b) for b in blocks]
    tree = [level]
    while len(level) > 1:
        level = [H(level[i] + level[i + 1 if i + 1 < len(level) else i]) for i in range(0, len(level), 2)]
        tree.append(level)
    return tree                                       # tree[-1][0] is the root

def diff(a, b, level=None, index=0, compared=None):
    """Return indexes of differing leaf blocks, descending only into differing subtrees."""
    compared = compared if compared is not None else [0]
    level = len(a) - 1 if level is None else level
    compared[0] += 1
    if a[level][index] == b[level][index]:
        return []
    if level == 0:
        return [index]
    out = []
    for child in (2 * index, 2 * index + 1):
        if child < len(a[level - 1]):
            out += diff(a, b, level - 1, child, compared)
    return out

blocks = [f"row-{i}".encode() for i in range(1024)]
replica = list(blocks)
replica[777] = b"row-777-changed"
ta, tb = build(blocks), build(replica)
counter = [0]
assert ta[-1][0] != tb[-1][0]
assert diff(ta, tb, compared=counter) == [777]
assert counter[0] < 30                                # ~2 × tree height comparisons, not 1024
```

### Other structures worth naming

| Structure | What it does | Where you'll mention it |
|---|---|---|
| **Skip list** | Sorted linked list with "express lanes"; O(log n) search/insert; easy to make concurrent | Redis sorted sets, LSM memtables |
| **Trie** | Prefix tree | Autocomplete ([16.4](#164-autocomplete-approaches)), IP routing tables |
| **Consistent hash ring** | Key → node with minimal movement on changes | Sharding, cache clusters ([12.5](#125-consistent-hashing-in-detail)) |
| **Ring buffer** | Fixed-size circular array | Sliding-window counters, recent-events logs |
| **Inverted index** | Term → documents | Search ([16.1](#161-inverted-index)) |
| **Min-heap** | Fast access to the smallest item | Schedulers (next job by time), top-K, merging sorted streams |

## 18. Reliability and resilience

### 18.1 Availability math

Availability = fraction of time the system works. "Nines" translate into allowed downtime:

| Availability | Downtime per year | Per month | Per day |
|---|---|---|---|
| 99% ("two nines") | 3.65 days | 7.2 h | 14.4 min |
| 99.9% | 8.8 h | 43 min | 1.4 min |
| 99.99% | 53 min | 4.3 min | 8.6 s |
| 99.999% | 5.3 min | 26 s | 0.9 s |

**Dependencies in series multiply** — every component a request *needs* lowers availability:

```text
LB (99.99%) → App (99.9%) → DB (99.95%)   →   0.9999 × 0.999 × 0.9995 ≈ 99.84%
```
A service calling 10 dependencies at 99.9% each is at best ~99.0%.

**Redundancy in parallel adds nines** — the system works if *any* copy works:

```text
one replica at 99%             → 99%
two independent replicas       → 1 − (0.01 × 0.01) = 99.99%
three                          → 99.9999%
```
(Only if failures are independent — two replicas on the same rack, same deploy, or same bug fail together.)

```python
def series(*a): 
    p = 1.0
    for x in a:
        p *= x
    return p

def parallel(a, n):
    return 1 - (1 - a) ** n

def downtime_minutes_per_year(a):
    return (1 - a) * 365 * 24 * 60

assert round(series(0.9999, 0.999, 0.9995), 4) == 0.9984
assert round(parallel(0.99, 2), 4) == 0.9999
assert round(downtime_minutes_per_year(0.999)) == 526        # ≈ 8.8 hours
assert series(*[0.999] * 10) < 0.991                          # 10 dependencies at three nines each
```

**Takeaways for design:** fewer hard dependencies on the request path; make optional ones optional (degrade instead of fail); replicate across independent failure domains (availability zones).

### 18.2 Timeouts

Without a timeout, one slow dependency takes everything down:

```text
Payment API normally answers in 100 ms; today it hangs forever.
Your service has 200 worker threads. Each request to it waits… and waits.
After a few seconds all 200 threads are stuck waiting → your service stops answering EVERYTHING,
including requests that never touch payments. Your callers' threads pile up next → cascading failure.
```

- **Every network call needs a timeout** (connect timeout + read timeout).
- **Size it from the dependency's latency:** a little above its p99 (p99 = 300 ms → timeout ~500 ms–1 s), not 30 s.
- **Deadline propagation:** the edge gives the whole request a budget (e.g., 2 s). Each hop passes on the *remaining* budget; a service doesn't start work it can't finish in time.

```text
Gateway: deadline = now + 2000 ms
  → Order service (used 300 ms) calls Inventory with 1700 ms remaining
     → Inventory calls DB with 1650 ms remaining … if the remaining budget is < expected cost, fail fast
```

### 18.3 Retries

Many failures are transient (a pod restarting, a dropped connection, a 503 during a deploy). A retry often succeeds. But naive retries make outages worse.

**Rules:**
1. Retry only **transient** errors: timeouts, connection errors, 429, 502/503/504. Never retry 400/401/403/404/422 — they'll fail again.
2. Retry only **idempotent** operations — or make them idempotent with an idempotency key ([5.6](#56-idempotency)).
3. **Exponential backoff:** wait 100 ms, 200 ms, 400 ms, 800 ms… (gives the dependency time to recover).
4. **Jitter:** randomize the wait. Without jitter, 10,000 clients that failed at the same moment all retry at exactly 100 ms, then 200 ms — synchronized waves that knock the service down again.
5. **Cap attempts** (e.g., 3) and total time (stay within the deadline).
6. **Retry at one layer only.** If the app, the service mesh, and the client SDK each retry 3×, one user request becomes 3 × 3 × 3 = 27 calls to a struggling service.
7. **Retry budget:** retries limited to e.g. 10% of normal traffic, so during a real outage retries can't multiply load.

```python
import random

def backoff_delays(attempts: int, base=0.1, cap=5.0, rng=random.Random(0)) -> list[float]:
    """'Full jitter': sleep a random time between 0 and the exponential ceiling."""
    return [rng.uniform(0, min(cap, base * 2 ** i)) for i in range(attempts)]

RETRYABLE = {429, 502, 503, 504}

def call_with_retries(call, max_attempts=4, sleep=lambda s: None):
    for attempt, delay in enumerate(backoff_delays(max_attempts - 1) + [None], start=1):
        status = call()
        if status < 400:
            return status, attempt
        if status not in RETRYABLE or delay is None:
            return status, attempt                    # permanent error, or out of attempts
        sleep(delay)

responses = iter([503, 503, 200])
assert call_with_retries(lambda: next(responses)) == (200, 3)        # transient → succeeded on 3rd try
assert call_with_retries(lambda: 404) == (404, 1)                     # not retryable → stop immediately
always_down = call_with_retries(lambda: 503)
assert always_down == (503, 4)                                        # gave up after max attempts

delays = backoff_delays(5)
assert all(0 <= d <= min(5.0, 0.1 * 2 ** i) for i, d in enumerate(delays))
```

### 18.4 Circuit breaker

Retries help with blips. During a **real outage**, you want the opposite: stop calling the broken dependency, fail fast, give it room to recover.

```text
        failures exceed threshold
CLOSED ───────────────────────────► OPEN ──(cool-down, e.g. 30 s)──► HALF-OPEN
(calls pass,                    (calls fail immediately,           (let a few trial calls through)
 failures counted)               use a fallback)                      │ success → CLOSED
   ▲                                                                   │ failure → OPEN again
   └───────────────────────────────────────────────────────────────────┘
```

Why it helps:
- Your threads aren't stuck waiting on timeouts → your service stays responsive.
- The failing dependency isn't hammered while it's trying to recover.
- You can serve a **fallback**: cached data, a default ("recommendations unavailable"), or a queued "we'll process this shortly".

```python
class CircuitOpen(Exception): ...

class CircuitBreaker:
    def __init__(self, failure_threshold=3, cooldown=30.0, clock=None):
        self.threshold, self.cooldown, self.clock = failure_threshold, cooldown, clock
        self.state, self.failures, self.opened_at = "CLOSED", 0, 0.0

    def call(self, fn):
        if self.state == "OPEN":
            if self.clock() - self.opened_at < self.cooldown:
                raise CircuitOpen("failing fast")            # don't even try
            self.state = "HALF_OPEN"                         # cool-down over: allow a trial call
        try:
            result = fn()
        except Exception:
            self.failures += 1
            if self.state == "HALF_OPEN" or self.failures >= self.threshold:
                self.state, self.opened_at = "OPEN", self.clock()
            raise
        self.state, self.failures = "CLOSED", 0
        return result

now = [0.0]
cb = CircuitBreaker(failure_threshold=3, cooldown=30, clock=lambda: now[0])
calls = []
def broken():
    calls.append(now[0])
    raise TimeoutError("payment service timeout")

for _ in range(3):
    try:
        cb.call(broken)
    except TimeoutError:
        pass
assert cb.state == "OPEN" and len(calls) == 3
for _ in range(100):                                        # 100 more requests during the outage
    try:
        cb.call(broken)
    except CircuitOpen:
        pass
assert len(calls) == 3                                      # none reached the broken service
now[0] = 31
assert cb.call(lambda: "ok") == "ok" and cb.state == "CLOSED"   # trial call succeeded → closed again
```

### 18.5 Bulkheads, load shedding, graceful degradation

**Bulkheads** (named after ship compartments that stop one leak from sinking the ship): give each dependency its **own** limited pool of threads/connections.

```text
Without bulkheads: one shared pool of 200 threads
  recommendations service hangs → all 200 threads stuck on it → checkout also dead

With bulkheads:
  checkout calls      → pool of 120
  search calls        → pool of 50
  recommendations     → pool of 30   ← hangs: only these 30 are stuck; checkout unaffected
```
In code this is often just a semaphore per dependency: try to acquire; if none free, fail fast or use the fallback.

**Load shedding:** when overloaded, reject some requests **early and cheaply** (429/503) so the ones you accept stay fast. Serving everyone slowly usually means timing out for everyone.
- Prioritize: checkout and login before recommendations and analytics.
- Shed based on queue length, in-flight requests, or CPU, before latency explodes.

**Graceful degradation:** lose features, not the whole page.

```text
Product page dependencies:   product info (required) · price (required) · reviews · recommendations · "people also bought"
Reviews service down        → show the page without reviews ("reviews temporarily unavailable")
Recommendations down        → show popular items from a static cache
Price service down          → can't sell safely → show "temporarily unavailable" for the buy button
```

**Queue-based load leveling:** put a queue in front of a slow backend so bursts are absorbed and processed at a steady rate (with backpressure, [13.12](#1312-backpressure)).

```python
import threading

class Bulkhead:
    def __init__(self, max_concurrent: int):
        self.slots = threading.BoundedSemaphore(max_concurrent)

    def call(self, fn, fallback):
        if not self.slots.acquire(blocking=False):         # pool exhausted → don't queue, degrade
            return fallback()
        try:
            return fn()
        finally:
            self.slots.release()

recommendations = Bulkhead(max_concurrent=2)
release = threading.Event()
results = []

def slow_recs():
    release.wait(1)                                         # recommendation service is hanging
    return ["item1", "item2"]

threads = [threading.Thread(target=lambda: results.append(
    recommendations.call(slow_recs, fallback=lambda: ["popular-item"]))) for _ in range(5)]
for t in threads: t.start()
release.set()
for t in threads: t.join()
assert results.count(["popular-item"]) >= 3                 # extra calls degraded instantly instead of piling up
```

### 18.6 Rate limiting

**Why:** protect your service from abuse and overload, enforce fair use between customers, enforce pricing tiers ("free plan: 100 requests/min"), and protect paid downstream APIs.

**Where:** at the API gateway (per API key/user/IP), inside services (protect themselves), and in client SDKs (be polite to others).

**Algorithms, with what they actually do:**

**Fixed window counter** — count requests per calendar minute.
```text
limit 100/min:  12:00:00–12:00:59 → counter; reset at 12:01:00
problem: 100 requests at 12:00:59 + 100 at 12:01:00 = 200 requests in 2 seconds
```

**Sliding window log** — store a timestamp per request; allow if fewer than 100 timestamps in the last 60 s. Exact, but stores every request (memory heavy at scale).

**Sliding window counter** — approximate the sliding window with two fixed windows:
```text
now = 12:01:15 (25% into the current minute)
estimate = current_minute_count + previous_minute_count × (1 − 0.25)
         = 30 + 80 × 0.75 = 90 → allowed (< 100)
```
Two counters per client, smooths the boundary burst. A good default.

**Token bucket** — a bucket holds up to C tokens and refills at R tokens/second; each request takes one token.
```text
C = 10, R = 1/s:  an idle client can burst 10 requests instantly, then sustain 1 per second
```
Allows short bursts (good for real users) while capping the average. Used by AWS, Stripe, most API gateways.

**Leaky bucket** — requests enter a queue that drains at a fixed rate → perfectly smooth output; bursts wait or are dropped. Good for shaping traffic to a fragile downstream.

```python
class TokenBucket:
    def __init__(self, capacity: int, refill_per_sec: float):
        self.capacity, self.rate = capacity, refill_per_sec
        self.tokens, self.last = float(capacity), None

    def allow(self, now: float) -> bool:
        if self.last is not None:
            self.tokens = min(self.capacity, self.tokens + (now - self.last) * self.rate)
        self.last = now
        if self.tokens >= 1:
            self.tokens -= 1
            return True
        return False

class SlidingWindowCounter:
    def __init__(self, limit: int, window: float):
        self.limit, self.window = limit, window
        self.counts: dict[int, int] = {}

    def allow(self, now: float) -> bool:
        current = int(now // self.window)
        elapsed_fraction = (now % self.window) / self.window
        estimate = self.counts.get(current, 0) + self.counts.get(current - 1, 0) * (1 - elapsed_fraction)
        if estimate < self.limit:
            self.counts[current] = self.counts.get(current, 0) + 1
            return True
        return False

tb = TokenBucket(capacity=10, refill_per_sec=1)
assert sum(tb.allow(0.0) for _ in range(15)) == 10            # burst of 10, then denied
assert tb.allow(1.0) and not tb.allow(1.0)                     # 1 token refilled per second

fixed_burst = 100 + 100                                        # fixed window would allow both edges
swc = SlidingWindowCounter(limit=100, window=60)
allowed_edge = sum(swc.allow(59.9) for _ in range(100)) + sum(swc.allow(60.1) for _ in range(100))
assert allowed_edge < fixed_burst and allowed_edge <= 101      # boundary burst blocked
```

**Distributed rate limiting:** with 50 gateway instances, the count must be shared → keep counters in Redis and update them **atomically** with a Lua script (otherwise two gateways both read "99" and both allow). Full design in [22.2](#222-rate-limiter-distributed).

**Responding:** `429 Too Many Requests` with `Retry-After: 30` and headers like `X-RateLimit-Limit: 100`, `X-RateLimit-Remaining: 0`, `X-RateLimit-Reset: 1735689600` so clients can back off properly.

### 18.7 Redundancy, failover, disaster recovery

**Find single points of failure:** for every box in your diagram ask "what happens if this dies right now?"

| Component | Make it redundant by |
|---|---|
| Stateless app servers | ≥ 2 instances per availability zone behind a load balancer |
| Load balancer | Managed multi-AZ LB, or active-passive pair with a floating IP |
| Database | Primary + synchronous standby in another AZ, automatic failover; replicas for reads |
| Cache | Replicated Redis (primary + replica, Sentinel/Cluster) — and the app survives a cold cache |
| Queue | Replicated brokers (Kafka RF=3) |
| Object storage | Already replicated by the provider |
| Region | Multi-region only if the business needs it (see below) |

**Availability zones (AZs):** separate data centers in one region with independent power/network, ~1–2 ms apart. Spreading across 2–3 AZs is the standard baseline — synchronous replication between them is affordable.

**RPO and RTO — the two numbers that define disaster recovery:**
- **RPO (recovery point objective):** how much data you can afford to lose. "RPO = 5 min" → backups/replication must be at most 5 minutes behind.
- **RTO (recovery time objective):** how long you can be down. "RTO = 1 h" → you must be able to restore and switch over within an hour.

```text
Strategy                 RPO              RTO          Cost
Backups to another region hours            hours        $
Pilot light (DB replicated, servers off)    seconds–minutes  ~30 min   $$
Warm standby (scaled-down copy running)     seconds          minutes   $$$
Active-active (both regions serve)          ~0 (or conflicts) ~0       $$$$
```

**Backups:** regular snapshots + continuous WAL archiving (point-in-time recovery). A backup you've never **restored** is a hope, not a backup — test restores regularly.

**Multi-region:**
- **Active-passive:** one region serves traffic; the other is a standby. Simpler; failover takes minutes.
- **Active-active:** both serve users (lower latency worldwide, survives a region loss) but you must handle concurrent writes in two regions — partition data by region (each user has a home region) or use conflict resolution/globally consistent databases.
- Route users with GeoDNS / anycast, and health-check regions.

### 18.8 Idempotency everywhere

Retries happen at every level, often without you asking:

```text
User double-taps "Pay" · mobile client retries on timeout · load balancer retries a failed upstream ·
service mesh retries · queue redelivers a message after a consumer crash · a cron job re-runs after a deploy
```
So **every write path must be safe to repeat**:
- Client-supplied **idempotency keys** for APIs that create/charge things ([5.6](#56-idempotency)).
- **Unique constraints** (one booking per seat per show, one row per event ID).
- **Upserts** instead of blind inserts.
- **Conditional updates / version checks** (`UPDATE … WHERE status = 'PENDING'`).
- **Dedup tables** for consumers (processed message IDs).
- Natural idempotency: "set status = SHIPPED" is safe to repeat; "add 1 to count" is not.

## 19. Security

### Authentication: who are you?

**Passwords** must never be stored as plain text or a plain hash. If the database leaks, plain SHA-256 hashes of common passwords are cracked in seconds using precomputed tables and GPUs.

Store a **slow, salted hash** (bcrypt, scrypt, Argon2, or PBKDF2 with many iterations):
- **Salt:** a random value per user, stored with the hash → identical passwords produce different hashes; precomputed tables are useless.
- **Slow:** ~100 ms per guess → fine for one login, ruinous for billions of guesses.

```python
import hashlib
import hmac
import os

def hash_password(password: str, salt: bytes | None = None) -> tuple[bytes, bytes]:
    salt = salt or os.urandom(16)
    digest = hashlib.scrypt(password.encode(), salt=salt, n=2**14, r=8, p=1)   # deliberately slow
    return salt, digest

def check_password(password: str, salt: bytes, digest: bytes) -> bool:
    return hmac.compare_digest(hashlib.scrypt(password.encode(), salt=salt, n=2**14, r=8, p=1), digest)

salt1, d1 = hash_password("hunter2")
salt2, d2 = hash_password("hunter2")
assert d1 != d2                                   # same password, different salt → different hash
assert check_password("hunter2", salt1, d1) and not check_password("hunter3", salt1, d1)
```
Plus: **MFA** (TOTP app, passkeys/WebAuthn), rate limits and lockouts on login, checks against breached-password lists.

**Sessions vs tokens — how a logged-in user is recognized on each request:**

```text
Server-side session:
  login → server creates session row {id: "r4nd0m…", user_id: 42} in Redis
        → Set-Cookie: sid=r4nd0m…; HttpOnly; Secure; SameSite=Lax
  each request → look up sid in Redis → user 42
  logout / ban → delete the row → instantly invalid

JWT (stateless token):
  login → server signs a token containing {"sub": 42, "exp": now+15min, "roles": ["user"]}
  each request → verify the signature + expiry locally → no lookup needed
  but: can't be revoked before it expires (it's valid wherever the signature checks out)
```

A **JWT** is three base64url parts: `header.payload.signature`. The signature proves the server issued it and nobody changed the payload:

```python
import base64
import hashlib
import hmac
import json

def b64url(data: bytes) -> str:
    return base64.urlsafe_b64encode(data).rstrip(b"=").decode()

def b64url_decode(s: str) -> bytes:
    return base64.urlsafe_b64decode(s + "=" * (-len(s) % 4))

KEY = b"server-secret"            # HS256 for illustration; production often uses RS256/ES256 key pairs

def issue(claims: dict) -> str:
    header = b64url(json.dumps({"alg": "HS256", "typ": "JWT"}).encode())
    payload = b64url(json.dumps(claims).encode())
    sig = b64url(hmac.new(KEY, f"{header}.{payload}".encode(), hashlib.sha256).digest())
    return f"{header}.{payload}.{sig}"

def verify(token: str, now: int) -> dict:
    header, payload, sig = token.split(".")
    if json.loads(b64url_decode(header))["alg"] != "HS256":
        raise ValueError("unexpected algorithm")            # never trust the token's own 'alg' blindly
    expected = b64url(hmac.new(KEY, f"{header}.{payload}".encode(), hashlib.sha256).digest())
    if not hmac.compare_digest(sig, expected):
        raise ValueError("bad signature")
    claims = json.loads(b64url_decode(payload))
    if now >= claims["exp"]:
        raise ValueError("expired")
    return claims

token = issue({"sub": 42, "role": "user", "exp": 1_000})
assert verify(token, now=500)["sub"] == 42
h, p, s = token.split(".")
forged_payload = b64url(json.dumps({"sub": 42, "role": "admin", "exp": 1_000}).encode())
for bad, now in [(f"{h}.{forged_payload}.{s}", 500), (token, 1_000)]:
    try:
        verify(bad, now); assert False
    except ValueError:
        pass                                                # tampered role and expired token both rejected
```
- The payload is only **encoded, not encrypted** — anyone can read it. Never put secrets in a JWT.
- With **RS256/ES256**, only the auth server holds the private key; every service verifies with the public key from a **JWKS** endpoint; keys rotate via the `kid` header.
- **Revocation:** short-lived access tokens (5–15 min) + long-lived **refresh tokens** (stored server-side, rotated on use, revocable). Emergency denylist by token ID (`jti`) in Redis.
- Store tokens in **HttpOnly, Secure, SameSite** cookies in browsers (JavaScript can't read them, so XSS can't steal them); avoid localStorage for long-lived tokens.

### OAuth 2.0 and OpenID Connect

**OAuth 2.0** lets an app act on a user's behalf **without getting their password**. "Allow PhotoPrint to read your Google Photos?" → PhotoPrint gets a scoped token, never your Google password. **OpenID Connect (OIDC)** adds login on top: an **ID token** that says who the user is ("Sign in with Google").

**Authorization Code flow with PKCE** (the right flow for web and mobile apps):

```text
1. App generates a random code_verifier, sends its SHA-256 (code_challenge) when redirecting:
   → accounts.google.com/authorize?client_id=…&scope=photos.read&code_challenge=…&redirect_uri=…
2. User logs in at Google (the app never sees the password) and approves the scopes
3. Google redirects back: redirect_uri?code=ONE_TIME_CODE
4. App's backend exchanges code + code_verifier for tokens:  POST /token
   Google checks SHA-256(code_verifier) == code_challenge  → issues access token (+ refresh, + ID token)
5. App calls the Photos API with "Authorization: Bearer <access token>"
```
**PKCE** stops an attacker who intercepts the one-time code (e.g., a malicious app registered for the same redirect) from using it: they don't have the `code_verifier`.

```python
import base64
import hashlib
import secrets

code_verifier = secrets.token_urlsafe(64)                    # kept secret by the app
code_challenge = base64.urlsafe_b64encode(hashlib.sha256(code_verifier.encode()).digest()).rstrip(b"=").decode()

def token_endpoint(received_verifier: str, stored_challenge: str) -> bool:
    computed = base64.urlsafe_b64encode(hashlib.sha256(received_verifier.encode()).digest()).rstrip(b"=").decode()
    return secrets.compare_digest(computed, stored_challenge)

assert token_endpoint(code_verifier, code_challenge)                   # legitimate app
assert not token_endpoint(secrets.token_urlsafe(64), code_challenge)   # attacker with a stolen code
```
**Client Credentials flow:** service-to-service, no user involved — a service authenticates with its own credentials to get a token.

### Authorization: what are you allowed to do?

- **RBAC (role-based):** permissions attached to roles; users get roles. `admin → {orders:refund, users:ban}`; `support → {orders:read}`.
- **ABAC (attribute-based):** rules over attributes: "a doctor can read a patient record if they're in the same hospital and on the care team".
- **Ownership checks (the most commonly missed):** RBAC says "customers can read orders"; you still must check **this** order belongs to **this** customer.

```text
GET /orders/43 as user 42
  authenticated? yes      has role customer? yes
  order 43 belongs to user 42? NO → 403 (or 404 to avoid revealing it exists)
Skipping this check = "IDOR" (insecure direct object reference): change the ID in the URL, read anyone's order.
```
Enforce authorization **on the server, on every request**, close to the data — never because "the UI hides the button".

### Protecting data

- **In transit:** TLS everywhere, including inside the data center; **mTLS** between services ([4.4](#44-tls-https)).
- **At rest:** disk/database encryption with keys in a KMS. **Envelope encryption:** each file/row encrypted with its own data key; data keys encrypted by a master key that never leaves the KMS → rotating the master key doesn't require re-encrypting all data.
- **Secrets** (DB passwords, API keys) in a secret manager (Vault, AWS Secrets Manager), injected at runtime, rotated — never in code, images, or logs.
- **PII:** collect the minimum, mask it in logs, define retention, support deletion requests (GDPR "right to be forgotten").
- **Multi-tenancy:** every query filtered by `tenant_id` (enforce in a data-access layer or with row-level security), so one bug can't leak another customer's data.
- **Audit logs:** append-only "who did what, when" for sensitive actions (refunds, role changes, data exports).

### Common attacks and their fixes

**SQL injection** — user input becomes part of the SQL text:

```python
import sqlite3

db = sqlite3.connect(":memory:")
db.execute("CREATE TABLE users (id INTEGER, email TEXT, is_admin INTEGER)")
db.executemany("INSERT INTO users VALUES (?, ?, ?)", [(1, "a@x.com", 0), (2, "boss@x.com", 1)])
user_input = "nobody@x.com' OR '1'='1"

unsafe_sql = f"SELECT email FROM users WHERE email = '{user_input}'"          # string building
assert len(db.execute(unsafe_sql).fetchall()) == 2                           # leaked every row

safe = db.execute("SELECT email FROM users WHERE email = ?", (user_input,))   # parameterized
assert safe.fetchall() == []                                                 # input treated as data
```

| Attack | What happens | Fix |
|---|---|---|
| **SQL injection** | Input changes the query (above) | Parameterized queries / ORM; least-privilege DB users |
| **XSS** (cross-site scripting) | Attacker's `<script>` stored in a comment runs in other users' browsers, steals data/tokens | Escape output by context; Content-Security-Policy; HttpOnly cookies |
| **CSRF** (cross-site request forgery) | A malicious site makes your logged-in browser send `POST /transfer` with your cookies | SameSite cookies; CSRF tokens; require custom headers for APIs |
| **SSRF** | Your server fetches a user-supplied URL like `http://169.254.169.254/` (cloud metadata) and leaks credentials | Allowlist outbound hosts; block internal IP ranges; separate egress proxy |
| **IDOR / broken access control** | Changing `/orders/43` to `/orders/44` shows someone else's order | Ownership checks on every object access |
| **Credential stuffing** | Leaked passwords from other sites tried on yours | Rate limiting, MFA, breached-password checks, bot detection |
| **Mass assignment** | Client sends `{"is_admin": true}` in a profile update and it's saved | Allowlist updatable fields |
| **DDoS** | Traffic floods exhaust bandwidth/servers | CDN/WAF at the edge, anycast, rate limits, autoscaling, cache aggressively |

## 20. Observability

Monitoring tells you **that** something is wrong; observability lets you find out **why** — from the outside, without deploying new code. Three main signals:

### Metrics — numbers over time

Cheap to store, great for dashboards and alerts.

```text
http_requests_total{service="orders", route="/orders", status="500"}  1,204   (counter)
http_request_duration_seconds{service="orders", quantile="0.99"}       0.840   (histogram → percentiles)
db_pool_in_use{service="orders"}                                       18      (gauge)
```
- **RED method** for every service: **R**ate (requests/s), **E**rrors (failed requests/s or %), **D**uration (latency percentiles).
- **USE method** for every resource (CPU, disk, pool, queue): **U**tilization, **S**aturation (queueing), **E**rrors.
- Avoid high-cardinality labels (user_id, request_id) — each unique combination is a new time series.

**Percentiles, not averages.** An average hides the users having a terrible time:

```python
import statistics

latencies_ms = [40] * 980 + [3000] * 20                  # 2% of requests hit a slow path

def percentile(values, p):
    ordered = sorted(values)
    return ordered[min(len(ordered) - 1, int(round(p / 100 * len(ordered))) - 1)]

avg = statistics.mean(latencies_ms)
p50, p99 = percentile(latencies_ms, 50), percentile(latencies_ms, 99)
assert round(avg) == 99 and p50 == 40 and p99 == 3000     # average looks fine; p99 users wait 3 s

# A page that makes 20 backend calls hits the slow path at least once for ~1 - 0.98**20 ≈ 33% of page loads
assert round(1 - 0.98 ** 20, 2) == 0.33
```

### Logs — what happened, in detail

```json
{"ts": "2025-05-01T12:30:00.123Z", "level": "ERROR", "service": "orders", "trace_id": "4bf92f35…",
 "request_id": "req_8f2c", "user_id": 42, "route": "POST /orders", "error": "PaymentTimeout", "duration_ms": 2003}
```
- **Structured JSON**, not free text → searchable and aggregatable.
- Always include `trace_id`/`request_id` → jump from a log line to the full trace.
- Levels; sampling for chatty debug logs; never log secrets or full PII.
- Centralized: shipped by agents to Elasticsearch/OpenSearch, Loki, ClickHouse, or a vendor.

### Traces — one request's journey across services

```text
trace 4bf92f35  POST /orders                                     total 2,140 ms
├─ gateway             auth + routing                              12 ms
├─ order-service       create order                                40 ms
│  ├─ postgres         INSERT orders                                6 ms
│  └─ inventory-svc    reserve items                               35 ms
└─ payment-service     charge                                   2,050 ms   ◄── the slow hop
   └─ psp-api          POST /charges                            2,031 ms   (timeout + retry)
```
- Each service creates **spans** and passes the trace context (`traceparent` header) on outgoing calls.
- OpenTelemetry SDKs → Jaeger, Tempo, Zipkin, or a vendor.
- Usually **sampled** (e.g., keep 1% of normal traces, 100% of errors/slow ones).

### SLIs, SLOs, SLAs and error budgets

- **SLI (indicator):** what you measure. "Fraction of `POST /orders` requests that succeed in under 500 ms."
- **SLO (objective):** the target. "99.9% of them over 30 days."
- **SLA (agreement):** a contract with customers, with penalties — set it looser than your SLO.
- **Error budget = 1 − SLO.** 99.9% over 30 days → 0.1% of requests may fail ≈ 43 minutes of full outage. Spend it on releases and experiments; when it's exhausted, slow down and fix reliability.

```python
slo = 0.999
monthly_requests = 50_000_000
budget = int(round(monthly_requests * (1 - slo)))         # failures allowed this month
failed_so_far = 30_000
assert budget == 50_000 and failed_so_far / budget == 0.6   # 60% of the budget used

days_elapsed = 10
burn_rate = (failed_so_far / budget) / (days_elapsed / 30)  # >1 means you'll run out before month end
assert round(burn_rate, 1) == 1.8                           # alert: burning budget ~2× too fast
```

### Alerting that people don't ignore

- Alert on **symptoms users feel** (SLO burn rate, error rate, latency), not every CPU spike.
- **Burn-rate alerts:** page when the budget is burning fast (e.g., 14× over 1 h); ticket when slow (e.g., 2× over 3 days).
- Every page needs an owner and a **runbook** (what to check, how to mitigate).
- **Synthetic checks:** a robot performs "log in → search → add to cart" every minute from several regions.

## 21. Architecture styles and deployment

### 21.1 Monolith vs microservices

**Monolith:** one codebase, one deployable, one database.

```text
[ web layer | orders | payments | catalog | users ]  →  one process  →  one Postgres
```

**Microservices:** many small services, each owning its data, talking over the network.

```text
gateway → order-svc (orders DB) → payment-svc (payments DB) → catalog-svc (catalog DB) …
          (calls via HTTP/gRPC, events via Kafka)
```

| | Monolith | Microservices |
|---|---|---|
| Deploy | Everything together | Each service independently |
| Scale | Whole app | Hot services only (scale search 10×, leave admin at 2) |
| Data | One DB, easy joins and transactions | DB per service → no cross-service joins, sagas, eventual consistency |
| Failure | A bug can crash everything | Failures isolated — if you build timeouts/circuit breakers |
| Calls | In-process function calls (ns) | Network calls (ms) that can fail |
| Teams | Fine up to a few teams | Many teams own and ship their services independently |
| Operational cost | Low | High: service discovery, tracing, CI/CD per service, versioning |

**Practical advice:**
- Start with a **modular monolith** — clear internal modules with boundaries, one deployable.
- Split a service out when there's a real reason: a team boundary, a very different scaling profile, a reliability/security isolation need.
- Each service **owns its data**; others use its API or events — **no shared tables**, otherwise you have a distributed monolith (all the costs, none of the benefits).
- Migrating from a monolith: the **strangler fig** pattern — route one feature at a time (`/payments/*`) to a new service through the gateway until the old code can be deleted.

### 21.2 Other styles

- **Event-driven:** services react to events on a log (Kafka) instead of calling each other — loose coupling, natural audit trail, harder to trace end to end ([14.7](#147-event-driven-patterns)).
- **Serverless (FaaS — Lambda, Cloud Functions):** you deploy functions that run per event; pay per invocation; scales to zero and up automatically. Costs: cold starts (100 ms–seconds), execution time limits, harder local testing, lock-in. Great for spiky glue work (image resize on upload, webhooks, cron).
- **Cell-based architecture:** run several complete, independent copies of the whole stack ("cells"), each serving a slice of customers. A bad deploy or overload in one cell affects only its customers → limits blast radius. Used at very large scale (AWS, Slack).

### 21.3 Containers and Kubernetes (enough for HLD)

**Container:** your app + its dependencies packaged as an image, run as an isolated process (shares the host kernel, unlike a VM). Same image runs on a laptop, CI, and production.

**Kubernetes** runs containers across a cluster of machines and keeps the actual state equal to the desired state you declare:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata: {name: orders}
spec:
  replicas: 4                       # "I want 4 copies running" — K8s replaces any that die
  selector: {matchLabels: {app: orders}}
  template:
    metadata: {labels: {app: orders}}
    spec:
      containers:
        - name: orders
          image: registry.example.com/orders:1.42.0
          resources: {requests: {cpu: "500m", memory: "512Mi"}, limits: {memory: "1Gi"}}
          readinessProbe: {httpGet: {path: /ready, port: 8080}}   # gets traffic only when ready
          livenessProbe: {httpGet: {path: /health, port: 8080}}   # restarted if this fails
---
apiVersion: v1
kind: Service                       # stable name + virtual IP load-balancing over the pods
metadata: {name: orders}
spec: {selector: {app: orders}, ports: [{port: 80, targetPort: 8080}]}
```

| Object | Purpose |
|---|---|
| **Pod** | One or more containers scheduled together (the unit K8s runs) |
| **Deployment** | Desired replica count + rolling updates for stateless apps |
| **Service** | Stable DNS name and load balancing to healthy pods |
| **Ingress / Gateway** | HTTP routing from outside the cluster |
| **HPA** | Horizontal Pod Autoscaler — scales replicas on CPU/custom metrics |
| **ConfigMap / Secret** | Configuration and credentials injected into pods |
| **StatefulSet** | Stable identities and disks for stateful workloads (databases, Kafka) |

### 21.4 Deployment strategies

Most outages are caused by changes. Deployment strategy is about limiting blast radius and rolling back fast.

```text
Rolling:     v1 v1 v1 v1 → v2 v1 v1 v1 → v2 v2 v1 v1 → v2 v2 v2 v2
             replace a few at a time; default in K8s; both versions run during rollout

Blue-green:  blue (v1) serves 100% │ green (v2) deployed and tested │ switch router → green 100%
             instant rollback = switch back; needs 2× capacity during the switch

Canary:      v2 gets 1% → watch error rate/latency vs v1 → 5% → 25% → 100%
             automatic rollback if metrics degrade; catches problems with real traffic, small blast radius
```

**Feature flags** separate *deploying* code from *releasing* it: the code ships dark and is turned on for 1% of users, a beta group, or one country — and turned off instantly without a deploy (kill switch). Percentage rollouts hash the user ID so each user consistently sees the same variant:

```python
import hashlib

def flag_enabled(flag: str, user_id: str, rollout_percent: int) -> bool:
    bucket = int(hashlib.sha256(f"{flag}:{user_id}".encode()).hexdigest(), 16) % 100
    return bucket < rollout_percent

users = [f"user-{i}" for i in range(10_000)]
on_5 = {u for u in users if flag_enabled("new_checkout", u, 5)}
on_20 = {u for u in users if flag_enabled("new_checkout", u, 20)}
assert 400 < len(on_5) < 600                    # ~5% of users
assert on_5 <= on_20                            # increasing the rollout keeps earlier users enabled
assert flag_enabled("new_checkout", "user-7", 20) == flag_enabled("new_checkout", "user-7", 20)  # stable
```

**Database migrations without downtime — expand, migrate, contract.** During a rolling deploy, old and new code run at the same time, so every schema change must work with both. Example: rename `users.name` → `full_name`:

```text
1. EXPAND    add column full_name (nullable)                 ← old code ignores it
2. DEPLOY    new code writes BOTH name and full_name, reads name
3. BACKFILL  copy name → full_name for existing rows, in batches
4. DEPLOY    new code reads full_name (still writes both)
5. DEPLOY    stop writing name
6. CONTRACT  drop column name                                ← nothing uses it any more
```
Never do "rename column" in one step while the old code is still running — every old instance breaks instantly.

---

# Part C — Problems

Every problem follows the framework: **requirements → estimates → API → data model → architecture → flows → deep dives → failures → follow-up Q&A.** Practice saying each one out loud in 35–40 minutes.

## 22. Tier 1 problems

### 22.1 URL shortener (TinyURL / bit.ly)

**Requirements**
- Functional: create a short URL for a long URL; redirect short → long; optional custom alias; optional expiry.
- Out of scope: accounts/billing, detailed analytics dashboard (mention as an extension).
- Non-functional: redirect p99 < 50 ms; 99.99% available for redirects; short codes unique; codes shouldn't be trivially enumerable.

**Estimates**
- Writes: 100M/day ≈ 1.2K/s (peak 3K/s). Reads 100:1 → 12K/s avg, ~100K/s peak.
- Storage: 180B URLs over 5 years × 500 B ≈ 90 TB.
- Code length: base62^7 ≈ 3.5 trillion → 7 chars.
- Cache: 20% of daily-read URLs × 500 B — a few tens of GB → fits a Redis cluster.

**API**
```
POST /v1/urls
  body: { long_url, custom_alias?, expires_at? }
  201:  { code, short_url, expires_at }
  409:  alias taken
GET /{code}
  302 Location: <long_url>      (404 if unknown, 410 if expired)
```
- **301 vs 302:** 301 is cached by browsers → fewer hits on your servers, but you can't count clicks or change the target. 302 → every click comes to you. Use 302 if analytics or editing matter.

**Data model**
```
urls: code (PK, varchar 7–16) | long_url | owner_id | created_at | expires_at
```
- Pure key-value access by `code` → DynamoDB/Cassandra (easy horizontal scale) or sharded Postgres (shard by `code`).
- Optional index `long_url_hash → code` if the same long URL must return the same code.

**Architecture**
```
              ┌──────────────► Write service ──► ID range allocator (ZooKeeper/DB)
Client ─► LB ─┤                     │
              │                     └──► URL store (sharded by code)
              └──────────────► Redirect service ──► Redis (code → long_url)
                                     │                 │ miss
                                     │                 └──► URL store
                                     └──► Kafka "clicks" ──► analytics (optional)
```

**Write flow:** validate URL (scheme, length, blocklist) → get a unique ID → base62-encode → store row → return.

**Read flow:** Redis lookup → hit: 302 → miss: DB lookup → fill cache → 302. Unknown codes are negative-cached for a minute.

**Deep dive 1: generating unique codes**

| Approach | How | Pros | Cons |
|---|---|---|---|
| Hash + truncate | `base62(sha256(long_url))[:7]` | Same URL → same code; stateless | Collisions → check DB, append salt, retry |
| Global counter + base62 | DB sequence → encode | No collisions | Single bottleneck; sequential (guessable) |
| **Range allocation** | Each write server leases a block (e.g., 1M IDs) from ZooKeeper/a DB row; hands them out locally | No per-request coordination, no collisions | IDs in an unused block are lost on crash (fine) |
| Pre-generated key service | Offline job fills a table with random unused codes; servers fetch batches | Random codes, no collisions | Extra service and storage |

- **Choose:** range allocation + base62. To make codes non-sequential, apply a reversible bit permutation (e.g., multiply by a large odd number mod 2^42, or a Feistel cipher) before encoding — still unique, not guessable.
- Custom alias: insert with a unique constraint on `code`; 409 on conflict. Reserve words (`api`, `admin`).

**Deep dive 2: the redirect path at 100K QPS**
- Stateless redirect servers behind an L7 LB, scaled horizontally.
- Redis cluster with LRU eviction; hit ratio > 90% because traffic is skewed (Zipf).
- In-process LRU on each redirect server for the hottest few thousand codes (saves a network hop).
- DB is rarely touched.

**Deep dive 3: expiry and cleanup**
- Check `expires_at` on read → 410.
- Background job deletes expired rows in batches; or DB-native TTL (DynamoDB/Cassandra TTL).

**Failures**
- Redis down → fall back to DB with a rate limit (protect DB), circuit breaker.
- Allocator down → servers still have leased ranges for a while; lease the next range early (at 80% used).

**Follow-ups**
- *Analytics?* Redirect service emits click events (code, time, referrer, country from IP, user agent) to Kafka asynchronously → stream aggregation per code per minute → OLAP store (ClickHouse). Never block the redirect on analytics.
- *Abuse (phishing links)?* Check against a safe-browsing list on create and periodically; rate-limit creation per IP/API key; allow takedown.
- *Same URL by different users?* Usually different codes (per-user analytics). Global dedup only if required.
- *Multi-region?* Replicate the URL store globally (reads local); writes go to a home region or use region-prefixed ID ranges so no coordination is needed.

---

### 22.2 Rate limiter (distributed)

**Requirements**
- Limit requests per key (user ID, API key, IP) per rule, e.g., 100/min per user, 10/s per IP on login.
- Rules configurable per endpoint and plan; multiple rules per request.
- Return 429 with headers. Add < 2 ms latency. Works across hundreds of API servers.
- Decide failure mode: **fail open** (allow on limiter failure — protects availability) for normal APIs; **fail closed** for security-critical ones (login, OTP).

**Where it runs:** API gateway / shared middleware. Also local limits inside critical services to protect themselves.

**Algorithm choice:** token bucket (allows short bursts, smooth average; 2 numbers per key) or sliding window counter (no burst at window edges). See [18.6](#186-rate-limiting).

**Data model (Redis)**
```
token bucket:   rl:{rule}:{key} → hash { tokens, last_refill_ms }      TTL ≈ 2 × refill time
sliding window: rl:{rule}:{key}:{window_start} → counter               TTL = 2 windows
rules:          rule_id → { match: endpoint/plan, limit, window, algorithm }   (config DB, cached in gateway)
```

**Architecture**
```
Client ─► API Gateway
             ├─ rules cache (refreshed every 30 s from config service)
             ├─ rate-limit check ──► Redis Cluster (sharded by key)
             │      allowed → forward to service
             │      denied  → 429 + Retry-After
             └─ metrics: allowed/denied per rule
```

**Deep dive 1: atomicity across servers**
- Problem: two gateways read `tokens = 1` simultaneously and both allow.
- Fix: run read-modify-write **inside Redis atomically** with a Lua script (Redis runs scripts without interleaving):

```lua
-- KEYS[1] = bucket key; ARGV = capacity, refill_per_sec, now_ms
local data = redis.call('HMGET', KEYS[1], 'tokens', 'ts')
local capacity, rate, now = tonumber(ARGV[1]), tonumber(ARGV[2]), tonumber(ARGV[3])
local tokens = tonumber(data[1]) or capacity
local ts = tonumber(data[2]) or now
tokens = math.min(capacity, tokens + (now - ts) / 1000 * rate)
local allowed = 0
if tokens >= 1 then
  tokens = tokens - 1
  allowed = 1
end
redis.call('HSET', KEYS[1], 'tokens', tokens, 'ts', now)
redis.call('PEXPIRE', KEYS[1], math.ceil(capacity / rate * 1000) * 2)
return {allowed, tokens}
```

- Sliding window counter alternative: `INCR` current window key + `GET` previous window key; `allowed = curr + prev × (1 − elapsed/window) < limit`.
- Sliding window log alternative: sorted set per key; `ZREMRANGEBYSCORE` older than window, `ZCARD`, `ZADD now` — exact but memory per request.

**Deep dive 2: performance at very high QPS**
- One Redis round trip per request (~0.5 ms) is fine for most systems.
- For extreme rates: each gateway keeps a **local bucket** with a slice of the global limit and syncs with Redis every 100 ms (approximate, far fewer Redis calls).
- Hot key (one API key sending 50K req/s) → local limiting handles it before Redis.

**Deep dive 3: clocks**
- Gateways' clocks differ → use Redis server time (`redis.call('TIME')`) inside the script, or accept small skew.

**Failures**
- Redis shard down → fail open with local in-memory limits as a fallback; alert.
- Config service down → keep using cached rules.

**Follow-ups**
- *Different limits per plan?* Rule lookup by `(endpoint, plan)`; plan comes from the auth token.
- *Global limit across regions?* Per-region limits (sum = global) or async sync between regions; exact global limits cost cross-region latency.
- *Queue instead of reject?* Leaky bucket / queue for background APIs; reject for interactive APIs.
- *DDoS?* Rate limiting per user doesn't stop a botnet → CDN/WAF, IP reputation, CAPTCHAs at the edge.

---

### 22.3 Chat / messaging (WhatsApp, Slack)

**Requirements**
- 1:1 and group chats (≤ 500 members), text + media, delivery and read receipts, online/last-seen presence, push notifications when offline, history synced across devices.
- Non-functional: delivery < 200 ms for online users; **no message loss**; **in-order within a conversation**; highly available; E2E encryption (mention).

**Estimates**
- 500M DAU × 40 msgs = 20B/day ≈ 230K msgs/s, peak ~600K/s.
- 20B × 200 B ≈ 4 TB/day (≈ 1.5 PB/year) → horizontally scalable write-heavy store.
- 150M concurrent connections ÷ 50K/gateway ≈ 3,000 gateways.

**API**
```
WebSocket (persistent):
  → send      { client_msg_id, conversation_id, body, media_ref? }
  ← ack       { client_msg_id, message_id, seq, server_ts }
  ← message   { conversation_id, message_id, seq, sender_id, body, ts }
  → receipt   { conversation_id, up_to_seq, type: DELIVERED|READ }
  ← presence  { user_id, status, last_seen }
REST:
  GET  /v1/conversations?cursor=            (chat list with unread counts)
  GET  /v1/conversations/{id}/messages?before_seq=&limit=50
  POST /v1/conversations                    (create group)
  POST /v1/media/upload-url                 (pre-signed URL)
```

**Data model**
```
messages (Cassandra):    PK (conversation_id, bucket) | CK seq DESC | message_id, sender_id, body, media_ref, ts
conversations (SQL/KV):  conversation_id | type | created_by | created_at
members:                 conversation_id | user_id | role | joined_at
user_inbox (KV):         PK user_id | CK conversation_id | last_seq_seen, last_read_seq, muted, last_preview
sessions (Redis):        user_id → set of {device_id, gateway_id}
presence (Redis):        presence:{user_id} → online, TTL 60 s ; last_seen:{user_id} → ts
```
- Partition messages by conversation → a chat's history lives together, ordered by `seq`.
- `bucket` (e.g., month) caps partition size for long-lived chats.

**Architecture**
```
Device ══ WebSocket ══► Chat Gateway (stateful, holds connections)
                             │
                             ├──► Chat Service ──► Sequencer (per-conversation seq: Redis INCR / DB)
                             │          ├──► Message store (Cassandra)
                             │          ├──► Fan-out: look up members' sessions ──► target gateways
                             │          │                        (internal pub/sub or direct RPC)
                             │          └──► Push service (APNs/FCM) for offline members
                             ├──► Presence service (Redis)
                             └──► Session registry (Redis)
Media: client ─► pre-signed URL ─► S3 ─► CDN
```

**Send flow**
1. Client sends with `client_msg_id` (UUID) — used for dedup if the client retries.
2. Gateway → chat service: dedup check (`client_msg_id` seen recently? return the original ack).
3. Assign `seq` from the conversation's sequencer → write to the message store.
4. Ack the sender (single tick). **Persist before ack** = no loss.
5. Fan-out: for each member, look up sessions → push to their gateways → gateways push over WebSocket.
6. Recipient device acks → delivery receipt to the sender (double tick). Read → `last_read_seq` → read receipt.
7. Members with no live session → push notification.

**Deep dive 1: ordering**
- Per-conversation monotonic `seq` from one sequencer (Redis `INCR conv:{id}` or a conditional DB update) → total order per chat.
- Clients render by `seq`, never by device clock. Gaps in `seq` → client fetches the missing range.

**Deep dive 2: offline sync and multi-device**
- Each device stores `last_seq` per conversation. On reconnect: "give me messages with seq > X" for conversations with new activity (`user_inbox` tells which ones).
- Every device of a user is a separate session; all receive the message; read state syncs via `last_read_seq`.

**Deep dive 3: groups**
- Store the message **once** (conversation partition); fan out only *notifications/deliveries*.
- Large groups/channels (Slack, 100K members): don't push to everyone instantly — push to online members, others pull on open; batch fan-out with workers.

**Deep dive 4: presence**
- Heartbeat every 30 s → `SET presence:{u} 1 EX 60`. Missing heartbeats → offline after the TTL.
- Don't broadcast every status change to all contacts (O(contacts) per change). Send presence only to users currently viewing that chat/profile (subscription-based), or have clients poll presence lazily.

**Deep dive 5: gateway failures and scaling**
- Gateway dies → its clients reconnect (jittered backoff) to another gateway → registry updated → client syncs by `seq`. No message lost because messages were persisted before ack.
- Session registry entries carry a TTL refreshed by heartbeats so dead gateways' entries expire.
- Deploys: drain gateways gradually (tell clients to reconnect) to avoid reconnect storms.

**Follow-ups**
- *Why WebSocket?* Frequent traffic both ways (messages, typing, receipts).
- *E2E encryption?* Signal protocol: keys on devices; server stores only ciphertext → server-side search impossible; multi-device needs per-device keys.
- *Message search?* Client-side for E2E apps; otherwise async index into Elasticsearch per user/workspace.
- *Edit/delete?* New event types referencing `message_id`; clients apply them; tombstones in storage.
- *Exactly-once display?* At-least-once delivery + client dedup by `message_id`.

---

### 22.4 News feed (Twitter / Instagram home timeline)

**Requirements**
- Post (text/media), follow/unfollow, home timeline of followed users' posts (reverse-chronological; ranked as an extension), infinite scroll.
- Non-functional: timeline p99 < 200 ms; eventual consistency OK (seconds); very read-heavy; highly available.

**Estimates**
- 300M DAU × 10 feed loads = 3B/day ≈ 35K QPS (peak ~100K).
- 150M posts/day ≈ 1.7K/s.
- Avg 200 followers → fan-out writes ≈ 1.7K × 200 = 340K feed inserts/s (heavy but parallel).
- Some accounts have 100M+ followers.

**API**
```
POST /v1/posts                 { text, media_ids[] }        → { post_id }
POST /v1/users/{id}/follow
DELETE /v1/users/{id}/follow
GET  /v1/feed?cursor=&limit=20 → { posts[], next_cursor }
```

**Data model**
```
posts (sharded by post_id):        post_id (Snowflake) | author_id | text | media_refs | created_at | deleted
user_posts (by author):            PK author_id | CK post_id DESC          (author's own timeline)
follows:                           followers(followee_id → follower_id), following(follower_id → followee_id)
feed cache (Redis):                feed:{user_id} → sorted set/list of post_ids (latest ~800)
post cache (Redis):                post:{post_id} → serialized post
```

**The core decision: fan-out on write vs on read**

| | Fan-out on write (push) | Fan-out on read (pull) |
|---|---|---|
| On post | Insert post_id into every follower's feed | Nothing |
| On feed read | Read one precomputed list | Fetch recent posts of everyone you follow and merge |
| Read latency | Very low | High (hundreds of lookups) |
| Write cost | O(followers) — celebrities = millions of writes | O(1) |
| Wasted work | Feeds of users who never log in | None |

**Hybrid (expected answer)**
- Regular authors (< ~10K followers): **push** via async fan-out workers.
- Celebrities: **pull** at read time — their recent posts are cached per celebrity and merged into the follower's feed.
- Inactive users (> 30 days): don't push; rebuild their feed with pull on next login.

**Architecture**
```
POST ─► Post service ─► posts DB + user_posts ─► Kafka "post_created"
                                                     │
                                       Fan-out workers (partitioned by author)
                                         ├─ author is celebrity? → skip push
                                         └─ for follower in active followers (paged):
                                              ZADD feed:{follower} post_id ; trim to 800

GET /feed ─► Feed service
               1. ZREVRANGE feed:{me} by cursor          (push part)
               2. + recent posts of celebrities I follow  (pull part, cached)
               3. merge by post_id (time-sortable), drop deleted/blocked
               4. hydrate: MGET post:{id} … + author profiles (cache)
               5. (optional) rank top N candidates
```

**Deep dives**
- **Store IDs, not posts, in feeds:** small fan-out writes; edits/deletes show up immediately at hydration time.
- **Pagination:** cursor = last post_id (Snowflake is time-sortable) → `ZREVRANGEBYSCORE` below the cursor.
- **Fan-out at scale:** Kafka partitioned by author; workers page through follower lists; idempotent `ZADD` (re-running is safe).
- **Ranking:** candidate generation (feed IDs) → feature fetch → ML model scores → top 20 → diversity rules. Mention only if asked.
- **Media:** uploaded to S3 via pre-signed URLs, served via CDN; posts store media IDs.

**Failures**
- Feed cache lost → rebuild from pull path (followees' `user_posts`). The pull path must exist anyway.
- Fan-out lag → posts appear late; monitor consumer lag.

**Follow-ups**
- *New follow?* Backfill the followee's recent posts into the follower's feed asynchronously. Unfollow → filter at read time + lazy cleanup.
- *Likes/comments counts?* Separate counter service (Redis + periodic flush); don't update the post row on every like.
- *Celebrity threshold?* Dynamic (by follower count and posting rate).

---

### 22.5 Notification system

**Requirements**
- Channels: mobile push (APNs/FCM), SMS, email, in-app.
- Triggered by services (transactional: OTP, order shipped) and campaigns (marketing to segments).
- User preferences and opt-outs; templates with localization; scheduling; per-user rate limits; delivery tracking.
- Non-functional: at-least-once, **no duplicate sends to users**, OTPs fast (seconds), campaigns to 50M users without hurting transactional traffic.

**API**
```
POST /v1/notifications
  { idempotency_key, recipient: { user_id } | { segment_id },
    type, template_id, params, channels?, priority: HIGH|NORMAL|LOW, send_at? }
  → 202 { notification_id }
GET  /v1/notifications/{id}          → status per channel
PUT  /v1/users/{id}/preferences
```

**Data model**
```
notifications:   id | idempotency_key UNIQUE | user_id | type | template_id | params | priority | send_at | status
deliveries:      notification_id | channel | status (QUEUED/SENT/DELIVERED/FAILED/BOUNCED) | attempts | provider_msg_id | updated_at
preferences:     user_id | type | channel | enabled | quiet_hours
devices:         user_id | device_token | platform | last_seen
templates:       template_id | channel | locale | version | body
```

**Architecture**
```
Producers ─► Notification API ─► validate, dedup by idempotency_key, persist
                     │
                     ├─ send_at in future → Scheduler (DB index on send_at / Redis ZSET) ─┐
                     ▼                                                                      │
               Router: load prefs + quiet hours + per-user caps → pick channels ◄──────────┘
                     │
         Kafka topics by channel × priority (push.high, push.low, sms.high, email.low, ...)
                     │
      Channel workers ─► render template ─► provider adapter (FCM/APNs/Twilio/SES)
             │                 └─ per-provider rate limit + circuit breaker + fallback provider
             ├─ transient failure → retry topic (backoff) → DLQ after N
             └─ update deliveries
Provider webhooks (delivered/bounced/invalid token) ─► update deliveries, prune bad device tokens
Campaign service ─► segment expansion in batches ─► throttled enqueue (LOW priority)
```

**Deep dives**
- **Dedup:** unique `idempotency_key` at the API. Workers also check `deliveries` status before calling the provider (Kafka may redeliver). Some providers accept their own idempotency key.
- **Priority isolation:** separate topics and worker pools → a 50M campaign never delays an OTP.
- **Campaigns:** expand segments page by page; enqueue gradually (e.g., 50K/s) to protect providers and downstream; respect per-user caps ("max 3 marketing pushes/day").
- **Provider failures:** circuit breaker per provider; fail over SMS to a secondary vendor; honor provider rate limits.
- **User experience:** quiet hours (delay non-urgent), collapse similar notifications ("5 people liked your post"), unsubscribe links (legal requirement for email).

**Follow-ups**
- *Exactly once?* Not possible with external providers; at-least-once + dedup makes duplicates rare.
- *In-app notifications?* Stored per user (`inbox` table), delivered over WebSocket/SSE if online, fetched on open; unread count in Redis.
- *Tracking opens/clicks?* Tracking pixel / redirect links → events → analytics.

---

### 22.6 Distributed cache / key-value store

Two flavors are asked: **"design Redis/Memcached cluster"** (in-memory cache) and **"design DynamoDB/Cassandra"** (durable KV). Cover the shared core, then the differences.

**Requirements**
- `put(key, value, ttl?)`, `get(key)`, `delete(key)`; values up to ~1 MB.
- Horizontally scalable to TBs and millions of ops/s; p99 < 5 ms; highly available; tunable consistency (durable flavor).

**Partitioning**
- Consistent hashing with virtual nodes (or fixed hash slots like Redis Cluster's 16,384).
- **Client-side routing:** smart clients hold the ring/slot map and talk to the owner directly (no proxy hop); refresh on `MOVED`/errors. Alternative: a proxy layer (Twemproxy, Envoy) — simpler clients, extra hop.

**Replication**
- Cache flavor: per shard one primary + replicas (async); failover by a coordinator (Sentinel-like) or consensus.
- Durable flavor: each key on N=3 nodes (next distinct nodes on the ring); **leaderless quorum** W=2, R=2.

**Durable flavor: write path**
1. Client → coordinator (any node, or the owner).
2. Coordinator sends to the N replicas; returns after W acks.
3. Each replica: append to **commit log (WAL)** → insert into **memtable** → ack. Memtable full → flush as an immutable sorted **SSTable**.
4. Background **compaction** merges SSTables, discards overwritten values and expired **tombstones**.

**Durable flavor: read path**
1. Coordinator queries R replicas.
2. Each replica checks memtable → SSTables newest-first, using **Bloom filters** to skip files and a sparse index to find the block.
3. Coordinator returns the newest version; stale replicas get **read repair**.

**Handling failures (durable)**
- **Gossip** for membership and failure detection.
- **Hinted handoff:** if a replica is down, another node stores the write plus a hint, forwards it on recovery.
- **Anti-entropy:** replicas compare **Merkle trees** per key range and sync only differing ranges.
- **Conflict resolution:** last-write-wins by timestamp (simple, may drop concurrent writes) or **vector clocks** returning siblings for the client to merge (e.g., union of shopping carts).

**Cache flavor specifics**
- Eviction per node: approximate LRU/LFU (sample a few keys, evict the best candidate) — exact LRU across millions of keys costs too much memory.
- TTL: lazy expiry on access + periodic random sampling of keys with TTLs.
- Memory management: slab allocator (Memcached) to limit fragmentation.
- Persistence optional (snapshots/AOF) for faster warm restarts.

**Hot keys and large values**
- Hot key → replicate across several nodes (`key#n`) or client-side local caching with a short TTL.
- Large values → chunk or compress; reject > limit.

**Follow-ups**
- *Adding a node?* It takes over vnode ranges; data streams from previous owners; reads fall back to old owners until the transfer completes.
- *Why not one big hash map?* Memory ceiling and a single point of failure.
- *Strong consistency option?* Leader per partition with Raft (like etcd/TiKV) instead of leaderless quorums — linearizable but lower write availability during partitions.

---

### 22.7 Video streaming (YouTube / Netflix)

**Requirements**
- Upload videos (up to several GB), process into multiple resolutions/codecs, stream with adaptive bitrate on any device, metadata/search, view counts, resume playback.
- Non-functional: smooth playback worldwide (low startup time, minimal rebuffering), uploads processed in minutes, durable storage, cost-efficient.

**Estimates**
- 500K uploads/day × 500 MB = 250 TB/day raw; transcoded renditions add ~1–2× → storage is the main cost.
- 1B views/day × avg 10 min × ~3 Mbps → egress of **petabytes per day** → CDN must serve > 95%.

**API**
```
POST /v1/videos                      { title, description, size, content_type } → { video_id, upload: multipart pre-signed URLs }
POST /v1/videos/{id}/complete        (client finished uploading parts)
GET  /v1/videos/{id}                 → metadata + manifest URL + status
GET  /v1/videos/{id}/manifest.m3u8   (via CDN)
POST /v1/videos/{id}/progress        { position_sec }
```

**Data model**
```
videos (SQL, sharded by video_id): video_id | owner_id | title | description | status (UPLOADING/PROCESSING/READY/FAILED) | duration | created_at
renditions:   video_id | resolution | codec | bitrate | manifest_path
watch_progress (KV): (user_id, video_id) → position, updated_at
view_counts (counter service): video_id → count
Object storage: raw/{video_id}/source ; processed/{video_id}/{rendition}/segment_00042.m4s
```

**Upload + processing pipeline**
```
Client ─► Upload API: create row (UPLOADING), return multipart pre-signed URLs
Client ─► S3 raw bucket (parallel parts, resumable)
Complete ─► Kafka "video_uploaded"
          ─► Orchestrator (workflow engine, DAG per video):
               1. validate + probe (ffprobe)
               2. split into chunks (GOP-aligned, e.g., 10 s)
               3. fan-out: transcode each chunk × each rendition (240p … 4K; H.264/VP9/AV1)  ← worker fleet
               4. package into HLS/DASH segments + manifests
               5. thumbnails, captions (speech-to-text), content moderation
               6. update status READY → notify uploader
Processed ─► S3 processed bucket ─► CDN (origin shield in front)
```
- Chunk-level parallelism: a 1-hour video transcodes in minutes across many workers; a failed chunk retries alone.
- Tasks idempotent by `(video_id, chunk, rendition)` output path.
- Priority queues: popular creators / short videos first.

**Playback**
- **Adaptive bitrate (ABR):** the master manifest lists renditions; each rendition is a sequence of 2–6 s segments. The player measures throughput and buffer level and picks the next segment's quality → no stalls on a slow network.
- Player → CDN edge (cache hit for popular videos) → regional cache → origin (S3) on miss.
- **Netflix-style Open Connect:** CDN boxes inside ISPs, pre-filled overnight with predicted popular content.
- Startup time: small first segments, low initial bitrate, prefetch the manifest.

**View counts**
- Don't `UPDATE videos SET views = views + 1` per view (hot rows).
- Player sends view events → Kafka → stream aggregation (per video per minute) → batch increments to the counter store; Redis for near-real-time display. Dedup/bot filtering in the stream job.

**Follow-ups**
- *Resumable upload?* Multipart: re-upload only missing parts; client keeps the upload ID.
- *Cost?* Encode long-tail videos in fewer renditions/on demand; newer codecs (AV1) for popular content (more CPU once, less bandwidth forever); cold storage for originals.
- *Live streaming?* Ingest (RTMP/SRT) → real-time transcoder → short segments (1–2 s, low-latency HLS) → CDN; 2–10 s latency; DVR window stored for replay.
- *Copyright?* Fingerprint audio/video during processing and match against a reference DB.

---

### 22.8 Ride hailing (Uber / Lyft)

**Requirements**
- Riders request a ride (pickup/drop); see nearby drivers and ETA; get matched to a driver; live tracking; trip lifecycle; fare and payment; ratings.
- Non-functional: match within seconds; location freshness ~5 s; a driver is never assigned two rides; highly available per city.

**Estimates**
- 1M active drivers × 1 location update / 4 s = **250K writes/s** → in-memory geo index, not a disk DB per update.
- 20M rides/day ≈ 230 ride requests/s (peaks much higher in rush hour).
- Riders polling nearby drivers: ~100K/s → served from the in-memory index.

**API**
```
Driver: POST /v1/drivers/location { lat, lng, heading, ts }   (or over a WebSocket)
        POST /v1/drivers/status   { AVAILABLE | OFFLINE }
        POST /v1/offers/{id}/accept | /decline
Rider:  GET  /v1/drivers/nearby?lat=&lng=
        POST /v1/rides            { pickup, dropoff, product } → { ride_id, fare_estimate }
        GET  /v1/rides/{id}        (+ WebSocket for live updates)
        POST /v1/rides/{id}/cancel
```

**Data model**
```
Geo index (in-memory / Redis, sharded by city/region):  cell_id → set of available driver_ids ; driver_id → (lat, lng, ts)
drivers (SQL):  driver_id | vehicle | rating | status
rides (SQL):    ride_id | rider_id | driver_id | status | pickup | dropoff | fare | created_at | version
ride_events:    ride_id | from_status | to_status | ts      (audit)
location history: Kafka → cold storage (analytics, ETA training, disputes)
```

**Architecture**
```
Driver app ─(every 4 s)─► Location service ─► Geo index (cells, e.g., H3 res 8–9)
                                       └─► Kafka "locations" (history, ETA, surge)
Rider app ─► Ride service ─► Matching service
                                ├─ query cells around pickup (expand rings if too few drivers)
                                ├─ rank candidates by ETA (routing service), rating, acceptance
                                ├─ reserve driver atomically (OFFERED, TTL 15 s)
                                └─ send offer (push/WebSocket) → accept → ASSIGNED / timeout → next candidate
Trip service (state machine) ─► rides DB ; Notification over WebSocket to rider & driver
Pricing service (fare estimate + surge per cell) ; Payment service on completion
```

**Deep dive 1: geo index**
- Map lat/lng → cell (geohash, S2, or H3). Each cell holds available drivers.
- Nearby query = pickup cell + neighboring rings; expand radius until K candidates.
- Kept in memory (Redis GEO or a custom service) because of the update rate; stale entries expire if a driver stops reporting.
- Shard by city/region — matching is local.

**Deep dive 2: no double assignment**
- Atomically flip driver state `AVAILABLE → OFFERED`:
  - Redis: `SET driver:{id}:offer {ride_id} NX EX 15`, or
  - SQL: `UPDATE drivers SET status='OFFERED', ride_id=:r WHERE id=:d AND status='AVAILABLE'` (1 row = success).
- Offer expires → driver returns to AVAILABLE automatically.
- Ride state transitions use optimistic locking (`version`).

**Deep dive 3: trip state machine**
`REQUESTED → MATCHING → DRIVER_ASSIGNED → DRIVER_ARRIVED → IN_PROGRESS → COMPLETED`, plus `CANCELLED` from early states. Server validates every transition; each change is an event (audit, notifications, billing).

**Deep dive 4: ETA and surge**
- ETA from a routing engine on the road graph with live traffic (precomputed contraction hierarchies for speed), not straight-line distance.
- Surge per cell = f(open requests / available drivers) over a sliding window, smoothed to avoid oscillation.

**Failures**
- Location service node dies → drivers reconnect; index rebuilds within one update interval (4 s).
- Region outage → city served from a standby region; in-flight trips continue from the rides DB.

**Follow-ups**
- *Pooling (shared rides)?* Matching considers detour cost for existing riders; harder optimization (batch matching every few seconds).
- *Batch matching?* Instead of greedy first-come, match all requests in a 2-second window to minimize total ETA (bipartite matching).

---

### 22.9 Ticket booking (BookMyShow / Ticketmaster)

**Requirements**
- Browse events/shows by city; view seat map with availability; select seats → **hold for 10 min** → pay → confirm → e-ticket; cancellation/refund.
- Non-functional: **never double-book**; handle flash sales (1M users for 50K seats); browsing can be slightly stale; holds must expire reliably.

**Estimates**
- Normal: a few thousand bookings/min. Flash sale: 1M concurrent users, hundreds of thousands of hold attempts in the first minutes, mostly on the same few rows → contention is the problem, not total volume.

**API**
```
GET  /v1/events?city=&date=
GET  /v1/shows/{id}/seats                 → seat map with status (cached 1–2 s)
POST /v1/shows/{id}/holds  { seat_ids }   → 201 { hold_id, expires_at } | 409 { unavailable_seats }
POST /v1/holds/{id}/payment-intent        → { client_secret }      (idempotency key header)
POST /v1/payments/webhook                 (PSP → us)
GET  /v1/bookings/{id}
POST /v1/bookings/{id}/cancel
```

**Data model (PostgreSQL — transactions needed)**
```
venues, screens, seats(seat_id, screen_id, row, number, type)
shows(show_id, event_id, screen_id, starts_at)
show_seats(show_id, seat_id, status, hold_id, hold_expires_at, booking_id, version)   PK (show_id, seat_id)
holds(hold_id, user_id, show_id, seat_ids, expires_at, status)
bookings(booking_id, hold_id UNIQUE, user_id, show_id, amount, status, payment_id, created_at)
```

**Hold (the core)**
```sql
BEGIN;
UPDATE show_seats
SET status = 'HELD', hold_id = :hold, hold_expires_at = now() + interval '10 minutes'
WHERE show_id = :show
  AND seat_id = ANY(:seats)
  AND (status = 'AVAILABLE' OR (status = 'HELD' AND hold_expires_at < now()));
-- application checks: rows_updated == len(:seats) ? COMMIT : ROLLBACK (409)
COMMIT;
```
- The conditional `UPDATE` locks the rows; concurrent requests on the same seats serialize on those row locks; the loser updates fewer rows → rollback → 409. **All-or-nothing.**
- Expired holds count as available in the `WHERE` → expiry works even if the sweeper is late. A sweeper job also resets expired holds for clean seat maps.

**Confirm**
1. Client pays (PSP) using an idempotency key = `hold_id`.
2. Webhook → in one transaction: `UPDATE show_seats SET status='BOOKED', booking_id=:b WHERE hold_id=:hold AND status='HELD' AND hold_expires_at > now()` must update all seats → insert booking.
3. If the hold already expired (payment too slow) → refund automatically (compensation) and notify.
4. Webhooks retry → handler is idempotent (`bookings.hold_id` is unique).

**Flash sale design**
- **Virtual waiting room:** users get a queue token; a gatekeeper admits N users/minute to the seat-selection page (Redis sorted set by arrival time). Protects the DB and is fairer.
- Seat map served from cache (1–2 s TTL) — display only; the hold is the source of truth.
- Option for extreme contention: holds in **Redis** (`SET seat:{show}:{seat} {hold} NX EX 600` via Lua for multiple seats) then persist bookings to the DB. Faster, but two systems must agree → reconciliation job.
- Per-user limits (max 6 tickets), bot protection (CAPTCHA at the edge), idempotent APIs.

**Follow-ups**
- *Why not lock when the seat map opens?* Browsers would hold seats they never buy; lock only on explicit selection with a short TTL.
- *Payment succeeded but our service crashed?* PSP webhook retries; reconciliation compares PSP records with bookings and fixes or refunds.
- *General admission (no seat numbers)?* Inventory counter: `UPDATE ticket_types SET sold = sold + :n WHERE id = ? AND sold + :n <= capacity`.

---

### 22.10 Typeahead / search autocomplete

**Requirements**
- As the user types, return the top 5–10 completions for the prefix, ranked by popularity (optionally personalized, localized, trending).
- Non-functional: end-to-end < 100 ms (server < 20 ms); very high QPS; suggestions can be minutes/hours stale; filter offensive terms.

**Estimates**
- 100M DAU × 10 searches × ~8 keystrokes (after debounce) = 8B requests/day ≈ 90K QPS avg, 250K peak.
- Popular queries: ~10M distinct after filtering × 30 B ≈ 300 MB + trie overhead → a few GB per serving node — fits in memory.

**API**
```
GET /v1/suggest?q=iph&locale=en-IN&limit=8  → ["iphone 15", "iphone 15 pro", ...]
```

**Approach A: trie with precomputed top-K**
- Each node stores the **top-K completions** for its prefix → lookup is O(length of prefix), no subtree traversal.
- Built offline: query logs → aggregate counts with time decay (recent weighs more) → filter (blocklist, min count) → build the trie → serialize → ship snapshot to serving nodes (blue-green swap).
- Sharding: by prefix range (balanced by traffic, not alphabet) or replicate the whole trie if it fits (simplest).

**Approach B: search engine**
- Elasticsearch completion suggester (in-memory FST) or edge n-gram index on a `suggestions` index with a popularity field.
- Better when you need filters (category, locale), fuzzy matching (typos), or frequent updates.

**Architecture**
```
Client: debounce 100–150 ms, cache prefix results locally, cancel stale requests
   ─► CDN/edge cache (short prefixes are extremely hot; cache 1–5 min)
   ─► Suggest service (in-memory trie snapshot) + Redis cache (prefix → results)
Offline:  search logs ─► Kafka ─► aggregator (hourly/daily counts, decay) ─► trie builder ─► snapshot store (S3) ─► serving nodes
Real-time: Kafka ─► stream job (Count-Min Sketch per window) ─► trending boosts merged at query time
```

**Deep dives**
- **Trending:** batch trie updates hourly/daily; a real-time trending service detects spikes (count in the last 10 min vs baseline) and injects them.
- **Personalization:** merge global top-K with the user's recent searches (client-side or a small per-user store), re-rank.
- **Typos:** fuzzy matching (edit distance ≤ 1–2), or a spell-correction step before lookup.
- **Short prefixes** (1–2 chars) → precompute and serve from CDN/browser cache.

**Follow-ups**
- *How do you update the trie without downtime?* Build a new snapshot; load it in the background; atomically swap the pointer.
- *Multi-language?* Separate tries per locale; Unicode normalization.

---

## 23. Tier 2 problems

### 23.1 File storage & sync (Dropbox / Google Drive)

**Requirements:** upload/download files, sync across devices, share with others, version history, offline edits. Non-functional: never lose data, efficient bandwidth for small edits to big files, strong consistency for metadata.

**Estimates:** 50M DAU; average 2 GB per user stored → 100 PB → object storage. Metadata: billions of file rows → sharded SQL.

**Key design: chunking + content addressing**
- Split files into ~4 MB chunks; hash each (SHA-256). A file version = ordered list of chunk hashes.
- Upload only chunks the server doesn't already have (**dedup**) → editing a 1 GB file uploads only changed chunks (**delta sync**). Content-defined chunking (rolling hash) keeps chunk boundaries stable when bytes are inserted.

**Data model**
```
files:          file_id | owner_id | parent_id | name | is_folder | latest_version | deleted   (sharded by owner/namespace)
file_versions:  file_id | version | chunk_hashes[] | size | modified_by | created_at
chunks:         chunk_hash (PK) | s3_key | size | ref_count
shares:         file_id | user_id | permission
journal:        namespace_id | seq | change (for sync cursors)
```

**Architecture**
```
Client (watcher + local DB of chunk hashes)
   ├─► Metadata service (SQL) ─► journal (per-namespace seq)
   ├─► Block service: "which of these hashes are missing?" → pre-signed URLs ─► S3
   └─► Notification service (long poll / WebSocket): "namespace changed, cursor X"
```

**Sync flow:** client edits → computes chunk hashes → asks which are missing → uploads them → commits a new version with `expected_parent_version` (optimistic concurrency) → journal entry → other devices notified → they fetch the journal since their cursor → download missing chunks.

**Conflicts:** two devices commit on the same parent version → second commit fails → client saves a "conflicted copy". (Google Docs-style real-time merging is a different problem — [23.10](#2310-collaborative-editor-google-docs).)

**Follow-ups:** sharing permissions inherited from folders · version retention (keep 30 days) · garbage-collect unreferenced chunks (ref counts + delayed delete) · large-folder listing pagination.

---

### 23.2 Web crawler

**Requirements:** crawl billions of pages, keep them fresh, be polite to websites, avoid duplicates and traps; store pages for indexing.

**Estimates:** 1B pages/month ≈ 400 pages/s; 500 KB avg → 500 TB/month raw (compressed less).

**Architecture**
```
Seed URLs ─► URL Frontier ─► Fetchers ─► DNS resolver (cached) ─► HTTP fetch (robots.txt checked)
                ▲                              │
                │                              ▼
          URL filter/dedup ◄── Link extractor ◄── Parser ─► Content dedup (hash / SimHash) ─► Page store (S3) ─► Indexer
```

**URL frontier (the heart)**
- **Prioritization:** front queues by priority (PageRank-ish importance, change frequency).
- **Politeness:** back queues **one per host**; each host has a "next allowed fetch time" (crawl delay, robots.txt). A heap picks the host whose time has come.
- Persist the frontier (disk-backed queues) — it's huge.

**Dedup**
- URL seen? normalize (lowercase host, remove fragments, sort params) → check a Bloom filter + a persistent store.
- Content seen? exact hash; near-duplicates via SimHash (Hamming distance).

**Distribution:** partition by **hostname hash** → each crawler node owns a set of hosts → politeness stays local, no cross-node coordination.

**Traps & safety:** max URL length/depth, detect infinite calendars, per-host page caps, content-type checks, timeouts, limit response size.

**Freshness:** re-crawl schedule based on observed change rate (news sites hourly, static pages monthly); conditional GETs (`If-Modified-Since`, ETag).

---

### 23.3 Payment system / digital wallet

**Requirements:** accept payments (cards via a PSP like Stripe/Razorpay), wallet balances and transfers, refunds, payouts to merchants, reconciliation. Non-functional: **never double-charge or lose money**, strong consistency, full audit trail, compliance (PCI — never store raw card numbers; the PSP tokenizes them).

**Data model**
```
payments:         payment_id | idempotency_key UNIQUE | merchant_id | user_id | amount | currency | status | psp_ref | created_at
ledger_entries:   entry_id | txn_id | account_id | direction (DEBIT/CREDIT) | amount | created_at      (append-only)
accounts:         account_id | owner | type (USER_WALLET, MERCHANT, PLATFORM_FEES, PSP_CLEARING) | balance (cached) | version
outbox:           id | event | payload | published
```

**Double-entry ledger**
- Every money movement writes ≥ 2 entries that **sum to zero** (debit one account, credit another). Example: user pays merchant ₹100 with 2% fee → debit user wallet 100, credit merchant 98, credit platform fees 2.
- Ledger is append-only; corrections are new reversing entries. Balances are derived from (and reconciled against) the ledger.

**Payment flow (card)**
1. Client → `POST /payments` with an idempotency key → create payment `PENDING`.
2. Call PSP with **our** idempotency key (PSP dedups too).
3. PSP result (sync response and/or webhook) → in one DB transaction: status `SUCCEEDED` + ledger entries + outbox event.
4. Unknown result (timeout) → status `UNKNOWN` → query PSP later; never blindly retry with a new key.

**Wallet transfer (internal)**
```sql
BEGIN;
SELECT * FROM accounts WHERE account_id IN (:a, :b) ORDER BY account_id FOR UPDATE;  -- fixed order avoids deadlocks
-- check balance(a) >= amount
INSERT INTO ledger_entries ... (debit a, credit b);
UPDATE accounts SET balance = balance - :amt, version = version + 1 WHERE account_id = :a;
UPDATE accounts SET balance = balance + :amt, version = version + 1 WHERE account_id = :b;
COMMIT;
```

**Reconciliation:** daily, compare our ledger with PSP settlement reports and bank statements; mismatches go to an exceptions queue for investigation. Every serious payment system has this.

**Other points:** state machine for payments (`CREATED → PENDING → SUCCEEDED/FAILED → REFUNDED`); sync replication for the ledger DB (RPO = 0); events via outbox; fraud/risk checks before charging; payouts batched to merchants.

---

### 23.4 Distributed job scheduler (cron at scale)

**Requirements:** schedule one-off and recurring jobs (cron expressions), millions of jobs; run each **at least once** near its time (seconds of delay OK); retries; job history; no job lost if a node dies.

**Data model**
```
jobs:      job_id | owner | cron_expr | payload | next_run_at | status | retry_policy | timeout   INDEX(next_run_at) WHERE status='ACTIVE'
job_runs:  run_id | job_id | scheduled_for | status | worker_id | attempt | started_at | finished_at | error
           UNIQUE(job_id, scheduled_for)     ← prevents duplicate runs of the same occurrence
```

**Architecture**
```
API ─► jobs DB
Scheduler nodes (partitioned: job_id hash → partition, each partition owned by one scheduler via lease)
   every second: SELECT due jobs in my partitions WHERE next_run_at <= now() LIMIT 500 FOR UPDATE SKIP LOCKED
     → INSERT job_runs (unique) → enqueue to Kafka/SQS → set next_run_at = next cron time
Workers ─► pull from queue ─► run with timeout ─► heartbeat ─► update job_runs
Watchdog ─► runs with stale heartbeats → mark failed → retry per policy
```

**Deep dives**
- **No double scheduling:** partition ownership via leases (etcd/ZooKeeper) + `SKIP LOCKED` + unique `(job_id, scheduled_for)`.
- **Worker crash mid-job:** visibility timeout / heartbeats → re-queued → **jobs must be idempotent**.
- **Very precise timing:** a time-bucketed in-memory wheel (timer wheel) for the next few minutes of jobs, loaded from the DB.
- **Thundering herd at :00:** many cron jobs at midnight → spread with jitter if the job allows; scale workers.

---

### 23.5 Food delivery (Swiggy / DoorDash)

**Requirements:** discover restaurants (by location, cuisine, ratings), menus, cart, place order, pay, restaurant accepts, assign delivery partner, live tracking, ETA, ratings.

**Services and data**
- **Restaurant/menu catalog:** SQL source of truth + Elasticsearch for discovery (geo filter + text + ranking) + cache for menus.
- **Cart:** Redis per user (AP is fine).
- **Order service:** SQL, order state machine:
  `PLACED → PAYMENT_CONFIRMED → ACCEPTED → PREPARING → READY_FOR_PICKUP → PICKED_UP → DELIVERED`, plus `CANCELLED/REFUNDED`.
- **Payment:** idempotent, saga with compensation (refund if the restaurant rejects).
- **Dispatch:** geo index of available delivery partners (like ride hailing).
- **Tracking:** partner location updates → WebSocket to the customer.

**Dispatch deep dive**
- Don't assign at order time: assign so the partner arrives when the food is ready (prep time estimate − travel time).
- Batching: combine two orders from the same/nearby restaurant going in the same direction.
- Assignment: atomic reservation of the partner (like Uber); offer → accept/timeout → next.

**Peak handling:** lunch/dinner spikes → autoscale; queue order placement; degrade non-critical features (recommendations).

**Availability changes:** restaurant toggles items/closes → event → invalidate menu cache + update search index; validate availability again at checkout.

---

### 23.6 E-commerce checkout + inventory (flash sale)

**Requirements:** catalog, cart, checkout with inventory reservation, payment, order; flash sales where 1M users want 10K units.

**Inventory correctness**
- Normal: `UPDATE inventory SET available = available - :n, reserved = reserved + :n WHERE sku = :s AND available >= :n` (atomic, no oversell).
- Reservation with TTL at checkout start; confirm on payment; release on timeout/failure (sweeper).

**Flash sale design**
```
Users ─► CDN (static product page) ─► Waiting room (admit N/s) ─► Sale API
   Sale API: Lua in Redis: if stock > 0 then DECR stock; record user (one per user) → token
           ─► Kafka "orders" ─► order workers (rate-controlled) ─► DB order + payment
```
- Redis absorbs the burst (100K+ ops/s); the DB sees only the winners at a controlled rate.
- Pre-load stock into Redis before the sale; reconcile with DB after.
- One unit per user (`SADD sale:{id}:buyers user` returns 0 if already bought).
- Payment timeout → return the unit to Redis stock.

**Order saga:** reserve inventory → charge → create order → notify; failures trigger compensations.

**Cart:** AP store (Redis/DynamoDB), merge guest cart on login; prices re-validated at checkout (never trust the cart's price).

---

### 23.7 Leaderboard, top-K, trending

**Exact leaderboard (game, contest)**
- Redis sorted set: `ZINCRBY lb:{game} points user` · top 10: `ZREVRANGE lb 0 9 WITHSCORES` · my rank: `ZREVRANK lb user` · neighbors: range around my rank. All O(log n).
- 100M players → shard by score ranges or by user hash; top-K = merge per-shard top-K; exact global rank with user-hash sharding needs counts from every shard (approximate with score histograms).
- Persistence: scores in a DB; the sorted set rebuilds from it.
- Time-based boards (daily/weekly) → one key per period, TTL.

**Top-K heavy hitters in a stream (trending hashtags, top products)**
- Kafka → stream processor partitioned by item → per-window counts.
- Memory-bounded approximate version: **Count-Min Sketch** for counts + **min-heap of size K** for candidates per window; merge windows (e.g., last 60 one-minute windows) for "last hour".
- Exact version: MapReduce/Spark batch (count per item → top-K per partition → merge) for daily reports.
- **Trending ≠ most popular:** compare current rate vs baseline (z-score / ratio) so always-popular items don't dominate.

---

### 23.8 Metrics, logging, and monitoring system

**Requirements:** collect metrics (CPU, latency, business counters) and logs from 100K hosts; dashboards; alerting; query recent data fast; retain long-term cheaply.

**Metrics pipeline**
```
Agents (batch, compress) ─► Collectors ─► Kafka ─► TSDB writers ─► Time-series DB (sharded by series)
                                                         └─► Downsampler (1 s → 1 min → 1 h)
Query service ◄─ Grafana dashboards ; Alert evaluator (rules every 30 s) ─► dedup/grouping ─► PagerDuty/Slack
```
- Series = metric name + tags. **Cardinality** (unique tag combinations) is the main scaling risk: never tag with user_id/request_id.
- Compression: delta-of-delta timestamps, XOR-encoded floats (Gorilla) → ~1.4 bytes per point.
- Retention tiers: raw for 15 days, 1-min rollups for 90 days, 1-h rollups for years.
- Pull (Prometheus scrapes targets) vs push (agents send) — pull is simple for discovery-driven environments; push suits short-lived jobs.

**Logs pipeline**
```
Apps (structured JSON) ─► agent (Fluent Bit/Vector) ─► Kafka ─► indexer ─► hot store (OpenSearch / ClickHouse / Loki) ─► S3 cold
```
- Full-text indexing everything is expensive → index only labels/fields (Loki) or columnar storage (ClickHouse) for cost.
- Sampling for debug logs; PII scrubbing at the agent.

**Alerting:** evaluate rules; avoid flapping (require N consecutive breaches); group related alerts; silence during maintenance; alert on SLO burn rate.

---

### 23.9 Hotel / Airbnb booking

**Requirements:** search listings by location/dates/guests/price; view listing; book dates; host calendar; payments; reviews.

**Search**
- Elasticsearch: geo filter (bounding box/radius), price range, amenities, plus **availability**.
- Availability is hard to keep in the search index precisely → store a coarse availability bitmap per listing (next 365 days) in the doc, updated asynchronously; the booking step re-checks against the DB.

**Data model**
```
listings(listing_id, host_id, location, price, capacity, ...)
availability(listing_id, date, status, price_override)          PK(listing_id, date)
bookings(booking_id, listing_id, guest_id, check_in, check_out, status, amount)
```

**Booking without overlap**
- Option A: lock dates — `UPDATE availability SET status='BOOKED', booking_id=:b WHERE listing_id=:l AND date >= :in AND date < :out AND status='AVAILABLE'` must update exactly (out − in) rows; else rollback.
- Option B: Postgres exclusion constraint on a date range:
  ```sql
  ALTER TABLE bookings ADD CONSTRAINT no_overlap
  EXCLUDE USING gist (listing_id WITH =, daterange(check_in, check_out) WITH &&)
  WHERE (status IN ('PENDING', 'CONFIRMED'));
  ```
  The DB itself rejects overlapping bookings.
- Pending booking holds dates for 15 min while payment completes; then confirm or release.

**Hotel variant:** room *types* with counts instead of unique listings → inventory per (hotel, room_type, date): `UPDATE ... SET booked = booked + 1 WHERE booked < total` for every night in one transaction. Overbooking allowance is a business rule (e.g., 105%).

---

### 23.10 Collaborative editor (Google Docs)

**Requirements:** many users edit the same document simultaneously; see each other's changes and cursors in real time; offline edits; version history; comments.

**Why naive approaches fail:** "last write wins" on the whole doc loses others' edits; locking paragraphs is a bad experience.

**Two families of solutions**
- **Operational Transformation (OT):** edits are operations (`insert 'x' at 5`). The server orders operations; when an operation was made on an older version, it's **transformed** against concurrent ones (if someone inserted 3 chars before position 5, my insert moves to 8). Needs a central server for ordering. Used by Google Docs.
- **CRDTs:** each character gets a unique, ordered ID (e.g., position identifiers between neighbors); operations commute, so any replica applying the same set of operations converges — no central ordering needed, great for offline/P2P. Costs metadata overhead. Used by Figma-style tools, Yjs/Automerge.

**Architecture**
```
Clients ══ WebSocket ══► Document session server (all editors of doc X routed to the same server: consistent hashing on doc_id)
                              ├─ in-memory doc state + pending ops
                              ├─ op log append (durable: Kafka/DB) ─► periodic snapshots (S3/DB)
                              └─ broadcast transformed ops + cursors to other clients
Load doc = latest snapshot + ops after it.  Version history = snapshots + op log.
```
- Cursors/presence are ephemeral (memory only).
- Session server failure → another server loads snapshot + op log and clients reconnect.
- Permissions checked on connect and per op; comments stored separately anchored to text ranges (anchors adjusted like ops).

---

## 24. Tier 3 problems

### 24.1 Proximity service (Yelp, "restaurants near me")

- **Requirements:** given location + radius (+ filters), return nearby businesses ranked by distance/rating. Businesses rarely change; read-heavy (e.g., 5K QPS).
- **Index:** geohash or quadtree over ~200M businesses. Small enough (a few GB) to keep **in memory on every read replica** of a "location service".
- **Query:** compute the user's geohash at a precision matching the radius → fetch the cell + 8 neighbors → exact distance filter → sort → paginate. Expand precision outward if too few results.
- **Quadtree vs geohash:** quadtree adapts to density (Manhattan cells small, desert cells big); geohash is simple strings that work with any DB index (`WHERE geohash LIKE '9q8yy%'`).
- Business details from SQL + cache; updates applied to the index nightly or via events.
- **Nearby friends** variant: locations change constantly → Redis GEO with TTL, or pub/sub per user channel; friends subscribe to each other's location channel while the app is open.

### 24.2 Ad click aggregation

- **Requirements:** count clicks per ad per minute for billing and dashboards; billions of clicks/day; results within minutes; accurate (money).
- **Pipeline:** click events (with click_id) → Kafka (partitioned by ad_id) → stream processor (Flink) with **tumbling 1-minute windows** keyed by ad_id → aggregated rows → OLAP DB (ClickHouse/Druid) → query service.
- **Correctness:**
  - Dedup by `click_id` within a window (state store).
  - **Event time + watermarks** handle late events; allowed lateness; very late events go to a correction path.
  - Exactly-once state via Flink checkpoints + idempotent/transactional sinks.
  - **Reconciliation:** raw events archived to S3; nightly batch job recomputes and corrects (lambda-style).
- **Hot ads:** a viral ad's partition overloads → salt the key (`ad_id#0..9`), aggregate twice.
- **Fraud:** filter bots (IP/device patterns) before billing.

### 24.3 Distributed message queue (design Kafka)

- **Storage:** topic → partitions → append-only log split into **segment files** with an offset index; sequential disk writes; OS page cache; zero-copy reads.
- **Replication:** each partition has a leader and followers; followers fetch from the leader; **ISR** tracks in-sync followers; a write is committed when all ISR have it (`acks=all`); **high watermark** = last committed offset (consumers read up to it).
- **Leader failure:** controller (Raft-based KRaft) elects a new leader from the ISR.
- **Producers:** batch + compress; partitioner by key; idempotent producer IDs + sequence numbers dedup retries.
- **Consumers:** pull model (consumers control pace), offsets stored in an internal compacted topic; consumer groups with a coordinator assigning partitions.
- **Retention:** delete segments older than N days or beyond size; **compaction** keeps the latest record per key.
- **Delayed messages / priorities:** not native → separate topics per delay level or priority.

### 24.4 Instagram (photo sharing)

- **Upload:** client → pre-signed URL → S3 → event → processing workers (resize to several sizes, strip EXIF, moderation, generate blurhash) → CDN URLs stored on the post.
- **Metadata:** posts sharded by user_id (Instagram famously used sharded Postgres with Snowflake-like IDs encoding the shard).
- **Feed:** hybrid fan-out ([22.4](#224-news-feed-twitter--instagram-home-timeline)).
- **Likes/comments:** counter service (Redis increments, async flush to DB); comments table partitioned by post_id with cursor pagination.
- **Explore/search:** separate ranking pipeline; hashtags in Elasticsearch.
- **Stories:** expire after 24 h → TTL storage; ring of followed users' stories precomputed per user.

### 24.5 Unique ID generator (distributed)

- **Requirements:** 64-bit, unique across 1,000+ machines, roughly time-sortable, 10K+ IDs/s per machine, no central bottleneck.
- **Design:** Snowflake — 41-bit timestamp (ms since custom epoch), 10-bit worker ID, 12-bit sequence.
- **Worker IDs:** assigned at startup from ZooKeeper/etcd (ephemeral node) or from the pod ordinal; must be unique — duplicates = duplicate IDs.
- **Same millisecond overflow:** sequence hits 4,096 → wait for the next millisecond.
- **Clock moves backwards (NTP):** refuse to generate (or wait) until the clock passes the last timestamp; alert.
- **Alternatives:** UUIDv7 (no coordination, 128-bit), DB range allocation (compact, gaps on crash).

### 24.6 LLM chat / RAG / agent systems

Covered in depth in the [GenAI guide](genai.md): chatbot architecture, streaming, conversation memory, RAG pipeline, vector search, agents and tools, LLM gateway, serving and cost.

---

# Part D

## 25. Presenting a system you built

Interviewers often say: *"Design the system you built, at 10× scale."* Prepare 2–3 of your own projects this way, with real numbers.

### 25.1 How to structure the answer
1. **Context (1 min):** what the product does, who uses it, scale (QPS, data size, users).
2. **Architecture (5 min):** draw it — clients, APIs, stores, queues, caches, external dependencies.
3. **Your part:** say clearly what you owned vs what the team or platform owned.
4. **Key decisions and tradeoffs:** for each major choice, the alternative you rejected and why.
5. **Hard problems:** one or two incidents or bottlenecks — how you measured, fixed, and verified.
6. **10× scale:** what breaks first and what you'd change.

### 25.2 Example: search service over a large product catalog
- Sketch: search API → cache layers (in-process LRU → Redis) → Elasticsearch (sharded, replicated, denormalized product/SKU docs) ← indexing consumers ← Kafka CDC ← PostgreSQL ← Kafka price/catalog consumers.
- Be ready for: why the database first then CDC (dual-write problem); per-key ordering via Kafka partition keys + version guards; zero-downtime reindex via alias swap; separating cached result IDs from cached item details and invalidating on price change; how a latency improvement was achieved and measured (p50 vs p99).

### 25.3 Example: agentic chat / LLM assistant
- Sketch: client → API → agent orchestrator (model ↔ tools loop) → LLM provider (streaming) → SSE to client; checkpoints and conversation state in a document store; retrieval constrained by an entity/taxonomy layer.
- Be ready for: SSE vs WebSocket, checkpoint/resume, idempotent tools, context window management, backoff on provider errors, evaluation. Deep material in the [GenAI guide](genai.md).

### 25.4 Example: asynchronous ingestion pipeline
- Sketch: source → parent job → queue fan-out → stage workers (parse, chunk, tag, embed, index) → fan-in → done; retries with backoff, DLQ, execution logs.
- Be ready for: exactly how idempotency works, poison messages, DLQ replay, backpressure, per-tenant isolation.

### 25.5 Example: shared auth with key rotation; video transcoding pipeline
- JWT: RS256, JWKS with `kid`, rotation overlap, short-lived tokens + refresh, algorithm pinning.
- HLS: renditions + segments + master playlist, object storage + CDN, ABR — same as [22.7](#227-video-streaming-youtube--netflix).

## 26. Common mistakes and phrases that help

**Mistakes**
- Drawing boxes before requirements and numbers.
- Choosing a database by brand name without an access-pattern reason.
- No data model, no partition key.
- Missing the hard part of the problem (celebrity fan-out, hot seats, hot keys, ordering).
- Strong consistency everywhere (slow, expensive) or eventual everywhere (double bookings).
- No idempotency on retried writes.
- Not discussing failure of each component.
- Over-engineering (multi-region active-active when nobody asked).
- Talking non-stop without checking in.

**Phrases**
- "Reads outnumber writes 100:1, so I'll optimize the read path and accept a few seconds of staleness."
- "This needs strong consistency because a double booking costs money, so this check happens in a DB transaction, not in the cache."
- "This box is a single point of failure; I'll run two across zones and fail over with a health check."
- "I'm choosing X over Y because …; the cost is …; if requirement Z changes, I'd switch to Y."
- "Which part would you like me to go deeper on — the fan-out or the storage?"
