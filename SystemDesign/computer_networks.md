# 🌐 Computer Networks

Welcome to the **Computer Networks** notes. Understanding how computers talk to each other is fundamental to distributed systems design. Every single request in a distributed system, whether a Google search or an Instagram like, travels across a network.

---

## 📁 Group 1: Networking Foundations

These concepts form the bedrock of distributed system communication and architecture.

### 1. Client-Server Model
Every web application follows a simple pattern: a **Client** (e.g., your browser or mobile app) initiates a request for a resource, and a **Server** (backend application/database) processes the request and returns a response.

#### Architecture Flow

```text
   ┌───────────────────────┐            ┌────────────────────────┐            ┌──────────────────────┐
   │        CLIENT         │            │         SERVER         │            │       DATABASE       │
   │  (Browser/Mobile App) │            │   (Backend Services)   │            │     (Data Store)     │
   └───────────┬───────────┘            └───────────┬────────────┘            └───────────┬──────────┘
               │                                    │                                     │
               │─────── 1. HTTP Request ───────────▶│                                     │
               │        (e.g., GET /search)         │                                     │
               │                                    │─────── 2. Query/Read Data ─────────▶│
               │                                    │                                     │
               │                                    │◀────── 3. Return Datasets ──────────│
               │                                    │                                     │
               │◀────── 4. HTTP Response ───────────│                                     │
               │        (200 OK + HTML/JSON)        │                                     │
```

#### Key Takeaways
* **Initiation:** The Client is always the one that initiates the connection.
* **Separation of Concerns:** The decoupled client and server allow independent evolution (e.g., developing a mobile client without rewriting the server APIs) and scaling.
* **Decoupled Scaling:** Backend systems can be scaled (scaled horizontally, replica sets, etc.) without requiring modifications to the client-side logic.

---

### 2. IP Address
An **IP (Internet Protocol) Address** is a unique numerical label assigned to each device connected to a computer network. IP addresses identify machines on a network so packets know where to route and deliver.

#### Packet Delivery Pathway

```text
  ┌─────────────────────────┐        ┌─────────────────────────┐        ┌─────────────────────────┐        ┌─────────────────────────┐
  │      YOUR DEVICE        │        │    ROUTER / GATEWAY     │        │    INTERNET BACKBONE    │        │   DESTINATION SERVER    │
  │  (Local: 192.168.1.10)  │        │  (Public: 103.45.67.89) │        │   (ISP & BGP Routers)   │        │  (Target: 142.250.80.46)│
  └────────────┬────────────┘        └────────────┬────────────┘        └────────────┬────────────┘        └────────────┬────────────┘
               │                                  │                                  │                                  │
               │─────── 1. Local Packet ─────────▶│                                  │                                  │
               │                                  │─── 2. NAT & Public Routing ─────▶│                                  │
               │                                  │                                  │─────── 3. IP-based Delivery ────▶│
```

#### Versions
* **IPv4:** 32-bit address space represented as four decimal numbers separated by dots (e.g., `192.168.1.1`). Provides ~4.3 billion unique addresses, which is insufficient for the modern web.
* **IPv6:** 128-bit address space represented in hexadecimal colon-separated groups (e.g., `2001:0db8:85a3::8a2e:0370:7334`). Offers a virtually infinite number of unique addresses.

> [!NOTE]
> **System Design Relevance:** 
> IP addresses identify machines on a network. Whether you are discussing servers, load balancers, database instances, or cache nodes, they all communicate using IP addresses.

---

### 3. DNS (Domain Name System)
Since human beings prefer readable names (like `google.com`) and computers route using IP addresses (like `142.250.80.46`), **DNS** serves as the "phone book of the internet," translating domains to IPs.

#### Resolution Workflow

```text
                             ┌──────────────────────────────────────┐
                             │       DNS RESOLVER (ISP/Local)       │
                             └──────────────────┬───────────────────┘
                                                │
                 ┌── 1. Where is google.com? ──▶│◀── 8. IP: 142.250.80.46 (Cached) ──┐
                 │                              │                                    │
    ┌────────────┴────────────┐                 │── 2. Where is .com? ───────────────┼───────────────┐
    │  CLIENT (google.com)    │                 │◀─ 3. Try TLD Server at 192.5.6.30 ─┼────────────┐  │
    └─────────────────────────┘                 │                                    │            │  │
                                                │── 4. Where is google.com? ─────────┼─────────┐  │  │
                                                │◀─ 5. Try Auth NS at 216.239.32.10 ─┼──────┐  │  │  │
                                                │                                    │      │  │  │  │
                                                │── 6. What is google.com's IP? ─────┼──┐   │  │  │  │
                                                │◀─ 7. IP is 142.250.80.46 ──────────┼─┐│   │  │  │  │
                                                │                                    │││   │  │  │  │
                                                ▼                                    ▼▼▼   ▼▼  ▼▼ ▼▼
                                     ┌─────────────────────┐                   ┌──────────┐  ┌───────────┐
                                     │  AUTHORITATIVE NS   │                   │   TLD    │  │   ROOT    │
                                     │    (google.com)     │                   │  (.com)  │  │    (.)    │
                                     └─────────────────────┘                   └──────────┘  └───────────┘
```

#### Resolution Steps
1. **Local Cache Check:** The browser first inspects local DNS caches (browser, OS, router).
2. **Recursive Resolver:** If not cached, it queries the ISP or a public resolver (e.g., Cloudflare `1.1.1.1` or Google `8.8.8.8`).
3. **Root Servers:** If the resolver doesn't know, it queries the root servers (`.`), which direct it to the appropriate Top-Level Domain (TLD) server.
4. **TLD Servers:** Resolves the suffix (like `.com`, `.org`) and points to the authoritative nameserver.
5. **Authoritative Nameservers:** Provides the final actual IP address of the domain.
6. **Caching:** The IP gets cached at every layer to minimize subsequent resolutions.

> [!TIP]
> **DNS in Scale & Resilience:**
> * **DNS Load Balancing:** Returns different IP addresses for a single domain to distribute traffic globally.
> * **Failover Routing:** Can point traffic to a standby server if the primary IP health check fails.

---

### 4. Proxy vs. Reverse Proxy
A **Proxy** is an intermediary service positioned between clients and servers. However, their directions and functions differ completely:

#### Architecture Comparison

```text
                                        [ FORWARD PROXY ]
                              (Hides & protects Client identities)

        ┌──────────┐
        │ Client A ├──┐
        └──────────┘  │      ┌───────────────┐                  ┌─────────────────────────┐
                      ├─────▶│ FORWARD PROXY │─────────────────▶│        INTERNET         │
        ┌──────────┐  │      │ (VPN/Gateway) │  [Hides Client]  │  (Destination Servers)  │
        │ Client B ├──┘      └───────────────┘                  └─────────────────────────┘
        └──────────┘


                                        [ REVERSE PROXY ]
                              (Hides & protects Server identities)
        ┌──────────┐
        │  PUBLIC  │         ┌───────────────┐                  ┌─────────────────────────┐
        │ CLIENTS  │────────▶│ REVERSE PROXY │─────────────────▶│    BACKEND SERVER 1     │
        │ (Traffic)│         │(Nginx/HAProxy)│  [Load Balance]  └─────────────────────────┘
        └──────────┘         └───────┬───────┘                  ┌─────────────────────────┐
                                     └─────────────────────────▶│    BACKEND SERVER 2     │
                                                                └─────────────────────────┘
```

| Aspect | Forward Proxy | Reverse Proxy |
| :--- | :--- | :--- |
| **Position** | Sits in front of **clients**. | Sits in front of **servers**. |
| **Hides Identity** | Hides the identity of the **client** from the server. | Hides the identity of the **server** from the client. |
| **Primary Examples** | VPNs, Corporate Web Filters. | Nginx, HAProxy, Envoy, Cloudflare. |
| **Key Roles** | Anonymity, content filtering, security, caching client requests. | Load balancing, SSL/TLS termination, caching static content, compression, rate-limiting. |

---

> [!IMPORTANT]
> **Up Next: Latency**
> Whenever a client communicates with a server, there’s always some delay. One of the biggest causes of this delay is physical distance. In the next section, we will cover **Latency** and how it impacts modern distributed architectures.
