# HLD Interview Guide (SDE2)

Everything for the system design round in one book: how the round works → estimation → every core concept in depth, each followed by practice problems that use it → GenAI engineering (LLMs, agents, RAG, serving, GenAI system design) → presenting your own systems.

Each concept chapter explains: **what it is → how it works → when to use it → tradeoffs → what to say.** Practice chapters appear right after the concepts they need.

Related: [LLD guide](lld-interview-guide.md) (OOP, patterns, machine coding, concurrency)

## Contents

- [1. What the SDE2 bar looks like](#1-what-the-sde2-bar-looks-like)
- [2. The 45-minute framework](#2-the-45-minute-framework)
- [3. Back-of-the-envelope estimation](#3-back-of-the-envelope-estimation)
  - [3.1 Numbers to memorize](#31-numbers-to-memorize)
  - [3.2 Method](#32-method)
  - [3.3 Worked example 1: social feed](#33-worked-example-1-social-feed)
  - [3.4 Worked example 2: chat](#34-worked-example-2-chat)
  - [3.5 Worked example 3: URL shortener](#35-worked-example-3-url-shortener)
- [4. Networking fundamentals](#4-networking-fundamentals)
  - [4.1 OSI vs TCP/IP models and how data flows end to end](#41-osi-vs-tcpip-models-and-how-data-flows-end-to-end)
  - [4.2 TCP vs UDP](#42-tcp-vs-udp)
  - [4.3 HTTP versions](#43-http-versions)
  - [4.4 TLS (HTTPS)](#44-tls-https)
  - [4.5 DNS](#45-dns)
  - [4.6 "What happens when you type a URL?"](#46-what-happens-when-you-type-a-url)
  - [4.7 IP, ports, NAT, proxies](#47-ip-ports-nat-proxies)
  - [4.8 IP addressing, subnets and CIDR](#48-ip-addressing-subnets-and-cidr)
  - [4.9 Routing, BGP and anycast](#49-routing-bgp-and-anycast)
  - [4.10 TCP in depth: setup, teardown, flow and congestion control](#410-tcp-in-depth-setup-teardown-flow-and-congestion-control)
  - [4.11 HTTP essentials: methods, headers, cookies, CORS](#411-http-essentials-methods-headers-cookies-cors)
  - [4.12 Sockets, ports and connection limits](#412-sockets-ports-and-connection-limits)
  - [4.13 ARP, DHCP, ICMP and other helpers](#413-arp-dhcp-icmp-and-other-helpers)
  - [4.14 How these protocols keep evolving](#414-how-these-protocols-keep-evolving)
- [5. API design](#5-api-design)
  - [5.1 REST](#51-rest)
  - [5.2 gRPC](#52-grpc)
  - [5.3 GraphQL](#53-graphql)
  - [5.4 Real-time communication](#54-real-time-communication)
  - [5.5 Voice and video: WebRTC in five minutes](#55-voice-and-video-webrtc-in-five-minutes)
  - [5.6 Pagination](#56-pagination)
  - [5.7 Idempotency](#57-idempotency)
  - [5.8 Other API concerns](#58-other-api-concerns)
  - [5.9 API design in practice: versioning and shapes](#59-api-design-in-practice-versioning-and-shapes)
- [6. Load balancing, proxies, gateways, service discovery](#6-load-balancing-proxies-gateways-service-discovery)
  - [6.1 Why](#61-why)
  - [6.2 L4 vs L7](#62-l4-vs-l7)
  - [6.3 Algorithms](#63-algorithms)
  - [6.4 Health checks and high availability](#64-health-checks-and-high-availability)
  - [6.5 Stateless servers and sessions](#65-stateless-servers-and-sessions)
  - [6.6 API gateway](#66-api-gateway)
  - [6.7 Service discovery](#67-service-discovery)
  - [6.8 Autoscaling](#68-autoscaling)
  - [6.9 How servers handle concurrent requests](#69-how-servers-handle-concurrent-requests)
  - [6.10 Load balancing at very large scale](#610-load-balancing-at-very-large-scale)
- [7. CDN and edge](#7-cdn-and-edge)
  - [7.1 When the edge itself breaks](#71-when-the-edge-itself-breaks)
- [8. Caching](#8-caching)
  - [8.1 Where caches live](#81-where-caches-live)
  - [8.2 Read and write patterns](#82-read-and-write-patterns)
  - [8.3 Eviction policies](#83-eviction-policies)
  - [8.4 Cache problems you must know](#84-cache-problems-you-must-know)
  - [8.5 Redis in depth](#85-redis-in-depth)
  - [8.6 Cache consistency strategies (pick per data type)](#86-cache-consistency-strategies-pick-per-data-type)
  - [8.7 When the cache takes down the database](#87-when-the-cache-takes-down-the-database)
- [9. Relational modeling and SQL](#9-relational-modeling-and-sql)
  - [9.1 Tables, keys and relationships](#91-tables-keys-and-relationships)
  - [9.2 Normalization: 1NF, 2NF, 3NF, BCNF](#92-normalization-1nf-2nf-3nf-bcnf)
  - [9.3 SQL you must be able to write](#93-sql-you-must-be-able-to-write)
  - [9.4 Joins: what they return and how the database runs them](#94-joins-what-they-return-and-how-the-database-runs-them)
  - [9.5 Query execution and EXPLAIN](#95-query-execution-and-explain)
  - [9.6 Views, materialized views, stored procedures and triggers](#96-views-materialized-views-stored-procedures-and-triggers)
  - [9.7 DBMS interview questions](#97-dbms-interview-questions)
  - [9.8 A story worth knowing: Uber, Postgres and write amplification](#98-a-story-worth-knowing-uber-postgres-and-write-amplification)
- [10. Databases: relational internals](#10-databases-relational-internals)
  - [10.1 ACID](#101-acid)
  - [10.2 How a write is made durable: WAL](#102-how-a-write-is-made-durable-wal)
  - [10.3 MVCC (multi-version concurrency control)](#103-mvcc-multi-version-concurrency-control)
  - [10.4 Isolation levels and anomalies](#104-isolation-levels-and-anomalies)
  - [10.5 Locks inside the database: 2PL, row, gap and next-key locks](#105-locks-inside-the-database-2pl-row-gap-and-next-key-locks)
  - [10.6 Indexes](#106-indexes)
  - [10.7 B-tree vs LSM-tree storage engines](#107-b-tree-vs-lsm-tree-storage-engines)
  - [10.8 Normalization vs denormalization](#108-normalization-vs-denormalization)
  - [10.9 Connection pooling](#109-connection-pooling)
  - [10.10 Lessons from production databases](#1010-lessons-from-production-databases)
- [11. Databases: NoSQL and data modeling](#11-databases-nosql-and-data-modeling)
  - [11.1 Choosing a database](#111-choosing-a-database)
  - [11.2 Wide-column (Cassandra) modeling](#112-wide-column-cassandra-modeling)
  - [11.3 DynamoDB modeling](#113-dynamodb-modeling)
  - [11.4 Document (MongoDB) modeling](#114-document-mongodb-modeling)
  - [11.5 Graph databases](#115-graph-databases)
  - [11.6 Time-series and columnar](#116-time-series-and-columnar)
  - [11.7 OLTP vs OLAP](#117-oltp-vs-olap)
- [12. Problem: URL shortener (TinyURL / bit.ly)](#12-problem-url-shortener-tinyurl--bitly)
- [13. Problem: Ticket booking (BookMyShow / Ticketmaster)](#13-problem-ticket-booking-bookmyshow--ticketmaster)
- [14. Problem: Hotel / Airbnb booking](#14-problem-hotel--airbnb-booking)
- [15. Replication](#15-replication)
  - [15.1 Why](#151-why)
  - [15.2 Leader-follower (primary-replica)](#152-leader-follower-primary-replica)
  - [15.3 Replication lag problems](#153-replication-lag-problems)
  - [15.4 Failover](#154-failover)
  - [15.5 Multi-leader](#155-multi-leader)
  - [15.6 Leaderless (Dynamo-style)](#156-leaderless-dynamo-style)
  - [15.7 When failover goes wrong: GitHub, October 2018](#157-when-failover-goes-wrong-github-october-2018)
- [16. Partitioning and sharding](#16-partitioning-and-sharding)
  - [16.1 Terms](#161-terms)
  - [16.2 When to shard](#162-when-to-shard)
  - [16.3 Strategies](#163-strategies)
  - [16.4 Choosing the shard key (most important sharding decision)](#164-choosing-the-shard-key-most-important-sharding-decision)
  - [16.5 Consistent hashing in detail](#165-consistent-hashing-in-detail)
  - [16.6 Problems after sharding](#166-problems-after-sharding)
  - [16.7 How companies actually sharded](#167-how-companies-actually-sharded)
- [17. Distributed systems core](#17-distributed-systems-core)
  - [17.1 Why distributed is hard](#171-why-distributed-is-hard)
  - [17.2 CAP and PACELC](#172-cap-and-pacelc)
  - [17.3 Consistency models (strong → weak)](#173-consistency-models-strong--weak)
  - [17.4 Quorums](#174-quorums)
  - [17.5 Consensus and leader election](#175-consensus-and-leader-election)
  - [17.6 Time and ordering](#176-time-and-ordering)
  - [17.7 Unique ID generation](#177-unique-id-generation)
  - [17.8 Distributed transactions](#178-distributed-transactions)
  - [17.9 Distributed locks](#179-distributed-locks)
  - [17.10 Failure detection and membership](#1710-failure-detection-and-membership)
  - [17.11 Exactly-once — the honest answer](#1711-exactly-once--the-honest-answer)
  - [17.12 Backpressure](#1712-backpressure)
  - [17.13 Coordinating concurrent work across servers](#1713-coordinating-concurrent-work-across-servers)
  - [17.14 Papers that shaped this chapter](#1714-papers-that-shaped-this-chapter)
- [18. Problem: Unique ID generator (distributed)](#18-problem-unique-id-generator-distributed)
- [19. Problem: Distributed cache / key-value store](#19-problem-distributed-cache--key-value-store)
- [20. Problem: Payment system / digital wallet](#20-problem-payment-system--digital-wallet)
- [21. Problem: E-commerce checkout + inventory (flash sale)](#21-problem-e-commerce-checkout--inventory-flash-sale)
- [22. Problem: Stock exchange / order matching engine](#22-problem-stock-exchange--order-matching-engine)
- [23. Problem: Distributed job scheduler (cron at scale)](#23-problem-distributed-job-scheduler-cron-at-scale)
- [24. Problem: Online code judge (LeetCode-style)](#24-problem-online-code-judge-leetcode-style)
- [25. Messaging and stream processing](#25-messaging-and-stream-processing)
  - [25.1 Why asynchronous messaging](#251-why-asynchronous-messaging)
  - [25.2 Queue vs log](#252-queue-vs-log)
  - [25.3 Kafka internals](#253-kafka-internals)
  - [25.4 RabbitMQ and SQS](#254-rabbitmq-and-sqs)
  - [25.5 Delivery semantics and ordering](#255-delivery-semantics-and-ordering)
  - [25.6 Failure handling patterns](#256-failure-handling-patterns)
  - [25.7 Event-driven patterns](#257-event-driven-patterns)
  - [25.8 Stream processing](#258-stream-processing)
- [26. Problem: Notification system](#26-problem-notification-system)
- [27. Problem: Chat / messaging (WhatsApp, Slack)](#27-problem-chat--messaging-whatsapp-slack)
- [28. Problem: News feed (Twitter / Instagram home timeline)](#28-problem-news-feed-twitter--instagram-home-timeline)
- [29. Problem: Collaborative editor (Google Docs)](#29-problem-collaborative-editor-google-docs)
- [30. Problem: Metrics, logging, and monitoring system](#30-problem-metrics-logging-and-monitoring-system)
- [31. Problem: Ad click aggregation](#31-problem-ad-click-aggregation)
- [32. Problem: Distributed message queue (design Kafka)](#32-problem-distributed-message-queue-design-kafka)
- [33. Storage: block, file, object, data lake, warehouse](#33-storage-block-file-object-data-lake-warehouse)
  - [33.1 When object storage went down: S3, 28 February 2017](#331-when-object-storage-went-down-s3-28-february-2017)
- [34. Search](#34-search)
  - [34.1 Inverted index](#341-inverted-index)
  - [34.2 Relevance scoring](#342-relevance-scoring)
  - [34.3 Elasticsearch architecture](#343-elasticsearch-architecture)
  - [34.4 Autocomplete approaches](#344-autocomplete-approaches)
- [35. Special data structures](#35-special-data-structures)
  - [35.1 Bloom filter — "have I seen this before?" without storing everything](#351-bloom-filter--have-i-seen-this-before-without-storing-everything)
  - [35.2 HyperLogLog — count unique items in ~12 KB](#352-hyperloglog--count-unique-items-in-12-kb)
  - [35.3 Count-Min Sketch — approximate frequency of each item in a stream](#353-count-min-sketch--approximate-frequency-of-each-item-in-a-stream)
  - [35.4 Geohash — turn a location into a string where nearby places share a prefix](#354-geohash--turn-a-location-into-a-string-where-nearby-places-share-a-prefix)
  - [35.5 Merkle tree — find which part of two huge datasets differs](#355-merkle-tree--find-which-part-of-two-huge-datasets-differs)
  - [35.6 Other structures worth naming](#356-other-structures-worth-naming)
- [36. Problem: Video streaming (YouTube / Netflix)](#36-problem-video-streaming-youtube--netflix)
- [37. Problem: Video calling (Zoom / Google Meet)](#37-problem-video-calling-zoom--google-meet)
- [38. Problem: File storage & sync (Dropbox / Google Drive)](#38-problem-file-storage--sync-dropbox--google-drive)
- [39. Problem: Typeahead / search autocomplete](#39-problem-typeahead--search-autocomplete)
- [40. Problem: Web crawler](#40-problem-web-crawler)
- [41. Problem: Distributed counter (likes, views)](#41-problem-distributed-counter-likes-views)
- [42. Problem: Leaderboard, top-K, trending](#42-problem-leaderboard-top-k-trending)
- [43. Problem: Proximity service (Yelp, "restaurants near me")](#43-problem-proximity-service-yelp-restaurants-near-me)
- [44. Problem: Ride hailing (Uber / Lyft)](#44-problem-ride-hailing-uber--lyft)
- [45. Problem: Food delivery (Swiggy / DoorDash)](#45-problem-food-delivery-swiggy--doordash)
- [46. Problem: Maps and ETA (Google Maps)](#46-problem-maps-and-eta-google-maps)
- [47. Reliability and resilience](#47-reliability-and-resilience)
  - [47.1 Availability math](#471-availability-math)
  - [47.2 Timeouts](#472-timeouts)
  - [47.3 Retries](#473-retries)
  - [47.4 Circuit breaker](#474-circuit-breaker)
  - [47.5 Bulkheads, load shedding, graceful degradation](#475-bulkheads-load-shedding-graceful-degradation)
  - [47.6 Rate limiting](#476-rate-limiting)
  - [47.7 Redundancy, failover, disaster recovery](#477-redundancy-failover-disaster-recovery)
  - [47.8 Idempotency everywhere](#478-idempotency-everywhere)
  - [47.9 Practices that came out of production pain](#479-practices-that-came-out-of-production-pain)
- [48. Problem: Rate limiter (distributed)](#48-problem-rate-limiter-distributed)
- [49. Security](#49-security)
  - [49.1 Authentication: who are you?](#491-authentication-who-are-you)
  - [49.2 OAuth 2.0 and OpenID Connect](#492-oauth-20-and-openid-connect)
  - [49.3 Authorization: what are you allowed to do?](#493-authorization-what-are-you-allowed-to-do)
  - [49.4 Protecting data](#494-protecting-data)
  - [49.5 Common attacks and their fixes](#495-common-attacks-and-their-fixes)
  - [49.6 Breaches worth learning from](#496-breaches-worth-learning-from)
- [50. Observability](#50-observability)
  - [50.1 Metrics — numbers over time](#501-metrics--numbers-over-time)
  - [50.2 Logs — what happened, in detail](#502-logs--what-happened-in-detail)
  - [50.3 Traces — one request's journey across services](#503-traces--one-requests-journey-across-services)
  - [50.4 SLIs, SLOs, SLAs and error budgets](#504-slis-slos-slas-and-error-budgets)
  - [50.5 Alerting that people don't ignore](#505-alerting-that-people-dont-ignore)
  - [50.6 Where these ideas came from](#506-where-these-ideas-came-from)
- [51. Architecture styles and deployment](#51-architecture-styles-and-deployment)
  - [51.1 Monolith vs microservices](#511-monolith-vs-microservices)
  - [51.2 Other styles](#512-other-styles)
  - [51.3 Containers and Kubernetes (enough for HLD)](#513-containers-and-kubernetes-enough-for-hld)
  - [51.4 Deployment strategies](#514-deployment-strategies)
  - [51.5 Stories behind the trade-offs](#515-stories-behind-the-trade-offs)
- [52. GenAI: what gets asked](#52-genai-what-gets-asked)
- [53. LLM fundamentals](#53-llm-fundamentals)
  - [53.1 What an LLM does](#531-what-an-llm-does)
  - [53.2 Tokens and tokenization](#532-tokens-and-tokenization)
  - [53.3 Context window](#533-context-window)
  - [53.4 Transformer and attention (high level is enough)](#534-transformer-and-attention-high-level-is-enough)
  - [53.5 Sampling and decoding](#535-sampling-and-decoding)
  - [53.6 Reasoning ("thinking") models](#536-reasoning-thinking-models)
  - [53.7 Embeddings](#537-embeddings)
  - [53.8 Multimodal models: images, documents, audio](#538-multimodal-models-images-documents-audio)
  - [53.9 Hallucination](#539-hallucination)
  - [53.10 Model choice](#5310-model-choice)
- [54. Prompting](#54-prompting)
  - [54.1 Structure of a request](#541-structure-of-a-request)
  - [54.2 Techniques that work](#542-techniques-that-work)
  - [54.3 Prompt engineering as engineering](#543-prompt-engineering-as-engineering)
- [55. Calling an LLM API](#55-calling-an-llm-api)
  - [55.1 Anatomy of a request and response](#551-anatomy-of-a-request-and-response)
  - [55.2 Streaming (needed for chat UIs)](#552-streaming-needed-for-chat-uis)
  - [55.3 Structured outputs](#553-structured-outputs)
  - [55.4 Prompt caching](#554-prompt-caching)
  - [55.5 Errors, retries, fallbacks](#555-errors-retries-fallbacks)
- [56. Tool calling (function calling)](#56-tool-calling-function-calling)
  - [56.1 How it works](#561-how-it-works)
  - [56.2 Designing good tools](#562-designing-good-tools)
  - [56.3 The loop in code](#563-the-loop-in-code)
- [57. Agents](#57-agents)
  - [57.1 Workflow or agent?](#571-workflow-or-agent)
  - [57.2 Building blocks of an agent](#572-building-blocks-of-an-agent)
  - [57.3 The agent loop](#573-the-agent-loop)
  - [57.4 Reliability features (what interviewers probe)](#574-reliability-features-what-interviewers-probe)
  - [57.5 Planning and reasoning styles](#575-planning-and-reasoning-styles)
  - [57.6 Multi-agent systems](#576-multi-agent-systems)
  - [57.7 Memory](#577-memory)
  - [57.8 Logging and tracing agent runs](#578-logging-and-tracing-agent-runs)
- [58. MCP (Model Context Protocol)](#58-mcp-model-context-protocol)
  - [58.1 What and why](#581-what-and-why)
  - [58.2 Roles](#582-roles)
  - [58.3 Server primitives](#583-server-primitives)
  - [58.4 How MCP fits with tool calling](#584-how-mcp-fits-with-tool-calling)
  - [58.5 Layers](#585-layers)
  - [58.6 MCP vs function calling](#586-mcp-vs-function-calling)
- [59. RAG in depth](#59-rag-in-depth)
  - [59.1 What and why](#591-what-and-why)
  - [59.2 Parsing and building a knowledge base](#592-parsing-and-building-a-knowledge-base)
  - [59.3 Chunking: what to use when](#593-chunking-what-to-use-when)
  - [59.4 Embeddings and vector search](#594-embeddings-and-vector-search)
  - [59.5 Searching: choices and trade-offs](#595-searching-choices-and-trade-offs)
  - [59.6 Augmentation: what goes into the prompt](#596-augmentation-what-goes-into-the-prompt)
  - [59.7 Practical implementation by data type](#597-practical-implementation-by-data-type)
  - [59.8 Advanced RAG patterns](#598-advanced-rag-patterns)
  - [59.9 Why RAG fails (debug in this order)](#599-why-rag-fails-debug-in-this-order)
- [60. Evaluation](#60-evaluation)
  - [60.1 Why it's the core skill](#601-why-its-the-core-skill)
  - [60.2 Building an eval set](#602-building-an-eval-set)
  - [60.3 Metrics](#603-metrics)
  - [60.4 LLM-as-judge](#604-llm-as-judge)
  - [60.5 Online evaluation](#605-online-evaluation)
- [61. Prompting vs RAG vs fine-tuning](#61-prompting-vs-rag-vs-fine-tuning)
- [62. Serving and inference](#62-serving-and-inference)
  - [62.1 Latency anatomy](#621-latency-anatomy)
  - [62.2 Self-hosting basics (open models)](#622-self-hosting-basics-open-models)
  - [62.3 KV-cache memory: the number that limits concurrency](#623-kv-cache-memory-the-number-that-limits-concurrency)
  - [62.4 Using hosted APIs at scale](#624-using-hosted-apis-at-scale)
- [63. Cost and latency optimization](#63-cost-and-latency-optimization)
- [64. LLM safety and security](#64-llm-safety-and-security)
  - [64.1 Prompt injection](#641-prompt-injection)
  - [64.2 Other risks (OWASP LLM Top 10 themes)](#642-other-risks-owasp-llm-top-10-themes)
  - [64.3 Guardrails in practice](#643-guardrails-in-practice)
- [65. How to approach: GenAI system design](#65-how-to-approach-genai-system-design)
- [66. Problem: Design a ChatGPT-like chatbot](#66-problem-design-a-chatgpt-like-chatbot)
- [67. Problem: Design enterprise document Q&A (RAG)](#67-problem-design-enterprise-document-qa-rag)
- [68. Problem: Design a customer-support agent](#68-problem-design-a-customer-support-agent)
- [69. Problem: Design a coding agent (like Claude Code / Cursor agent)](#69-problem-design-a-coding-agent-like-claude-code--cursor-agent)
- [70. Problem: Design an LLM gateway (platform for many internal teams)](#70-problem-design-an-llm-gateway-platform-for-many-internal-teams)
- [71. Problem: Design a recommendation system (embeddings + ranking)](#71-problem-design-a-recommendation-system-embeddings--ranking)
- [72. GenAI interview Q&A](#72-genai-interview-qa)
- [73. Presenting a system you built](#73-presenting-a-system-you-built)
  - [73.1 How to structure the answer](#731-how-to-structure-the-answer)
  - [73.2 Questions to prepare for every project](#732-questions-to-prepare-for-every-project)
  - [73.3 Example: walking through an asynchronous processing pipeline](#733-example-walking-through-an-asynchronous-processing-pipeline)
- [74. Common mistakes and phrases that help](#74-common-mistakes-and-phrases-that-help)
- [75. GenAI references](#75-genai-references)
- [76. Appendix: Calling a real model (Claude Python SDK, as of October 2026)](#76-appendix-calling-a-real-model-claude-python-sdk-as-of-october-2026)
  - [76.1 Setup](#761-setup)
  - [76.2 Basic call](#762-basic-call)
  - [76.3 Streaming](#763-streaming)
  - [76.4 Structured outputs](#764-structured-outputs)
  - [76.5 Prompt caching](#765-prompt-caching)
  - [76.6 Errors and refusal fallbacks](#766-errors-and-refusal-fallbacks)
  - [76.7 Tool use: manual loop](#767-tool-use-manual-loop)
  - [76.8 Tool use: the SDK tool runner](#768-tool-use-the-sdk-tool-runner)

---

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

---

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

---

## 3. Back-of-the-envelope estimation

### 3.1 Numbers to memorize

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
| Postgres/MySQL (good hardware) | ~10K–50K simple reads/s, ~5K–10K+ simple writes/s (conservative planning numbers; measure yours); a few TB comfortably |
| Redis | ~100K ops/s per node (single-threaded command execution) |
| Cassandra node | ~10K+ writes/s per node, scales linearly |
| Kafka broker | hundreds of MB/s |
| Elasticsearch node | hundreds to a few thousand queries/s (query-dependent) |
| WebSocket server | 50K–100K+ concurrent connections |
| Network card | 10 Gbps ≈ 1.25 GB/s |

### 3.2 Method

1. **Traffic:** DAU × actions per user per day ÷ 10^5 = average QPS. × 2–3 = peak.
2. **Read/write split:** separate the two; they scale differently.
3. **Storage:** writes per day × size × retention. Add replication factor (×3) if asked.
4. **Bandwidth:** QPS × response size.
5. **Memory for cache:** 80/20 rule — cache 20% of daily read data.
6. **Servers:** peak QPS ÷ per-server capacity (with headroom).

### 3.3 Worked example 1: social feed

- 200M DAU, each opens feed 10×/day → 2B reads/day ≈ **23K QPS**, peak ~70K.
- Each user posts 0.5×/day → 100M posts/day ≈ **1.2K writes/s**.
- Post = 1 KB text/metadata → 100 GB/day → **~36 TB/year** (before replication) → needs sharding.
- Images: 20% of posts have a 500 KB image → 20M × 0.5 MB = 10 TB/day → object storage + CDN.
- Feed cache: 200M users × 800 post IDs × 8 B ≈ 1.3 TB → Redis cluster (≈ 20–30 nodes with headroom).

### 3.4 Worked example 2: chat

- 100M DAU × 50 messages/day = 5B/day ≈ **58K msg/s**, peak ~150K.
- 5B × 200 B = 1 TB/day → wide-column store.
- 20M concurrent connections ÷ 50K per gateway = **400 gateway servers** (+ headroom).

### 3.5 Worked example 3: URL shortener

- 100M new URLs/day ≈ 1.2K writes/s average; at 100:1 reads → ≈ 120K reads/s average, ~300K/s at peak.
- 5 years ≈ 180B URLs × 500 B ≈ **90 TB**.
- 62^7 ≈ 3.5 × 10^12 > 180 × 10^9 → **7-char base62 codes**.

**Tip:** round aggressively, say the numbers out loud, and state the *consequence* ("70K reads/s → one DB can't do it → cache + replicas").

---

## 4. Networking fundamentals

### 4.1 OSI vs TCP/IP models and how data flows end to end

**Why two models?**
- **OSI (7 layers)** is a teaching/reference model — great for naming *where* something happens ("that's an L7 load balancer").
- **TCP/IP (4 layers)** is what the internet actually runs. Its layers map onto groups of OSI layers.

**What each layer does:**

| OSI layer | Job | Data unit | TCP/IP layer |
|---|---|---|---|
| 7 Application | What the app wants to say | Message | Application |
| 6 Presentation | Format, encode, encrypt | Data | Application |
| 5 Session | Open, keep and close conversations | Data | Application |
| 4 Transport | Process-to-process delivery: ports, reliability, ordering, flow and congestion control | Segment (TCP) / datagram (UDP) | Transport |
| 3 Network | Host-to-host delivery across networks: IP addressing, routing | Packet | Internet |
| 2 Data link | Delivery to the next device on the local network: MAC addresses | Frame | Link |
| 1 Physical | Bits as signals | Bits | Link |

**Where familiar things live:**

| OSI layer | Protocols | Devices / components |
|---|---|---|
| 7 Application | HTTP, gRPC, DNS, SMTP, WebSocket | L7 load balancer, API gateway, WAF, CDN |
| 6 Presentation | TLS (encryption), JSON/Protobuf, compression | — |
| 5 Session | Sessions, TLS session resumption | — |
| 4 Transport | TCP, UDP, QUIC | L4 load balancer, stateful firewall |
| 3 Network | IPv4, IPv6, ICMP | Router, L3 switch, NAT gateway |
| 2 Data link | Ethernet, Wi-Fi (802.11), ARP | Switch, Wi-Fi access point |
| 1 Physical | Copper, fibre, radio | NIC, cables |

**Memory trick (L7 → L1):** "All People Seem To Need Data Processing."

**Gotchas interviewers like**
- TLS doesn't fit neatly: it encrypts application data (presentation-like) but runs on top of TCP; in practice "between L7 and L4".
- QUIC is a transport protocol built on UDP, with TLS 1.3 built in; HTTP/3 runs on it.
- DNS is application layer; normally UDP port 53 (TCP for large responses and zone transfers; DNS over HTTPS/TLS exists).
- ARP (IP → MAC lookup on a local network) sits between L2 and L3.
- "L4 vs L7 load balancer" literally means which header it reads: ports/IPs vs HTTP paths/headers.

**Encapsulation: each layer wraps the one above it**

```text
Application  [ HTTP: GET /orders/42, Host: api.example.com ]
TLS          [ TLS header | encrypted(HTTP request) ]
Transport    [ TCP: port 51544 → 443, seq/ack | TLS record ]          segment
Network      [ IP: 192.168.1.20 → 93.184.216.34, TTL 64 | segment ]   packet
Data link    [ Ethernet: MAC laptop → MAC router | packet | FCS ]     frame
Physical     0101100111010...                                         bits
```
The receiver does the reverse (**decapsulation**): each layer strips its header and hands the payload up.

**End-to-end: what happens to one request (laptop → server)**

```text
Laptop ──Wi-Fi──► Home router ──► ISP routers ──► … internet …
(L7–L1)           (L3 + NAT)      (L3 hops)
        ──► Cloud edge / load balancer ──► App server
            (L4 or L7)                     (L7)
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

Which status code to use when is covered with examples in [5.1 REST](#51-rest).

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
 Browser (client)                         Server (api.example.com)
 has: trusted root CAs                    has: certificate + private key
      (built into OS/browser)                  (cert = its public key, signed
                                               by an intermediate CA, signed
                                               by a root CA)

 1. ClientHello ─────────────────────────────►
    "TLS 1.3; I support AES-GCM, ChaCha20;
     my temporary DH public value is A"

 ◄───────────────────────────── 2. ServerHello
    "Use AES-GCM. My temporary DH value is B"

 ── both compute the SAME secret from A and B ──
 ── from here on, the handshake is encrypted ──

 ◄───────────────────────── 3. Certificate chain
 ◄───────────────────────── 4. CertificateVerify
    (a signature over the whole handshake so far,
     made with the server's PRIVATE key)
 ◄───────────────────────── 5. Finished

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

### 4.8 IP addressing, subnets and CIDR

*Asked occasionally (more in networking/infra roles). Know the basics; skip the bit-twiddling unless asked.*

- An **IPv4 address** is 32 bits, written as four bytes: `192.168.1.20`. **IPv6** is 128 bits (`2001:db8::1`) because IPv4's ~4.3 billion addresses ran out.
- **CIDR notation** `10.0.0.0/16` = "the first 16 bits are the network, the rest identify hosts" → 2^16 = 65,536 addresses.
- **Subnet:** a slice of a network. In the cloud you carve a VPC (`10.0.0.0/16`) into subnets per availability zone and tier:

```text
VPC 10.0.0.0/16
├── public  subnet 10.0.1.0/24  (load balancers, NAT gateway)       256 addresses
├── private subnet 10.0.2.0/24  (app servers — no public IPs)
└── private subnet 10.0.3.0/24  (databases — only app subnet may connect)
```
- **Private ranges** (not routable on the internet): `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`. Machines in them reach the internet through **NAT**.
- Why it matters in design: security groups and firewall rules are written in CIDR ("allow 5432 only from 10.0.2.0/24"), and you must plan ranges so VPCs you later connect (peering, VPN) don't overlap.

```python
import ipaddress

vpc = ipaddress.ip_network("10.0.0.0/16")
subnets = list(vpc.subnets(new_prefix=24))                   # carve /24s out of the /16
assert vpc.num_addresses == 65_536 and len(subnets) == 256
app = subnets[2]
assert str(app) == "10.0.2.0/24" and app.num_addresses == 256
assert ipaddress.ip_address("10.0.2.57") in app               # firewall rule check
assert ipaddress.ip_address("10.0.3.57") not in app
assert ipaddress.ip_address("192.168.1.20").is_private and not ipaddress.ip_address("8.8.8.8").is_private
assert vpc.overlaps(ipaddress.ip_network("10.0.128.0/17"))    # peering would conflict
```

### 4.9 Routing, BGP and anycast

*Asked rarely in SDE interviews; worth knowing for outages and CDNs.*

- **Routing:** each router has a table "destination prefix → next hop". It forwards a packet toward the **most specific** matching prefix (longest prefix match).
- **Autonomous systems (AS):** the internet is ~tens of thousands of independently run networks (ISPs, clouds, large companies). **BGP** (Border Gateway Protocol) is how they announce to each other "I can reach these IP prefixes".
- **Anycast:** many servers worldwide announce the **same** IP; BGP sends each user to the nearest one. Used by CDNs and public DNS (Cloudflare's 1.1.1.1, Google's 8.8.8.8) — one address, served from hundreds of locations, and it soaks up DDoS traffic by spreading it.

**In the real world — Facebook's 2021 outage:** on 4 October 2021, a configuration change during maintenance cut Facebook's backbone. Their DNS servers, designed to stop announcing their BGP routes when they couldn't reach the data centers, did exactly that — so the rest of the internet could no longer find facebook.com, Instagram or WhatsApp for about six hours. Engineers also lost the internal tools they'd use to fix it, and had to get physical access to equipment. Lesson used in interviews: your recovery tools must not depend on the system that's down.

### 4.10 TCP in depth: setup, teardown, flow and congestion control

*Commonly asked — especially the handshake, TIME_WAIT, and "why TCP is slow on lossy links".*

**Connection setup (3-way handshake):**

```text
Client                                   Server
  │ SYN, seq=x            ──────────►     │   "I want to talk; my numbers start at x"
  │ ◄──────────  SYN-ACK, seq=y, ack=x+1  │   "OK; mine start at y; got your x"
  │ ACK, ack=y+1          ──────────►     │   connection ESTABLISHED (1 round trip before data)
```
- **Why 3 messages and not 2?** Both sides must confirm the other received their starting sequence number; it also protects against old duplicate SYNs from a previous connection creating a ghost connection.
- **SYN flood attack:** an attacker sends millions of SYNs and never completes the handshake, filling the server's half-open connection queue. Defense: **SYN cookies** (the server encodes state in its sequence number instead of storing it).

**Reliable delivery:** every byte has a sequence number; the receiver ACKs what it got; unacknowledged data is retransmitted after a timeout or after 3 duplicate ACKs (fast retransmit). The receiver reorders out-of-order segments, so the application always sees an ordered **byte stream**.

**TCP is a byte stream, not a message stream.** Two `send()` calls of 10 bytes can arrive as one 20-byte `recv()`, or as 7 + 13. Every protocol on TCP must add **framing** (length prefix, delimiter, or HTTP's Content-Length):

```python
import socket
import struct

def send_msg(sock, payload: bytes):
    sock.sendall(struct.pack("!I", len(payload)) + payload)     # 4-byte length prefix

def recv_exact(sock, n):
    buf = b""
    while len(buf) < n:
        chunk = sock.recv(n - len(buf))
        if not chunk:
            raise ConnectionError("peer closed")
        buf += chunk
    return buf

def recv_msg(sock) -> bytes:
    (length,) = struct.unpack("!I", recv_exact(sock, 4))
    return recv_exact(sock, length)

# a connected stream pair, no network needed
a, b = socket.socketpair()
a.sendall(b"hello"); a.sendall(b"world")
# the two sends usually arrive merged
merged = b.recv(100)
# boundaries are NOT preserved
assert merged in (b"helloworld", b"hello")
if merged == b"hello":
    b.recv(100)
send_msg(a, b"order:42"); send_msg(a, b"order:43")
# framing restores message boundaries
assert recv_msg(b) == b"order:42" and recv_msg(b) == b"order:43"
a.close(); b.close()
```

**Connection teardown and TIME_WAIT:**

```text
Active closer                         Passive closer
  │ FIN      ──────────►              │  "I'm done sending"
  │ ◄──────────  ACK                   │
  │ ◄──────────  FIN                   │  "me too"
  │ ACK      ──────────►              │
  │ TIME_WAIT for 2 × MSL (often 60 s) │
```
- The side that closes first sits in **TIME_WAIT** so late packets from the old connection can't be mistaken for a new one using the same ports.
- **Gotcha in production:** a service that opens a new outbound connection per request (to a DB or another service) accumulates tens of thousands of sockets in TIME_WAIT and can run out of **ephemeral ports** (~28K–64K per destination IP:port). Fix: **connection pooling / keep-alive** — reuse connections.

**Flow control (protect the receiver):** the receiver advertises a **window** — how many bytes it can buffer. The sender never has more than that in flight. A slow consumer → window shrinks → sender slows.

**Congestion control (protect the network):**
- **Slow start:** start with a small congestion window (cwnd ≈ 10 segments), double it every round trip.
- **Congestion avoidance:** after a threshold, grow by ~1 segment per round trip (additive increase).
- **On loss:** cut the window (multiplicative decrease — AIMD). This sawtooth is how millions of flows share links fairly.
- Modern algorithms: **CUBIC** (Linux default for years), **BBR** (Google, 2016) which models bandwidth and RTT instead of reacting only to loss — big gains on lossy, long-distance links.

```python
def cwnd_trace(rtts: int, loss_at: set, ssthresh: int = 64, start: int = 10) -> list[int]:
    cwnd, trace = start, []
    for t in range(rtts):
        trace.append(cwnd)
        # loss detected: multiplicative decrease
        if t in loss_at:
            ssthresh = max(2, cwnd // 2)
            cwnd = ssthresh
        elif cwnd < ssthresh:
            cwnd = min(cwnd * 2, ssthresh)                # slow start: exponential
        else:
            # congestion avoidance: additive
            cwnd += 1
    return trace

t = cwnd_trace(12, loss_at={6})
assert t[:4] == [10, 20, 40, 64]                         # exponential until ssthresh
# +1 per RTT, then halved after loss
assert t[4:7] == [65, 66, 67] and t[7] == 33
```

**Why this matters in design:**
- A new connection starts slow (handshake + slow start) → **reuse connections** (keep-alive, pools, HTTP/2 multiplexing) for latency.
- Long-distance + packet loss hurts TCP throughput → CDNs terminate connections close to users; QUIC/HTTP/3 avoids TCP head-of-line blocking.
- **Nagle's algorithm** batches small writes; for latency-sensitive protocols (games, trading, RPC) set `TCP_NODELAY`.

### 4.11 HTTP essentials: methods, headers, cookies, CORS

*Commonly asked: cookies vs tokens, and CORS.*

**Anatomy of a request and response:**

```http
POST /v1/cart/items HTTP/1.1
Host: api.shop.example
Cookie: sid=r4nd0m
Content-Type: application/json
Origin: https://shop.example

{"sku": "tee", "qty": 1}
```
```http
HTTP/1.1 201 Created
Set-Cookie: sid=r4nd0m; HttpOnly; Secure; SameSite=Lax; Max-Age=86400
Content-Type: application/json
Access-Control-Allow-Origin: https://shop.example
Access-Control-Allow-Credentials: true

{"items": 3}
```

**Cookies:** small values the browser stores and sends back automatically to the same site.
- `HttpOnly`: JavaScript can't read it (protects session IDs from XSS).
- `Secure`: only sent over HTTPS.
- `SameSite` controls cross-site sending, and is the main CSRF defence:
  - `Strict`: never sent on cross-site requests.
  - `Lax` (what browsers assume when the attribute is missing): sent only on top-level GET navigations — clicking a link to your site — so a cross-site form POST arrives without the cookie.
  - `None`: always sent; requires `Secure`. Needed for genuinely cross-site use (embedded widgets, some SSO flows).
- Cookies are per-domain and sent on every request — keep them small.

**CORS (cross-origin resource sharing) — what it really is:**
- Browsers enforce the **same-origin policy**: JavaScript from `https://shop.example` may not read responses from `https://api.shop.example` (different origin = scheme + host + port) unless that server says so.
- CORS is how the **server** opts in, using response headers. It protects **users** from malicious sites reading their data; it is **not** server-side security (curl ignores it).

```text
"Simple" request (GET, or POST with form content types):
  browser sends it with Origin: https://shop.example
  server replies with Access-Control-Allow-Origin: https://shop.example → JS may read the response

"Preflighted" request (PUT/DELETE, JSON body, custom headers like Authorization):
  1. browser sends OPTIONS /v1/cart/items
        Origin: https://shop.example
        Access-Control-Request-Method: POST
        Access-Control-Request-Headers: content-type, authorization
  2. server replies 204
        Access-Control-Allow-Origin: https://shop.example
        Access-Control-Allow-Methods: GET, POST, DELETE
        Access-Control-Allow-Headers: content-type, authorization
        Access-Control-Max-Age: 600            ← cache this answer for 10 min
  3. only now the browser sends the real POST
```
- Common bug: `Access-Control-Allow-Origin: *` together with cookies — browsers reject that combination; you must echo a specific allowed origin and send `Access-Control-Allow-Credentials: true`.
- Performance: preflights add a round trip → set `Access-Control-Max-Age`, or serve the API from the same origin (`shop.example/api`) via the gateway.

**Other headers worth knowing:** `Cache-Control`/`ETag` (see [5.1 REST](#51-rest)), `Authorization: Bearer …`, `Content-Encoding: gzip`/`br` (compression), `Accept`/`Content-Type` (content negotiation), `X-Request-Id`/`traceparent` (tracing), `Strict-Transport-Security` (force HTTPS).

### 4.12 Sockets, ports and connection limits

*Asked moderately, usually as "how many connections can one server hold?"*

- A **TCP connection** is identified by the 4-tuple (source IP, source port, destination IP, destination port). A server listening on port 443 can therefore hold far more than 65,535 connections — each client contributes a different (IP, port).
- What actually limits concurrent connections: **file descriptors** (each socket is one; raise `ulimit -n`), **memory** per connection (kernel buffers + your app's per-connection state), and the **concurrency model** (a thread per connection runs out of memory long before an event loop does — see [6.9 How servers handle concurrent requests](#69-how-servers-handle-concurrent-requests)).
- The **C10K problem** (handling 10,000 concurrent connections, framed in 1999) was solved by event-driven I/O (`epoll`/`kqueue`); servers now hold millions of mostly-idle connections (chat, push).
- **Ephemeral port exhaustion** is the opposite problem — too many *outbound* connections from one machine to one destination (see TIME_WAIT above). Fix with pooling and keep-alive.

### 4.13 ARP, DHCP, ICMP and other helpers

*Rarely asked in SDE rounds — one line each is enough.*

- **ARP:** "who has IP 192.168.1.1? tell me your MAC address" — maps IP → MAC on a local network.
- **DHCP:** a new device asks the network for an IP address, subnet mask, gateway and DNS server.
- **ICMP:** control messages — `ping` (echo request/reply), "destination unreachable", "TTL exceeded" (which `traceroute` uses to discover each hop).
- **NTP:** clock synchronization (and why clocks still drift — see [17.6 Time and ordering](#176-time-and-ordering)).
- **Proxy protocols:** `X-Forwarded-For` (original client IP through proxies), PROXY protocol for L4 load balancers.

### 4.14 How these protocols keep evolving

- **QUIC and HTTP/3:** Google built QUIC (around 2012–2013) to cut connection setup time and avoid TCP head-of-line blocking for Chrome and its services; it was standardized by the IETF, with HTTP/3 published as RFC 9114 in 2022. Its user-space implementation also let browsers and servers ship transport improvements without waiting for OS kernel updates.
- **TLS 1.3** (RFC 8446, 2018) cut the handshake from two round trips to one and removed RSA key exchange — forcing forward secrecy everywhere.
- **BBR congestion control** was deployed by Google on its services (including YouTube) around 2016, improving throughput on lossy, high-latency networks where loss-based algorithms back off too much.

---

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
| POST | Create / run a command | No | **No** | `POST /orders` twice → **two orders**. Needs an idempotency key ([5.7](#57-idempotency)) |

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
        # (etag, body, fresh_until)
        self.server, self.clock, self.entry = server, clock, None

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
        # dispatch after all currently-running resolvers have asked
        if not self.scheduled:
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
                       # 1 + 1, duplicates removed
                       "SELECT * FROM users WHERE id IN (3, 5, 8)"]
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
User.email resolver:
    if viewer.id == user.id or viewer.is_admin: return user.email
    else: return null (or an error)
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
        # duplicate delivery: ack, do nothing
        return 200
    seen_event_ids.add(event_id)
    # enqueue real processing here
    return 200

body = b'{"type": "payment.succeeded", "order_id": 42}'
sig = sign(body, 1_000)
assert handle_webhook(body, 1_000, sig, now=1_010, event_id="evt_1") == 200
# retry, ignored
assert handle_webhook(body, 1_000, sig, now=1_020, event_id="evt_1") == 200
assert handle_webhook(b'{"type": "payment.succeeded", "order_id": 99}', 1_000, sig, 1_010, "evt_2") == 401
assert handle_webhook(body, 1_000, sig, now=5_000, event_id="evt_3") == 400
```

### 5.5 Voice and video: WebRTC in five minutes

*Asked occasionally, usually as a follow-up to chat ("now add voice and video calls").*

Audio and video can't tolerate TCP's retransmission delays, and routing every call through your servers is expensive, so browsers and apps use **WebRTC**:

```text
1. Signaling (your server, e.g. over WebSocket): the two clients exchange
   "offers/answers" describing codecs and their network addresses (ICE candidates).
2. NAT traversal: each client asks a STUN server "what's my public IP:port?"
   and tries to connect directly (peer-to-peer, over UDP).
3. If a direct path is impossible (strict corporate NAT/firewalls), traffic is
   relayed through a TURN server — always works, but costs you bandwidth.
4. Media flows encrypted (SRTP); the codec adapts bitrate to the network.
```

- **Mesh** (everyone sends to everyone) works for 2–4 people; upload bandwidth explodes after that.
- **SFU** (selective forwarding unit): each participant uploads one stream to a server, which forwards it to the others — the standard for group calls. With **simulcast**, clients upload several qualities and the SFU picks one per receiver.
- **MCU** mixes everything into one stream server-side — least client work, most server CPU; rarely used now.

A full design is in [37. Problem: Video calling (Zoom / Google Meet)](#37-problem-video-calling-zoom--google-meet).

### 5.6 Pagination

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
# a new post arrives between page loads
db.execute("INSERT INTO posts VALUES (11, 110)")
page2 = offset_page(3, 3)
c_page2, _ = cursor_page(3, cursor)
assert page1 == [10, 9, 8] and page2 == [8, 7, 6]          # offset: post 8 shown twice
# cursor: correct continuation
assert c_page1 == [10, 9, 8] and c_page2 == [7, 6, 5]
```

### 5.7 Idempotency

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
        # replay the original result
        return 200, json.loads(response)
    # the side effect, done once
    charges.append(body["amount"])
    response = {"payment_id": len(charges), "amount": body["amount"], "status": "SUCCEEDED"}
    db.execute("UPDATE idempotency_keys SET status = 'DONE', response = ? WHERE key = ?",
               (json.dumps(response), key))
    db.commit()
    return 201, response

assert create_payment("k1", {"amount": 500})[0] == 201
# client retry after a timeout
status, resp = create_payment("k1", {"amount": 500})
assert status == 200 and resp["payment_id"] == 1 and charges == [500]   # charged once
# same key, different request
assert create_payment("k1", {"amount": 900})[0] == 422
```

### 5.8 Other API concerns

- **Authentication:** API keys for server-to-server partners; OAuth2 access tokens (often JWTs) for user-facing apps (details in [chapter 49](#49-security)).
- **Rate limiting** per API key/user, returning 429 + `Retry-After` (algorithms in [47.6](#476-rate-limiting)).
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

### 5.9 API design in practice: versioning and shapes

- **Stripe's date-based versions.** Instead of `/v1` → `/v2`, Stripe pins each account to the API version that was current when it integrated (identified by a date), and lets requests override it with a header. Internally, they keep the latest behaviour and apply "version change" transformations backwards for older clients — so they can keep shipping without breaking anyone. It's a heavier system than path versioning, worth it when thousands of businesses depend on your API and can't all upgrade at once.
- **GitHub's GraphQL API (2016).** GitHub launched a GraphQL API alongside REST largely because integrators needed many REST calls to assemble one view (repositories, then issues, then labels…). GraphQL let them ask for exactly that shape in one request — the over/under-fetching problem from [5.3 GraphQL](#53-graphql) in a real product.
- **gRPC (open-sourced by Google in 2015)** came from Stubby, the RPC system Google had used internally for years between its services. That lineage explains its priorities: strong contracts, efficient binary encoding, deadlines and streaming — service-to-service concerns, not browser ones.

---

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
# load-aware picking avoids queueing behind slow requests
assert w_lc < w_rr and w_p2 < w_rr
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

### 6.9 How servers handle concurrent requests

| Model | How | Examples | Strengths | Weaknesses |
|---|---|---|---|---|
| Process per worker | N worker processes, each handles requests | Gunicorn sync workers, PHP-FPM | Isolation, uses all cores | Memory per process; one slow request blocks a worker |
| Thread per request (pool) | Worker threads from a pool | Tomcat, Spring MVC, Django under WSGI threads | Simple blocking code | Thread count caps concurrency; memory |
| Event loop | One thread, many connections, non-blocking I/O | Node.js, asyncio/Uvicorn, Nginx | Tens of thousands of connections | Any blocking call stalls everyone; CPU work needs offloading |
| Hybrid | Processes × event loops (× thread pool) | Gunicorn/Uvicorn workers, Node.js cluster mode | Cores + many connections | Per-process state isn't shared |

**Example: Python ASGI servers (FastAPI on Uvicorn)**
- An `async def` handler runs on the event loop, so it must only `await` non-blocking I/O; a blocking call inside it (a synchronous HTTP or DB client) stalls every request on that worker.
- A plain `def` handler is run in a thread pool, so blocking libraries are fine but concurrency is capped by the pool size.
- `--workers 4` starts 4 processes, each with its own event loop, memory, caches, locks and connection pool — the same picture applies to Node.js cluster mode or Gunicorn workers.

**Sizing with Little's law:** `concurrency = throughput × latency`.
- 1,000 req/s, each holding a DB connection 20 ms → 20 connections busy on average → pool of ~30 across all workers (with headroom).
- Total DB connections = processes × pool size per process → must stay under the DB's `max_connections`.
- More threads/connections than the bottleneck can serve only adds queueing and contention.

### 6.10 Load balancing at very large scale

- **Google's Maglev (NSDI 2016 paper)** replaced hardware load balancers with software running on ordinary servers. Routers spread packets across Maglev machines (ECMP), and each Maglev uses a special consistent-hashing scheme so packets of the same connection reach the same backend even as Maglev machines are added or removed. The general idea — commodity machines + consistent hashing instead of a few expensive appliances — is how large clouds build their L4 layer.
- **Power of two choices in practice:** Envoy's `LEAST_REQUEST` load balancing picks two random hosts and sends to the one with fewer active requests — the algorithm from [6.3 Algorithms](#63-algorithms), chosen because it needs no global state and avoids herding onto one "least loaded" server.

---

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
- **Signed URLs/cookies:** `https://cdn.example.com/video.m3u8?expires=1735689600&sig=…` — the edge checks the signature, so only paying users can fetch paid content (code in [chapter 33](#33-storage-block-file-object-data-lake-warehouse)).
- **WAF + DDoS protection** run at the edge, before traffic reaches you.

**Edge compute** (Cloudflare Workers, Lambda@Edge): small functions run at the edge — redirects, A/B bucketing, auth checks, header rewrites, geo-blocking — with no round trip to origin.

**Key metric: cache hit ratio.** 95% hit ratio means the origin sees 5% of traffic; raising it to 99% cuts origin load by another 5×.

### 7.1 When the edge itself breaks

On 2 July 2019, Cloudflare deployed a new WAF (web application firewall) rule containing a regular expression that backtracked catastrophically. Because the rule went to every edge server worldwide at once, CPU across the network hit 100% and Cloudflare-proxied sites returned errors for about half an hour. Cloudflare's post-incident write-up led to changes in how they test regexes and, more importantly, how they **roll out configuration**: gradually, with automatic rollback, instead of globally in one step. The broader lesson for any edge or config system: a global push is a global outage waiting to happen.

---

## 8. Caching

A cache keeps a copy of data somewhere faster than where it normally lives. Reading from memory takes ~100 ns; from Redis over the network ~0.5 ms; from a database query ~1–10 ms; from another service 10–100 ms.

### 8.1 Where caches live

```text
Browser cache ─► CDN ─► API gateway / reverse proxy cache ─► App server
                                                               │
                  ┌────────────────────────────────────────────┘
                  ├─ in-process cache (dict/LRU in RAM)    ~100 ns
                  ├─ distributed cache (Redis/Memcached)   ~0.5 ms
                  └─ database (+ its own buffer pool)      ~1–10 ms
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
READ                                         WRITE
app: GET user:42 from Redis                  1. UPDATE users SET … WHERE id = 42
  hit  → return it                           2. DEL user:42 from Redis
  miss → SELECT … FROM users WHERE id = 42      (database first, then invalidate)
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
    # next reader (cache-aside)
    value = cache.data.get("user:42") or db.rows["user:42"]
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
                # follower: wait, then re-check the cache
                event.wait()
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
# 50 concurrent misses → 1 database query
assert db_queries == ["home_feed"]
```

**2. Cache penetration.** Requests for keys that **don't exist** (`GET /users/99999999`, often an attack) always miss the cache and always hit the DB.
Fixes: cache the "not found" result with a short TTL; put a **Bloom filter** of existing IDs in front ([chapter 35](#35-special-data-structures)); validate inputs.

**3. Cache avalanche.** Many keys expire together (they were all loaded at startup with the same TTL), or the whole cache cluster restarts → a flood of misses.
Fixes: jittered TTLs; replicated cache; warm the cache before taking traffic; rate-limit and circuit-break DB access so the DB survives.

**4. Hot key.** One key gets a huge share of traffic (a celebrity's profile, the flash-sale item) → the one Redis shard that owns it saturates while others idle.
Fixes: keep a copy in each app server's **in-process cache** for a few seconds; or replicate it under several keys (`item:9#1 … item:9#8`) and read a random copy.
The same trick for **write-hot counters** (a viral post's like count): see [41. Problem: Distributed counter (likes, views)](#41-problem-distributed-counter-likes-views).

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

### 8.7 When the cache takes down the database

On 23 September 2010, Facebook was down for about two and a half hours. An automated system that checked configuration values found what it considered an invalid value in the cache and "fixed" it by deleting the cached entry and querying the database. But the invalid value was also in the database, so every client that read it repeated the cycle — tens of thousands of queries per second hitting the database cluster, which collapsed; and as it collapsed, more errors triggered more fixes. They had to take the site offline to break the feedback loop.

It's the cache stampede and avalanche problems from this chapter at their worst, and the fixes are the same ones: limit how many clients can rebuild a key at once (single-flight, leases), make error handling back off instead of retrying harder, and protect the database with rate limits and circuit breakers so a cache problem can't become a database problem.

---

## 9. Relational modeling and SQL

Before the internals (indexes, WAL, MVCC), you need to model data well and write SQL fluently. Interviews test this directly ("write a query for…", "normalize this table", "which index?") and indirectly in every design (the data model step of the framework).

### 9.1 Tables, keys and relationships

- **Primary key:** uniquely identifies a row; never null; ideally never changes.
- **Candidate keys:** all columns (or column sets) that *could* be the primary key (`user_id`, `email`).
- **Unique key:** enforces uniqueness on a non-primary column (`email`). Most databases (Postgres, MySQL, Oracle, SQLite) allow **many** NULLs in a unique column, because NULL is never equal to NULL; SQL Server allows only one. Postgres 15+ can opt out with `NULLS NOT DISTINCT`.
- **Composite key:** more than one column (`(order_id, line_no)`).
- **Foreign key:** a column that must match a primary key in another table (`orders.user_id → users.id`); the database rejects orphans and can cascade deletes.
- **Surrogate vs natural key:**
  - Natural = a real-world value (email, PAN number). Problem: real-world values change and leak meaning.
  - Surrogate = a meaningless generated ID (auto-increment, UUIDv7, Snowflake). **Default to surrogate keys**, plus a unique constraint on the natural key.

**Relationship types and how to store them:**

```text
1 : 1   user ── user_profile          → profile.user_id PRIMARY KEY REFERENCES users(id)
1 : N   user ──< orders               → orders.user_id REFERENCES users(id)  (FK on the "many" side)
N : M   students >──< courses         → junction table enrollments(student_id, course_id, enrolled_at)
                                         PRIMARY KEY (student_id, course_id)
```

### 9.2 Normalization: 1NF, 2NF, 3NF, BCNF

*Asked often in campus and DBMS rounds; in system design you mostly need the intuition: one fact in one place.*

Start with one messy table of food orders:

```text
order_id | customer_name | customer_phone | items                    | restaurant_id
    | restaurant_name
9001     | Asha          | 98xxxxx001     | "Dosa x2, Coffee x1"     | 7             | Udupi Café
9002     | Asha          | 98xxxxx001     | "Idli x3"                | 7             | Udupi Café
```

**Problems (anomalies):**
- **Update anomaly:** Asha changes her phone → must update every order row; miss one and data contradicts itself.
- **Insert anomaly:** can't store a new restaurant until someone orders from it.
- **Delete anomaly:** deleting the last order of a restaurant deletes the restaurant's name too.

**1NF — atomic values, no repeating groups.** `items` holds a list → split into rows:
```text
order_items(order_id, item_name, qty)
```

**2NF — 1NF + every non-key column depends on the *whole* key** (matters for composite keys). In `order_items(order_id, item_name, qty, item_price)`, `item_price` depends only on `item_name`, not on the order → move it to `menu_items(item_name, price)`.

**3NF — 2NF + no non-key column depends on another non-key column (no transitive dependency).** In `orders`, `restaurant_name` depends on `restaurant_id`, not on `order_id` → move it to `restaurants(id, name)`. Same for customer details → `customers(id, name, phone)`.

**BCNF — every determinant is a candidate key.** A stricter 3NF that matters in rare overlapping-key cases. Example: `(student, course, instructor)` where each instructor teaches one course: `instructor → course` but `instructor` isn't a key → split into `(student, instructor)` and `(instructor, course)`.

**Result:**
```text
customers(id, name, phone)
restaurants(id, name)
menu_items(id, restaurant_id, name, price)
orders(id, customer_id, restaurant_id, created_at)
order_items(order_id, menu_item_id, qty, unit_price_at_order)   ← price copied deliberately: history
```
That last column is **deliberate denormalization**: an order must keep the price paid, not today's price. Normalize by default; denormalize on purpose, for history or read speed ([10.8 Normalization vs denormalization](#108-normalization-vs-denormalization)).

### 9.3 SQL you must be able to write

Most "write a query" questions use a handful of patterns. All of these run on SQLite (window functions included):

```python
import sqlite3

db = sqlite3.connect(":memory:")
db.executescript("""
CREATE TABLE employees (id INTEGER PRIMARY KEY, name TEXT, dept TEXT, salary INTEGER, manager_id INTEGER);
INSERT INTO employees VALUES
  (1, 'Asha',  'eng',   300, NULL),
  (2, 'Ravi',  'eng',   250, 1),
  (3, 'Meera', 'eng',   250, 1),
  (4, 'John',  'sales', 200, 1),
  (5, 'Sara',  'sales', 180, 4),
  (6, 'Ravi',  'ops',   150, 1);
CREATE TABLE orders (id INTEGER PRIMARY KEY, employee_id INTEGER, amount INTEGER, created_at TEXT);
INSERT INTO orders VALUES (1, 4, 100, '2025-05-01'), (2, 4, 50, '2025-05-02'), (3, 5, 70, '2025-05-02');
""")
q = lambda sql: db.execute(sql).fetchall()

# 1. Second highest DISTINCT salary
assert q("SELECT MAX(salary) FROM employees WHERE salary < (SELECT MAX(salary) FROM employees)") == [(250,)]

# 2. Nth highest salary (N = 3) with DENSE_RANK — ties share a rank
assert q("""SELECT DISTINCT salary FROM (
              SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS r FROM employees)
            WHERE r = 3""") == [(200,)]

# 3. Highest-paid employee(s) in each department
assert q("""SELECT dept, name FROM (
              SELECT dept, name, RANK() OVER (PARTITION BY dept ORDER BY salary DESC) AS r FROM employees)
            WHERE r = 1 ORDER BY dept, name""") == [("eng", "Asha"), ("ops", "Ravi"), ("sales", "John")]

# 4. Departments with more than 2 people and their average salary (WHERE filters rows, HAVING filters groups)
assert q("SELECT dept, COUNT(*), AVG(salary) FROM employees GROUP BY dept HAVING COUNT(*) > 2") == [("eng", 3, 800 / 3)]

# 5. Duplicate names
assert q("SELECT name, COUNT(*) FROM employees GROUP BY name HAVING COUNT(*) > 1") == [("Ravi", 2)]

# 6. Employees with no orders (anti-join: LEFT JOIN … IS NULL, or NOT EXISTS)
assert q("""SELECT e.name FROM employees e LEFT JOIN orders o ON o.employee_id = e.id
            WHERE o.id IS NULL ORDER BY e.id""") == [("Asha",), ("Ravi",), ("Meera",), ("Ravi",)]

# 7. Self join: employee and their manager
assert q("""SELECT e.name, m.name FROM employees e JOIN employees m ON e.manager_id = m.id
            WHERE e.id = 5""") == [("Sara", "John")]

# 8. Running total of order amounts by date (window frame)
assert q("""SELECT created_at, SUM(amount) OVER (ORDER BY created_at, id) FROM orders""") == \
       [("2025-05-01", 100), ("2025-05-02", 150), ("2025-05-02", 220)]

# 9. Each employee's salary vs department average (window without collapsing rows).
#    Gotcha: window functions run AFTER WHERE — filtering first would average over one row.
wrong = q("SELECT salary - AVG(salary) OVER (PARTITION BY dept) FROM employees WHERE id = 2")
assert wrong == [(0.0,)]
row = q("""SELECT name, diff FROM (
             SELECT id, name, salary - AVG(salary) OVER (PARTITION BY dept) AS diff FROM employees)
           WHERE id = 2""")
assert round(row[0][1], 1) == round(250 - 800 / 3, 1)

# 10. Org chart: everyone under Asha (recursive CTE)
assert q("""WITH RECURSIVE reports(id) AS (
              SELECT id FROM employees WHERE manager_id = 1
              UNION ALL
              SELECT e.id FROM employees e JOIN reports r ON e.manager_id = r.id)
            SELECT COUNT(*) FROM reports""") == [(5,)]
```

**Execution order of a SELECT** (explains why you can't use a column alias in WHERE but can in ORDER BY):

```text
FROM / JOIN → WHERE → GROUP BY → HAVING → SELECT (window functions here) → DISTINCT → ORDER BY → LIMIT
```

### 9.4 Joins: what they return and how the database runs them

**What each join returns** (orders ⋈ customers):

| Join | Returns |
|---|---|
| INNER | Only matching pairs |
| LEFT (OUTER) | All left rows; right columns NULL when no match |
| RIGHT (OUTER) | Mirror of LEFT |
| FULL (OUTER) | All rows from both sides |
| CROSS | Every combination (n × m) — rarely intended |
| SELF | A table joined to itself (employee ↔ manager) |
| SEMI / ANTI (via EXISTS / NOT EXISTS) | Left rows that do / don't have a match, without duplicating them |

**How the database executes a join — the three algorithms:**

| Algorithm | How | Good when | Cost |
|---|---|---|---|
| **Nested loop** | For each row of the outer table, look up matches in the inner (ideally via an index) | Outer side small + index on the inner join column | O(n × log m) with index; O(n × m) without (disaster) |
| **Hash join** | Build a hash table on the smaller input's join key, probe it with each row of the bigger | Large inputs, equality joins, no useful index | O(n + m), needs memory for the hash table |
| **Merge join** | Both inputs sorted on the join key → walk them together | Inputs already sorted (index order) or need sorted output | O(n + m) after sorting |

The **query optimizer** picks one using table statistics (row counts, value distributions). A wrong estimate (stale statistics) → wrong algorithm → a query that was 10 ms becomes 30 s. That's why databases run `ANALYZE` and why "the query suddenly got slow" is often a plan change.

```python
from collections import defaultdict

orders = [(1, 10), (2, 11), (3, 10), (4, 12)]                 # (order_id, customer_id)
customers = [(10, "Asha"), (11, "Ravi")]

def nested_loop(outer, inner):
    # n × m comparisons
    return [(o, c) for o in outer for c in inner if o[1] == c[0]]

def hash_join(probe, build):
    table = defaultdict(list)
    # build on the smaller side
    for c in build:
        table[c[0]].append(c)
    return [(o, c) for o in probe for c in table.get(o[1], [])]           # n + m work

def merge_join(left, right):
    left, right = sorted(left, key=lambda r: r[1]), sorted(right)
    out, j = [], 0
    for o in left:
        while j < len(right) and right[j][0] < o[1]:
            j += 1
        k = j
        while k < len(right) and right[k][0] == o[1]:
            out.append((o, right[k]))
            k += 1
    return out

expected = sorted(nested_loop(orders, customers))
assert sorted(hash_join(orders, customers)) == expected == sorted(merge_join(orders, customers))
# order 4's customer 12 doesn't exist (inner join)
assert len(expected) == 3
```

### 9.5 Query execution and EXPLAIN

```text
SQL text → parser (syntax) → rewriter/binder (resolve tables, views)
        → optimizer: enumerate plans (index vs scan, join order, join algorithm),
                     estimate the cost of each from table statistics
        → executor: runs the chosen plan, row by row or in batches
```

**Reading a plan** (`EXPLAIN ANALYZE` in Postgres, `EXPLAIN` in MySQL/SQLite): look for
- **Seq Scan / SCAN** on a big table where you expected an index → missing or unusable index.
- **Estimated vs actual rows** far apart → stale statistics.
- **Sort** or **temp B-tree** on a large result → an index could provide the order.
- **Nested loop** over two big inputs → missing index on the join column.

**Classic reasons a query is slow:**
1. Function on an indexed column (`WHERE DATE(created_at) = …`, `LOWER(email) = …`) → rewrite as a range or use an expression index.
2. Leading wildcard `LIKE '%abc'` → full scan; use full-text search.
3. `OR` across different columns → may skip indexes; consider `UNION`.
4. Implicit type conversion (comparing a string column to a number).
5. `SELECT *` pulling large columns you don't need (prevents covering indexes).
6. N+1 queries from the application (see [5.3 GraphQL](#53-graphql)).
7. `OFFSET 100000` pagination → keyset pagination ([5.6 Pagination](#56-pagination)).

### 9.6 Views, materialized views, stored procedures and triggers

*Asked occasionally; one paragraph each.*

- **View:** a saved query you can select from like a table. No data stored; it runs every time. Used to simplify and to restrict columns per role.
- **Materialized view:** the query's **result is stored** and refreshed on demand or on a schedule. Great for expensive aggregations (daily revenue per city) at the cost of staleness. Postgres: `REFRESH MATERIALIZED VIEW CONCURRENTLY`.
- **Stored procedure:** code that runs inside the database. Fewer round trips, but business logic in the DB is harder to test, version and scale — most modern teams keep it in the application.
- **Trigger:** code the DB runs automatically on insert/update/delete (audit logs, updated_at timestamps). Hidden side effects make debugging hard; use sparingly. For "react to changes" at scale, prefer CDC ([17.8 Distributed transactions](#178-distributed-transactions)).

### 9.7 DBMS interview questions

| Question | Short answer |
|---|---|
| DELETE vs TRUNCATE vs DROP | DELETE removes chosen rows, logged per row, can roll back, fires triggers. TRUNCATE removes all rows fast (deallocates pages), minimal logging. DROP removes the table itself |
| WHERE vs HAVING | WHERE filters rows before grouping; HAVING filters groups after GROUP BY |
| UNION vs UNION ALL | UNION removes duplicates (extra sort/hash); UNION ALL keeps them and is faster |
| Primary key vs unique key | PK: one per table, never NULL, often the clustered index. Unique: many per table; most databases allow multiple NULLs (SQL Server allows one) |
| Clustered vs non-clustered index | Clustered: the table rows are stored in index order (one per table, InnoDB's PK). Non-clustered: separate structure pointing to rows |
| CHAR vs VARCHAR | Fixed vs variable length; use VARCHAR/TEXT unless values are truly fixed-width |
| NULL behaviour | `NULL = NULL` is unknown (not true) → use `IS NULL`; `COUNT(col)` skips NULLs, `COUNT(*)` doesn't |
| What is a deadlock in a DB? | Two transactions each hold a row lock the other needs; the DB detects the cycle and aborts one → app must retry. Avoid by locking rows in a consistent order |
| OLTP vs OLAP | See [11.7 OLTP vs OLAP](#117-oltp-vs-olap) |
| When to denormalize | Read-heavy paths that would otherwise need expensive joins; history that must not change (prices at purchase time) |

### 9.8 A story worth knowing: Uber, Postgres and write amplification

In 2016 Uber published "Why Uber Engineering Switched from Postgres to MySQL". Their tables were update-heavy and carried many indexes. In Postgres an update writes a new version of the row, and every index that points at the row's physical location has to be updated too — so one logical change turned into many disk writes (**write amplification**). Their replication shipped those physical changes between data centers, multiplying bandwidth.

The interesting part for interviews isn't "MySQL beat Postgres" — plenty of large companies run Postgres happily, and Postgres has features (like HOT updates) that avoid some of this cost. It's that **the shape of your writes** (how often rows change, how many indexes they touch) should drive the choice of engine and schema. When you add an index in a design discussion, say what it costs on writes.

---

## 10. Databases: relational internals

### 10.1 ACID

A **transaction** groups several statements into one unit. ACID describes what the database promises about it. Example: transfer ₹100 from account A to B:

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 'A';
UPDATE accounts SET balance = balance + 100 WHERE id = 'B';
COMMIT;
```

- **Atomicity:** both updates happen, or neither. If the server crashes after the first `UPDATE`, recovery undoes it — money never disappears.
- **Consistency:** constraints hold before and after (`CHECK (balance >= 0)`, foreign keys, unique keys). A transaction that would break one is rejected.
- **Isolation:** another transaction never sees the half-done state (A debited, B not yet credited). How strictly this is enforced is the **isolation level** ([10.4](#104-isolation-levels-and-anomalies)).
- **Durability:** once `COMMIT` returns, the change survives a power cut — because it's in the WAL on disk ([10.2](#102-how-a-write-is-made-durable-wal)).

### 10.2 How a write is made durable: WAL

Writing data pages in place on every commit would mean many random disk writes (slow). Instead databases use a **write-ahead log (WAL)**:

```text
COMMIT of "UPDATE accounts … id='A'":
1. Append a log record "page 17, account A: 500 → 400" to the WAL  (sequential, fast)
2. fsync the WAL   ← the durability point; COMMIT returns now
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
        # RAM is gone; start from disk pages
        self.memory = dict(self.pages)
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

### 10.3 MVCC (multi-version concurrency control)

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

### 10.4 Isolation levels and anomalies

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
SELECT stock FROM products WHERE id = 9 FOR UPDATE;   -- others wait here
-- application checks the value it read (e.g., stock > 0), then:
UPDATE products SET stock = stock - 1 WHERE id = 9;
COMMIT;

-- 3. Optimistic lock: version column, retry on conflict
-- 0 rows → someone else won
UPDATE products SET stock = 9, version = version + 1 WHERE id = 9 AND version = 4;

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
# should be 8
assert db.execute("SELECT stock FROM products WHERE id = 9").fetchone()[0] == 9

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

### 10.5 Locks inside the database: 2PL, row, gap and next-key locks

*Common in DBMS rounds; useful in design rounds when you explain why a query blocked.*

MVCC lets readers avoid locks, but **writers still lock**. How a database decides what to lock explains most "why did this query wait?" questions.

**Two-phase locking (2PL):** a transaction acquires locks as it goes (growing phase) and releases them only at the end (shrinking phase — in practice at commit, called *strict* 2PL). It guarantees serializability, at the cost of blocking and deadlocks. Classic databases used it for everything; modern ones combine MVCC for reads with locks for writes.

**Lock granularity:**

| Lock | Protects | Example |
|---|---|---|
| Table lock | Whole table | `ALTER TABLE`, `LOCK TABLE`, some bulk operations |
| Row lock | One row | `UPDATE … WHERE id = 9`, `SELECT … FOR UPDATE` |
| Gap lock (MySQL InnoDB) | The *gap* between index entries, so nobody can insert there | Range query under REPEATABLE READ |
| Next-key lock (InnoDB) | A row **plus** the gap before it | Default locking for range scans in InnoDB |
| Intention locks | A marker on the table saying "someone holds row locks inside" | Lets a table-lock request see conflicts without checking every row |

**Why InnoDB uses gap locks:** under REPEATABLE READ, `SELECT … WHERE price BETWEEN 100 AND 200 FOR UPDATE` must stop another transaction from **inserting** a new row with price 150 — otherwise a second read would see a phantom. Locking the gaps in the index range prevents that insert. Side effect: inserts into "nearby" ranges can block unexpectedly, and deadlocks between inserts get more likely. Postgres avoids gap locks and handles phantoms through snapshots plus serializable-snapshot checks instead.

**Shared vs exclusive:** shared (S) locks allow other readers but not writers (`SELECT … FOR SHARE`); exclusive (X) locks block everyone (`FOR UPDATE`, writes).

**Deadlocks in the database:** transaction A locks row 1 then wants row 2; B locks row 2 then wants row 1. The database detects the cycle and aborts one with an error — your code must **retry** that transaction. Prevention: lock rows in a consistent order (e.g., sort IDs), keep transactions short, and index the columns you filter on (an unindexed `UPDATE … WHERE` may lock far more rows than you think).

### 10.6 Indexes

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
# no index: read every row
assert "SCAN" in plan(q)
db.execute("CREATE INDEX idx_user_status_time ON orders (user_id, status, created_at)")
assert "SEARCH" in plan(q) and "idx_user_status_time" in plan(q)          # index seek
# no separate sort step
assert "TEMP B-TREE" not in plan(q)
# leftmost column missing → scan
assert plan("SELECT id FROM orders WHERE status = 'PAID'").startswith("SCAN")
```

### 10.7 B-tree vs LSM-tree storage engines

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
        # newest first; each is sorted (key, value)
        self.sstables: list[list[tuple]] = []
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
# third flush (includes b's tombstone)
lsm.put("f", 6); lsm.put("g", 7)
lsm.compact()
assert len(lsm.sstables) == 1 and lsm.get("b") is None and lsm.get("c") == 3
# tombstoned b dropped
assert [k for k, _ in lsm.sstables[0]] == ["a", "c", "d", "e", "f", "g"]
```

### 10.8 Normalization vs denormalization

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

### 10.9 Connection pooling

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

### 10.10 Lessons from production databases

- **Postgres VACUUM and transaction-ID wraparound:** MVCC keeps old row versions until VACUUM cleans them. Several companies (Sentry and Mailchimp's Mandrill among the public write-ups) have had serious outages when vacuum fell behind and Postgres approached transaction-ID wraparound, forcing the database into a protective read-only mode until a long vacuum finished. Lesson: monitor vacuum progress and table bloat; avoid very long-running transactions.

- **InnoDB's clustered index** is why MySQL users are told to prefer sequential primary keys: random UUIDv4 keys insert all over the B+ tree, causing page splits and poor cache locality. This is one reason time-ordered IDs (Snowflake, ULID, UUIDv7 — standardized in RFC 9562 in 2024) became popular.
- **LSM-trees at scale:** Google's Bigtable paper (2006) popularized memtables + SSTables + Bloom filters; that design lives on in HBase, Cassandra, LevelDB and RocksDB (built at Facebook from LevelDB and now embedded in many databases).

---

## 11. Databases: NoSQL and data modeling

### 11.1 Choosing a database

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

### 11.2 Wide-column (Cassandra) modeling

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
-- one partition, already sorted
WHERE conversation_id = ? AND month = 202505 LIMIT 50;

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
- **Tunable consistency per query:** `ONE` (fast), `QUORUM` (majority of replicas), `ALL`. `QUORUM` writes + `QUORUM` reads → reads see the latest write ([17.4](#174-quorums)).
- Updates of the same row resolve by **last-write-wins** using timestamps.

### 11.3 DynamoDB modeling

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
        hi = bisect.bisect_left(rows, (sk_prefix + "￿",))     # "￿": highest code point
        out = rows[lo:hi][::-1] if reverse else rows[lo:hi]
        return [sk for sk, _ in out[:limit]]

t = SingleTable()
t.put("USER#42", "PROFILE", {"name": "Asha"})
t.put("USER#42", "ORDER#2025-05-03#9007", {"total": 120})
t.put("USER#42", "ORDER#2025-04-28#8800", {"total": 300})
t.put("USER#42", "ORDER#2025-05-01#9001", {"total": 498})
assert t.query("USER#42", "ORDER#2025-05") == ["ORDER#2025-05-01#9001", "ORDER#2025-05-03#9007"]
# latest order
assert t.query("USER#42", "ORDER#", reverse=True, limit=1) == ["ORDER#2025-05-03#9007"]
# whole collection
assert len(t.query("USER#42")) == 4
```

### 11.4 Document (MongoDB) modeling

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
- Index the fields you filter and sort by; compound index rules are the same leftmost-prefix rules as SQL ([10.6](#106-indexes)).

### 11.5 Graph databases

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

What "index-free adjacency" looks like on disk:

```text
Node records (fixed size)          Relationship records (fixed size)
┌────────────┬──────────┐          ┌────┬──────┬──────┬───────────┬───────────┐
│ #42 Asha   │ first: r7│ ───────► │ r7 │ from │ to   │ next(42): │ next(57): │
└────────────┴──────────┘          │    │ #42  │ #57  │ r9        │ r3        │
                                   └────┴──────┴──────┴───────────┴───────────┘
"Asha's friends" = start at r7, follow next(42) → r9 → … until null.
Record N lives at offset N × record_size, so each hop is one direct read.
```

### 11.6 Time-series and columnar

**Time-series databases** store `(series, timestamp, value)`, e.g. `cpu{host="web-3", region="ap-south"} @ 12:00:05 = 71.2`.
- Writes are append-only and arrive in time order; queries are "aggregate this series over a time range".
- **Compression:** consecutive timestamps are nearly evenly spaced and values change slowly → delta-of-delta and XOR encoding get ~1–2 bytes per point instead of 16.
- **Downsampling + retention:** keep raw per-second data for 15 days, per-minute averages for 90 days, hourly for years.
- **Cardinality** (number of unique series) is the scaling limit — see the worked example in [30. Problem: Metrics, logging, and monitoring system](#30-problem-metrics-logging-and-monitoring-system).

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
# row store: all 33 columns of every row
assert row_fields_read == 33 * len(rows)
# column store: only the 2 columns needed
assert col_fields_read == 2 * len(rows)
```

### 11.7 OLTP vs OLAP

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

---

## 12. Problem: URL shortener (TinyURL / bit.ly)

**How often asked:** very common · **Builds on:** [5.1 REST](#51-rest), [8.2 Read and write patterns](#82-read-and-write-patterns), [11.1 Choosing a database](#111-choosing-a-database), [17.7 Unique ID generation](#177-unique-id-generation)

**Requirements**
- Functional: create a short URL for a long URL; redirect short → long; optional custom alias; optional expiry.
- Out of scope: accounts/billing, detailed analytics dashboard (mention as an extension).
- Non-functional: redirect p99 < 50 ms; 99.99% available for redirects; short codes unique; codes shouldn't be trivially enumerable.

**Estimates**
- Writes: 100M/day ≈ 1.2K/s average (peak ~3K/s). Reads at 100:1 → ≈ 120K/s average, ~300K/s peak.
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

- **Choose:** range allocation + base62. To make codes hard to enumerate casually, run each ID through a **permutation of exactly [0, 62^7)** before encoding — for example, multiply by a secret number coprime to 62^7 (mod 62^7), or use a small Feistel network with a secret key. Every ID maps to a unique 7-character code. This is obfuscation, not security: anyone who sees enough codes could work out a simple multiplier, so never rely on codes being secret.
- Custom alias: insert with a unique constraint on `code`; 409 on conflict. Reserve words (`api`, `admin`).

**Deep dive 2: the redirect path at 120K–300K QPS**
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

#### Why not just hash the URL?

It's the first idea everyone has, so it's worth knowing exactly why it fails. Take the first 7 base62 characters of a hash: that's about 3.5 trillion possible codes. Sounds huge — but collisions follow the birthday paradox. With n codes already issued out of N possible, the expected number of colliding pairs is roughly n² / 2N:

```python
N = 62 ** 7                      # possible 7-char codes
for n in (1_000_000, 100_000_000, 1_000_000_000):
    expected_collisions = n * n / (2 * N)
    print(f"{n:>13,} URLs → ~{expected_collisions:,.0f} collisions")
assert 1_000_000 ** 2 / (2 * N) < 1               # a million URLs: probably fine
assert 1_000_000_000 ** 2 / (2 * N) > 100_000      # a billion: guaranteed, constantly
```

So hashing works only with a collision check on every insert (read before write, retry with a salt), which adds a database round trip and a race between two writers that pick the same code. A counter can't collide by construction — which is why the counter/range-allocation design wins at scale. Hashing still has one real advantage: the same long URL always maps to the same code, if that's a requirement.

#### The part people skip: abuse

Public shorteners are attractive to spammers and phishers, because a short link hides where it goes. Real services spend a surprising share of effort here: checking destinations against malware/phishing lists at creation time and periodically afterwards, rate-limiting creation per account/IP, showing interstitial warning pages for flagged links, and handling takedown requests. Mentioning this unprompted is a cheap way to show product sense.

---

## 13. Problem: Ticket booking (BookMyShow / Ticketmaster)

**How often asked:** very common · **Builds on:** [10.4 Isolation levels and anomalies](#104-isolation-levels-and-anomalies), [5.7 Idempotency](#57-idempotency), [8.4 Cache problems you must know](#84-cache-problems-you-must-know)

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

#### Why the database, and not a distributed lock per seat?

A tempting design: acquire a Redis lock `lock:show:42:seat:A7`, check the seat in the DB, update it, release the lock. It adds a second system that must agree with the database, and the lock can expire while the holder is paused ([17.9 Distributed locks](#179-distributed-locks)) — then two users both "own" the seat. The conditional `UPDATE … WHERE status = 'AVAILABLE'` makes the database itself the arbiter: one statement, one source of truth, no lease timing to reason about. Reach for Redis holds only when contention is so extreme that the database can't keep up, and then treat Redis as a fast pre-filter with the DB still deciding.

#### What really breaks on sale day

The hard part of a big on-sale isn't the booking transaction — it's that demand is enormous for a few minutes and much of it is automated. When Taylor Swift's Eras Tour presale opened on Ticketmaster in November 2022, demand (including large amounts of bot traffic) far exceeded what the system had been prepared for; queues stalled, the general sale was cancelled, and the incident became a public controversy. The design lessons that came out of incidents like this are exactly the ones in this chapter: a **virtual waiting room** that admits users at a rate the inventory system can handle, **bot filtering before** the queue, per-account limits, and treating "fair queueing" as a product requirement rather than an afterthought.

---

## 14. Problem: Hotel / Airbnb booking

**How often asked:** common · **Builds on:** [9.1 Tables, keys and relationships](#91-tables-keys-and-relationships), [10.4 Isolation levels and anomalies](#104-isolation-levels-and-anomalies), [5.7 Idempotency](#57-idempotency), [34.3 Elasticsearch architecture](#343-elasticsearch-architecture)

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
  -- needs: CREATE EXTENSION btree_gist;  (lets a GiST index handle "=" on plain columns)
  WHERE (status IN ('PENDING', 'CONFIRMED'));
  ```
  The DB itself rejects overlapping bookings.
- Pending booking holds dates for 15 min while payment completes; then confirm or release.

**Hotel variant:** room *types* with counts instead of unique listings → inventory per (hotel, room_type, date): `UPDATE ... SET booked = booked + 1 WHERE booked < total` for every night in one transaction. Overbooking allowance is a business rule (e.g., 105%).

#### Two ways to stop double bookings — and why you might pick each

- **One row per (listing, night)** and update all nights of the stay in one transaction. Easy to understand, makes "is anything free in May?" a simple query, and lets you price each night differently. Cost: a 30-night stay touches 30 rows, and you must pre-create rows for the calendar.
- **One row per booking with a range-exclusion constraint** (`EXCLUDE USING gist (listing_id WITH =, stay WITH &&)` in Postgres). The database refuses overlapping ranges itself; no calendar rows to maintain. Cost: Postgres-specific, and "find free listings" needs range queries.

Neither is wrong — the first is common when per-night pricing and availability calendars are core features (as in hotel inventory systems); the second is elegant when bookings are the main thing you store.

#### Paying exactly once

Airbnb wrote about this in "Avoiding Double Payments in a Distributed Payments System" (2019): payments flow through several services, and a retry at any hop could charge a guest twice. Their answer was an idempotency library used across services — the same idempotency-key pattern described in [5.7 Idempotency](#57-idempotency), applied consistently rather than per endpoint. The interview takeaway: in booking systems, the booking itself and the payment are two different "exactly once" problems, and you need both.

---

## 15. Replication

### 15.1 Why

**Replication** = keeping copies of the same data on several machines.
- **Availability:** if the primary dies, a replica takes over.
- **Read scaling:** spread reads across replicas.
- **Latency:** a replica close to users (Mumbai, Frankfurt).
- **Isolation:** run backups and reports on a replica without slowing the primary.

### 15.2 Leader-follower (primary-replica)

```text
writes ──► Leader (primary) ──WAL stream──► Follower 1 ──► reads
                             └─WAL stream──► Follower 2 ──► reads
```
All writes go to the leader; it streams its log to followers, which replay it. Reads can go to followers.

**Synchronous vs asynchronous — when does the leader say "committed"?**

```text
Synchronous:  client → leader writes → waits for follower's "got it" → OK to client
              + no data loss if the leader dies
              − every write waits for the slowest follower; follower down = writes stall

Asynchronous: client → leader writes → OK to client → (follower catches up a bit later)
              + fast writes
              − leader dies before shipping → its last writes are LOST on failover

Semi-sync:    wait for at least ONE follower; others async      ← common compromise
```

### 15.3 Replication lag problems

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
# stale: replica lag
assert db.read("asha", "photo:asha", now=0.05) is None
# routed to leader
assert db.read("asha", "photo:asha", now=0.05, read_your_writes=True) == "new.jpg"
# others may see old data briefly
assert db.read("ravi", "photo:asha", now=0.05, read_your_writes=True) is None
# replica caught up
assert db.read("ravi", "photo:asha", now=0.5) == "new.jpg"
```

### 15.4 Failover

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
- **Split brain:** a network partition makes the old leader *look* dead; a new leader is promoted; both accept writes → conflicting data. Prevent with a consensus system (etcd/ZooKeeper) deciding who is leader, **fencing** (the old leader's writes are rejected by token, [17.9](#179-distributed-locks)), or STONITH ("shoot the other node in the head" — power it off).
- **Timeout too short:** a GC pause or brief network blip triggers a needless failover (which itself causes disruption). Too long: minutes of downtime.

Managed databases (Amazon RDS Multi-AZ, Cloud SQL, Aurora) automate this with a synchronous standby, so no committed data is lost. Failover typically takes a minute or two for a classic standby instance; newer cluster designs (e.g., RDS Multi-AZ DB clusters, Aurora) advertise well under a minute.

### 15.5 Multi-leader

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

### 15.6 Leaderless (Dynamo-style)

No leader: the client (or any node acting as coordinator) sends each write to **N** replicas and each read to several replicas.

```text
N = 3 replicas, W = 2, R = 2

write(x = 5) ──► replica A ✓, replica B ✓, replica C ✗ (down)   → 2 acks ≥ W → success
read(x)      ──► replica B (x=5, v2), replica C (x=3, v1)        → 2 replies ≥ R → newest wins → 5
                 (and C gets fixed: "read repair")
```
Because **R + W > N** (2 + 2 > 3), every read set overlaps every write set in at least one replica → the read sees the latest acknowledged write. Details and code in [17.4](#174-quorums).

Keeping replicas in sync over time:
- **Read repair:** fix stale replicas noticed during reads.
- **Hinted handoff:** if C is down, another node holds C's writes ("hints") and delivers them when C returns.
- **Anti-entropy:** replicas periodically compare **Merkle trees** of their data to find and sync only the differing ranges ([chapter 35](#35-special-data-structures)).

Used by Cassandra, ScyllaDB, Riak, and DynamoDB's internals.

### 15.7 When failover goes wrong: GitHub, October 2018

On 21 October 2018, routine network maintenance cut connectivity between a GitHub network hub and their primary US East Coast data center for **43 seconds**. Their automated failover (Orchestrator, managing MySQL) reacted as designed: it promoted database primaries in their US West Coast data center. When connectivity returned, writes had been accepted in **both** places for some clusters, with a few seconds of writes on the old primaries that hadn't replicated. Reconciling that safely took GitHub about **24 hours** of degraded service, restoring from backups and replaying writes.

Everything in [15.4 Failover](#154-failover) shows up in this one incident: a brief partition looks like a failure, failover promotes across regions, writes diverge, and the lost/duplicated writes have to be reconciled by hand. GitHub's post-incident write-up described changing their failover tooling so that a cross-region promotion would no longer happen automatically in a situation like this — a good example of choosing consistency (and some unavailability) over automatic availability.

---

## 16. Partitioning and sharding

### 16.1 Terms

- **Partitioning:** splitting one dataset into pieces.
- **Sharding:** putting those pieces on **different machines** (horizontal partitioning across nodes). Each shard holds a subset of rows and handles their reads and writes.
- **Vertical partitioning:** splitting by columns or by feature — move big blob columns out, or move the `orders` tables to their own database.
- Postgres "declarative partitioning" (`orders_2025_01`, `orders_2025_02`) splits a table **within one server** — great for dropping old data and pruning scans, but it's not sharding.

### 16.2 When to shard

Sharding adds a lot of complexity (cross-shard queries, transactions, rebalancing). Shard only when one machine truly can't cope:
- Data doesn't fit (many TBs), or
- Write throughput exceeds what one primary can handle.

Try first: better indexes and queries → caching → read replicas → bigger machine → archiving old data → splitting by feature (vertical). Many large companies run on a well-tuned single primary + replicas for a long time.

### 16.3 Strategies

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

### 16.4 Choosing the shard key (most important sharding decision)

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

### 16.5 Consistent hashing in detail

**The problem with `hash(key) % N`:** going from 4 to 5 servers changes the result for ~80% of keys → a cache cluster suddenly misses on almost everything, or a database must move most of its data.

**Consistent hashing:**
1. Imagine a ring of numbers 0 … 2³² − 1.
2. Hash each **server** to several points on the ring.
3. Hash each **key** to a point; walk clockwise; the first server you meet owns the key.

```text
              0
         S1 ●───────● S2
        /               \
   key k1 ●               ● S3      k1 walks clockwise (upwards) → meets S1 first
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
# vnodes keep load roughly even
assert all(0.12 < share < 0.28 for share in load.values())
# moved keys only go to the new node
assert all(after[k] == "s5" for k in keys if before[k] != after[k])
```

Redis Cluster uses a fixed set of **16,384 hash slots** instead of a ring; nodes own ranges of slots, and rebalancing moves whole slots. Same goal, simpler bookkeeping.

### 16.6 Problems after sharding

**1. Cross-shard queries.** "Top 10 orders by amount across all users" with orders sharded by `user_id` → ask every shard for its top 10, merge (**scatter-gather**). Slow and fragile at 100 shards. Fix: maintain a separate read model for that query (a search index, an analytics store, a table keyed differently).

**2. Cross-shard transactions.** Moving money between users on different shards can't use one local transaction → design to avoid it, or use a saga / 2PC ([17.8](#178-distributed-transactions)).

**3. Unique IDs.** Each shard's auto-increment would collide → use Snowflake IDs or UUIDv7 ([17.7](#177-unique-id-generation)).

**4. Hot partitions.** One key gets disproportionate traffic (a celebrity's posts, a viral product). Fix: **salt** the key — write to `post_likes:9#0 … #9` (10 sub-keys on different shards), read all 10 and sum; or give that key a dedicated shard/cache.

**5. Resharding (adding capacity).**
- **Pre-split into many logical shards** (e.g., 4,096) mapped onto a few physical servers. To scale, move whole logical shards to new servers — no row-level rehashing. This is how Redis Cluster slots and many sharded MySQL setups work.
- **Online migration:** start double-writing to old and new layouts → backfill existing data → verify → switch reads → stop old writes.

### 16.7 How companies actually sharded

- **Instagram (2012):** thousands of logical shards as PostgreSQL schemas, packed onto a few physical servers, with the shard number embedded in each ID ("Sharding & IDs at Instagram").
- **Pinterest (2015, "Sharding Pinterest: How we scaled our MySQL fleet"):** the same two ideas on MySQL — many virtual shards mapped onto fewer machines, and 64-bit IDs that encode the shard ID, so any object ID can be routed without a lookup. They deliberately avoided cross-shard joins and kept the design simple enough to operate.
- **Notion (2021, "Herding elephants: Lessons learned from sharding Postgres at Notion"):** they split their single Postgres into 480 logical shards across 32 physical databases, sharding by workspace so a workspace's data stays together. The interesting part is the migration: double-writing to old and new databases, backfilling, verifying, then cutting over — the online resharding recipe from [16.6 Problems after sharding](#166-problems-after-sharding).
- **Figma (2023–2024):** first split their database *vertically* (moving groups of tables to separate Postgres instances), and only when the largest tables outgrew a single machine did they shard *horizontally*, building a proxy layer that routes queries by shard key. A good illustration of the "try simpler things first" ordering in [16.2 When to shard](#162-when-to-shard).

The repeated pattern: **many logical shards on fewer machines**, a shard key that matches how the product is used (user, workspace), IDs or a lookup that make routing trivial, and a careful online migration.

---

## 17. Distributed systems core

### 17.1 Why distributed is hard

On one machine, a function call either returns or throws. Across a network, a request can:

```text
Client ──request──► Server
  1. lost on the way               → server never saw it
  2. server crashed while working  → maybe half done
  3. server did it, reply lost     → work done, client thinks it failed
  4. server is just slow (GC pause, overload) → reply comes after the client gave up
```
From the client's side, **all four look identical: a timeout.** You can't tell "slow" from "dead", and you don't know whether the action happened. Add clocks that disagree between machines and nodes that pause unpredictably — that's why distributed systems need retries **plus** idempotency, timeouts, replication, consensus, and careful ordering.

### 17.2 CAP and PACELC

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

Real systems mix both per feature — e.g., a shopping cart can be AP (the Dynamo paper's example), while the payment step must be CP.

### 17.3 Consistency models (strong → weak)

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

### 17.4 Quorums

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

    # replicas that accepted the write
    def write(self, key, value, replica_ids):
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
# R + W > N: every combination reads "new"
assert always_fresh

s = QuorumStore(3)
s.write("x", "old", range(3))
s.write("x", "new", [0])                                     # W = 1
# R = 1: R + W = 2 ≤ 3 → stale read possible
assert s.read("x", [1]) == "old"
```

Caveats: concurrent writes, failed partial writes and "sloppy" quorums (writes accepted by stand-in nodes during failures) weaken the guarantee in practice — quorums alone aren't full linearizability.

### 17.5 Consensus and leader election

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

### 17.6 Time and ordering

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

# cart created in Mumbai       {mumbai: 1}
v0 = bump({}, "mumbai")
# item added in Mumbai          {mumbai: 2}
v1 = bump(v0, "mumbai")
# item added in Frankfurt from v0 {mumbai: 1, frankfurt: 1}
v2 = bump(v0, "frankfurt")
assert compare(v0, v1) == "a happened before b"
# neither saw the other → merge (e.g., union of items)
assert compare(v1, v2) == "concurrent (conflict)"
merged = bump({n: max(v1.get(n, 0), v2.get(n, 0)) for n in set(v1) | set(v2)}, "mumbai")
assert compare(v1, merged) == "a happened before b" and compare(v2, merged) == "a happened before b"
```

**Practical answer in system design:** don't rely on clocks for correctness. Get ordering from **one sequencer per entity**: a database sequence, a per-conversation counter, or a Kafka partition (all events for one key go through one ordered log). Google Spanner is the exception: it uses atomic clocks/GPS to bound clock error (TrueTime) and waits out that uncertainty at commit.

### 17.7 Unique ID generation

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
┌──────┬───────────────────────────┬──────────────────┬─────────────────────┐
│ sign │ 41 bits: ms since a       │ 10 bits:         │ 12 bits: sequence   │
│ (0)  │ custom epoch (~69 years)  │ machine id (1024)│ (4096 per ms)       │
└──────┴───────────────────────────┴──────────────────┴─────────────────────┘
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
                # 4096 IDs used this ms → wait for next ms
                if self.sequence == 0:
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
# unique + time-ordered
assert len(set(ids)) == 4 and ids[:3] == sorted(ids[:3]) and later > max(ids)
assert Snowflake.parse(ids[3]) == {"ms": 1_746_000_000_000, "machine": 2, "seq": 0}
t[0] -= 5
try:
    gen_a.next_id(); assert False
except RuntimeError:
    pass
```

### 17.8 Distributed transactions

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
        # publish
        broker.append({"event_id": row_id, "type": event, **json.loads(payload)})
        with db:
            db.execute("UPDATE outbox SET published = 1 WHERE id = ?", (row_id,))

place_order(1)
try:
    place_order(2, fail_midway=True)
except RuntimeError:
    pass
relay_once()
# order 2 rolled back…
assert db.execute("SELECT id FROM orders").fetchall() == [(1,)]
# …and no ghost event
assert broker == [{"event_id": 1, "type": "OrderCreated", "order_id": 1}]
```

**Change data capture (CDC):** instead of polling, read the database's replication log (Postgres logical decoding, MySQL binlog) and turn every committed row change into an event (Debezium → Kafka). Use it to update caches, search indexes and analytics without any dual writes in application code.

### 17.9 Distributed locks

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

### 17.10 Failure detection and membership

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
# 100× more nodes, only a few more rounds
assert r1k < 15 and r100k < 25 and r100k - r1k < 10
```

### 17.11 Exactly-once — the honest answer

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

### 17.12 Backpressure

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

### 17.13 Coordinating concurrent work across servers

In-process locks protect memory **inside one process**. Production runs many processes on many machines, so shared state needs coordination in a shared system. The techniques are spread across this book; this table is the one-page summary. Thread-level locking inside one process is in the [LLD guide's concurrency chapters](lld-interview-guide.md#42-concurrency-what-gets-asked).

| Problem | First choice | Fallback / when the first doesn't fit | Details |
|---|---|---|---|
| Two requests decrement the same counter or stock | Atomic conditional update: `UPDATE … SET qty = qty - 1 WHERE id = ? AND qty > 0` | `SELECT … FOR UPDATE`, or a version column (optimistic) | [10.4 Isolation levels and anomalies](#104-isolation-levels-and-anomalies) |
| Two users claim the same seat / username / slot | Unique constraint or conditional update — the database decides | Short-lived hold with expiry | [13. Problem: Ticket booking (BookMyShow / Ticketmaster)](#13-problem-ticket-booking-bookmyshow--ticketmaster) |
| Many workers taking jobs from one table | `SELECT … FOR UPDATE SKIP LOCKED` | A real queue (SQS, Kafka) | [23. Problem: Distributed job scheduler (cron at scale)](#23-problem-distributed-job-scheduler-cron-at-scale) |
| All updates for one entity must apply in order | Single writer per key (Kafka partition by entity ID) | Version numbers; consumers ignore older versions | [25.5 Delivery semantics and ordering](#255-delivery-semantics-and-ordering) |
| Very hot counter (likes, stock in a flash sale) | Redis atomic op / Lua script | Sharded counters, write-back to the DB | [41. Problem: Distributed counter (likes, views)](#41-problem-distributed-counter-likes-views) |
| Only one instance may run a task (cron, leader-only work) | Leader election / lease in etcd or ZooKeeper | Redis lock **with fencing tokens** | [17.9 Distributed locks](#179-distributed-locks) |
| Client retries must not repeat a side effect | Idempotency key stored with a unique constraint | Dedup table for consumers | [5.7 Idempotency](#57-idempotency) |

**Interview question — your in-process lock works locally, but double bookings happen in production. Why?** Production runs several workers or containers, and each has its own lock. Move the guarantee to shared state: a conditional update or unique constraint in the database, or a distributed lock with fencing tokens.

### 17.14 Papers that shaped this chapter

You don't need to have read these, but knowing what each contributed makes the concepts stick — and interviewers enjoy it when you can say where an idea came from.

| Paper | Year | What it gave us |
|---|---|---|
| Lamport, "Time, Clocks, and the Ordering of Events in a Distributed System" | 1978 | Logical clocks; the idea that "happened before" matters more than wall-clock time |
| Google File System | 2003 | Big files split into replicated chunks, a single master for metadata — the model for HDFS |
| MapReduce | 2004 | Map → shuffle → reduce over huge datasets on commodity machines |
| Bigtable | 2006 | Wide-column storage, SSTables, memtables, Bloom filters — ancestor of HBase and Cassandra's storage |
| Chubby | 2006 | A Paxos-based lock and configuration service — the model for ZooKeeper and etcd |
| Amazon Dynamo | 2007 | Consistent hashing, sloppy quorums, hinted handoff, vector clocks — ancestor of Cassandra and Riak |
| Spanner | 2012 | Globally distributed, strongly consistent transactions using TrueTime (bounded clock uncertainty) |
| Raft, "In Search of an Understandable Consensus Algorithm" | 2014 | A consensus algorithm designed to be easier to understand than Paxos — used by etcd, Consul, CockroachDB, Kafka KRaft |

---

## 18. Problem: Unique ID generator (distributed)

**How often asked:** occasional · **Builds on:** [17.6 Time and ordering](#176-time-and-ordering), [17.7 Unique ID generation](#177-unique-id-generation), [10.6 Indexes](#106-indexes)

- **Requirements:** 64-bit, unique across 1,000+ machines, roughly time-sortable, 10K+ IDs/s per machine, no central bottleneck.
- **Design:** Snowflake — 41-bit timestamp (ms since custom epoch), 10-bit worker ID, 12-bit sequence.
- **Worker IDs:** assigned at startup from ZooKeeper/etcd (ephemeral node) or from the pod ordinal; must be unique — duplicates = duplicate IDs.
- **Same millisecond overflow:** sequence hits 4,096 → wait for the next millisecond.
- **Clock moves backwards (NTP):** refuse to generate (or wait) until the clock passes the last timestamp; alert.
- **Alternatives:** UUIDv7 (no coordination, 128-bit), DB range allocation (compact, gaps on crash).

#### Where these IDs came from

- **Twitter Snowflake (2010).** Twitter built Snowflake while moving storage from MySQL to Cassandra, which has no auto-increment. They needed IDs that were unique across many machines, roughly time-ordered (so timelines sort naturally), and fit in 64 bits. The layout in this chapter is essentially theirs, and the name stuck: Discord, for example, calls its IDs "snowflakes" and uses the same idea.
- **Instagram and Pinterest** embedded the shard number inside each ID ([16.7 How companies actually sharded](#167-how-companies-actually-sharded)). **MongoDB ObjectId** packs a timestamp, a random value and a counter into 12 bytes — the same "time + uniqueness + sequence" idea.
- **UUIDv7** (standardized in RFC 9562, 2024) puts a millisecond timestamp in the high bits of a UUID. It gives you most of Snowflake's benefits with no machine-ID coordination at all, at the cost of 128 bits. For a new system that doesn't need 64-bit IDs, it's often the simplest good choice.

#### The subtle failure: duplicate machine IDs

Snowflake's guarantee depends entirely on no two live generators sharing a machine ID. If two containers both start with `machine_id = 7` (a copy-pasted config, an autoscaling template), they can produce identical IDs in the same millisecond. Lease machine IDs from a coordination service (ZooKeeper/etcd ephemeral nodes) or derive them from something unique (a StatefulSet ordinal), and alert if a generator's clock goes backwards.

---

## 19. Problem: Distributed cache / key-value store

**How often asked:** very common · **Builds on:** [16.5 Consistent hashing in detail](#165-consistent-hashing-in-detail), [15.6 Leaderless (Dynamo-style)](#156-leaderless-dynamo-style), [17.4 Quorums](#174-quorums), [10.7 B-tree vs LSM-tree storage engines](#107-b-tree-vs-lsm-tree-storage-engines), [8.5 Redis in depth](#85-redis-in-depth)

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

#### Two famous designs, two different goals

**Amazon Dynamo (2007 paper).** Amazon's shopping cart had one overriding requirement: always accept writes, even during failures — a lost "add to cart" is lost revenue. So Dynamo chose availability over consistency: consistent hashing with virtual nodes, sloppy quorums with hinted handoff, Merkle-tree anti-entropy, and **vector clocks** that keep conflicting versions and let the application merge them (for a cart: take the union of items, which occasionally brings back a deleted item — a trade-off Amazon accepted). Almost every leaderless store since (Cassandra, Riak, Voldemort) borrows from this paper.

Note that **DynamoDB** (2012), despite the name, is a different system: a managed service where each partition has a leader and replicas agree using Multi-Paxos, offering strongly consistent reads as an option. Its 2022 USENIX paper is a good read on running a database as a service.

**Facebook's memcache (2013 paper, "Scaling Memcache at Facebook").** Here the goal was read throughput for a social graph, with MySQL as the source of truth. Problems they hit and the fixes are textbook material:
- **Stale sets:** a slow client could write an old value into the cache after a newer delete. Fix: **leases** — the cache hands a token on a miss, and only the token holder may fill the key.
- **Thundering herds:** the same lease mechanism rate-limits refills of a hot key, so thousands of misses don't all hit MySQL.
- **Invalidation at scale:** instead of the application deleting cache keys, a daemon (McSqueal) tails the MySQL commit log and sends deletes — the CDC pattern from [17.8 Distributed transactions](#178-distributed-transactions).
- **Cross-region consistency:** replicas in other regions could serve stale data, so they added "remote markers" to route certain reads to the primary region.

If an interviewer asks "design a distributed cache", walking through why Facebook needed leases is a great way to show you understand more than the happy path.

---

## 20. Problem: Payment system / digital wallet

**How often asked:** common · **Builds on:** [5.7 Idempotency](#57-idempotency), [10.4 Isolation levels and anomalies](#104-isolation-levels-and-anomalies), [17.8 Distributed transactions](#178-distributed-transactions), [17.11 Exactly-once — the honest answer](#1711-exactly-once--the-honest-answer)

**Requirements:** accept payments (cards via a PSP like Stripe/Razorpay), wallet balances and transfers, refunds, payouts to merchants, reconciliation. Non-functional: **never double-charge or lose money**, strong consistency, full audit trail, compliance (PCI — never store raw card numbers; the PSP tokenizes them).

**Data model**
```
payments:         payment_id | idempotency_key UNIQUE | merchant_id | user_id
                  | amount | currency | status | psp_ref | created_at
ledger_entries:   entry_id | txn_id | account_id | direction (DEBIT/CREDIT) | amount
                  | created_at      (append-only)
accounts:         account_id | owner
                  | type (USER_WALLET, MERCHANT, PLATFORM_FEES, PSP_CLEARING)
                  | balance (cached) | version
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
-- fixed order avoids deadlocks
SELECT * FROM accounts WHERE account_id IN (:a, :b) ORDER BY account_id FOR UPDATE;
-- check balance(a) >= amount
INSERT INTO ledger_entries ... (debit a, credit b);
UPDATE accounts SET balance = balance - :amt, version = version + 1 WHERE account_id = :a;
UPDATE accounts SET balance = balance + :amt, version = version + 1 WHERE account_id = :b;
COMMIT;
```

**Reconciliation:** daily, compare our ledger with PSP settlement reports and bank statements; mismatches go to an exceptions queue for investigation. Every serious payment system has this.

**Other points:** state machine for payments (`CREATED → PENDING → SUCCEEDED/FAILED → REFUNDED`); sync replication for the ledger DB (RPO = 0); events via outbox; fraud/risk checks before charging; payouts batched to merchants.

#### Why not just store a balance column?

`UPDATE accounts SET balance = balance - 100` looks simpler than a ledger, and many early systems started that way. The trouble shows up later: when a balance is wrong, there's no record of how it got there; refunds, chargebacks, fees and currency conversions turn into special cases; and auditors (and regulators) want every movement explained. A double-entry ledger makes the balance a **derived value** — the sum of entries — so you can always answer "why is it this number?", replay history, and reconcile against the bank. Keep a cached balance column for fast reads if you like, but treat the ledger as the truth.

#### Retries are the real enemy

Stripe's 2017 post "Designing robust and predictable APIs with idempotency" (Brandur Leach) explains why every mutating Stripe API accepts an `Idempotency-Key`: a network failure between "card charged" and "response received" is indistinguishable from "charge never happened", and the only safe client behaviour is to retry — which must not charge twice. The same discipline applies internally: every hop between your services and your payment provider needs a key that survives retries.

#### The unknown outcome

The case candidates forget: you call the payment provider and the request **times out**. You don't know whether the card was charged. Don't mark it failed and don't retry with a new key. Mark it `UNKNOWN`, query the provider for that idempotency key (or wait for its webhook), and let a reconciliation job settle anything still unknown. Saying this in an interview signals real payments experience.

---

## 21. Problem: E-commerce checkout + inventory (flash sale)

**How often asked:** common · **Builds on:** [10.4 Isolation levels and anomalies](#104-isolation-levels-and-anomalies), [8.5 Redis in depth](#85-redis-in-depth), [17.8 Distributed transactions](#178-distributed-transactions)

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

#### Why the database alone struggles: one hot row

Every buyer of the flash-sale item updates the **same** inventory row. Row locks serialize those updates, so throughput is capped by how long each transaction holds the lock:

```python
# transaction holding the row lock: update + commit
lock_hold_ms = 5
max_updates_per_sec = 1000 / lock_hold_ms
# adding app servers doesn't help: they all queue on one row
assert max_updates_per_sec == 200
```

That's why flash-sale designs move the contended counter somewhere that can decrement atomically at memory speed (Redis + Lua), admit only the winners to the slower order pipeline, and keep the database as the durable record. Another option when you must stay in the database: **split the stock into N buckets** (`stock_bucket_0 … stock_bucket_9`, 10 units each) and have buyers pick a random bucket — contention drops roughly N-fold, at the cost of occasionally reporting "sold out" while one bucket still has stock (rebalance when a bucket empties).

#### Reserve or decrement at payment time?

- **Decrement at checkout start (reservation with TTL):** the user who reaches checkout is guaranteed the item; abandoned carts hold stock until the TTL expires. Right for flash sales and limited items.
- **Decrement at payment success:** stock is never held by browsers, but two users can both pay for the last unit — one gets an apology and a refund. Acceptable for plentiful items, painful for scarce ones.

---

## 22. Problem: Stock exchange / order matching engine

**How often asked:** occasional (more common at trading and fintech companies) · **Builds on:** [17.6 Time and ordering](#176-time-and-ordering), [25.7 Event-driven patterns](#257-event-driven-patterns), [15.2 Leader-follower (primary-replica)](#152-leader-follower-primary-replica)

This one looks like a normal "high-throughput service" problem and isn't. The core requirement — **every order on a symbol must be processed in one exact, fair order** — pushes the design towards a single-threaded, in-memory component, which surprises people who reach for sharding and databases.

**Requirements**
- Clients place limit orders (buy 100 shares of X at ≤ ₹500) and market orders, and cancel them.
- Orders match by **price-time priority**: best price first; at the same price, whoever came first.
- Every trade is reported to both sides and published as market data (latest price, order book depth).
- Non-functional: deterministic and fair (same input → same trades), never lose an acknowledged order, latency in microseconds to low milliseconds, survives a machine failure without double-filling an order.

**Estimates**
- A busy exchange: thousands of symbols, millions of orders/s at peak, but **per symbol** usually tens of thousands/s — a single core can match that if it touches only memory.
- Market data fans out to many thousands of subscribers → multicast or a dedicated distribution tier.

**The order book**

For each symbol, two sides: bids (buyers, highest price first) and asks (sellers, lowest price first). Within a price level, a FIFO queue.

```text
        ASKS (sell)                      BIDS (buy)
  502.0 │ 300 (t=7)                 500.5 │ 200 (t=3), 100 (t=9)
  501.5 │ 100 (t=2), 50 (t=8)       500.0 │ 500 (t=1)
  ── spread ──  best ask 501.5 / best bid 500.5
A new BUY 120 @ 501.5 matches 100 from t=2, then 20 from t=8.
```

```python
import heapq
from dataclasses import dataclass, field
from itertools import count

@dataclass(order=True)
class Order:
    sort_key: tuple
    id: int = field(compare=False)
    side: str = field(compare=False)
    price: int = field(compare=False)        # in paise: never floats for money
    qty: int = field(compare=False)

class OrderBook:
    def __init__(self):
        # heaps; bids store -price for "highest first"
        self.bids, self.asks = [], []
        self.seq = count(1)                   # arrival sequence = time priority
        self.cancelled: set[int] = set()

    def place(self, side: str, price: int, qty: int) -> tuple[int, list[tuple]]:
        oid, trades = next(self.seq), []
        book = self.asks if side == "BUY" else self.bids
        crosses = (lambda best: price >= best.price) if side == "BUY" else (lambda best: price <= best.price)
        while qty and book:
            best = book[0]
            if best.id in self.cancelled:
                heapq.heappop(book)
                continue
            if not crosses(best):
                break
            fill = min(qty, best.qty)
            # trade at the resting order's price
            trades.append((oid, best.id, best.price, fill))
            qty, best.qty = qty - fill, best.qty - fill
            if best.qty == 0:
                heapq.heappop(book)
        # remainder rests on the book
        if qty:
            key = (-price, oid) if side == "BUY" else (price, oid)
            heapq.heappush(self.bids if side == "BUY" else self.asks, Order(key, oid, side, price, qty))
        return oid, trades

    def cancel(self, oid: int) -> None:
        # lazy removal on the next match
        self.cancelled.add(oid)

ob = OrderBook()
s1, _ = ob.place("SELL", 50150, 100)
s2, _ = ob.place("SELL", 50150, 50)
s3, _ = ob.place("SELL", 50200, 300)
b1, trades = ob.place("BUY", 50150, 120)
assert trades == [(b1, s1, 50150, 100), (b1, s2, 50150, 20)]     # price-time priority
ob.cancel(s2)
b2, trades = ob.place("BUY", 50300, 400)
assert trades == [(b2, s3, 50200, 300)]                          # cancelled s2 skipped
# remainder rests as a bid
assert ob.bids[0].qty == 100 and ob.bids[0].price == 50300
```

**Architecture**
```text
Clients ─► Gateways (auth, validation, rate limits)
         ─► Risk checks (enough cash/shares? position limits?)
         ─► Sequencer: stamps every order with a global sequence number
              and writes it to a replicated journal (append-only log)
         ─► Matching engine (one thread per symbol group, all in memory)
              ├─► trades + order updates ─► clients (via gateways)
              └─► market data publisher ─► subscribers (multicast)
Hot standby engine replays the same journal in lockstep.
```

**Deep dive: why single-threaded?**
Fairness requires one total order of events per symbol. With several threads matching the same book you'd need locks, and lock acquisition order would become your (non-deterministic) priority rule. One thread processing a sequenced stream is both faster (no locks, data stays in CPU caches) and deterministic. Scale comes from **partitioning by symbol**: different symbols run on different threads or machines. The LMAX retail exchange described this approach publicly (Martin Fowler's 2011 article "The LMAX Architecture"): business logic on a single thread processing around six million orders per second, fed by a lock-free ring buffer (the Disruptor).

**Deep dive: durability without a database in the hot path**
The engine doesn't write each order to a database. Instead the **sequencer's journal** is the source of truth: an order is acknowledged once it's durably appended (and replicated). Because matching is deterministic, the engine's state can always be rebuilt by replaying the journal from the last snapshot — event sourcing ([25.7 Event-driven patterns](#257-event-driven-patterns)). The standby replays the same journal and can take over at the exact same state; output sequence numbers let downstream systems drop duplicates after a failover.

**Deep dive: market data**
Every book change produces an event. Publish incremental updates with sequence numbers plus periodic snapshots; a subscriber that sees a gap requests a snapshot and resumes. Fan-out uses UDP multicast inside co-location data centres, and a separate tier (WebSockets, CDN-like relays) for retail apps.

**Failures**
- Matching engine crash → the standby, already replaying the journal, takes over; clients reconnect through gateways.
- Gateway crash → clients reconnect; orders already sequenced are in the journal, and clients query their order status rather than resending blindly (or resend with a client order ID, which the gateway deduplicates).
- Clock disputes → order is decided by the sequencer's numbers, never by wall-clock timestamps from different machines ([17.6 Time and ordering](#176-time-and-ordering)).

**Why not a database with row locks?** Every match would be a transaction touching the same few hot rows (the best price level), so throughput collapses to the lock rate and the order of lock acquisition decides who trades first. Databases are used *around* the engine — for accounts, settlement and history — fed from the journal.

---

## 23. Problem: Distributed job scheduler (cron at scale)

**How often asked:** common · **Builds on:** [10.4 Isolation levels and anomalies](#104-isolation-levels-and-anomalies), [17.5 Consensus and leader election](#175-consensus-and-leader-election), [17.9 Distributed locks](#179-distributed-locks)

**Requirements:** schedule one-off and recurring jobs (cron expressions), millions of jobs; run each **at least once** near its time (seconds of delay OK); retries; job history; no job lost if a node dies.

**Data model**
```
jobs:      job_id | owner | cron_expr | payload | next_run_at | status | retry_policy
           | timeout   INDEX(next_run_at) WHERE status='ACTIVE'
job_runs:  run_id | job_id | scheduled_for | status | worker_id | attempt
           | started_at | finished_at | error
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

#### Why not a cron server?

One machine running crontab is the simplest scheduler, and it fails in two predictable ways: it's a single point of failure (the box dies, nothing runs), and it can't scale past one machine's capacity. Running the same crontab on two machines to fix the first problem creates a new one — every job runs twice. The design in this chapter is what you get when you fix both: durable job definitions in a database, claiming with `SKIP LOCKED` or leases so each occurrence runs once, and workers that scale independently.

Open-source tools built on the same ideas are worth naming: **Apache Airflow** (started at Airbnb around 2014) for scheduled data pipelines with dependencies between tasks, and **Temporal** (a 2019 fork of Uber's Cadence) for durable workflows where each step's state is persisted and retried — the "orchestrated saga" from [17.8 Distributed transactions](#178-distributed-transactions) as a product.

---

## 24. Problem: Online code judge (LeetCode-style)

**How often asked:** occasional · **Builds on:** [25.1 Why asynchronous messaging](#251-why-asynchronous-messaging), [23. Problem: Distributed job scheduler (cron at scale)](#23-problem-distributed-job-scheduler-cron-at-scale), [49. Security](#49-security)

The interesting part isn't storing problems — it's **running untrusted code safely and fairly**, and surviving contest spikes.

**Requirements**
- Users submit code in several languages; the system compiles it, runs it against hidden test cases with time and memory limits, and returns a verdict (Accepted, Wrong Answer, Time Limit Exceeded, Runtime Error, Compile Error).
- Contests: thousands of users submit in the same minutes; a live leaderboard.
- Non-functional: user code must never reach other users' data, the network, or the host; verdicts must be consistent (the same code shouldn't pass, then fail, because a machine was busy).

**Estimates**
- Normal day: ~1M submissions/day ≈ 12/s. A big contest: 20K participants × a submission every few minutes → **hundreds per second** for an hour.
- Each run: up to ~2 s CPU per test × ~50 tests, but most stop early → plan for ~1–5 CPU-seconds per submission → a contest peak needs on the order of 1,000 cores of workers, scaled up before the contest starts.

**API**
```
POST /v1/submissions {problem_id, language, code}   → 202 {submission_id}
GET  /v1/submissions/{id}                           → {status, verdict, runtime_ms, memory_kb}
(or a WebSocket/SSE channel that pushes the verdict)
```

**Architecture**
```text
Client ─► API ─► submissions DB (status = QUEUED) ─► queue (per language/priority)
                                                      │
                Judge workers (autoscaled) ◄──────────┘
                  for each submission:
                    start a fresh sandbox (container + seccomp, gVisor, or microVM)
                    compile → run each test with limits → compare output
                    write verdict → DB, publish "submission done" event
Test data: object storage, cached on workers. Leaderboard: Redis sorted set.
```

**Deep dive: the sandbox**
Layers, from cheapest to strongest:
- **Resource limits** (cgroups): CPU time, memory, process count, output size, wall-clock timeout.
- **No network, read-only filesystem**, a non-root user, and a syscall allowlist (seccomp) — user code can't open sockets or fork-bomb.
- **Stronger isolation** when needed: gVisor (a user-space kernel that intercepts syscalls) or a lightweight VM such as Firecracker (built by AWS for Lambda), which boots in a fraction of a second, so each submission can get its own VM.
A fresh sandbox per submission means one user's leftover files or processes can never affect the next run.

**Deep dive: fair timing**
Measure **CPU time**, not wall-clock time, and don't pack too many runs onto one machine (noisy neighbours change cache behaviour). Pin each run to dedicated cores, use identical worker machine types, and rerun borderline TLE results once before reporting them.

**Deep dive: contest spikes**
The queue absorbs the burst; workers scale on queue depth, and are pre-warmed before a scheduled contest. Contest submissions get their own high-priority queue so practice traffic can't delay them. If the queue grows anyway, users see "Queued (position 240)" rather than timeouts.

**Failures**
- Worker dies mid-run → the queue message's visibility timeout expires and another worker reruns it; verdict writes are idempotent by submission ID ([25.5 Delivery semantics and ordering](#255-delivery-semantics-and-ordering)).
- Infinite loop or huge output → killed by limits → TLE or "Output Limit Exceeded".
- Sandbox escape attempt → defence in depth: even if one layer fails, the next (VM, no network, no credentials on the host) contains it.

**Why not run the code inside the API servers?** One malicious submission could read secrets, attack the network, or take the server down — and a CPU-heavy submission would slow every API request. Untrusted, CPU-bound work belongs in isolated, separately scaled workers behind a queue.

---

## 25. Messaging and stream processing

### 25.1 Why asynchronous messaging

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

### 25.2 Queue vs log

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

### 25.3 Kafka internals

```text
Topic "orders" (3 partitions, replication factor 3)

Partition 0: leader on broker 1, followers on brokers 2, 3   [0][1][2][3][4]…
Partition 1: leader on broker 2, followers on brokers 3, 1   [0][1][2]…
Partition 2: leader on broker 3, followers on brokers 1, 2   [0][1][2][3]…
```

**Producing:**
- A message has an optional **key**. Kafka picks the partition with `hash(key) % partitions` → **all messages with the same key land in the same partition, in order.** Key by `order_id` → every event of order 42 is processed in sequence. No key → spread across partitions (modern clients send a whole batch to one partition, then move to the next), with no ordering guarantee.
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

# toy model; real Kafka hashes keys with murmur2
class MiniKafka:
    def __init__(self, partitions: int):
        self.logs = [[] for _ in range(partitions)]
        # (group, partition) -> next offset to read
        self.committed = defaultdict(int)

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
# more consumers than partitions → idle
assert k.assign(["a", "b", "c", "d"])["d"] == []

start, batch = k.poll("email", p42)
k.commit("email", p42, start + len(batch))
assert k.poll("email", p42)[1] == []                      # email group is caught up
# analytics group reads independently
assert len(k.poll("analytics", p42)[1]) == len(batch)
```

### 25.4 RabbitMQ and SQS

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

### 25.5 Delivery semantics and ordering

Where you commit the offset/ack decides what a crash does:

```text
At-most-once:   commit offset → process   crash after commit, before processing → message LOST
At-least-once:  process → commit offset   crash after processing, before commit → processed TWICE
```
**Default to at-least-once + idempotent consumers** ([17.11](#1711-exactly-once--the-honest-answer)): losing data is worse than handling a duplicate, and duplicates are easy to neutralize.

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
                # duplicate → skip
                pass
        else:
            state["balance"] += amount
        if offset == crash_before_commit_at:
            crash_before_commit_at = None
            # "crash": offset not committed; restart re-reads this message
            continue
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
# duplicate delivery, single effect
assert state["balance"] == 175
```
(In real code the dedupe insert and the business update share one DB transaction.)

**Ordering:**
- Kafka guarantees order **within a partition** only. Key messages by the entity whose order matters (`account_id`, `order_id`): order within an entity, parallelism across entities.
- Retries can reorder (message 1 fails and goes to a retry topic while message 2 succeeds). If strict per-key order matters, block that key until message 1 succeeds, or carry version numbers and let consumers ignore older versions.

### 25.6 Failure handling patterns

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

### 25.7 Event-driven patterns

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

### 25.8 Stream processing

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
        # window already closed → too late
        if start + window <= watermark:
            late.append((event_time, key))
            continue
        open_windows[start][key] += 1
        watermark = max(watermark, event_time - allowed_lateness)
        for s in [s for s in open_windows if s + window <= watermark]:
            results[s] = dict(open_windows.pop(s))           # emit closed windows
    for s, counts in open_windows.items():                   # end of stream: flush
        results[s] = dict(counts)
    return results, late

# 50 arrives late but within lateness
events = [(5, 5, "pune"), (20, 21, "delhi"), (50, 95, "pune"),
          # event at t=10 arrives far too late
          (70, 71, "pune"), (100, 101, "delhi"), (10, 130, "delhi")]
results, late = tumbling_counts(events)
# late-but-allowed event counted in the right window
assert results[0] == {"pune": 2, "delhi": 1}
assert results[60] == {"pune": 1, "delhi": 1}
# beyond allowed lateness → side output
assert late == [(10, "delhi")]
```

**State and fault tolerance:** a stream job keeps state (counts per key per window) locally and **checkpoints** it to durable storage together with input offsets. After a crash it restores the checkpoint and re-reads from those offsets → state stays exactly right.

**Lambda vs Kappa:**
- **Lambda:** a batch layer recomputes accurate results from raw data periodically + a speed layer gives fast approximate results; merge at query time. Two codebases.
- **Kappa:** stream processing only; to recompute, replay the log through a new version of the job. One codebase.

---

## 26. Problem: Notification system

**How often asked:** very common · **Builds on:** [25.5 Delivery semantics and ordering](#255-delivery-semantics-and-ordering), [25.6 Failure handling patterns](#256-failure-handling-patterns), [5.7 Idempotency](#57-idempotency)

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
notifications:   id | idempotency_key UNIQUE | user_id | type | template_id | params
                 | priority | send_at | status
deliveries:      notification_id | channel
                 | status (QUEUED/SENT/DELIVERED/FAILED/BOUNCED) | attempts
                 | provider_msg_id | updated_at
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

#### Where people go wrong

- **Sending inside the request.** `POST /orders` that calls the SMS provider before returning couples your checkout latency and availability to an SMS vendor. Enqueue and return.
- **One queue for everything.** A 20-million-user marketing campaign sitting in front of password-reset OTPs is a real outage pattern. Separate queues (or priorities) per notification class is the single most valuable design decision here.
- **No per-user caps.** A bug that fires the same "your order shipped" event in a loop can send a customer hundreds of messages. A per-user, per-type rate limit is a cheap safety net.
- **Ignoring provider feedback.** Invalid device tokens and hard email bounces must be removed, or deliverability (and sender reputation with email providers) degrades over time.

---

## 27. Problem: Chat / messaging (WhatsApp, Slack)

**How often asked:** very common · **Builds on:** [5.4 Real-time communication](#54-real-time-communication), [11.2 Wide-column (Cassandra) modeling](#112-wide-column-cassandra-modeling), [16.4 Choosing the shard key (most important sharding decision)](#164-choosing-the-shard-key-most-important-sharding-decision), [17.6 Time and ordering](#176-time-and-ordering)

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
messages (Cassandra):    PK (conversation_id, bucket) | CK seq DESC
                         | message_id, sender_id, body, media_ref, ts
conversations (SQL/KV):  conversation_id | type | created_by | created_at
members:                 conversation_id | user_id | role | joined_at
user_inbox (KV):         PK user_id | CK conversation_id
                         | last_seq_seen, last_read_seq, muted, last_preview
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

#### Connections per server: the WhatsApp lesson

WhatsApp famously ran on Erlang, a language and runtime built for telecom switches — lightweight processes, message passing, and the ability to keep millions of mostly-idle connections per machine. In 2012 their engineering blog described pushing a single server past **2 million concurrent TCP connections**, which is how a very small team served a huge user base. For chat design that means: connection gateways are about holding many idle sockets cheaply (event-driven I/O, small per-connection memory — see the OS chapter on I/O models in the [LLD guide](lld-interview-guide.md#41-operating-systems-file-systems-io-and-ipc)), and they should do as little business logic as possible.

#### How Discord's message storage evolved

Discord's engineering blog tells one of the best storage-evolution stories:
- **2015–2017:** messages started in a single MongoDB replica set. As data grew past what fit in RAM, latency became unpredictable.
- **2017 — "How Discord Stores Billions of Messages":** they moved to **Cassandra**, partitioning messages by `(channel_id, time bucket)` — the same model as this chapter's message table. Writes became cheap and predictable.
- **2023 — "How Discord Stores Trillions of Messages":** at trillions of messages, Cassandra clusters suffered from hot partitions (a huge server announcing to everyone), compaction backlogs and garbage-collection pauses, which made on-call painful. They migrated to **ScyllaDB** (Cassandra-compatible, written in C++, no JVM GC), and put a "data services" layer written in Rust in front of the database that **coalesces concurrent requests for the same channel** into one database query — so a burst of users opening the same busy channel turns into a single read.

Notice what stayed the same: the data model barely changed across the migrations; what changed was the engine underneath and the layer that protects it from hot keys.

#### Follow-up: "now add voice and video calls"

Reuse the chat connection for **signaling** — the call invitation, accept/decline, and the WebRTC offer/answer exchange are just messages to the other user's devices (with a push notification to wake a sleeping phone). Media goes elsewhere: peer-to-peer for 1:1 calls when the network allows, through TURN relays when it doesn't, and through SFU media servers for group calls. The basics are in [5.5 Voice and video: WebRTC in five minutes](#55-voice-and-video-webrtc-in-five-minutes) and the full design in [37. Problem: Video calling (Zoom / Google Meet)](#37-problem-video-calling-zoom--google-meet).

---

## 28. Problem: News feed (Twitter / Instagram home timeline)

**How often asked:** very common · **Builds on:** [8.5 Redis in depth](#85-redis-in-depth), [25.3 Kafka internals](#253-kafka-internals), [16.6 Problems after sharding](#166-problems-after-sharding), [5.6 Pagination](#56-pagination)

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
posts (sharded by post_id):        post_id (Snowflake) | author_id | text
                                   | media_refs | created_at | deleted
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

#### Two companies, opposite choices

**Twitter** (described in Raffi Krikorian's 2013 talk "Timelines at Scale") went heavily **fan-out on write**: when you tweet, your tweet ID is inserted into the in-memory (Redis) timeline of each follower, so reading your home timeline is a single cheap lookup. Users with huge follower counts were the exception — their tweets were merged in at read time, which is exactly the hybrid in this chapter. The design fits Twitter because reads vastly outnumber writes and the timeline was (originally) mostly reverse-chronological.

**Facebook's News Feed** leaned the other way, toward **assembling the feed at read time** (their aggregator/"Multifeed" systems gather recent actions from your friends and pages, then rank them). The reasons: ranking needs fresh signals at read time anyway, the candidate set is large and varied (posts, likes, comments, page posts), and precomputing a ranked feed for every user on every action would be enormously wasteful.

The interview point: "push vs pull" isn't a matter of taste. It follows from your read/write ratio, how skewed follower counts are, and whether the feed is ranked at read time.

#### Variant: photo sharing (Instagram)

"Design Instagram" is this problem plus media:
- **Upload:** the client gets a pre-signed URL and uploads straight to object storage; an event triggers workers that resize to several sizes, strip EXIF metadata (it can contain GPS location), run moderation and generate a tiny placeholder; the post stores the resulting CDN URLs ([33. Storage: block, file, object, data lake, warehouse](#33-storage-block-file-object-data-lake-warehouse)).
- **Metadata:** posts sharded by user ID with IDs that encode the shard — Instagram's own approach, described in [16.7 How companies actually sharded](#167-how-companies-actually-sharded).
- **Likes and comments:** a counter service, not `UPDATE posts SET likes = likes + 1` on a hot row ([41. Problem: Distributed counter (likes, views)](#41-problem-distributed-counter-likes-views)); comments partitioned by post ID with cursor pagination.
- **Stories:** expire after 24 hours → storage with TTL; the "who has a new story" ring is precomputed per user, like a mini feed.

Instagram's 2011 post "What Powers Instagram" is a useful reference for technology choices: a small team ran on Django, PostgreSQL, Redis, Memcached and S3, and later sharded Postgres rather than switching databases. Well-understood tools pushed far usually beat exotic ones.

---

## 29. Problem: Collaborative editor (Google Docs)

**How often asked:** common · **Builds on:** [5.4 Real-time communication](#54-real-time-communication), [15.5 Multi-leader](#155-multi-leader), [17.6 Time and ordering](#176-time-and-ordering)

**Requirements:** many users edit the same document simultaneously; see each other's changes and cursors in real time; offline edits; version history; comments.

**Why naive approaches fail:** "last write wins" on the whole doc loses others' edits; locking paragraphs is a bad experience.

**Two families of solutions**
- **Operational Transformation (OT):** edits are operations (`insert 'x' at 5`). The server orders operations; when an operation was made on an older version, it's **transformed** against concurrent ones (if someone inserted 3 chars before position 5, my insert moves to 8). Needs a central server for ordering. Used by Google Docs.
- **CRDTs:** each character gets a unique, ordered ID (e.g., position identifiers between neighbors); operations commute, so any replica applying the same set of operations converges — no central ordering needed, great for offline/P2P. Costs metadata overhead. Used by Figma-style tools, Yjs/Automerge.

**Architecture**
```
Clients ══ WebSocket ══► Document session server
                         (every editor of doc X reaches the same server:
                          consistent hashing on doc_id)
                              ├─ in-memory doc state + pending ops
                              ├─ op log append (durable: Kafka/DB) ─► periodic snapshots (S3/DB)
                              └─ broadcast transformed ops + cursors to other clients
Load doc = latest snapshot + ops after it.  Version history = snapshots + op log.
```
- Cursors/presence are ephemeral (memory only).
- Session server failure → another server loads snapshot + op log and clients reconnect.
- Permissions checked on connect and per op; comments stored separately anchored to text ranges (anchors adjusted like ops).

#### Why Figma didn't copy Google Docs

Google Docs uses operational transformation, which grew out of research systems like Jupiter and was used in Google Wave before Docs. OT is powerful but notoriously hard to get right: every pair of operation types needs a correct transform function, and subtle bugs corrupt documents.

When Figma built multiplayer editing, their 2019 post "How Figma's multiplayer technology works" explains a different choice. A design file isn't a long text string — it's a tree of objects with properties (position, colour, text). So they used a **server-authoritative** model inspired by CRDTs: each property change is last-writer-wins per (object, property), the server orders changes, and clients apply them optimistically and reconcile. That's much simpler than general OT or full CRDTs, and good enough because two people rarely edit the same property of the same object at the same instant — and when they do, "last one wins" is acceptable.

The practical rule: pick the simplest conflict model your data can tolerate. Text needs OT or a text CRDT (Yjs, Automerge); independent properties often need only per-field last-writer-wins.

---

## 30. Problem: Metrics, logging, and monitoring system

**How often asked:** common · **Builds on:** [11.6 Time-series and columnar](#116-time-series-and-columnar), [25.3 Kafka internals](#253-kafka-internals), [25.8 Stream processing](#258-stream-processing)

**Requirements:** collect metrics (CPU, latency, business counters) and logs from 100K hosts; dashboards; alerting; query recent data fast; retain long-term cheaply.

**Metrics pipeline**
```
Agents (batch, compress) ─► Collectors ─► Kafka ─► TSDB writers
    ─► Time-series DB (sharded by series)
                                                         └─► Downsampler (1 s → 1 min → 1 h)
Query service ◄─ Grafana dashboards ; Alert evaluator (rules every 30 s)
    ─► dedup/grouping ─► PagerDuty/Slack
```
- Series = metric name + tags. **Cardinality** (unique tag combinations) is the main scaling risk — see the worked example below.
- Compression: delta-of-delta timestamps, XOR-encoded floats (Gorilla) → ~1.4 bytes per point.
- Retention tiers: raw for 15 days, 1-min rollups for 90 days, 1-h rollups for years.
- Pull (Prometheus scrapes targets) vs push (agents send) — pull is simple for discovery-driven environments; push suits short-lived jobs.

**Logs pipeline**
```
Apps (structured JSON) ─► agent (Fluent Bit/Vector) ─► Kafka ─► indexer
    ─► hot store (OpenSearch / ClickHouse / Loki) ─► S3 cold
```
- Full-text indexing everything is expensive → index only labels/fields (Loki) or columnar storage (ClickHouse) for cost.
- Sampling for debug logs; PII scrubbing at the agent.

**Alerting:** evaluate rules; avoid flapping (require N consecutive breaches); group related alerts; silence during maintenance; alert on SLO burn rate.

#### How time-series storage got so compact

Facebook's "Gorilla" paper (VLDB 2015) described an in-memory time-series database for their monitoring data. Its key trick is compression that exploits how metrics behave: timestamps arrive at nearly fixed intervals, so storing the **difference of differences** usually takes a bit or two; values change slowly, so XOR-ing each value with the previous one leaves mostly zeros. They reported averaging well under two bytes per data point instead of sixteen. Prometheus and many other TSDBs adopted the same encoding ideas.

#### The cardinality trap, concretely

A metric `http_requests_total` with labels `{service, route, status}` might produce a few thousand series. Add `user_id` as a label and it becomes millions — each with its own index entry and memory footprint — and the monitoring system that's supposed to warn you about outages falls over itself. Rule: labels are for dimensions you **aggregate by** (route, status, region); identifiers you **search for** (user, request, order) belong in logs and traces.

#### Why Loki indexes so little

Grafana Loki (2018) took the opposite approach to full-text log indexing: index only a few labels (service, environment, pod) and store the log lines compressed in object storage, then grep through the relevant chunks at query time. Queries are slower than a full-text index, but storage and ingestion costs drop dramatically — a sensible trade for logs that are mostly written and rarely read.

---

## 31. Problem: Ad click aggregation

**How often asked:** common · **Builds on:** [25.8 Stream processing](#258-stream-processing), [25.5 Delivery semantics and ordering](#255-delivery-semantics-and-ordering), [11.7 OLTP vs OLAP](#117-oltp-vs-olap)

**Requirements**
- Record every click on an ad, and report clicks per ad per minute — for advertiser dashboards (near real time) and for **billing** (must be exact).
- Queries: "clicks for ad X between T1 and T2", "top 100 ads in the last hour".
- Non-functional: billions of clicks/day; dashboard freshness within a few minutes; billing numbers exactly right (no double-counting, no loss); click redirect must stay fast.

**Estimates**
- 1B clicks/day ≈ 12K/s average, ~50K/s at peak.
- Raw event ~200 B → 200 GB/day of raw clicks; kept for reconciliation and audits.
- Aggregates: 1M active ads × 1,440 minutes/day = 1.4B rows/day if stored per minute → roll up to hourly/daily for older data.

**Click flow and API**
```text
User clicks ad → GET /click?ad=42&imp=<impression id>  ─► Click service
    1. log the event (async, to Kafka)        2. 302 redirect to the advertiser's page
```
The redirect must not wait for anything except writing to a local buffer or Kafka.

**Data**
```
raw clicks (Kafka topic, then S3/Parquet): click_id | ad_id | impression_id
    | user/device | ts | ip | ua
aggregates (OLAP: ClickHouse/Druid/Pinot): ad_id | minute | clicks | unique_users (HLL) | …
```

**Architecture**
```text
Click service ─► Kafka "clicks" (partitioned by ad_id) ─┬─► Stream job (Flink):
                                                        │     dedup by click_id → filter bots
                                                        │     → 1-min tumbling windows per ad_id
                                                        │     → upsert into OLAP store
                                                        └─► Archiver ─► S3 (raw, immutable)
Nightly batch job (Spark) over S3 ─► recompute exact daily totals ─► billing tables
Dashboards ─► Query service ─► OLAP store
```

**Deep dive: exactly-once counting**
- Duplicates come from client retries and from at-least-once delivery. Every click carries a `click_id`; the stream job keeps recently seen IDs in its state store (with a TTL longer than any retry window) and drops repeats.
- The stream job checkpoints its state and Kafka offsets together; after a crash it resumes from the checkpoint, so counts aren't doubled.
- Writes to the OLAP store are **upserts keyed by (ad_id, minute)** — replaying a window rewrites the same row instead of adding to it.

**Deep dive: late events**
Mobile clicks can arrive minutes late. Use **event time** with a watermark (e.g., 2 minutes of allowed lateness): windows are emitted when the watermark passes, and events later than that go to a side output that updates the aggregate row again (an upsert, so it's safe). Billing uses the nightly batch, which sees everything.

**Deep dive: hot ads**
A viral ad sends a huge share of clicks to one Kafka partition and one stream task. Salt the key (`ad_id#0…ad_id#9`), aggregate per salted key, then sum the ten partial counts in a second step.

**Why not just `UPDATE ads SET clicks = clicks + 1`?** At 50K writes/s concentrated on popular rows, a relational database spends its life on row-lock contention, and you lose the raw events you need for audits and fraud investigations. Keeping an immutable event log and deriving counts from it is what makes the numbers reproducible.

**Fraud and bots:** filter before billing — repeated clicks from one device/IP, data-center IP ranges, impossible click rates. Advertisers dispute bills, so keep the raw evidence.

**Failures**
- Stream job down → Kafka retains events; the job catches up from its checkpoint; dashboards are briefly stale.
- OLAP store down → the stream job retries writes; nothing is lost because Kafka and S3 hold the raw data.
- Wrong logic deployed → fix it and **replay** from Kafka (or from S3 for older data) into a fresh table.

#### Lambda or Kappa?

The batch + streaming design here is the **Lambda architecture** (named by Nathan Marz): a fast streaming path for fresh approximate numbers, and a batch path that recomputes exact numbers from raw data. Its downside is maintaining the same logic twice. Jay Kreps' 2014 essay "Questioning the Lambda Architecture" proposed **Kappa**: keep the raw events in a durable log (Kafka) and, when you need to recompute, replay the log through a new version of the streaming job. Modern stream processors with exactly-once state (Flink checkpoints) made Kappa practical for many teams — but billing-grade systems often keep a batch reconciliation anyway, because money tolerates zero drift.

---

## 32. Problem: Distributed message queue (design Kafka)

**How often asked:** occasional · **Builds on:** [25.3 Kafka internals](#253-kafka-internals), [15.2 Leader-follower (primary-replica)](#152-leader-follower-primary-replica), [17.5 Consensus and leader election](#175-consensus-and-leader-election)

**Requirements**
- Producers append messages to named topics; many independent consumer groups read them, each at its own pace, and can replay from an earlier point.
- Ordering per key; durability (no acknowledged message lost if a machine dies); retention by time or size.
- Non-functional: very high throughput (millions of messages/s per cluster), low latency (ms), horizontal scaling by adding machines.

**Estimates (to size partitions)**
- Target: 1M messages/s × 1 KB = ~1 GB/s into the cluster; with replication factor 3, ~3 GB/s of disk writes across brokers.
- One partition is consumed by one consumer per group. If a consumer can process ~10K messages/s, you need at least 1M / 10K = **100 partitions** for that topic — plus headroom for growth, because increasing partitions later changes which partition each key maps to.
- 7 days of retention: 1 GB/s × 604,800 s × 3 replicas ≈ 1.8 PB of disk.

**Core design: a partitioned, replicated log**
```text
Topic "orders" → partitions 0..99; each partition = append-only log on disk,
split into segment files (e.g., 1 GB each) plus a sparse offset index.

Partition 7:  [segment 0 (offsets 0–1.2M)] [segment 1 (1.2M–2.4M)] [active segment …]
              old segments are deleted (or compacted) whole → retention is cheap
```
- **Why it's fast:** appends and sequential reads (disks and SSDs love them), the OS page cache serves recent data from memory, producers batch and compress, and data is sent from disk to network without copying through the application (zero-copy).
- **Consumers pull** and track their own offset per partition → slow consumers don't slow anyone else; replay is "set offset back".

**Replication and the in-sync set**
```text
Partition 7: leader on broker 2, followers on brokers 5 and 8
producer ──► leader appends at offset 1000
followers fetch from the leader (like consumers) and append
ISR (in-sync replicas) = replicas caught up within a time limit, e.g. {2, 5, 8}
with acks=all: the write is acknowledged once every ISR member has it
high watermark = highest offset copied to all ISR → consumers can only read up to it
```

**Walking through a leader failure**
1. Broker 2 dies. Offsets up to the high watermark exist on 5 and 8.
2. The controller (a Raft quorum — KRaft) picks a new leader **from the ISR**, say broker 5.
3. Producers and consumers refresh metadata and talk to broker 5. Anything broker 2 had written above the high watermark was never acknowledged with `acks=all`, so no acknowledged message is lost.
4. With `min.insync.replicas=2`, if two of three replicas are down, the partition **refuses writes** rather than accepting data it can't protect — Kafka choosing consistency over availability.
5. `unclean.leader.election` (allowing a non-ISR replica to lead) trades data loss for availability — off by default.

**Delivery guarantees**
- Producer retries can duplicate → **idempotent producer** (producer ID + sequence number per partition; the broker drops repeats).
- Consumers commit offsets after processing → at-least-once; transactions can atomically write output and commit offsets (exactly-once within Kafka).

**What it deliberately doesn't do:** per-message acks and redelivery timers (that's a queue like SQS/RabbitMQ), message priorities, or delayed delivery — those are built on top with extra topics.

**Failures to mention**
- Hot partition (one key gets most traffic) → better key choice or salting.
- Consumer lag growing → more consumers (up to the partition count) or faster processing.
- Disk full → retention by size, tiered storage (older segments in object storage).

#### Why a log, and not a queue?

Kafka was built at LinkedIn (open-sourced in 2011) because they had many systems that each needed the same streams of events — page views, profile updates, database changes — and point-to-point queues between every pair didn't scale. Jay Kreps' 2013 essay "The Log: What every software engineer should know about real-time data's unifying abstraction" makes the argument: an append-only, ordered, replayable log is the one structure every consumer can read independently, at its own pace, and replay when its logic changes. Deleting messages on consumption (as a queue does) throws that away.

#### Kafka removing ZooKeeper

Kafka originally stored cluster metadata (which broker leads which partition, topic configs) in ZooKeeper. As clusters grew, that became an operational burden and a scaling limit on partition counts. KIP-500 replaced it with **KRaft**, a Raft-based metadata quorum inside Kafka itself; KRaft became production-ready in the 3.x series, and Kafka 4.0 (2025) removed ZooKeeper mode entirely. It's a nice example of "use a coordination service" evolving into "embed consensus" once a system is big enough.

---

## 33. Storage: block, file, object, data lake, warehouse

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
2. App uploads the file DIRECTLY to S3 using that URL
   (multipart for big files: parallel 8 MB parts, retry only the failed parts)
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
# expired
assert not verify("GET", url, now=1_001)
# tampered expiry
assert not verify("GET", url.replace("expires=1000", "expires=9999"), now=900)
# different video
assert not verify("GET", url.replace("/9001/", "/9002/"), now=900)
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
# data split across 3 "workers"
chunks = [orders[0:2], orders[2:4], orders[4:6]]

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

### 33.1 When object storage went down: S3, 28 February 2017

An engineer debugging a slow billing system in AWS's US-East-1 region ran a command meant to remove a small number of S3 servers. A typo removed far more, including servers for subsystems S3 needed to function. S3 in that region was unavailable for roughly four hours — and because so many services (including parts of AWS itself, like the status dashboard's icons) depended on S3 there, the impact spread widely.

AWS's follow-up changed the tool to remove capacity more slowly and refuse to take a subsystem below its minimum safe size. Two lessons for design interviews: even "eleven nines of durability" says nothing about **availability**, and a single region is a single failure domain — critical systems need a plan for a region-level outage.

---

## 34. Search

### 34.1 Inverted index

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

# toy stemmer; real engines use Porter/Snowball
def stem(word: str) -> str:
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
        # TF-IDF: rare matching terms count more
        def score(d):
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

### 34.2 Relevance scoring

Finding matches is easy; **ranking** them is the real work.

- **TF (term frequency):** a document mentioning "dosa" 5 times is probably more about dosa than one mentioning it once.
- **IDF (inverse document frequency):** a word in every document ("the", "buy") says little; a rare word ("dosa") says a lot. `idf = log(N / docs_containing_term)`.
- **TF-IDF** = TF × IDF.
- **BM25** (the default in Elasticsearch/Lucene) improves TF-IDF:
  - **Saturation:** the 10th mention adds much less than the 2nd (TF can't be gamed by repeating words).
  - **Length normalization:** a match in a short title counts more than a match in a 5,000-word page.
- **Field boosts:** a match in `title` worth 3× a match in `description`.
- **Business signals:** combine text relevance with popularity, rating, recency, availability, margin (`function_score`), or a learned ranking model (learning-to-rank) trained on clicks.
- **Semantic (vector) search** matches meaning, not words ("cheap sneakers" ↔ "budget running shoes"). **Hybrid search** runs both and merges the rankings — see [59.5 Searching: choices and trade-offs](#595-searching-choices-and-trade-offs).

### 34.3 Elasticsearch architecture

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

### 34.4 Autocomplete approaches

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
            # keep only the best K per prefix
            node["__top"] = heapq.nlargest(self.k, top)

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

Full design (trending, personalization, CDN caching of short prefixes) in [chapter 39](#39-problem-typeahead--search-autocomplete).

---

## 35. Special data structures

These trade a little accuracy for huge savings in memory or time. Each solves a specific system-design problem.

### 35.1 Bloom filter — "have I seen this before?" without storing everything

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
        # double hashing → k positions
        return [(h1 + i * h2) % self.m for i in range(self.k)]

    def add(self, item: str):
        for p in self._positions(item):
            self.bits[p // 8] |= 1 << (p % 8)

    def might_contain(self, item: str) -> bool:
        return all(self.bits[p // 8] & (1 << (p % 8)) for p in self._positions(item))

bf = BloomFilter(expected_items=10_000, fp_rate=0.01)
for i in range(10_000):
    bf.add(f"user:{i}")
# no false negatives, ever
assert all(bf.might_contain(f"user:{i}") for i in range(10_000))
fp = sum(bf.might_contain(f"other:{i}") for i in range(20_000)) / 20_000
# ~1% FP with < 10 bits per item
assert fp < 0.02 and bf.m / 10_000 < 10
```

### 35.2 HyperLogLog — count unique items in ~12 KB

"How many unique visitors today?" A set of 100M user IDs needs GBs. HyperLogLog estimates it with **~1% error in 12 KB** (Redis `PFADD` / `PFCOUNT`).

**Intuition:** hash each item to random bits. Seeing a hash that starts with 20 zero bits is about a 1-in-a-million event — if you've seen one, you've probably seen around a million distinct items. HLL splits hashes into many buckets, keeps the longest zero-run per bucket, and averages them for a stable estimate. HLLs can be **merged** (union of today's and yesterday's visitors) by taking the max per bucket.

### 35.3 Count-Min Sketch — approximate frequency of each item in a stream

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
# never underestimates
assert all(cms.estimate(t) >= n for t, n in true_counts.items())
assert abs(cms.estimate("#ipl") - 5000) / 5000 < 0.05
top3 = heapq.nlargest(3, true_counts, key=cms.estimate)
# heavy hitters found
assert top3 == ["#ipl", "#budget", "#monsoon"]
```

### 35.4 Geohash — turn a location into a string where nearby places share a prefix

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
# ~400 m apart → share a long prefix
assert mg_road[:5] == brigade_road[:5]
# different cities → differ early
assert mg_road[:2] != mumbai[:2]
assert geohash(57.64911, 10.40744, 11) == "u4pruydqqvj"   # standard reference example
```

**Quadtree:** recursively split a square into 4 until each cell has ≤ K points → small cells downtown, huge cells over the ocean (adapts to density). **H3 (Uber) / S2 (Google):** hierarchical hexagon/square cells on a sphere; hexagons have equidistant neighbours, nice for ride-hailing.

### 35.5 Merkle tree — find which part of two huge datasets differs

Hash each data block, then hash pairs of hashes up to a single **root hash**. Two replicas compare roots: equal → identical, done. Different → compare the two children, and only descend into subtrees whose hashes differ.

```text
                root = H(H12 + H34)
              /                     \
       H12 = H(H1+H2)          H34 = H(H3+H4)
        /        \               /        \
    H(block1) H(block2)     H(block3)  H(block4)

1M blocks, 1 differs → about 2 comparisons per level × 20 levels ≈ 40 hash comparisons,
instead of comparing 1M blocks
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
# ~2 × tree height comparisons, not 1024
assert counter[0] < 30
```

### 35.6 Other structures worth naming

| Structure | What it does | Where you'll mention it |
|---|---|---|
| **Skip list** | Sorted linked list with "express lanes"; O(log n) search/insert; easy to make concurrent | Redis sorted sets, LSM memtables |
| **Trie** | Prefix tree | Autocomplete ([34.4](#344-autocomplete-approaches)), IP routing tables |
| **Consistent hash ring** | Key → node with minimal movement on changes | Sharding, cache clusters ([16.5](#165-consistent-hashing-in-detail)) |
| **Ring buffer** | Fixed-size circular array | Sliding-window counters, recent-events logs |
| **Inverted index** | Term → documents | Search ([34.1](#341-inverted-index)) |
| **Min-heap** | Fast access to the smallest item | Schedulers (next job by time), top-K, merging sorted streams |

---

## 36. Problem: Video streaming (YouTube / Netflix)

**How often asked:** very common · **Builds on:** [7. CDN and edge](#7-cdn-and-edge), [33. Storage: block, file, object, data lake, warehouse](#33-storage-block-file-object-data-lake-warehouse), [25.1 Why asynchronous messaging](#251-why-asynchronous-messaging)

**Requirements**
- Upload videos (up to several GB), process into multiple resolutions/codecs, stream with adaptive bitrate on any device, metadata/search, view counts, resume playback.
- Non-functional: smooth playback worldwide (low startup time, minimal rebuffering), uploads processed in minutes, durable storage, cost-efficient.

**Estimates**
- 500K uploads/day × 500 MB = 250 TB/day raw; transcoded renditions add ~1–2× → storage is the main cost.
- 1B views/day × avg 10 min × ~3 Mbps → egress of **petabytes per day** → CDN must serve > 95%.

**API**
```
POST /v1/videos                      { title, description, size, content_type }
                                     → { video_id, upload: multipart pre-signed URLs }
POST /v1/videos/{id}/complete        (client finished uploading parts)
GET  /v1/videos/{id}                 → metadata + manifest URL + status
GET  /v1/videos/{id}/manifest.m3u8   (via CDN)
POST /v1/videos/{id}/progress        { position_sec }
```

**Data model**
```
videos (SQL, sharded by video_id): video_id | owner_id | title | description
                                   | status (UPLOADING/PROCESSING/READY/FAILED)
                                   | duration | created_at
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

#### Why adaptive streaming beat "just download the MP4"

Early web video was a single file downloaded progressively. One bitrate can't serve both a phone on a weak 3G signal and a 4K TV on fibre, and switching quality meant restarting playback. Apple's **HLS** (2009) and the later **MPEG-DASH** standard split video into short segments at several bitrates, described by a manifest, so the player can switch quality at every segment boundary — over plain HTTP, which means ordinary CDNs and caches work. That last property is why it won: video became just files on a CDN.

#### What Netflix did differently

- **Its own CDN.** Netflix's Open Connect program (launched in 2012) places Netflix-owned cache servers inside ISPs' networks and at internet exchanges, pre-filled during off-peak hours with what each region is predicted to watch. Most Netflix traffic never crosses the wider internet.
- **Per-title encoding (2015).** Instead of one bitrate ladder for every title, Netflix analyses each title's complexity: a simple animated show looks great at bitrates that would make an action movie blocky. Choosing the ladder per title saved bandwidth while improving quality — and they later went further, optimizing per scene ("shot-based" encoding).

Both are good answers to "how would you reduce cost at 10× scale": move bytes closer to users, and spend bits only where the eye notices.

---

## 37. Problem: Video calling (Zoom / Google Meet)

**How often asked:** occasional · **Builds on:** [5.5 Voice and video: WebRTC in five minutes](#55-voice-and-video-webrtc-in-five-minutes), [4. Networking fundamentals](#4-networking-fundamentals), [27. Problem: Chat / messaging (WhatsApp, Slack)](#27-problem-chat--messaging-whatsapp-slack)

Unlike video streaming, a call is **live in both directions**: there's no time to buffer, so a frame that arrives 400 ms late is useless. That one fact drives every choice — UDP instead of TCP, servers close to users, and forwarding instead of re-encoding.

**Requirements**
- 1:1 and group calls (up to ~100 video participants, more as view-only), screen sharing, mute, chat, recording.
- Non-functional: mouth-to-ear latency under ~150–300 ms, adapts to bad networks, works behind corporate firewalls, a server failure drops a call briefly at most.

**Estimates**
- 1M concurrent meetings × 5 participants = 5M participants.
- Upload per participant: ~1.5 Mbps for 720p video. With simulcast (low/mid/high copies) ~2.5 Mbps.
- A 5-person meeting through a forwarding server: each participant receives 4 streams → server egress ≈ 5 × 4 × 1.5 Mbps = **30 Mbps per meeting** → 30 Tbps across 1M meetings. Bandwidth, not CPU, is the dominant cost — which is why receivers get lower-quality copies for small tiles.

**Architecture**
```text
Client ── WebSocket ──► Signaling service (join, offers/answers, ICE candidates,
                         mute state, who's in the meeting)
   │                        └─ meeting state in Redis; assigns an SFU
   │
   └── UDP (SRTP) ──► SFU media server in the nearest region
                       ├─ forwards each participant's stream to the others,
                       │  choosing a simulcast layer per receiver
                       ├─ cascades to SFUs in other regions for global meetings
                       └─ recorder bot (joins like a participant) ─► object storage
STUN servers: tell clients their public address.
TURN servers: relay media when direct UDP is blocked (TCP/443 fallback).
```

**Deep dive: why an SFU (selective forwarding unit)?**
- **Peer-to-peer mesh** needs each client to upload a copy of its video to every other participant — fine for 1:1 calls (which often stay peer-to-peer), impossible for 10 people on home upload speeds.
- **MCU** (mixing server) decodes everyone and encodes one combined video per receiver → least client bandwidth, but enormous server CPU and an extra encode delay.
- **SFU** receives one stream per sender and forwards packets without decoding — cheap CPU, low latency, and with **simulcast** it can send a high layer to whoever is pinned and low layers for thumbnails.

**Deep dive: bad networks**
Receivers report packet loss and delay; the SFU switches them to a lower simulcast layer, and senders lower bitrate (congestion control). Audio gets priority over video — a call with frozen video is usable; one with broken audio isn't. Lost packets are concealed or re-requested once (NACK), never retransmitted repeatedly like TCP.

**Deep dive: placement**
Assign each meeting to an SFU near most of its participants; participants far away connect to a nearby SFU that **cascades** to the meeting's main one, so each long-distance hop carries one copy of each stream instead of one per viewer.

**Failures**
- SFU crash → clients detect the media timeout, ask signaling for a new SFU, and re-join in a few seconds; meeting state in Redis survives.
- Signaling instance crash → WebSocket reconnects to another instance; media keeps flowing meanwhile.
- UDP blocked → TURN over TCP/443, slower but works.

**Why not just use the chat system?** Chat is store-and-forward: messages must not be lost, ordering matters, a second of delay is fine. Media is the opposite: losing a packet is fine, delay is not. Signaling (who joined, mute state) does look like chat and reuses the same WebSocket infrastructure.

---

## 38. Problem: File storage & sync (Dropbox / Google Drive)

**How often asked:** common · **Builds on:** [33. Storage: block, file, object, data lake, warehouse](#33-storage-block-file-object-data-lake-warehouse), [15.5 Multi-leader](#155-multi-leader), [5.4 Real-time communication](#54-real-time-communication)

**Requirements:** upload/download files, sync across devices, share with others, version history, offline edits. Non-functional: never lose data, efficient bandwidth for small edits to big files, strong consistency for metadata.

**Estimates:** 50M DAU; average 2 GB per user stored → 100 PB → object storage. Metadata: billions of file rows → sharded SQL.

**Key design: chunking + content addressing**
- Split files into ~4 MB chunks; hash each (SHA-256). A file version = ordered list of chunk hashes.
- Upload only chunks the server doesn't already have (**dedup**) → editing a 1 GB file uploads only changed chunks (**delta sync**). Content-defined chunking (rolling hash) keeps chunk boundaries stable when bytes are inserted.

**Data model**
```
files:          file_id | owner_id | parent_id | name | is_folder | latest_version
                | deleted   (sharded by owner/namespace)
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

**Conflicts:** two devices commit on the same parent version → second commit fails → client saves a "conflicted copy". (Google Docs-style real-time merging is a different problem — [chapter 29](#29-problem-collaborative-editor-google-docs).)

**Follow-ups:** sharing permissions inherited from folders · version retention (keep 30 days) · garbage-collect unreferenced chunks (ref counts + delayed delete) · large-folder listing pagination.

#### Leaving S3: Dropbox's Magic Pocket

Dropbox originally stored file contents in Amazon S3, with metadata in their own systems. In 2016 they announced they had moved the vast majority of user data — hundreds of petabytes — into **Magic Pocket**, their own block storage system, built because at that scale owning the hardware and the software was cheaper and gave them more control over performance. It's a good example of the build-vs-buy curve: object storage from a cloud provider is the right default until storage *is* your product and your scale is enormous.

#### Rewriting the sync engine

The client-side sync engine is the hardest part of this product — files can change on several devices, offline, at the same time. Dropbox later rewrote its desktop sync engine (the project they wrote about as "Nucleus", around 2020) in Rust, with a much stricter data model of the three trees involved (remote state, local disk state, and the last-synced state) so that every conflict case is handled explicitly. The lesson for interviews: say that sync is a **state reconciliation** problem between three views, not just "upload changed chunks".

---

## 39. Problem: Typeahead / search autocomplete

**How often asked:** very common · **Builds on:** [34.4 Autocomplete approaches](#344-autocomplete-approaches), [8.1 Where caches live](#81-where-caches-live), [7. CDN and edge](#7-cdn-and-edge)

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
Offline:  search logs ─► Kafka ─► aggregator (hourly/daily counts, decay)
    ─► trie builder ─► snapshot store (S3) ─► serving nodes
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

#### Why not query the database (or Elasticsearch) on every keystroke?

`WHERE query LIKE 'iph%' ORDER BY popularity LIMIT 10` works for a demo. At hundreds of thousands of requests per second it means scanning and sorting matching rows for every keystroke — and short prefixes match enormous numbers of rows. The trie-with-top-K design moves all that work offline: ranking happens once when the trie is built, and each request is a handful of memory lookups. Elasticsearch's completion suggester takes the same approach internally (an in-memory automaton built at index time), which is why it's much faster than a prefix query on a normal field.

---

## 40. Problem: Web crawler

**How often asked:** common · **Builds on:** [25.1 Why asynchronous messaging](#251-why-asynchronous-messaging), [4.5 DNS](#45-dns), [33. Storage: block, file, object, data lake, warehouse](#33-storage-block-file-object-data-lake-warehouse)

**Requirements:** crawl billions of pages, keep them fresh, be polite to websites, avoid duplicates and traps; store pages for indexing.

**Estimates:** 1B pages/month ≈ 400 pages/s; 500 KB avg → 500 TB/month raw (compressed less).

**Architecture**
```
Seed URLs ─► URL Frontier ─► Fetchers ─► DNS resolver (cached) ─► HTTP fetch (robots.txt checked)
                ▲                              │
                │                              ▼
          URL filter/dedup ◄── Link extractor ◄── Parser
              ─► Content dedup (hash / SimHash) ─► Page store (S3) ─► Indexer
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

#### The frontier design comes from Mercator

The two-level frontier in this chapter — front queues for priority, back queues one per host for politeness — was described in the **Mercator** crawler paper (Heydon and Najork, 1999), and it's still the standard way to explain crawler scheduling. Google's original 1998 paper ("The Anatomy of a Large-Scale Hypertextual Web Search Engine") is also worth skimming for how simple the first version was.

Politeness has a formal basis too: `robots.txt` started as an informal convention in 1994 and was finally standardized as RFC 9309 in 2022. Respecting it (and a per-host crawl delay) isn't just etiquette — impolite crawlers get IP-blocked, which ends the crawl.

---

## 41. Problem: Distributed counter (likes, views)

**How often asked:** common, usually inside another problem ("how do you count likes on a viral post?") · **Builds on:** [8.5 Redis in depth](#85-redis-in-depth), [35.2 HyperLogLog — count unique items in ~12 KB](#352-hyperloglog--count-unique-items-in-12-kb), [15.5 Multi-leader](#155-multi-leader)

`UPDATE posts SET likes = likes + 1 WHERE id = 42` works until one post goes viral. Then thousands of writes per second hit **one row**, each waiting for the previous one's lock, and the database's write rate on that row becomes your ceiling. Counting at scale is about spreading those writes out — and deciding how exact the number really has to be.

**First, separate two questions**
- "Did *I* like this post?" must be exact and per user → a `likes(user_id, post_id)` row with a unique key. Liking twice is a no-op; unliking deletes the row.
- "How many likes does it have?" can be slightly behind or approximate → a counter maintained separately.

**Approaches, from simplest**

| Approach | How | Good for | Cost |
|---|---|---|---|
| Single counter in Redis | `INCR post:42:likes` | Up to ~100K increments/s on one key | One shard's limit; one key can still be hot |
| Sharded counter | N sub-counters `likes:42:0..N-1`; write a random one; read = sum of N | Very hot keys | N reads per count (cache the sum for a second) |
| Buffer and flush | Each app server counts in memory, flushes `+delta` every second | Views, impressions | Lose up to a second of counts if a server dies |
| Event log + aggregation | Each like is an event in Kafka; a stream job sums per post | Exact, auditable totals; analytics | Seconds of delay |
| HyperLogLog | Unique viewers per post in ~12 KB, ~1% error | "Unique views" | Approximate only |

```python
import random
from threading import Lock, Thread

class ShardedCounter:
    """N sub-counters, each with its own lock: concurrent increments rarely collide."""
    def __init__(self, shards: int = 8):
        self._counts = [0] * shards
        self._locks = [Lock() for _ in range(shards)]

    def incr(self, n: int = 1) -> None:
        i = random.randrange(len(self._counts))
        with self._locks[i]:
            self._counts[i] += n

    def value(self) -> int:
        # a read may be a moment behind, never wrong once quiet
        return sum(self._counts)

likes = ShardedCounter()
threads = [Thread(target=lambda: [likes.incr() for _ in range(1000)]) for _ in range(8)]
for t in threads: t.start()
for t in threads: t.join()
assert likes.value() == 8000
```

**Multi-region: counters that merge**
If users in two regions like the same post and each region accepts writes, you need counts that merge without conflicts. A **G-counter** (a CRDT) keeps one count per region; merging takes the max per region; the value is the sum. Supporting unlikes needs two of them (a **PN-counter**: increments minus decrements).

```python
def merge(a: dict, b: dict) -> dict:
    return {r: max(a.get(r, 0), b.get(r, 0)) for r in a.keys() | b.keys()}

def pn_value(inc: dict, dec: dict) -> int:
    return sum(inc.values()) - sum(dec.values())

# (increments, decrements) as seen in India
india = ({"in": 5, "us": 0}, {"in": 1})
# the US replica is behind on India's counts
us    = ({"in": 3, "us": 4}, {"in": 0, "us": 2})
inc, dec = merge(india[0], us[0]), merge(india[1], us[1])
assert inc == {"in": 5, "us": 4} and pn_value(inc, dec) == 6   # 9 likes − 3 unlikes
# merging again changes nothing
assert merge(inc, india[0]) == inc
```

**Design for "likes on a social network"**
```text
like/unlike ─► Like service ─► likes table (user_id, post_id) unique   ← source of truth
                     └─► Kafka "like-events" ─► counter job ─► Redis sharded counters
                                                       └─► periodic write-back to posts.like_count
read count ─► Redis (sum of shards, cached ~1 s) ─► fallback: posts.like_count
```
The table answers "did I like it?" exactly; the counter is eventually consistent, and can always be rebuilt from the table or the event log.

**Why exactness is negotiable:** YouTube's view counter famously used to pause at around 301 views on new videos while views were verified as genuine, and the platform changed that system in 2015 to show counts updating more frequently. Counts that drive money (ad billing) get the exact, event-log path ([31. Problem: Ad click aggregation](#31-problem-ad-click-aggregation)); counts shown on screen can lag.

---

## 42. Problem: Leaderboard, top-K, trending

**How often asked:** common · **Builds on:** [8.5 Redis in depth](#85-redis-in-depth), [25.8 Stream processing](#258-stream-processing)

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

#### Why a sorted set and not `ORDER BY score`?

`SELECT … ORDER BY score DESC LIMIT 10` with an index is fine for the top 10. The hard query is **"what's my rank?"** — the database must count every row with a higher score, which is O(n) per request for 100 million players. A Redis sorted set is a skip list plus a hash table, which keeps elements ordered with per-node span counts, so `ZREVRANK` is O(log n). For very large boards where even that is too much, games often show an **approximate** rank ("top 3%") from a score histogram, and exact ranks only near the top.

---

## 43. Problem: Proximity service (Yelp, "restaurants near me")

**How often asked:** common · **Builds on:** [15.1 Why](#151-why), [8.1 Where caches live](#81-where-caches-live), [34. Search](#34-search)

**Requirements**
- Given a location and radius (and optional filters like category, open now, rating), return nearby businesses ranked by distance and relevance; show business details and reviews.
- Business owners add and edit businesses; changes may take minutes to appear in search.
- Non-functional: p99 < 200 ms for search, highly available, read-heavy (businesses change rarely).
- Variant often asked next: **nearby friends**, where the points move constantly.

**Estimates**
- 100M DAU × 5 searches/day = 500M/day ≈ 6K QPS average, ~20K peak.
- 200M businesses × ~1 KB of searchable data ≈ 200 GB in a database; the **geo index alone** (ID + lat/lng + cell) is ~200M × ~30 B ≈ 6 GB — small enough to keep **in memory** on each search server.
- Writes: a few thousand business edits per minute at most.

**API**
```
GET  /v1/search/nearby?lat=&lng=&radius_m=1000&category=cafe&cursor=   → businesses + next cursor
GET  /v1/businesses/{id}
POST /v1/businesses            (owner)        PUT /v1/businesses/{id}
```

**Data model**
```
businesses (SQL, sharded by business_id): id | name | category | lat | lng | geohash
    | rating | hours | …
geo index (in memory on search nodes):     cell (geohash prefix) → [business_id, lat, lng]
reviews (separate service/store)
```

**Architecture**
```text
Client ─► LB ─► Search service (stateless, many replicas)
                  ├─ in-memory geo index (rebuilt from DB, updated via events)
                  └─ business details: cache ─► business DB (read replicas)
Owners ─► Business service ─► business DB ─► CDC/event ─► search nodes update their index
```

**How a search works**
1. Pick a geohash precision whose cells are about the size of the radius (precision 6 ≈ 1.2 km × 0.6 km).
2. Compute the user's cell **and its 8 neighbours** (the user might be at a cell's edge).
3. Collect candidates from those 9 cells, compute exact distances, drop those outside the radius, apply filters.
4. Rank (distance, rating, relevance), paginate with a cursor, fetch details from cache.
5. Too few results? Move to a coarser precision (bigger cells) and repeat.

**Deep dive: why an in-memory index and not a database query?**
`WHERE geohash LIKE 'tdr1y%'` on an indexed column works and is a fine starting point. At 20K QPS with filters and ranking, a purpose-built in-memory index on stateless search nodes is cheaper and faster, and because businesses change slowly, rebuilding it from the database or applying change events every few minutes is easy. Each node holds the whole index (6 GB), so there's no sharding of the index at all — just more replicas for more QPS.

**Deep dive: dense vs sparse areas**
A fixed geohash precision gives thousands of candidates in Manhattan and almost none in the countryside. A **quadtree** fixes this: split a cell into four whenever it has more than, say, 100 businesses, so cells are small where things are dense. Build it in memory at startup; a search walks down to the leaf containing the user and expands to neighbouring leaves.

**Nearby friends (the moving-points variant)**
- Locations change every few seconds and are only interesting while the app is open → don't write them to a database each time.
- Store the latest location in Redis (`GEOADD` with a TTL refresh), and publish each update on a **channel per user**. When a user opens the app, subscribe to their friends' channels (over a WebSocket) and filter by distance on the server.
- Scale: the hard part is the pub/sub fan-out (each update goes to all online friends), so the pub/sub layer is sharded by user ID.

**Failures**
- A search node dies → the load balancer drops it; others have the full index.
- Index lag after a business edit → acceptable (minutes); the details page always reads the database/cache.
- Region outage → search is read-only and replicated, so serve from another region.

#### Geohash, quadtree, S2 or H3?

- **Geohash** turns locations into strings, so any database with a string index can do "nearby" with prefix queries — the most portable option. Redis's `GEOADD`/`GEOSEARCH` actually store geohash-based scores in a sorted set. Weakness: cells are rectangles of different shapes at different latitudes, and edge effects force neighbour lookups.
- **Quadtree** adapts to density (small cells downtown, huge cells in the countryside) but is an in-memory structure you maintain yourself.
- **Google S2** maps the sphere onto a cube and uses a space-filling curve; cells are hierarchical and fairly uniform in area.
- **Uber H3** (open-sourced in 2018) uses hexagons. A hexagon's six neighbours are all the same distance from its centre, which makes "expand the search ring" and smoothing (e.g., surge pricing across neighbouring areas) much cleaner than with squares, whose diagonal neighbours are farther away.

For a read-heavy "restaurants near me", geohash or a quadtree is plenty, and geohash is the easiest to explain in an interview. For moving objects and area analytics (ride hailing, delivery), H3 or S2 is what the large players use.

---

## 44. Problem: Ride hailing (Uber / Lyft)

**How often asked:** very common · **Builds on:** [5.4 Real-time communication](#54-real-time-communication), [16.3 Strategies](#163-strategies), [17.9 Distributed locks](#179-distributed-locks)

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
Geo index (in-memory / Redis, sharded by city/region):
    cell_id → set of available driver_ids
    driver_id → (lat, lng, ts)
drivers (SQL):  driver_id | vehicle | rating | status
rides (SQL):    ride_id | rider_id | driver_id | status | pickup | dropoff | fare
                | created_at | version
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

#### How Uber's dispatch evolved

Uber has talked publicly about rewriting its dispatch system around 2014–2015: the earlier design had grown city by city and struggled with scale and new products (shared rides, delivery). The rewrite introduced a sharded, location-aware dispatch service, and Uber open-sourced **Ringpop**, a library for consistent hashing plus gossip-based membership, so dispatch nodes could own geographic shards and route requests to the right owner. Later Uber open-sourced **H3** for its geospatial indexing. The pieces map directly onto this chapter: shard by geography, keep driver locations in memory, and route each request to the node that owns that area.

#### Greedy versus batched matching

Assigning each rider the nearest available driver the instant they request is simple, but globally it can be poor: a driver who's 1 minute from rider A and 2 minutes from rider B gets sent to A, leaving B with a driver 12 minutes away. **Batched matching** collects requests for a second or two and solves an assignment problem that minimizes total wait. The cost is a short added delay before matching, and a harder optimization problem to solve every few seconds.

---

## 45. Problem: Food delivery (Swiggy / DoorDash)

**How often asked:** common · **Builds on:** [44. Problem: Ride hailing (Uber / Lyft)](#44-problem-ride-hailing-uber--lyft), [17.8 Distributed transactions](#178-distributed-transactions), [34.3 Elasticsearch architecture](#343-elasticsearch-architecture)

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

#### What's different from ride hailing

It looks like the same dispatch problem, but there's a third party with its own timeline: the restaurant. A rider who arrives 15 minutes before the food is ready wastes 15 minutes of capacity; one who arrives late makes the food cold. So dispatch optimizes for **arrival ≈ food-ready time**, which makes prep-time prediction (by restaurant, dish, time of day, current load) a core model rather than a detail. Batching two orders from the same area into one trip is also far more valuable here than in ride hailing, because food can wait a few minutes and passengers usually can't share a trip.

---

## 46. Problem: Maps and ETA (Google Maps)

**How often asked:** occasional · **Builds on:** [35.4 Geohash — turn a location into a string where nearby places share a prefix](#354-geohash--turn-a-location-into-a-string-where-nearby-places-share-a-prefix), [25.8 Stream processing](#258-stream-processing), [7. CDN and edge](#7-cdn-and-edge)

"Design Google Maps" has three separate systems hiding in it — **showing the map**, **finding a route**, and **predicting how long it takes** — and a good answer names all three, then goes deep on one.

**Requirements**
- Show a zoomable map; search for places; route from A to B (driving, walking); ETA that reflects live traffic; reroute when traffic changes.
- Non-functional: map tiles load instantly; a route in under ~1 s; ETAs accurate within a few minutes; hundreds of millions of daily users.

**Estimates**
- Route requests: 100M/day ≈ 1.2K/s average, ~5K/s at peak.
- Location pings from navigating phones: 10M active × 1 ping / 5 s = **2M pings/s** → the traffic pipeline is the write-heavy part.
- Road graph for a large country: tens of millions of intersections and road segments — a few GB in memory.

**Part 1: map tiles**
The world is cut into square tiles per zoom level (zoom z has 4^z tiles, addressed as `z/x/y`). Tiles are pre-rendered — today usually **vector tiles** (geometry, styled on the device) rather than images — stored in object storage and served through a CDN. Almost all map traffic is cacheable static content, which makes this the easy part.

**Part 2: routing**
The road network is a weighted graph: intersections are nodes, road segments are edges, weights are travel times. Plain Dijkstra on a country-sized graph explores millions of nodes per query — too slow. Production routers **precompute**:
- **Contraction hierarchies:** add "shortcut" edges that skip unimportant nodes, so a query mostly travels on highways and touches only thousands of nodes (used by open-source engines like OSRM).
- **Partition-based methods** split the graph into cells with precomputed costs across each cell, which makes it cheap to update when traffic changes edge weights.

```python
import heapq

def shortest_time(graph: dict, src: str, dst: str) -> tuple[float, list[str]]:
    """Dijkstra over travel times in minutes."""
    dist, prev, heap = {src: 0.0}, {}, [(0.0, src)]
    while heap:
        d, node = heapq.heappop(heap)
        if node == dst:
            break
        if d > dist[node]:
            continue
        for nxt, minutes in graph[node].items():
            if d + minutes < dist.get(nxt, float("inf")):
                dist[nxt], prev[nxt] = d + minutes, node
                heapq.heappush(heap, (d + minutes, nxt))
    path = [dst]
    while path[-1] != src:
        path.append(prev[path[-1]])
    return dist[dst], path[::-1]

roads = {"home": {"highway": 4, "main_st": 3}, "highway": {"office": 10},
         "main_st": {"office": 14}, "office": {}}
assert shortest_time(roads, "home", "office") == (14, ["home", "highway", "office"])
# live traffic: a jam on the highway
roads["highway"]["office"] = 25
assert shortest_time(roads, "home", "office") == (17, ["home", "main_st", "office"])
```

**Part 3: traffic and ETA**
```text
Phones ─► ingestion ─► Kafka (partitioned by region)
   ─► map matching: snap noisy GPS points to the road segment actually driven
   ─► stream job: average speed per segment per minute
   ─► live speeds store ─► router updates edge weights every minute or so
Historical speeds per segment per time-of-week (Tuesday 9:00 is slow)
   + live speeds ─► ETA model ─► ETA shown to the user
```
- Live speed alone predicts the next five minutes; for a 40-minute drive you need to predict what traffic will be *when you get there* → blend historical patterns with live data. Google described using graph neural networks for this in Google Maps (DeepMind, 2020), reporting large accuracy gains in some cities.
- **Privacy:** pings are anonymised and aggregated per segment; individual trips aren't kept in the speed pipeline.

**Failures**
- Traffic pipeline lags → fall back to historical speeds; ETAs get less precise, routing still works.
- A router node dies → stateless replicas, each holding the graph for its region.
- A road closes → an incident feed sets that edge to "impassable"; affected navigating users are rerouted.

**Why not compute routes on the phone?** Some apps do for offline maps, but live traffic, the full graph and frequent updates make server-side routing the default; the phone caches tiles and the current route.

---

## 47. Reliability and resilience

### 47.1 Availability math

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
# 10 dependencies at three nines each
assert series(*[0.999] * 10) < 0.991
```

**Takeaways for design:** fewer hard dependencies on the request path; make optional ones optional (degrade instead of fail); replicate across independent failure domains (availability zones).

### 47.2 Timeouts

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

### 47.3 Retries

Many failures are transient (a pod restarting, a dropped connection, a 503 during a deploy). A retry often succeeds. But naive retries make outages worse.

**Rules:**
1. Retry only **transient** errors: timeouts, connection errors, 429, 502/503/504. Never retry 400/401/403/404/422 — they'll fail again.
2. Retry only **idempotent** operations — or make them idempotent with an idempotency key ([5.7](#57-idempotency)).
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
            # permanent error, or out of attempts
            return status, attempt
        sleep(delay)

responses = iter([503, 503, 200])
# transient → succeeded on 3rd try
assert call_with_retries(lambda: next(responses)) == (200, 3)
# not retryable → stop immediately
assert call_with_retries(lambda: 404) == (404, 1)
always_down = call_with_retries(lambda: 503)
# gave up after max attempts
assert always_down == (503, 4)

delays = backoff_delays(5)
assert all(0 <= d <= min(5.0, 0.1 * 2 ** i) for i, d in enumerate(delays))
```

### 47.4 Circuit breaker

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
            # cool-down over: allow a trial call
            self.state = "HALF_OPEN"
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
# 100 more requests during the outage
for _ in range(100):
    try:
        cb.call(broken)
    except CircuitOpen:
        pass
# none reached the broken service
assert len(calls) == 3
now[0] = 31
# trial call succeeded → closed again
assert cb.call(lambda: "ok") == "ok" and cb.state == "CLOSED"
```

### 47.5 Bulkheads, load shedding, graceful degradation

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
Product page dependencies:   product info (required) · price (required)
                             reviews · recommendations · "people also bought" (optional)
Reviews service down        → show the page without reviews ("reviews temporarily unavailable")
Recommendations down        → show popular items from a static cache
Price service down          → can't sell safely → show "temporarily unavailable" for the buy button
```

**Queue-based load leveling:** put a queue in front of a slow backend so bursts are absorbed and processed at a steady rate (with backpressure, [17.12](#1712-backpressure)).

```python
import threading

class Bulkhead:
    def __init__(self, max_concurrent: int):
        self.slots = threading.BoundedSemaphore(max_concurrent)

    def call(self, fn, fallback):
        # pool exhausted → don't queue, degrade
        if not self.slots.acquire(blocking=False):
            return fallback()
        try:
            return fn()
        finally:
            self.slots.release()

recommendations = Bulkhead(max_concurrent=2)
release = threading.Event()
# 2 slow calls + the test itself
both_slots_busy = threading.Barrier(3)

def slow_recs():
    # signal "I'm holding a slot"
    both_slots_busy.wait()
    # recommendation service is hanging
    release.wait(5)
    return ["item1", "item2"]

slow_results = []
slow = [threading.Thread(target=lambda: slow_results.append(
    recommendations.call(slow_recs, fallback=lambda: ["popular-item"]))) for _ in range(2)]
for t in slow: t.start()
# now both slots are definitely taken
both_slots_busy.wait()
extra = [recommendations.call(slow_recs, fallback=lambda: ["popular-item"]) for _ in range(3)]
# degraded instantly instead of piling up
assert extra == [["popular-item"]] * 3
release.set()
for t in slow: t.join()
assert slow_results == [["item1", "item2"]] * 2
```

### 47.6 Rate limiting

**Why:** protect your service from abuse and overload, enforce fair use between customers, enforce pricing tiers ("free plan: 100 requests/min"), and protect paid downstream APIs.

**Where:** at the API gateway (per API key/user/IP), inside services (protect themselves), and in client SDKs (be polite to others).

**Algorithms, with what they actually do:**

**Fixed window counter** — count requests per calendar minute.
```text
limit 100/min:  12:00:00–12:00:59 → counter; reset at 12:01:00
problem: 100 requests at 12:00:59 + 100 at 12:01:00
         = 200 requests in 2 seconds
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
C = 10, R = 1/s:  an idle client can burst 10 requests instantly,
                  then sustain 1 per second
```
Allows short bursts (good for real users) while capping the average. Used by AWS, Stripe, most API gateways.

**Leaky bucket** — requests enter a queue that drains at a fixed rate → perfectly smooth output; bursts wait or are dropped. Good for shaping traffic to a fragile downstream.

| Algorithm | Memory per client | Bursts | Accuracy |
|---|---|---|---|
| Fixed window | 1 counter | Up to 2× at window edges | Approximate |
| Sliding window log | 1 timestamp per request | No | Exact |
| Sliding window counter | 2 counters | Smoothed | Close to exact |
| Token bucket | 2 numbers | Yes, up to capacity | Exact for its definition |
| Leaky bucket | A queue | Queued, output smooth | Exact for its definition |

Runnable implementations of all of these, with a pluggable strategy per client, are in [the LLD rate limiter chapter](lld-interview-guide.md#20-problem-rate-limiter).

**Distributed rate limiting:** with 50 gateway instances, the count must be shared → keep counters in Redis and update them **atomically** with a Lua script (otherwise two gateways both read "99" and both allow). Full design in [48. Problem: Rate limiter (distributed)](#48-problem-rate-limiter-distributed).

**Responding:** `429 Too Many Requests` with `Retry-After: 30` and headers like `X-RateLimit-Limit: 100`, `X-RateLimit-Remaining: 0`, `X-RateLimit-Reset: 1735689600` so clients can back off properly.

### 47.7 Redundancy, failover, disaster recovery

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

| Strategy | RPO | RTO | Cost |
|---|---|---|---|
| Backups copied to another region | Hours | Hours | $ |
| Pilot light (DB replicated, servers off) | Seconds–minutes | ~30 min | $$ |
| Warm standby (scaled-down copy running) | Seconds | Minutes | $$$ |
| Active-active (both regions serve) | ~0 (or conflicts to resolve) | ~0 | $$$$ |

**Backups:** regular snapshots + continuous WAL archiving (point-in-time recovery). A backup you've never **restored** is a hope, not a backup — test restores regularly.

**Multi-region:**
- **Active-passive:** one region serves traffic; the other is a standby. Simpler; failover takes minutes.
- **Active-active:** both serve users (lower latency worldwide, survives a region loss) but you must handle concurrent writes in two regions — partition data by region (each user has a home region) or use conflict resolution/globally consistent databases.
- Route users with GeoDNS / anycast, and health-check regions.

### 47.8 Idempotency everywhere

Retries happen at every level, often without you asking:

```text
User double-taps "Pay" · mobile client retries on timeout · load balancer retries a failed upstream ·
service mesh retries · queue redelivers a message after a consumer crash · a cron job re-runs after a deploy
```
So **every write path must be safe to repeat**:
- Client-supplied **idempotency keys** for APIs that create/charge things ([5.7](#57-idempotency)).
- **Unique constraints** (one booking per seat per show, one row per event ID).
- **Upserts** instead of blind inserts.
- **Conditional updates / version checks** (`UPDATE … WHERE status = 'PENDING'`).
- **Dedup tables** for consumers (processed message IDs).
- Natural idempotency: "set status = SHIPPED" is safe to repeat; "add 1 to count" is not.

### 47.9 Practices that came out of production pain

- **Chaos engineering (Netflix, 2011):** after moving to AWS, Netflix built **Chaos Monkey** to randomly terminate production instances during working hours, forcing every service to tolerate instance loss. It grew into the "Simian Army" (latency injection, zone outages) and the discipline of chaos engineering: if failure is inevitable, rehearse it on purpose.
- **Circuit breakers as a library (Netflix Hystrix, 2012):** Hystrix popularized circuit breakers, bulkheads (thread pool per dependency) and fallbacks in Java services. It's now in maintenance mode — Netflix moved toward adaptive concurrency limits — and libraries like resilience4j took over, but the patterns in this chapter are exactly the ones it made mainstream.
- **"The Tail at Scale" (Dean and Barroso, 2013):** at Google scale, a request that fans out to 100 servers is slow if *any one* of them is slow, so rare slowness becomes common. Their techniques include **hedged requests** — send a request to a second replica if the first hasn't answered within, say, the 95th-percentile latency, and use whichever answers first. It cuts tail latency for a small amount of extra load.

---

## 48. Problem: Rate limiter (distributed)

**How often asked:** very common · **Builds on:** [47.6 Rate limiting](#476-rate-limiting), [8.5 Redis in depth](#85-redis-in-depth)

**Requirements**
- Limit requests per key (user ID, API key, IP) per rule, e.g., 100/min per user, 10/s per IP on login.
- Rules configurable per endpoint and plan; multiple rules per request.
- Return 429 with headers. Add < 2 ms latency. Works across hundreds of API servers.
- Decide failure mode: **fail open** (allow on limiter failure — protects availability) for normal APIs; **fail closed** for security-critical ones (login, OTP).

**Where it runs:** API gateway / shared middleware. Also local limits inside critical services to protect themselves.

**Algorithm choice:** token bucket (allows short bursts, smooth average; 2 numbers per key) or sliding window counter (no burst at window edges). See [47.6](#476-rate-limiting).

**Data model (Redis)**
```
token bucket:   rl:{rule}:{key} → hash { tokens, ts }   (ts = last refill time, ms)
                TTL ≈ 2 × time to refill the bucket
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

#### Rate limiting is one of several limiters

Stripe's 2017 post "Scaling your API with rate limiters" is a good reference because it shows rate limiting isn't one thing. They described four kinds of limiter:
1. A **request rate limiter** per user (token bucket) — the classic one in this chapter.
2. A **concurrent requests limiter** — caps how many requests a user can have *in flight*, which protects against expensive slow endpoints better than a per-second rate.
3. A **fleet usage load shedder** — reserves a share of total capacity for critical requests (like charges) and sheds non-critical traffic when the fleet is busy.
4. A **worker utilization load shedder** — when workers are saturated, sheds lower-priority traffic first.

Mentioning the last three turns a standard answer into a strong one: per-user fairness is only half the job; protecting the system as a whole under overload is the other half ([47.5 Bulkheads, load shedding, graceful degradation](#475-bulkheads-load-shedding-graceful-degradation)).

---

## 49. Security

### 49.1 Authentication: who are you?

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
    # deliberately slow
    digest = hashlib.scrypt(password.encode(), salt=salt, n=2**14, r=8, p=1)
    return salt, digest

def check_password(password: str, salt: bytes, digest: bytes) -> bool:
    return hmac.compare_digest(hashlib.scrypt(password.encode(), salt=salt, n=2**14, r=8, p=1), digest)

salt1, d1 = hash_password("hunter2")
salt2, d2 = hash_password("hunter2")
# same password, different salt → different hash
assert d1 != d2
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

# HS256 for illustration; production often uses RS256/ES256 key pairs
KEY = b"server-secret"

def issue(claims: dict) -> str:
    header = b64url(json.dumps({"alg": "HS256", "typ": "JWT"}).encode())
    payload = b64url(json.dumps(claims).encode())
    sig = b64url(hmac.new(KEY, f"{header}.{payload}".encode(), hashlib.sha256).digest())
    return f"{header}.{payload}.{sig}"

def verify(token: str, now: int) -> dict:
    header, payload, sig = token.split(".")
    if json.loads(b64url_decode(header))["alg"] != "HS256":
        # never trust the token's own 'alg' blindly
        raise ValueError("unexpected algorithm")
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
        # tampered role and expired token both rejected
        pass
```
- The payload is only **encoded, not encrypted** — anyone can read it. Never put secrets in a JWT.
- With **RS256/ES256**, only the auth server holds the private key; every service verifies with the public key from a **JWKS** endpoint; keys rotate via the `kid` header.
- **Revocation:** short-lived access tokens (5–15 min) + long-lived **refresh tokens** (stored server-side, rotated on use, revocable). Emergency denylist by token ID (`jti`) in Redis.
- Store tokens in **HttpOnly, Secure, SameSite** cookies in browsers (JavaScript can't read them, so XSS can't steal them); avoid localStorage for long-lived tokens.

### 49.2 OAuth 2.0 and OpenID Connect

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
# attacker with a stolen code
assert not token_endpoint(secrets.token_urlsafe(64), code_challenge)
```
**Client Credentials flow:** service-to-service, no user involved — a service authenticates with its own credentials to get a token.

### 49.3 Authorization: what are you allowed to do?

- **RBAC (role-based):** permissions attached to roles; users get roles. `admin → {orders:refund, users:ban}`; `support → {orders:read}`.
- **ABAC (attribute-based):** rules over attributes: "a doctor can read a patient record if they're in the same hospital and on the care team".
- **Ownership checks (the most commonly missed):** RBAC says "customers can read orders"; you still must check **this** order belongs to **this** customer.

```text
GET /orders/43 as user 42
  authenticated? yes      has role customer? yes
  order 43 belongs to user 42? NO → 403 (or 404 to avoid revealing it exists)
Skipping this check = "IDOR" (insecure direct object reference):
change the ID in the URL, read anyone's order.
```
Enforce authorization **on the server, on every request**, close to the data — never because "the UI hides the button".

### 49.4 Protecting data

- **In transit:** TLS everywhere, including inside the data center; **mTLS** between services ([4.4](#44-tls-https)).
- **At rest:** disk/database encryption with keys in a KMS. **Envelope encryption:** each file/row encrypted with its own data key; data keys encrypted by a master key that never leaves the KMS → rotating the master key doesn't require re-encrypting all data.
- **Secrets** (DB passwords, API keys) in a secret manager (Vault, AWS Secrets Manager), injected at runtime, rotated — never in code, images, or logs.
- **PII:** collect the minimum, mask it in logs, define retention, support deletion requests (GDPR "right to be forgotten").
- **Multi-tenancy:** every query filtered by `tenant_id` (enforce in a data-access layer or with row-level security), so one bug can't leak another customer's data.
- **Audit logs:** append-only "who did what, when" for sensitive actions (refunds, role changes, data exports).

### 49.5 Common attacks and their fixes

**SQL injection** — user input becomes part of the SQL text:

```python
import sqlite3

db = sqlite3.connect(":memory:")
db.execute("CREATE TABLE users (id INTEGER, email TEXT, is_admin INTEGER)")
db.executemany("INSERT INTO users VALUES (?, ?, ?)", [(1, "a@x.com", 0), (2, "boss@x.com", 1)])
user_input = "nobody@x.com' OR '1'='1"

# string building
unsafe_sql = f"SELECT email FROM users WHERE email = '{user_input}'"
# leaked every row
assert len(db.execute(unsafe_sql).fetchall()) == 2

# parameterized
safe = db.execute("SELECT email FROM users WHERE email = ?", (user_input,))
# input treated as data
assert safe.fetchall() == []
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

### 49.6 Breaches worth learning from

- **Capital One, 2019 — SSRF to cloud credentials.** An attacker exploited a misconfigured web application firewall to make the server send requests to the AWS instance metadata service, which returned temporary credentials with broad access to S3 buckets; data on roughly 100 million people was taken. AWS later introduced **IMDSv2**, which requires a session token obtained with a PUT request — a simple SSRF that can only make GET requests can no longer read credentials. Lessons: block internal addresses for any server-side fetch of user-supplied URLs, and give each workload the narrowest IAM permissions.
- **Equifax, 2017 — the unpatched dependency.** Attackers exploited a known vulnerability in Apache Struts, a web framework, for which a patch had been available for about two months. Personal data on around 147 million people was exposed. The lesson isn't exotic: inventory your dependencies, track vulnerabilities, and patch on a schedule — most breaches use known holes.
- **Log4Shell, 2021** — the logging-library vulnerability — is covered in the [LLD logging chapter](lld-interview-guide.md#31-problem-logging-framework).

---

## 50. Observability

Monitoring tells you **that** something is wrong; observability lets you find out **why** — from the outside, without deploying new code. Three main signals:

### 50.1 Metrics — numbers over time

Cheap to store, great for dashboards and alerts.

```text
http_requests_total{service="orders", route="/orders", status="500"}  1,204   (counter)
http_request_duration_seconds{service="orders", quantile="0.99"}       0.840   (histogram → percentiles)
db_pool_in_use{service="orders"}                                       18      (gauge)
```
- **RED method** for every service: **R**ate (requests/s), **E**rrors (failed requests/s or %), **D**uration (latency percentiles).
- **USE method** for every resource (CPU, disk, pool, queue): **U**tilization, **S**aturation (queueing), **E**rrors.
- Avoid high-cardinality labels (user_id, request_id) — each unique combination is a new time series (worked example in [30. Problem: Metrics, logging, and monitoring system](#30-problem-metrics-logging-and-monitoring-system)).

**Percentiles, not averages.** An average hides the users having a terrible time:

```python
import statistics

# 2% of requests hit a slow path
latencies_ms = [40] * 980 + [3000] * 20

def percentile(values, p):
    ordered = sorted(values)
    return ordered[min(len(ordered) - 1, int(round(p / 100 * len(ordered))) - 1)]

avg = statistics.mean(latencies_ms)
p50, p99 = percentile(latencies_ms, 50), percentile(latencies_ms, 99)
# average looks fine; p99 users wait 3 s
assert round(avg) == 99 and p50 == 40 and p99 == 3000

# A page that makes 20 backend calls hits the slow path at least once for ~1 - 0.98**20 ≈ 33% of page loads
assert round(1 - 0.98 ** 20, 2) == 0.33
```

### 50.2 Logs — what happened, in detail

```json
{"ts": "2025-05-01T12:30:00.123Z", "level": "ERROR", "service": "orders", "trace_id": "4bf92f35…",
 "request_id": "req_8f2c", "user_id": 42, "route": "POST /orders", "error": "PaymentTimeout", "duration_ms": 2003}
```
- **Structured JSON**, not free text → searchable and aggregatable.
- Always include `trace_id`/`request_id` → jump from a log line to the full trace.
- Levels; sampling for chatty debug logs; never log secrets or full PII.
- Centralized: shipped by agents to Elasticsearch/OpenSearch, Loki, ClickHouse, or a vendor.

### 50.3 Traces — one request's journey across services

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

### 50.4 SLIs, SLOs, SLAs and error budgets

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
# >1 means you'll run out before month end
burn_rate = (failed_so_far / budget) / (days_elapsed / 30)
# alert: burning budget ~2× too fast
assert round(burn_rate, 1) == 1.8
```

### 50.5 Alerting that people don't ignore

- Alert on **symptoms users feel** (SLO burn rate, error rate, latency), not every CPU spike.
- **Burn-rate alerts:** page when the budget is burning fast (e.g., 14× over 1 h); ticket when slow (e.g., 2× over 3 days).
- Every page needs an owner and a **runbook** (what to check, how to mitigate).
- **Synthetic checks:** a robot performs "log in → search → add to cart" every minute from several regions.

### 50.6 Where these ideas came from

- **Distributed tracing:** Google's **Dapper** paper (2010) described tracing every request across thousands of services with low-overhead sampling. Twitter open-sourced **Zipkin** (2012) based on it; Uber built **Jaeger**. The competing open standards OpenTracing and OpenCensus merged into **OpenTelemetry** (2019), now the common way to emit traces, metrics and logs.
- **SLOs and error budgets** come from Google's **Site Reliability Engineering** book (2016), which reframed reliability as a product decision: decide how unreliable you can afford to be, and spend that budget on shipping changes.

---

## 51. Architecture styles and deployment

### 51.1 Monolith vs microservices

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

### 51.2 Other styles

- **Event-driven:** services react to events on a log (Kafka) instead of calling each other — loose coupling, natural audit trail, harder to trace end to end ([25.7](#257-event-driven-patterns)).
- **Serverless (FaaS — Lambda, Cloud Functions):** you deploy functions that run per event; pay per invocation; scales to zero and up automatically. Costs: cold starts (100 ms–seconds), execution time limits, harder local testing, lock-in. Great for spiky glue work (image resize on upload, webhooks, cron).
- **Cell-based architecture:** run several complete, independent copies of the whole stack ("cells"), each serving a slice of customers. A bad deploy or overload in one cell affects only its customers → limits blast radius. AWS recommends it in its Well-Architected guidance, and Slack described moving to a cellular architecture in 2023.

### 51.3 Containers and Kubernetes (enough for HLD)

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

### 51.4 Deployment strategies

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
# increasing the rollout keeps earlier users enabled
assert on_5 <= on_20
# stable
assert flag_enabled("new_checkout", "user-7", 20) == flag_enabled("new_checkout", "user-7", 20)
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

### 51.5 Stories behind the trade-offs

- **Knight Capital, 1 August 2012 — a deployment that cost $440 million.** During a manual deployment of new trading code, one of eight servers wasn't updated. The new release reused an old feature flag; on that one server, the flag switched on long-dead code that started sending orders in a loop. In about 45 minutes the firm lost roughly $440 million and needed a rescue. The lessons map directly onto [51.4 Deployment strategies](#514-deployment-strategies): automate deployments so every server gets the same build, never reuse feature flags, remove dead code, and have a fast kill switch.
- **Amazon Prime Video, 2023 — back to a monolith.** A Prime Video team wrote about moving their video-quality monitoring service from a distributed design built on serverless functions and step functions to a single process. The orchestration overhead and the cost of passing video frames between components dominated; the consolidated service was reported to cut infrastructure cost by about 90%. It's not an argument against microservices — it's an argument for choosing the architecture per workload, which is the point of [51.1 Monolith vs microservices](#511-monolith-vs-microservices).
- **Kubernetes (2014)** came out of Google's experience running **Borg**, its internal cluster manager, for over a decade. Many of its ideas — declarative desired state, controllers that reconcile reality to it, pods, labels — are Borg's lessons made open source.

---

## 52. GenAI: what gets asked

LLM fundamentals → prompting → calling an LLM API → tool calling → agents → MCP → RAG → evaluation → fine-tuning → serving, cost, safety → GenAI system design problems → interview Q&A.

**Code conventions for the GenAI chapters:** the `python` blocks use only the standard library and run as they are; a fake model stands in for the LLM so you can see the mechanics. The concepts apply to every provider. Code that calls a real model API changes every few months, so it's collected in one dated place at the end: [76. Appendix: Calling a real model (Claude Python SDK, as of October 2026)](#76-appendix-calling-a-real-model-claude-python-sdk-as-of-october-2026).

| Round | Typical questions |
|---|---|
| Concepts | Tokens, context window, embeddings, temperature, hallucination, RAG vs fine-tuning, how attention works (high level) |
| Applied/coding | Build a mini RAG; write an agent loop with tools; chunk documents; compute retrieval metrics; parse structured output |
| GenAI system design | Design a chatbot, enterprise document Q&A, customer-support agent, coding agent, LLM gateway, semantic search |
| Production | Latency/cost optimization, evals, guardrails, prompt injection, observability, scaling inference |
| Past projects | Deep dive into agent/RAG work you've done: why each choice, how you measured quality |

**SDE2 bar:** you can build a reliable LLM feature end to end — not just call an API — and you talk about **evaluation, failure modes, cost, and latency** without being asked.

---

## 53. LLM fundamentals

### 53.1 What an LLM does

- A large language model is a neural network (a **transformer**) trained to **predict the next token** given previous tokens.
- **Pre-training:** next-token prediction on huge text corpora → general knowledge and language ability.
- **Post-training:** instruction tuning (SFT on prompt/response pairs) + preference tuning (RLHF / RLAIF / DPO) → follows instructions, safer, more helpful.
- Generation is **autoregressive**: produce one token, append it, predict the next, until a stop token or `max_tokens`.

### 53.2 Tokens and tokenization

- Text is split into **tokens** (sub-word pieces via BPE/SentencePiece). Rough rule for English: **1 token ≈ 4 characters ≈ 0.75 words**. Code, numbers, and non-English text often use more tokens.
- Everything is priced and limited in tokens: input tokens, output tokens, context window.
- Different models have different tokenizers → token counts differ for the same text. Count with the provider's token-counting API, not a guess.
- Tokenization explains some failures: counting letters in a word, arithmetic on long numbers, exact string manipulation.

### 53.3 Context window

- The maximum tokens the model can attend to in one request (input + output). Current frontier models: up to ~1M tokens.
- Bigger context ≠ better answers: cost and latency grow with input size, and models can miss details buried in long contexts ("lost in the middle"). Retrieval + curation usually beats stuffing.
- The model has **no memory between requests** — every call must include whatever context it needs (conversation history, retrieved documents, tool results).

### 53.4 Transformer and attention (high level is enough)

- Input tokens → **embeddings** (vectors) + position information.
- **Self-attention:** each token computes a weighted mix of all previous tokens' representations. Weights come from query·key similarity: `softmax(QKᵀ/√d)·V`.
- **Multi-head attention:** several attention "views" in parallel. Stacked with feed-forward layers, many times.
- Attention cost grows **quadratically** with sequence length (compute), which is why long contexts are expensive.
- **KV cache:** during generation, keys/values of past tokens are stored so each new token only computes attention for itself → faster decoding, but memory grows with context length × batch size.

### 53.5 Sampling and decoding

- The model outputs a probability distribution over the vocabulary for the next token.
- **Greedy:** always the most likely token (deterministic, can be repetitive).
- **Temperature:** scales the distribution; low = focused/deterministic, high = diverse/creative.
- **Top-p (nucleus):** sample only from the smallest set of tokens whose probabilities sum to p. **Top-k:** only the k most likely.
- **Stop sequences / max tokens** end generation.
- Some hosted APIs have started removing these knobs on their newest models (the Claude API, for example, now rejects `temperature`/`top_p`/`top_k`), steering you to prompts, an effort/reasoning setting, and structured outputs instead. Open models and many other providers still expose them, and interviewers still ask what they do.

### 53.6 Reasoning ("thinking") models

- Models can spend extra tokens reasoning before the final answer (chain of thought done internally). Better on math, code, multi-step planning, agentic tasks.
- Costs: more output tokens and latency. Controlled with an **effort** level (low → max) on current Claude models.
- Use high effort for hard reasoning and coding; low effort for classification, extraction, chat.

### 53.7 Embeddings

- An **embedding** maps text (or images) to a fixed-length vector (e.g., 768–3,072 dimensions) such that similar meanings are close.
- Similarity: **cosine similarity** `a·b / (|a||b|)` (or dot product on normalized vectors).
- Uses: semantic search, RAG retrieval, clustering, deduplication, recommendations, classification features, semantic caching.
- Embedding models are separate, smaller models than chat LLMs. Use the **same model** for documents and queries; re-embed everything if you change models.

### 53.8 Multimodal models: images, documents, audio

*Asked occasionally — usually "how would you extract data from receipts?" or "can the bot read a screenshot?".*

**How images get in:** the image is cut into small patches, a vision encoder turns each patch into an embedding, and those embeddings sit in the same sequence as the text tokens. To the model, an image is just more tokens.

- **Cost scales with resolution.** Anthropic's docs estimate image tokens as roughly `width × height / 750`, so a 1000×1000 screenshot is about 1,300 tokens. Downscale images to the smallest size that keeps the detail you need.
- **PDFs** are often sent as page images plus extracted text, so the model sees tables and layout as well as the words. Long PDFs get expensive fast → extract the relevant pages first.
- **Audio:** either a speech-to-text step and then a text model (simple, cheap, easy to debug), or a native audio model for real-time voice, where the latency of speech → text → LLM → speech adds up to seconds.
- **Video:** sample frames (e.g., one per second, or on scene changes) and send them as images with timestamps.

**Typical design: receipt or invoice extraction**
```text
upload → resize/deskew → model with an image + a JSON schema
       → validate (totals add up? date parses? currency known?)
       → low confidence or failed check → human review queue
```
The validation step matters more than the model choice: arithmetic checks catch most misreads.

### 53.9 Hallucination

- The model generates fluent but false content — it predicts plausible text, it doesn't look facts up.
- Causes: missing knowledge, outdated knowledge (training cutoff), ambiguous prompts, pressure to answer.
- Mitigations: **grounding** with retrieved evidence (RAG) and live tools; require **citations**; allow "I don't know"; structured outputs; verification steps; evals that measure faithfulness.

### 53.10 Model choice

| Factor | Bigger/frontier model | Smaller/faster model |
|---|---|---|
| Quality on hard tasks | Best | Weaker |
| Latency | Higher | Lower |
| Cost per token | Higher | Much lower |
| Good for | Agents, coding, complex reasoning | Classification, routing, extraction, high-volume simple tasks |

Common pattern: **route** easy requests to a small model and hard ones to a big model — but first measure whether the big model at low effort is good enough (one model is simpler and keeps one cache).

---

## 54. Prompting

### 54.1 Structure of a request

- **System prompt:** role, goals, rules, output format, constraints. Stable across requests (cache it).
- **Messages:** alternating `user` and `assistant` turns (plus tool calls and tool results).
- **Context:** retrieved documents, user profile, tool outputs — clearly delimited.

### 54.2 Techniques that work

- **Be explicit and specific:** task, audience, format, length, what to do when unsure.
- **Give context and the "why":** models follow rules better when they know the reason.
- **Delimit inputs** with tags: `<document>...</document>`, `<question>...</question>` — separates instructions from data.
- **Few-shot examples:** 2–5 input/output examples for format and edge cases (diverse, not repetitive).
- **Ask for reasoning or use thinking** on multi-step problems; ask for the final answer in a fixed format.
- **Structured output:** JSON schema enforced by the API (structured outputs) rather than "please return JSON".
- **Decompose:** chain several focused calls (extract → analyze → write) instead of one giant prompt.
- **Allow abstention:** "If the answer isn't in the documents, say you don't know."
- **Positive instructions** ("write in plain prose") work better than long lists of don'ts.

### 54.3 Prompt engineering as engineering

- Version prompts like code; store `prompt_version` with every response.
- Change one thing at a time and measure on an eval set (see [section 60](#60-evaluation)).
- Keep the stable prefix stable (system prompt, tool definitions) → prompt caching works.
- Treat retrieved content and tool results as **data, not instructions** (prompt injection, [section 64](#64-llm-safety-and-security)).

---

## 55. Calling an LLM API

### 55.1 Anatomy of a request and response

Every chat-model API has the same shape, whatever the provider calls the fields:

```text
request:   model, max output tokens, system instructions,
           messages = [ {role: user, content}, {role: assistant, content}, … ],
           optional: tool definitions, output schema, stream = true

response:  content = [ text blocks, tool-call blocks ]
           stop reason
           usage = input tokens, output tokens, cached input tokens
```

**Always check the stop reason before using the content** — several "failures" come back as a normal HTTP 200:

| Stop reason (names vary) | Meaning | What your code does |
|---|---|---|
| End of turn | The model finished | Use the answer |
| Max tokens / length | Output was cut off | Raise the limit, or ask for shorter output; never show a half-answer as complete |
| Tool use | The model wants a tool run | Run it, send the result back ([56. Tool calling (function calling)](#56-tool-calling-function-calling)) |
| Refusal / content filter | Declined by safety checks | Show a safe message, or route to a fallback |
| Stop sequence | Hit a stop string you set | Usually expected |

```python
from dataclasses import dataclass

@dataclass
class Reply:
    text: str
    stop_reason: str
    input_tokens: int
    output_tokens: int

class Truncated(Exception): ...
class Refused(Exception): ...

def answer_text(reply: Reply) -> str:
    """Turn a model reply into text the app can show, or raise for the cases that need handling."""
    if reply.stop_reason == "max_tokens":
        raise Truncated(f"cut off after {reply.output_tokens} tokens")
    if reply.stop_reason == "refusal":
        raise Refused()
    return reply.text

assert answer_text(Reply("Order 1042 shipped.", "end_turn", 900, 6)) == "Order 1042 shipped."
for bad, exc in [("max_tokens", Truncated), ("refusal", Refused)]:
    try:
        answer_text(Reply("partial…", bad, 900, 1024))
        raise AssertionError("should have raised")
    except exc:
        pass
```

`usage` is what you bill and budget on: log it per request with the user/tenant, model and prompt version.

### 55.2 Streaming (needed for chat UIs)

A long answer takes 10–30 seconds to generate. With **streaming**, the API sends tokens as they're produced, and the user sees the first words in about a second (**time to first token**, TTFT) instead of staring at a spinner.

```text
model API ──(chunks)──► your server ──(SSE)──► browser
                         │
                         └─ also appends each chunk to a buffer (Redis),
                            and saves the full message when the stream ends
```

- **Server → browser** is usually **SSE** (server-sent events): plain HTTP, one `data: …` line per chunk, automatic reconnect built into browsers ([5.4 Real-time communication](#54-real-time-communication)).
- **Give each event an ID.** A reconnecting browser sends `Last-Event-ID`, and the server replays only what was missed from its buffer.
- **If the user closes the tab**, either finish and save the answer (they'll see it on reload) or cancel the model call to save cost — decide per product.
- Long outputs should always be streamed; a single non-streaming response can exceed HTTP timeouts.

```python
import json

def sse_events(chunks: list[str], after_id: int = 0) -> list[str]:
    """Format buffered chunks as SSE events, skipping those the client already has."""
    return [f"id: {i}\ndata: {json.dumps({'text': c})}\n\n"
            for i, c in enumerate(chunks, start=1) if i > after_id]

# chunks received from the model so far
buffer = ["Your ", "order ", "1042 ", "shipped ", "today."]
# client connected, got 2 events, then dropped
first = sse_events(buffer[:2])
assert first[1].startswith("id: 2\n")
# reconnect with Last-Event-ID: 2
resumed = sse_events(buffer, after_id=2)
assert [json.loads(e.split("data: ")[1])["text"] for e in resumed] == ["1042 ", "shipped ", "today."]
```

### 55.3 Structured outputs

When code consumes the output (a ticket category, extracted fields), you need valid JSON in a known shape. Three levels of reliability:

1. **Ask in the prompt** ("reply with JSON") — usually works, sometimes adds prose or breaks the format.
2. **JSON mode** — guarantees valid JSON, not your fields.
3. **Schema-constrained output** — you pass a JSON schema and the provider constrains generation so the output always matches it. Use this when your provider has it.

Even with level 3, the schema only guarantees *shape*. **Business rules are still your job:** is the category one you support? Is the amount positive? Does the order ID belong to this user?

```python
import json

ALLOWED = {"payments", "shipping", "returns", "account"}

def parse_ticket(raw: str) -> dict:
    data = json.loads(raw)
    if data.get("category") not in ALLOWED:
        raise ValueError(f"unknown category {data.get('category')!r}")
    if data.get("priority") not in {"low", "normal", "urgent"}:
        raise ValueError("bad priority")
    return data

def classify(model, text: str, attempts: int = 2) -> dict:
    """Call the model; if the output breaks a rule, retry once with the error in the prompt."""
    prompt, last_error = text, None
    for _ in range(attempts):
        try:
            return parse_ticket(model(prompt))
        except ValueError as exc:            # json.JSONDecodeError is a ValueError too
            last_error = exc
            prompt = f"{text}\nYour previous answer was invalid: {exc}. Use one of {sorted(ALLOWED)}."
    raise last_error

# not an allowed category
replies = iter(['{"category": "billing", "priority": "urgent"}',
                '{"category": "payments", "priority": "urgent"}'])
fake_model = lambda prompt: next(replies)
assert classify(fake_model, "Card charged twice for order 1042")["category"] == "payments"
```

### 55.4 Prompt caching

Most requests to the same feature start with the same long prefix: system instructions, tool definitions, reference documents, and in a chat, the whole earlier conversation. Providers can **cache the processed prefix**, so the next request that starts with exactly the same bytes skips recomputing it.

- **It's a prefix match.** The cache key is everything up to a cache point, in order (typically tools → system → messages). Change one byte early on and everything after it is a miss.
- **Put stable content first, varying content last.** A timestamp or request ID at the top of the system prompt silently disables caching for every request.
- **Cached input is billed at a fraction of the normal input price** (writes can cost a little more), and it cuts time to first token for long prompts. Entries expire after minutes of disuse, so caching pays off for busy prompts and multi-turn chats.
- Verify it works: the response `usage` reports cached tokens. If it's zero on the second identical-prefix request, something in the prefix is changing.

```python
def cached_tokens(prev: list[str], cur: list[str]) -> int:
    """How many leading tokens of `cur` match the previous request (what a prefix cache can reuse)."""
    n = 0
    for a, b in zip(prev, cur):
        if a != b:
            break
        n += 1
    return n

# 1,200 stable tokens
SYSTEM = ["You", "are", "a", "support", "assistant", "…"] * 200
good_1 = SYSTEM + ["Where", "is", "order", "1042?"]
good_2 = SYSTEM + ["Can", "I", "return", "shoes?"]
bad_1 = ["Time:", "10:00:01"] + SYSTEM + ["Where", "is", "order", "1042?"]
bad_2 = ["Time:", "10:00:02"] + SYSTEM + ["Can", "I", "return", "shoes?"]
assert cached_tokens(good_1, good_2) == 1200     # whole system prompt reused
assert cached_tokens(bad_1, bad_2) == 1          # a timestamp at the top ruins it
```

### 55.5 Errors, retries, fallbacks

| Error | Retry? | Why |
|---|---|---|
| 400 bad request, 401/403 auth, 404 | No | A bug or config problem; retrying repeats it |
| 429 rate limited | Yes, after the `retry-after` delay | You're over your requests/tokens per minute |
| 500, 502, 503, "overloaded" (some providers use 529) | Yes, with backoff | Transient on the provider's side |
| Timeout / connection reset | Yes, with backoff | Network; but for long generations prefer streaming |
| 200 with truncation or refusal | Not as an error | Handle the stop reason ([55.1 Anatomy of a request and response](#551-anatomy-of-a-request-and-response)) |

Production patterns on top of that:
- **Bounded retries** with exponential backoff and jitter ([47.3 Retries](#473-retries)).
- **Fallback model** (another model or provider) when the primary keeps failing, behind a **circuit breaker** so you stop hammering a provider that's down.
- **Per-tenant rate limits** in your own gateway, so one team's batch job can't use up the shared provider quota ([70. Problem: Design an LLM gateway (platform for many internal teams)](#70-problem-design-an-llm-gateway-platform-for-many-internal-teams)).
- Retries before the first streamed token are invisible to the user; after it, show what arrived and offer "retry".

```python
import random

class APIError(Exception):
    def __init__(self, status: int, retry_after: float | None = None):
        self.status, self.retry_after = status, retry_after

RETRYABLE = {429, 500, 502, 503, 529}

def call_with_fallback(models: list, request: str, attempts: int = 3, sleep=lambda s: None) -> tuple[str, str]:
    """Try each model in order; retry transient errors with backoff; never retry 4xx bugs."""
    for name, call in models:
        for attempt in range(attempts):
            try:
                return name, call(request)
            except APIError as e:
                if e.status not in RETRYABLE:
                    raise
                delay = e.retry_after or min(8, 0.5 * 2 ** attempt) * random.uniform(0.5, 1.0)
                sleep(delay)
    raise RuntimeError("all models failed")

def overloaded(req):
    raise APIError(529)

def flaky_then_ok():
    calls = {"n": 0}
    def call(req):
        calls["n"] += 1
        if calls["n"] == 1:
            raise APIError(429, retry_after=2)
        return "answer"
    return call

waits = []
assert call_with_fallback([("primary", overloaded), ("backup", flaky_then_ok())], "hi",
                          sleep=waits.append) == ("backup", "answer")
# 3 backoffs on primary, then retry-after
assert len(waits) == 4 and waits[-1] == 2
try:
    call_with_fallback([("primary", lambda r: (_ for _ in ()).throw(APIError(400)))], "hi")
    raise AssertionError("400 must not be retried")
except APIError as e:
    assert e.status == 400
```

---

## 56. Tool calling (function calling)

### 56.1 How it works

1. You send the model a list of **tools**: name, description, JSON schema for inputs.
2. The model decides whether to call one; it returns a `tool_use` block with arguments (it **does not execute** anything).
3. **Your code** validates the arguments, runs the function, and sends back a `tool_result` with the same `tool_use_id`.
4. The model continues — calls more tools or writes the final answer.

```text
user → model → tool_use(get_order, {"id": 42}) → your code runs get_order → tool_result → model → answer
```

### 56.2 Designing good tools

- **Clear names and descriptions** — the description is the prompt for when/how to use the tool. Say what it returns and when *not* to use it.
- **Few, well-separated tools** beat many overlapping ones (the model picks wrong when tools look similar).
- **Typed, constrained inputs:** enums, required fields, formats; `strict: true` guarantees schema-valid arguments.
- **Return compact, useful results:** IDs + key fields, not 50 KB of raw JSON (context is precious). Paginate.
- **Errors as results:** return `is_error: true` with a helpful message ("order 42 not found; ask the user for the order number") so the model can recover.
- **Side effects need guardrails:** confirmation for destructive actions, idempotency keys, permissions checked in code — never trust the model's arguments for authorization.
- **Parallel tool calls:** one response can request several tools; run them concurrently and return **all results in one message**.

### 56.3 The loop in code

The loop itself — call the model, run the requested tools, send the results back, repeat with a step limit — is the agent loop, built and tested with a fake model in [57.3 The agent loop](#573-the-agent-loop). The same loop against a real API, written by hand and with an SDK helper that runs it for you, is in [76.7 Tool use: manual loop](#767-tool-use-manual-loop).

---

## 57. Agents

### 57.1 Workflow or agent?

- **Workflow:** code decides the steps; the LLM fills in each step. Predictable, testable, cheaper. Use when the steps are known.
- **Agent:** the LLM decides which tool to call next in a loop until done. Flexible for open-ended tasks, but costlier, slower, and harder to test.
- **Start simple:** single call → workflow → agent, only as needed.

**Common workflow patterns**

| Pattern | How | Example |
|---|---|---|
| Prompt chaining | Output of step 1 feeds step 2 (with checks between) | Outline → draft → edit |
| Routing | Classify the input, send to a specialized prompt/model | Billing vs tech support vs sales |
| Parallelization | Run independent subtasks concurrently, then aggregate; or vote | Check a doc against 5 policies in parallel |
| Orchestrator–workers | A lead model splits work dynamically and delegates to workers | Research across many sources |
| Evaluator–optimizer | One call generates, another critiques; loop until good | Code generation with test feedback |

### 57.2 Building blocks of an agent

- **System prompt:** purpose, boundaries, response rules, when to stop.
- **Tools:** name, description, argument schema, and the application function behind it.
- **Memory:** task context (working memory) and relevant information from earlier interactions (long-term memory).
- **Orchestrator:** builds the context, calls the model, executes tools, repeats, enforces limits.

The model **requests** actions; application code **validates and executes** them.

### 57.3 The agent loop

```text
while not done:
    response = call_llm(context, tools)
    append response to history
    if response has tool calls:
        results = execute_tools(tool_calls)      # validate, authorize, run, time out
        append results to history
    else:
        return response
```

Apply **step, time, and cost limits**; stop when repeated actions make no progress.

**Roles:** user, assistant, tool results. System instructions are separate configuration. The chat UI shows user messages and final answers; internal tool exchanges are stored but usually hidden. Each model call = system prompt + summary of older turns + recent messages + current task state.

### 57.4 Reliability features (what interviewers probe)

- **Limits:** max steps, max tokens/cost per run (task budget), wall-clock timeout, per-tool timeout.
- **Loop detection:** same tool + same args repeated → stop or nudge.
- **Checkpoints:** persist state after each step (messages so far, step index, completed tool results) → a crash, deploy, or provider outage resumes from the last step instead of redoing work.
- **Idempotent tools:** side-effecting tools take an idempotency key (`run_id + step + tool_call_id`) so a resumed run doesn't repeat an action.
- **Human-in-the-loop:** pause for approval before destructive or expensive actions; the run state waits in storage.
- **Backoff and fallback** on provider errors (429/529/5xx).
- **Cancellation:** user cancels → check a flag between steps, stop the model stream, mark the run CANCELLED.
- **Guardrails:** validate tool arguments; enforce permissions in code; limit what tools can reach (allowlists, sandboxing for code/shell).

### 57.5 Planning and reasoning styles

- **ReAct:** interleave reasoning ("I need the order status") and actions (tool calls), observing results each step. The default shape of tool-using agents.
- **Plan-and-execute:** first write a plan, then execute steps (re-plan on failure). Good for long tasks; plans go stale.
- **Reflection / self-critique:** review your own output against criteria, then revise. Works best with external feedback (tests, validators).
- **Thinking models** do much of this internally; you mainly control effort and give clear goals.

### 57.6 Multi-agent systems

- **Orchestrator + sub-agents:** a lead agent splits work; sub-agents run with their own fresh context (e.g., read 50 documents) and return short summaries → keeps the lead's context clean.
- **Benefits:** parallelism, separation of concerns, smaller contexts per agent.
- **Costs:** more tokens overall, coordination failures, harder debugging. Use when the task truly fans out.
- Use cheaper models for read-heavy sub-tasks, the strongest model for planning/synthesis.

### 57.7 Memory

The model remembers nothing between calls, so "memory" is whatever your code chooses to put back into the prompt. One example runs through this section: **a support bot for an online store**. In March a customer said "please always reply in Hindi"; today they ask "where's my order 1042?".

- **Working memory** is what *this* conversation needs: recent messages, tool results, a summary of older turns, and the task state (which order we're discussing, what we've already checked).
- **Long-term memory** is what should survive the session: "reply in Hindi", the current shipping address, and lessons the bot has learned ("E-104 on a retry usually means the bank wants OTP approval").

Both can live in an ordinary database table keyed by user; vector search is only needed once there are too many memories to filter by simple fields.

**Working memory: keep everything, send a bounded slice**

Store the full history. For each model call, assemble:

```text
system instructions
+ saved memories that apply ("reply in Hindi", shipping address v2)
+ summary of older turns ("asked about a refund for 1037 last week; resolved")
+ as many recent messages as fit the token budget
+ task state ("order 1042; status already fetched: SHIPPED")
```

When the budget is exceeded, summarize the oldest completed turns — but never cut the middle of a tool call and its result. Keep the originals, so the bot can look up the exact earlier message if a summary is too vague.

```python
def assemble_context(
    system: str,
    memories: list[str],
    summary: str,
    history: list[dict],
    task_state: str,
    budget: int,
    count=lambda s: len(s) // 4,
) -> list[dict]:
    """Keep fixed parts, then as many recent messages as fit; older ones are covered by the summary."""
    fixed = [system, "\n".join(memories), summary, task_state]
    used = sum(count(p) for p in fixed)
    kept = []
    for msg in reversed(history):  # newest first
        cost = count(msg["content"])
        if used + cost > budget:
            break
        kept.append(msg)
        used += cost
    kept.reverse()
    header = f"{system}\n<memory>{'; '.join(memories)}</memory>\n<summary>{summary}</summary>\n<task>{task_state}</task>"
    return [{"role": "system", "content": header}] + kept


history = [
    {"role": "user", "content": "x" * 400},
    {"role": "assistant", "content": "y" * 400},
    {"role": "user", "content": "Where is my order 1042?"},
]
ctx = assemble_context(
    "You are the store's support assistant.",
    ["reply in Hindi"],
    "Refund for order 1037 resolved last week.",
    history,
    "order 1042",
    budget=150,
)
# oldest dropped
assert [m["content"] for m in ctx[1:]] == ["y" * 400, "Where is my order 1042?"]
```

**Long-term memory: extract → check → merge → store → retrieve**

After a conversation, an extraction step (a model call with instructions on what counts) proposes candidate memories; code checks and stores them with a source, timestamp, scope and optional expiry. What the support bot should do with different things a customer says:

| Customer says | What it is | What to store |
|---|---|---|
| "Always reply in Hindi" | Lasting preference | Save |
| "I'm at the office today, deliver there" | Temporary | Nothing, or save with a one-day expiry |
| "Ship to my new address in Pune from now on" | Correction | Replace the old address; keep where the change came from |
| "My card is 4111…" | Sensitive | Never store; payment data stays in the payment system |

Procedural lessons ("E-104 on a retry usually means OTP approval") are useful but riskier: check them against outcomes before the bot relies on them, and never let them override permissions. Deleting a memory must also remove it from summaries and indexes that would bring it back.

**Who decides to save:** either the agent calls a `save_memory` tool when the prompt says something qualifies, or the application runs the extraction step after each conversation. The second is easier to test and to keep consistent.

### 57.8 Logging and tracing agent runs

When the support bot gives a wrong answer, you need to see exactly what it saw and did. Record **one trace per run**, with a child span for every model call, tool call and memory lookup:

```text
run 7f3a  "Where is my order 1042?"   outcome: answered   2.1 s   $0.004
├─ memory.retrieve   2 memories (language=hi, address v2)              12 ms
├─ model.call #1     prompt v14 · in 1,850 (cached 1,600) · out 40    640 ms
│                    → tool_use get_order {order_id: 1042}
├─ tool get_order    → SHIPPED, eta 3 Nov                              85 ms
└─ model.call #2     in 1,920 · out 60                                710 ms
                     → end_turn
```

- **Run:** IDs, outcome and why it stopped (answered, step limit, error, cancelled), total time and cost.
- **Model call:** model, prompt version, input/output/cached tokens, time to first token and total latency.
- **Tool call:** name, call ID, status, retries, duration — with arguments and results stored by reference in access-controlled storage, not pasted into logs.
- **Never log** credentials or payment details.

Dashboards on top of traces show task success rate, cost per resolved conversation, and repeated identical tool calls (a sign of loops). Every bad trace a user reports becomes a test case in the eval set ([60. Evaluation](#60-evaluation)).

---

## 58. MCP (Model Context Protocol)

```text
Host (your app, an IDE, a desktop assistant)         LLM provider
┌────────────────────────────────────────┐          ┌───────────┐
│ orchestrator ──────────────────────────┼─────────►│   model   │
│   ├─ MCP client A ── stdio ────────────┼──► server: files ───► disk
│   ├─ MCP client B ── HTTP ─────────────┼──► server: GitHub ──► GitHub API
│   └─ MCP client C ── HTTP ─────────────┼──► server: Postgres ► database
└────────────────────────────────────────┘
One client per server. Servers never talk to the model; the host does.
```

### 58.1 What and why

**MCP is a standard protocol for connecting LLM applications to external tools and data.**

- Without MCP: agent → your custom GitHub integration → GitHub API. You write each integration's tool definitions and execution adapter.
- With MCP: agent → MCP client → GitHub MCP server → GitHub API. Any compatible server exposes tools through a shared interface; any compatible host can use it.
- You still own authentication, permissions, and application logic.

### 58.2 Roles

- **Host:** the LLM application (Claude Desktop, Claude Code, Cursor, or your app). Manages one client per server.
- **Client:** the protocol handler inside the host; one client ↔ one server connection. It connects (stdio or HTTP), discovers capabilities (`tools/list`, `resources/list`, `prompts/list`), sends requests, and passes responses back. The 1:1 relationship isolates failures and permissions — one crashed server doesn't take down the others.
- **Server:** exposes a capability — a filesystem, GitHub, a Postgres database, Slack, an internal API. Runs locally as a subprocess or remotely over HTTP.


### 58.3 Server primitives


- **Tools — actions.** Callable operations with a name, description, and input schema; may have side effects (write a file, send a message). Hosts should show available tools and require confirmation for sensitive ones.
- **Resources — data.** Read-only context identified by URIs (`file:///notes.txt`, `db://products/schema`); clients can list, read, and subscribe to changes.
- **Prompts — templates.** Reusable prompt templates (often surfaced as slash commands), user-controlled.

A minimal server with the official Python `mcp` package (`pip install mcp`; not runnable here without it):

```py
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("docs-server")

@mcp.resource("docs://changelog/{version}")
def get_changelog(version: str) -> str:
    """Return the changelog for a specific API version."""
    return load_changelog(version)

@mcp.tool()
def search_codebase(query: str, file_type: str = "") -> str:
    """Search the codebase for files matching a query.

    Args:
        query: Search term to look for in file contents.
        file_type: Optional file extension filter (e.g., '.py').
    """
    return format_results(perform_search(query, file_type))

@mcp.prompt()
def code_review(language: str, code: str) -> str:
    """Guide a structured code review."""
    return f"Review this {language} code for correctness, performance, readability, security:\n{code}"
```

**Client primitives (server → client requests)**
- **Sampling:** the server asks the host's model for a completion (stays model-independent).
- **Elicitation:** the server asks the user for more information or confirmation.
- **Logging:** the server sends log messages to the client.

### 58.4 How MCP fits with tool calling

Every client follows the same lifecycle: connect (stdio or HTTP) → initialize and agree on capabilities → discover tools and resources → use them → close. A tool call then flows like this:

```text
Host / MCP client             MCP server              Model API
   │── initialize ───────────►│
   │◄─ capabilities ──────────│
   │── tools/list ───────────►│
   │◄─ get_order, … (schemas) │
   │── user question + tool definitions ─────────────────►│
   │◄─ tool call: get_order {order_id: 1042} ─────────────│
   │── tools/call get_order ─►│
   │◄─ {status: SHIPPED} ─────│
   │── tool result ──────────────────────────────────────►│
   │◄─ "Your order shipped and arrives on 3 Nov." ────────│
```

The model only ever sees ordinary tool definitions and results; MCP is how the host found the tools and how it runs them.

### 58.5 Layers

- **Data layer (what):** JSON-RPC 2.0 requests, responses, errors, notifications; capability negotiation; primitives; change notifications (e.g., tool list updated).
- **Transport layer (how):** **stdio** (host launches a local server process, talks over stdin/stdout) or **Streamable HTTP** (POST for requests, optional SSE for streaming, supports auth for remote servers).

```text
Operation:  tools/call("get_order", arguments)
Format:     JSON-RPC 2.0 request
Transport:  stdio or Streamable HTTP
```

### 58.6 MCP vs function calling

- **Function calling:** the model's ability to emit structured tool calls for tools the developer defined.
- **MCP:** how tools (and resources/prompts) are **packaged, discovered, and shared** across applications.
- They work together: MCP discovers tools → they're converted into the model's function-calling format → the model picks one → the call executes through MCP.
- MCP adds: dynamic discovery, standardized reusable integrations, capability negotiation, separation of tool logic from app code.

**Why build your own MCP client?** For custom authorization, logging, tool filtering, retries, or embedding MCP into your own product. General hosts (Claude Code, Claude Desktop, Cursor) already include clients but not your business logic.

**Security:** MCP servers are code with access to your systems — vet them, scope credentials minimally, require confirmation for side effects, and treat tool output as untrusted data (prompt injection).

---

## 59. RAG in depth

### 59.1 What and why

**Retrieval-Augmented Generation:** retrieve relevant evidence, put it in the prompt, generate a grounded answer with citations.

- Gives the model **private and fresh knowledge** without retraining.
- Reduces hallucination (answer from evidence) and enables citations.
- Documents provide knowledge; **APIs/databases provide live operational values** (don't retrieve "current price" from a stale document).

```text
Indexing: documents → parse/OCR → clean → chunk → enrich → embed → lexical + vector indexes
Query:    question → rewrite/resolve entities → filters → hybrid search → rerank
          → assemble context → answer + citations
```

### 59.2 Parsing and building a knowledge base

- **Parse first:** recover headings, reading order, tables, units, and diagram captions; remove repeated headers/footers. Bad parsing ruins everything downstream.
- **Keep provenance:** chunk/document IDs, parent section, page, source link, version, effective date, owner, **permissions**.
- **Add domain metadata:** product/model, component, error code, site, document type, language → used as filters.
- **Resolve terminology:** maintain aliases/acronyms; preserve exact identifiers; don't equate different models or revisions.
- **Update incrementally:** detect changed documents (hash/version); replace affected chunks; remove deleted ones; propagate permission changes.

### 59.3 Chunking: what to use when

| Strategy | Useful for | Trade-off |
| --- | --- | --- |
| Fixed token windows + overlap | Fast baseline for plain text | Cheap; can split a definition from its qualification |
| Recursive splitting | General prose: paragraph → sentence → token boundaries | Simple; doesn't understand meaning |
| Structure-based | Manuals, policies, SOPs: split by headings/sections | Preserves organization |
| Semantic splitting | Split at topic changes (embedding similarity drops) | More preprocessing and tuning |
| Parent–child | Retrieve small precise passages, then include their parent section | Better context; larger prompts |
| Table/layout-aware | Specifications, limits, scanned PDFs | Keeps headers, units, captions; parsing is harder |
| Whole item | Short FAQs, definitions, incident records | Keeps Q and A together; bad for long mixed topics |

- Small chunks → precise matches but lose context; large chunks → context but noise and cost.
- Overlap where boundaries cut meaning; too much overlap duplicates results.
- Start around ~300–800 tokens, then tune against real questions. There's no universal optimum.

```python
import re

def chunk_by_headings(doc: str, max_chars: int = 400, overlap: int = 50) -> list[dict]:
    """Structure-first chunking: split on markdown headings, then window long sections."""
    chunks, title = [], "Untitled"
    for block in re.split(r"(?m)^(#+ .*)$", doc):
        block = block.strip()
        if not block:
            continue
        if block.startswith("#"):
            title = block.lstrip("# ").strip()
            continue
        start = 0
        while start < len(block):
            piece = block[start:start + max_chars]
            # keep the section as context
            chunks.append({"section": title, "text": piece})
            if start + max_chars >= len(block):
                break
            start += max_chars - overlap
    return chunks

doc = "# Payments\nError E-104 means the card was declined.\n# Shipping\n" + "Orders ship in two days. " * 40
chunks = chunk_by_headings(doc)
assert chunks[0] == {"section": "Payments", "text": "Error E-104 means the card was declined."}
assert all(c["section"] == "Shipping" for c in chunks[1:]) and len(chunks) >= 3
```

### 59.4 Embeddings and vector search

- Embed each chunk (plus title/section context) → store vector + metadata in a vector index.
- Query: embed the question with the **same** model → find nearest vectors (cosine/dot product).
- **Exact search** (flat, brute force) is O(N) per query — fine up to ~100K vectors.
- **Approximate nearest neighbor (ANN)** indexes for scale:

| Index | How | Pros | Cons |
|---|---|---|---|
| **HNSW** | Multi-layer proximity graph; greedy search from a top layer down | High recall, fast queries, incremental inserts | Memory-heavy; slower builds |
| **IVF** | Cluster vectors (k-means); search only the nearest clusters (`nprobe`) | Less memory, fast | Recall depends on `nprobe`; needs training |
| **PQ** (product quantization) | Compress vectors into short codes | Huge memory savings | Lower accuracy |
| IVF-PQ / ScaNN / DiskANN | Combinations, disk-based graphs | Billion-scale | Tuning complexity |

- **Vector stores:** pgvector (Postgres), Elasticsearch/OpenSearch kNN, Pinecone, Weaviate, Milvus, Qdrant, Chroma, FAISS (library).
- Pick by scale, filtering needs, hybrid search support, ops burden. If you already run Postgres or Elasticsearch, start there.
- **Filtering:** pre-filter (apply metadata filter, then ANN) vs post-filter (ANN then filter — can return too few). Good vector DBs support filtered ANN.

### 59.5 Searching: choices and trade-offs

- **Keyword/BM25:** error codes, part numbers, exact terms; weak on paraphrases.
- **Vector:** similar meaning and vocabulary mismatch; can confuse near-identical identifiers ("model X-200" vs "X-210").
- **Hybrid:** run both, fuse ranks with **Reciprocal Rank Fusion (RRF)**: `score(d) = Σ 1 / (k + rank_i(d))`, k ≈ 60.
- **Reranking:** a cross-encoder (or LLM) scores (question, passage) pairs over the top ~50 → better ordering at extra latency/cost. Big quality win in practice.
- **Filters:** enforce authorization and confirmed model/version constraints **before** evidence reaches the LLM. Guessed filters can hide the answer.
- **Query rewriting:** resolve "that machine" using conversation context; keep original identifiers; decompose multi-part questions only when needed.
- **HyDE:** generate a hypothetical answer, embed it, search with it (helps when questions are short/vague).
- **Multi-query:** generate a few paraphrases, search each, fuse.
- **Graph retrieval:** for explicit relationships (equipment → component → failure mode); requires entity resolution and maintained edges. Add when ordinary retrieval misses relationship questions.
- **Agentic RAG:** the model decides when/what to search (search is a tool), can search multiple times and refine.

Mini hybrid retriever (BM25 + bag-of-words cosine + RRF), runnable:

```python
import math
import re
from collections import Counter

def tokenize(text: str) -> list[str]:
    return re.findall(r"[a-z0-9]+(?:-[a-z0-9]+)*", text.lower())

class BM25:
    def __init__(self, docs: list[str], k1: float = 1.5, b: float = 0.75):
        self.docs = [tokenize(d) for d in docs]
        self.k1, self.b = k1, b
        self.avgdl = sum(map(len, self.docs)) / len(self.docs)
        df = Counter(t for d in self.docs for t in set(d))
        n = len(self.docs)
        self.idf = {t: math.log(1 + (n - c + 0.5) / (c + 0.5)) for t, c in df.items()}

    def scores(self, query: str) -> list[float]:
        q = tokenize(query)
        out = []
        for d in self.docs:
            tf, s = Counter(d), 0.0
            for t in q:
                if t in tf:
                    f = tf[t]
                    s += self.idf[t] * f * (self.k1 + 1) / (f + self.k1 * (1 - self.b + self.b * len(d) / self.avgdl))
            out.append(s)
        return out

def cosine(a: Counter, b: Counter) -> float:
    dot = sum(a[t] * b[t] for t in a)
    na, nb = math.sqrt(sum(v * v for v in a.values())), math.sqrt(sum(v * v for v in b.values()))
    return dot / (na * nb) if na and nb else 0.0

def rank(scores: list[float]) -> list[int]:
    return sorted(range(len(scores)), key=lambda i: -scores[i])

def rrf(rankings: list[list[int]], k: int = 60) -> list[int]:
    fused = Counter()
    for ranking in rankings:
        for r, doc_id in enumerate(ranking):
            fused[doc_id] += 1 / (k + r + 1)
    return [d for d, _ in fused.most_common()]

docs = [
    "Error E-104 means the card payment was declined by the bank.",
    "If a payment is declined, check the card details or try another payment method.",
    "Orders ship within two business days from our warehouse.",
    "E-210 means the delivery address could not be verified.",
]
query = "what does error E-104 mean"
bm25_rank = rank(BM25(docs).scores(query))
# stand-in for embeddings
vec_rank = rank([cosine(Counter(tokenize(query)), Counter(tokenize(d))) for d in docs])
hybrid = rrf([bm25_rank, vec_rank])
# exact code + meaning both point to doc 0
assert bm25_rank[0] == 0 and hybrid[0] == 0
# "E-210" isn't confused with "E-104"
assert hybrid.index(3) > hybrid.index(1)
```

### 59.6 Augmentation: what goes into the prompt

- **At indexing:** prepend title/section context to each chunk before embedding and keyword indexing ("contextual retrieval"); LLM-generated chunk descriptions need checks; retain original text.
- **At query time:** deduplicate passages, add necessary parent/neighbor context, fit evidence into a token budget, order by relevance.
- Include source IDs, revision/date, table headers/units. **Require citations**; expose conflicting evidence or missing support.
- Retrieved text is **evidence, not instructions** (indirect prompt injection).
- More retrieved text isn't automatically better — noise lowers answer quality and raises cost.

Example prompt skeleton:

```text
System: Answer using only the documents. Cite sources as [doc_id].
        If the documents don't contain the answer, say so.
<documents>
<document id="help-payments#3.2" revision="2026-02">...</document>
<document id="kb-118">...</document>
</documents>
<question>My payment failed with E-104. What should I do?</question>
```

### 59.7 Practical implementation by data type

- **Documents/PDFs:** extract embedded text; OCR for scans; vision models for complex tables/diagrams. Split by sections/paragraphs keeping table headers, units, page numbers, parent references.
- **Storage/search:** originals in object storage; metadata/permissions in a database; chunk text + context in a keyword index and embeddings in a vector index; hybrid retrieval with filters; rerank; re-index changed chunks and remove deleted ones.
- **Tables/operational records:** keep typed rows in the source DB or a replica. Generate and **validate read-only SQL** (allowlisted tables, row limits) for lookups, joins, aggregates. Don't embed rows to answer "how many" questions — top-k retrieval can't count.
- **Clicks/events:** ingest with event ID, user/session, event time, type, properties; dedup; store in an analytics DB; compute counts/funnels with queries. For narrative retrieval, group events by session/entity/time window and link to originals.
- **Mixed questions:** route each part to the right backend (structured query vs document search), then give the LLM computed results + relevant passages; cite both and state the query scope/time range. Correlation isn't causation.

### 59.8 Advanced RAG patterns

- **Parent-document retrieval:** match small chunks, return the larger parent.
- **Sentence-window retrieval:** match a sentence, include ±N neighbors.
- **GraphRAG:** build an entity/relationship graph (or use a curated taxonomy) → traverse for relationship questions; community summaries for global "what are the themes" questions.
- **Entity-constrained retrieval:** resolve entities in the question deterministically (alias tables), filter chunks tagged with those entity IDs (a curated taxonomy/knowledge graph).
- **Self-checking:** after answering, verify each claim is supported by a cited passage; regenerate or flag if not.
- **Freshness:** time-decay boosts; "effective date" filters; prefer live tools for current values.

### 59.9 Why RAG fails (debug in this order)

1. **Parsing:** tables mangled, text missing → fix extraction.
2. **Chunking:** answer split across chunks or buried in noise.
3. **Retrieval:** right chunk not in top-k (measure recall@k) → hybrid, better embeddings, query rewriting, metadata filters.
4. **Ranking:** right chunk retrieved but too low → reranker.
5. **Context assembly:** truncated or drowned in irrelevant text.
6. **Generation:** model ignores evidence or hallucinates → prompt (cite, abstain), stronger model.
7. **Data:** the answer isn't in the corpus, or it's outdated/conflicting.

Diagnose retrieval before changing the generator.

---

## 60. Evaluation

### 60.1 Why it's the core skill

LLM outputs are non-deterministic and quality regresses silently when prompts, models, or data change. **Without evals you can't tell if a change helped.**

### 60.2 Building an eval set

- 50–500 realistic inputs from real traffic (anonymized) + hard edge cases + known past failures.
- For each: expected answer, or required facts, or the relevant document IDs, or a rubric.
- Split: development set (iterate on it) and held-out test set (report on it).
- Version the dataset; add every production failure as a new case.

### 60.3 Metrics

**Retrieval**
- **Recall@k:** fraction of questions whose relevant document is in the top k. The most important RAG metric.
- **Precision@k:** fraction of the top k that is relevant.
- **MRR:** mean of 1/rank of the first relevant result.
- **nDCG:** rank-aware, supports graded relevance.

**Generation**
- **Correctness:** matches the expected answer (exact match for short answers; rubric/LLM judge for long ones).
- **Faithfulness / groundedness:** every claim supported by the provided context.
- **Citation accuracy:** cited passages actually support the claims.
- **Answer relevance, completeness, format compliance, refusal appropriateness.**

**System:** latency (TTFT, total), cost per request/task, error rate, tool-call success rate.

**Agents:** task success rate, steps/tokens per task, tool error rate, trajectory checks (did it call the right tools in a sensible order), safety violations.

```python
def recall_at_k(results: list[list[str]], relevant: list[set[str]], k: int) -> float:
    hits = sum(bool(set(r[:k]) & rel) for r, rel in zip(results, relevant))
    return hits / len(results)

def mrr(results: list[list[str]], relevant: list[set[str]]) -> float:
    total = 0.0
    for r, rel in zip(results, relevant):
        total += next((1 / (i + 1) for i, d in enumerate(r) if d in rel), 0.0)
    return total / len(results)

results = [["d3", "d1", "d9"], ["d2", "d7", "d4"], ["d5", "d6", "d8"]]
relevant = [{"d1"}, {"d2"}, {"d0"}]
# 2 of 3 queries hit in top 2
assert round(recall_at_k(results, relevant, k=2), 3) == 0.667
assert round(mrr(results, relevant), 3) == 0.5                      # (1/2 + 1 + 0) / 3
```

### 60.4 LLM-as-judge

- Use a model to grade outputs against a rubric (correct? grounded? follows format?) → scalable.
- Make it reliable: specific rubric with examples, ask for a verdict + short justification, use binary or small scales, grade one criterion at a time, **calibrate against human labels** (measure agreement).
- Biases: position bias (prefers first option in pairwise), verbosity bias, self-preference. Swap order, control length, use a different model family for judging when possible.

### 60.5 Online evaluation

- Logging + user feedback (thumbs, edits, regenerations), A/B tests on prompts/models, guardrail hit rates, escalation rates (support bots), task completion.
- Canary new prompts/models on a slice of traffic; compare metrics before full rollout.
- **CI gate:** run the eval suite on every prompt/model change; block if quality drops beyond a threshold.

---

## 61. Prompting vs RAG vs fine-tuning

| Need | Best tool |
|---|---|
| Change behavior/format with a few examples | **Prompting** (+ few-shot, structured outputs) |
| Use private, changing, or large knowledge; need citations | **RAG** |
| Live values (inventory, prices, status) | **Tool calls / APIs** |
| Consistent style/format at scale, specialized narrow task, smaller/cheaper model matching a big one | **Fine-tuning** (or distillation) |
| Teach new facts | Usually **RAG**, not fine-tuning (fine-tuned facts go stale and are hard to cite) |

**Fine-tuning methods**
- **Full fine-tuning:** update all weights — expensive.
- **LoRA / QLoRA:** train small low-rank adapter matrices on top of frozen weights (QLoRA: base model quantized to 4-bit) → cheap, swappable adapters.
- **SFT:** supervised pairs (prompt → ideal response).
- **Preference tuning:** RLHF (reward model + RL), **DPO** (directly from preferred/rejected pairs) → aligns tone and quality.
- **Distillation:** train a small model on a large model's outputs for one task.

**Order of operations:** prompt engineering → RAG/tools → evals → only then consider fine-tuning, with a clear metric it must beat.

---

## 62. Serving and inference

### 62.1 Latency anatomy

- **Prefill:** process all input tokens in parallel → dominates **time to first token (TTFT)** for long prompts.
- **Decode:** generate output tokens one at a time → **time per output token (TPOT)**; total ≈ TTFT + output_tokens × TPOT.
- Output tokens are slower and more expensive than input tokens → limit output length when you can.

### 62.2 Self-hosting basics (open models)

- **GPU memory ≈ parameters × bytes per parameter + KV cache.** A 7B model in FP16 ≈ 14 GB of weights; 70B ≈ 140 GB (multiple GPUs). 4-bit quantization ≈ ¼ of that.
- **KV cache** per request grows with context length → limits how many concurrent requests fit (worked numbers below).
- **Continuous batching:** add/remove requests from the GPU batch at every decode step (instead of waiting for a whole batch) → much higher throughput.
- **PagedAttention (vLLM):** stores KV cache in pages to avoid fragmentation → more concurrent requests.
- **Quantization:** INT8/INT4/FP8 → less memory, faster, small quality loss.
- **Speculative decoding:** a small draft model proposes several tokens; the big model verifies them in one pass → faster decode.
- **Prefix caching:** reuse KV cache for shared prompt prefixes (system prompts).
- **Parallelism:** tensor parallel (split layers across GPUs), pipeline parallel (split layers in stages), replicas for throughput.
- Serving stacks: vLLM, TGI, TensorRT-LLM, SGLang, Ollama/llama.cpp (local).
- **Autoscale on queue depth / GPU utilization**, not CPU.

### 62.3 KV-cache memory: the number that limits concurrency

*Comes up in GenAI infrastructure rounds: "how many concurrent users can one GPU serve?"*

For every token in every active sequence, each layer stores a key vector and a value vector:

```text
KV bytes per token = 2 (K and V) × layers × KV heads × head dim × bytes per value
```

Take a 70B-class model with 80 layers, head dimension 128, FP16 (2 bytes):
- With 64 KV heads (one per attention head): 2 × 80 × 64 × 128 × 2 ≈ **2.6 MB per token** → a 4K-token conversation needs ~10.7 GB.
- With **grouped-query attention** (8 KV heads shared by the 64 query heads, as in Llama 2 70B): **~0.33 MB per token** → ~1.3 GB per 4K conversation.

On an 80 GB GPU pair holding ~140 GB of FP16 weights in a 160 GB total, the ~20 GB left over fits about 15 such conversations at once. That's why GQA, KV-cache quantization (FP8) and PagedAttention matter: they decide how many users share a GPU, which decides cost per request.

```python
def kv_bytes_per_token(layers: int, kv_heads: int, head_dim: int, bytes_per_value: int = 2) -> int:
    return 2 * layers * kv_heads * head_dim * bytes_per_value

full = kv_bytes_per_token(80, 64, 128)
gqa = kv_bytes_per_token(80, 8, 128)
# ~2.6 MB vs ~0.33 MB per token
assert full == 2_621_440 and gqa == 327_680
per_conv_gb = gqa * 4096 / 1e9
assert round(per_conv_gb, 1) == 1.3
# ~15 concurrent 4K conversations in 20 GB
assert int(20 / per_conv_gb) == 14
```

### 62.4 Using hosted APIs at scale

- Rate limits (requests/min, tokens/min) per org/model → a **gateway** that queues, rate-limits per tenant, and retries with backoff.
- **Batch API** for offline jobs (evals, backfills, classification of millions of rows) → ~50% cheaper, results in hours.
- Streaming for interactive UX; timeouts sized for long outputs.
- Multi-provider or multi-model fallback for availability.

---

## 63. Cost and latency optimization

Free wins first, then trade-offs:

1. **Prompt caching** of stable prefixes (system prompt, tools, long documents, conversation history).
2. **Trim input:** retrieve fewer, better chunks (rerank); summarize old turns; compact tool results; don't resend huge documents.
3. **Limit output:** concise instructions, structured outputs, sensible `max_tokens`.
4. **Response caching:** exact-match cache for identical requests; **semantic cache** (embedding similarity above a high threshold) for FAQ-like traffic — beware returning a wrong cached answer for a subtly different question.
5. **Batch** non-interactive work.
6. **Effort / model routing:** lower effort or a smaller model for easy requests; measure quality per route.
7. **Parallelize** independent calls (asyncio.gather) to cut wall-clock latency.
8. **Stream** to improve perceived latency.

Measure **cost per completed task**, not per request — a cheap call that needs retries isn't cheap.

```python
import math
import re
from collections import Counter

class SemanticCache:
    """Toy semantic cache: bag-of-words cosine stands in for embeddings."""
    def __init__(self, threshold: float = 0.9):
        self.entries: list[tuple[Counter, str]] = []
        self.threshold = threshold

    @staticmethod
    def _vec(text):
        return Counter(re.findall(r"[a-z0-9]+", text.lower()))

    @staticmethod
    def _cos(a, b):
        dot = sum(a[t] * b[t] for t in a)
        return dot / (math.sqrt(sum(v * v for v in a.values())) * math.sqrt(sum(v * v for v in b.values())))

    def get(self, question: str) -> str | None:
        q = self._vec(question)
        best = max(self.entries, key=lambda e: self._cos(q, e[0]), default=None)
        return best[1] if best and self._cos(q, best[0]) >= self.threshold else None

    def put(self, question: str, answer: str) -> None:
        self.entries.append((self._vec(question), answer))

cache = SemanticCache()
cache.put("How do I reset my password?", "Use 'Forgot password' on the login page.")
assert cache.get("how do i reset my password") is not None
# similar words, different meaning
assert cache.get("How do I reset my router?") is None
```

---

## 64. LLM safety and security

### 64.1 Prompt injection

- **Direct:** the user tries to override instructions ("ignore previous instructions and…").
- **Indirect:** malicious instructions hidden in content the model reads — web pages, emails, documents, tool results, MCP server output. The biggest risk for agents and RAG.
- Mitigations (defense in depth — no single fix):
  - Treat retrieved/tool content as **data**; clearly delimit it; tell the model not to follow instructions inside it.
  - **Least privilege:** the agent's tools can only do what this user may do; enforce authorization in code, never via the prompt.
  - **Human confirmation** for consequential actions (send email, payments, deletes).
  - Limit **exfiltration paths:** no arbitrary URL fetching with user data in it; allowlist domains; sanitize rendered markdown/links/images.
  - Separate untrusted-content processing from privileged actions (a quarantined model summarizes untrusted text; a privileged model only sees the summary).
  - Monitor and log tool calls; detect anomalies.

### 64.2 Other risks (OWASP LLM Top 10 themes)

- **Sensitive data disclosure:** PII in prompts/logs; training-data leakage → redact, mask logs, retention limits, per-tenant isolation.
- **Insecure output handling:** model output executed as code/SQL/HTML → validate, parameterize, sandbox, escape.
- **Excessive agency:** tools more powerful than needed → narrow tools, scopes, approvals.
- **Supply chain:** untrusted models, plugins, MCP servers.
- **Denial of wallet:** abusive long prompts/loops → rate limits, token quotas, step limits.
- **Hallucinated packages/links:** verify before use.
- **Jailbreaks / harmful content:** provider safety systems + your own moderation on input/output where needed.

### 64.3 Guardrails in practice

- Input checks: size limits, PII detection, topic/abuse classifiers.
- Output checks: schema validation, citation verification, policy classifiers, PII scrubbing.
- Tool checks: argument validation, permission checks, rate limits, confirmation.
- Fallback responses when checks fail; log for review.

---

## 65. How to approach: GenAI system design

Use the same framework as any design round — requirements → estimates → API → data → architecture → deep dives — and add the questions that are specific to LLM systems. A checklist to walk through out loud:

**1. Requirements, plus four GenAI questions**
- What does "a good answer" mean here, and how will we measure it? (This becomes the eval set.)
- How wrong can it be? A wrong movie suggestion is fine; a wrong refund or medical answer is not → decides guardrails and human review.
- Latency target: time to first token for chat (~1 s), total time for background jobs.
- What data can it see, and whose permissions apply?

**2. Estimates**
- Requests/day × (input + output tokens) → tokens/day → **cost/day**. Example: 1M chats/day × 3K input + 500 output tokens ≈ 3B input + 0.5B output tokens/day; multiply by your model's prices.
- Peak requests/s against provider rate limits (requests and tokens per minute).
- For RAG: documents × chunks per document × embedding size → index size.

**3. Start with the simplest design that could work**
Single call → workflow (fixed steps) → agent only if the steps really can't be known in advance ([57.1 Workflow or agent?](#571-workflow-or-agent)). Say this explicitly; interviewers look for it.

**4. Architecture**
Client → API/gateway (auth, rate limits) → orchestrator (prompt assembly, retrieval, tools) → model provider via an LLM gateway → streaming back to the client; plus storage (conversations, documents, vectors), an async path (indexing, moderation, analytics), and tracing.

**5. Deep dives to offer**
- **Quality:** eval set, offline metrics, online feedback, regression tests on every prompt/model change ([60. Evaluation](#60-evaluation)).
- **Retrieval:** chunking, hybrid search, reranking, permissions ([59. RAG in depth](#59-rag-in-depth)).
- **Safety:** prompt injection, tool permissions in code, PII handling ([64. LLM safety and security](#64-llm-safety-and-security)).
- **Cost and latency:** caching, routing to smaller models, output limits, batch for offline work ([63. Cost and latency optimization](#63-cost-and-latency-optimization)).
- **Reliability:** provider fallback, timeouts, retries, idempotent tools ([55.5 Errors, retries, fallbacks](#555-errors-retries-fallbacks)).

**6. Failure modes to name before being asked**
Hallucinated answers, stale or missing documents, permission leaks through retrieval, runaway agent loops, a provider outage, and a cost spike from one tenant.

---

## 66. Problem: Design a ChatGPT-like chatbot

**How often asked:** very common · **Builds on:** [55.2 Streaming (needed for chat UIs)](#552-streaming-needed-for-chat-uis), [57.7 Memory](#577-memory), [63. Cost and latency optimization](#63-cost-and-latency-optimization), [27. Problem: Chat / messaging (WhatsApp, Slack)](#27-problem-chat--messaging-whatsapp-slack)

**Requirements**
- Multi-turn chat with streamed answers; conversation history and titles; file uploads (ask questions about a PDF); a choice of models.
- Non-functional: 10M DAU, time to first token < 1 s, per-user rate limits and plan quotas, safe outputs, history never lost.

**Estimates**
- 10M DAU × 10 messages/day = 100M messages/day ≈ 1.2K/s average, ~5K/s at peak.
- Each request sends history: say 3K input + 400 output tokens → 300B input and 40B output tokens/day. Cost is dominated by **input** tokens, which is why history summarization and prompt caching matter so much.
- Concurrent streams: 5K/s × ~15 s per answer ≈ **75K open streams** at peak → event-driven servers.
- Storage: 100M messages × ~2 KB ≈ 200 GB/day of message text.

**API**
```
POST /v1/conversations                               → {conversation_id}
POST /v1/conversations/{id}/messages  {content, model, file_ids}
     → SSE stream: event: delta {text} … event: done {message_id, usage}
GET  /v1/conversations?cursor=        GET /v1/conversations/{id}/messages?cursor=
POST /v1/files                        (pre-signed upload; async parsing)
```

**Architecture**
```text
Client ══ HTTPS/SSE ══► API gateway (auth, rate limits, quotas)
                          └► Chat service (async, stateless)
                               ├─ load conversation + summary (DB, cache)
                               ├─ context builder: system prompt, memory,
                               │    recent turns, file chunks (RAG)
                               ├─ LLM gateway ─► model providers (streaming)
                               ├─ stream tokens → client; buffer in Redis
                               └─ on finish: save message, record usage
Storage: messages (Cassandra/DynamoDB, partitioned by conversation_id),
         conversations (Postgres), files (S3) + vector index for file RAG,
         Redis for rate limits and in-flight stream buffers
Async:   moderation, title generation, summary updates, memory extraction
```

**Deep dive: streaming at scale**
Long-lived SSE connections need event-loop servers and least-connections load balancing ([6. Load balancing, proxies, gateways, service discovery](#6-load-balancing-proxies-gateways-service-discovery)). Each chunk is also appended to a Redis buffer keyed by message ID, so a client that reconnects with `Last-Event-ID` resumes instead of regenerating. If the user leaves, finish and save the answer (it appears on reload) — cancelling saves cost but loses the answer; pick per plan tier.

**Deep dive: context management**
The conversation grows every turn, and so does cost. Keep a running summary per conversation (updated asynchronously after each turn), send summary + the last N turns within a token budget, and keep the system prompt and older history as a stable prefix so prompt caching hits ([55.4 Prompt caching](#554-prompt-caching)).

**Deep dive: data model**
`messages(conversation_id, seq, role, content_blocks, model, tokens_in, tokens_out, created_at)` with partition key `conversation_id` and clustering by `seq` — the only query is "messages of one conversation in order", exactly the chat-storage pattern from [27. Problem: Chat / messaging (WhatsApp, Slack)](#27-problem-chat--messaging-whatsapp-slack).

**Rate limits and quotas:** token buckets per user in Redis on both requests/minute and tokens/minute; plan quotas (messages per day on the free tier) checked before calling the model.

**Failures**
- Provider error before the first token → retry or fall back to another model, invisibly.
- Error mid-stream → keep the partial answer, mark it incomplete, offer "regenerate".
- Chat service crash mid-stream → the client reconnects to another instance; the buffer in Redis has what was generated; generation itself is lost and restarted.
- Moderation flags an answer after it streamed → replace it in the UI and history; log for review.

**Why not keep history in the model?** There's nothing to keep it in — the model is stateless. Every request must carry its own context, which is why the context builder is the heart of this design.

---

## 67. Problem: Design enterprise document Q&A (RAG)

**How often asked:** very common · **Builds on:** [59. RAG in depth](#59-rag-in-depth), [60. Evaluation](#60-evaluation), [64. LLM safety and security](#64-llm-safety-and-security)

**Requirements**
- Employees ask questions over ~10M internal documents (wiki pages, shared drives, PDFs, tickets) and get answers with citations.
- **Permissions must be respected**: nobody sees an answer built from a document they can't open.
- Documents change daily; deleted documents must stop appearing quickly. p95 < 5 s for a full answer.

**Estimates**
- 10M documents × ~20 chunks = **200M chunks**. At 1,024 dimensions × 4 bytes, that's ~800 GB of raw vectors → quantization (e.g., 8-bit or product quantization) or a managed vector database, sharded by tenant or source.
- 50K employees × 10 questions/day = 500K questions/day ≈ 6 QPS average, ~30 at peak — small for search, meaningful for LLM cost.
- Daily churn of 1% → 100K documents re-parsed and re-embedded per day.

**API**
```
POST /v1/ask {question, conversation_id?}  → SSE: answer deltas … done {citations[]}
POST /v1/feedback {answer_id, rating, comment}
(ingestion is driven by connectors, not a public API)
```

**Architecture**
```text
Connectors (wiki, drive, ticketing) ─► change feed / webhooks ─► ingestion queue
  ─► workers: fetch → parse/OCR → chunk → add context (title, section,
     ACLs, dates) → embed
  ─► indexes: vector + BM25 (same chunk IDs) + metadata/ACL store; originals in S3

Query: user → auth (resolve groups) → rewrite question using the conversation
  → hybrid search with ACL filter → rerank top 50 → top ~8 into the prompt
  → answer with [citations] → check citations exist → stream
Feedback + traces → eval store → regression suite
```

**Deep dive: permissions**
Store the allowed users and groups on every chunk, and **filter inside the search** (pre-filter), not after — post-filtering top-50 results can leave nothing, and anything retrieved can leak into the answer. Permission changes are more urgent than content changes, so sync ACLs on their own fast path. Never rely on the prompt ("don't reveal documents the user can't see") to enforce access.

**Deep dive: freshness and deletes**
Track each document's version or content hash; re-ingest only what changed; delete a document's chunks from every index when it's deleted at the source. Changing the embedding model means re-embedding everything: build a new index alongside the old one and switch an alias when it's ready.

**Deep dive: quality**
Hybrid search plus reranking, contextual chunk headers, parent-child retrieval for long sections, and **abstaining** when the best reranker score is below a threshold ("I couldn't find this in the documents you can access"). See [59.9 Why RAG fails (debug in this order)](#599-why-rag-fails-debug-in-this-order).

**Evaluation**
A labelled set of questions with the documents that answer them → recall@k for retrieval; faithfulness and citation accuracy scored by a judge model on the answers; thumbs up/down in production feeding new test cases.

**Failures**
- Connector falls behind → answers are stale; show "last indexed" per source and alert on lag.
- Vector index node down → replicas; fall back to BM25-only search rather than failing.
- Model provider down → fall back to another model; if none, show the top retrieved passages without a generated answer.

**Why not fine-tune a model on the documents?** Fine-tuning doesn't reliably teach facts, can't cite sources, can't enforce per-user permissions, and must be redone every time documents change. Retrieval handles all four.

#### What improves retrieval the most

Anthropic's 2024 post "Introducing Contextual Retrieval" measured something every RAG builder runs into: chunks lose meaning when cut out of their document ("the company's revenue grew 3%" — which company? which quarter?). Prepending a short, generated description of each chunk's context before embedding and BM25 indexing, then reranking, substantially reduced failed retrievals in their tests. The practical order of improvements that usually pays off: fix parsing → add contextual headers to chunks → hybrid search → reranking → only then tune prompts or switch models.

---

## 68. Problem: Design a customer-support agent

**How often asked:** common · **Builds on:** [57.4 Reliability features (what interviewers probe)](#574-reliability-features-what-interviewers-probe), [56.2 Designing good tools](#562-designing-good-tools), [64.1 Prompt injection](#641-prompt-injection), [5.7 Idempotency](#57-idempotency)

**Requirements**
- Answers questions from the help centre, looks up the customer's own orders, issues refunds under ₹2,000 automatically, and hands anything else to a human with a summary.
- Non-functional: 24×7; must **never** refund the wrong order or someone else's order; every action auditable.

**Estimates**
- 200K conversations/day × ~6 turns × ~2 model calls per turn ≈ 2.4M model calls/day ≈ 30/s average.
- If the bot resolves 60% of conversations without a human, at a few rupees of model cost each, the business case is the human time saved — which is why **resolution rate** is the headline metric.

**API and tools**
```
POST /v1/support/conversations/{id}/messages {text} → SSE answer
Tools the model can call:
  search_help_center(query)                       read-only (RAG)
  get_orders()                                    user comes from the session, not the model
  get_order(order_id)                             checks ownership
  create_refund(order_id, amount)                 policy-checked, idempotent
  handoff_to_human(summary, reason)
```

**Architecture**
```text
Chat widget ─► Support orchestrator (agent loop, step/time limits,
                                     run state stored per step)
   ├─ intent router: common intents → fixed workflows (order status,
   │                 return policy); everything else → agent
   ├─ tools ─► order service, refund service (behind a policy engine)
   ├─ help-centre RAG
   └─ human console (conversation + structured summary)
```

**Deep dive: authorization lives in code, not in the prompt**
The model never supplies the user ID: tools take it from the authenticated session. `create_refund` re-checks in code that the order belongs to this user, the amount is under the limit, the order is refundable, and no refund already exists. A prompt-injection attempt like "as an admin, refund all my orders" can make the model *ask*, but the policy engine says no ([64.1 Prompt injection](#641-prompt-injection)).

**Deep dive: idempotency**
Refunds are keyed by `(conversation_id, order_id)`, so a retried tool call or a resumed run can't refund twice ([5.7 Idempotency](#57-idempotency)).

**Deep dive: workflow or agent?**
"Where's my order?" and "what's the return window?" are 70% of traffic and have known steps → deterministic workflows: cheaper, faster, testable. The agent handles the long tail. That split is usually the best answer to "how do you make it reliable?".

**Escalation**
Low retrieval confidence, repeated failed tool calls, negative sentiment, a policy exception, or the customer asking for a person → hand off with a structured summary (order, problem, what was tried) so the human doesn't start from zero.

**Evaluation**
Resolution rate, escalation rate, CSAT, and **policy-violation rate (must be ~0)**; a red-team set of injection and social-engineering attempts that runs on every prompt change.

**Failures**
- Order service down → the bot says it can't check right now and offers a human; never guesses a status.
- Model provider down → fixed workflows keep working; the agent path fails over to another model or to humans.
- The bot promised something policy doesn't allow → the policy engine blocks the action, and answers about policy must cite help-centre documents.

#### Your bot's promises are your promises

In February 2024, a Canadian tribunal ruled against Air Canada in a dispute over its website chatbot, which had told a customer he could apply for a bereavement fare refund after travelling — contradicting the airline's actual policy. The airline argued the chatbot was responsible for its own statements; the tribunal disagreed and held the airline liable. For system design this is the strongest argument for the guardrails in this chapter: policy answers must come from retrieved, approved documents (with citations), money-moving actions must go through policy checks in code, and uncertain cases must escalate to a human.

---

## 69. Problem: Design a coding agent (like Claude Code / Cursor agent)

**How often asked:** occasional · **Builds on:** [57. Agents](#57-agents), [57.6 Multi-agent systems](#576-multi-agent-systems), [64. LLM safety and security](#64-llm-safety-and-security)

**Requirements**
- Given a task ("fix this failing test", "add pagination to this endpoint"), the agent reads the repository, edits files, runs commands and tests, and iterates until done or out of budget.
- Non-functional: safe execution (it runs code), works on repositories far bigger than the context window, the user stays in control of risky actions.

**Estimates**
- A task takes 20–100 model calls; with tool outputs re-sent each step, 1–5M input tokens per task is common → **prompt caching and context compaction decide the cost**.
- One sandbox per active session: 10K concurrent sessions × (1–2 vCPU, 4 GB) is a real container fleet.

**Tools**
```
list_files(glob)   search(regex)   read_file(path, line_range)
edit_file(path, old_text, new_text)        run_command(cmd, timeout)
run_tests(selector)
```

**Architecture**
```text
Client (CLI / IDE) ─► Agent loop (model + tools, step and cost limits)
   ├─ context manager: repo map, search instead of reading everything,
   │                   compaction of old tool output, sub-agents for
   │                   wide exploration
   ├─ permission layer: allow / ask / deny per tool and command pattern
   └─ sandbox: container per session, CPU/memory/time limits,
               network policy, no secrets mounted
```

**Deep dive: context**
No repository fits in a prompt. The agent *searches* (regex, symbol index), reads only the line ranges it needs, and old tool output is summarized or dropped once it's no longer relevant. Sub-agents can read 50 files in their own context and return a one-paragraph summary, keeping the main context small.

**Deep dive: edits**
Targeted "replace this exact text with that" edits beat rewriting whole files: fewer output tokens, and far fewer accidental changes to code the model didn't mean to touch. Edits fail loudly if the old text doesn't match exactly once, which catches stale assumptions.

**Deep dive: the feedback loop**
Tests, linters and type checkers are what make an agent reliable: run them after edits, feed failures back, stop when green or when the budget runs out. Without a check the agent can run, it's guessing.

**Deep dive: safety**
Commands run in a sandbox; destructive actions (deleting files, force-pushing, installing packages, network access) need approval; credentials never enter the sandbox; file contents and web pages are treated as untrusted data, because a README can contain injected instructions.

**Failures**
- Loops (same failing command again and again) → detect repeats and stop or change approach.
- Sandbox dies → resume from the saved run state; edits are in the workspace, not in memory.
- The agent "fixes" a test by deleting it → review diffs, and give the agent an instruction and a check against weakening tests.

---

## 70. Problem: Design an LLM gateway (platform for many internal teams)

**How often asked:** common · **Builds on:** [55.5 Errors, retries, fallbacks](#555-errors-retries-fallbacks), [48. Problem: Rate limiter (distributed)](#48-problem-rate-limiter-distributed), [47.4 Circuit breaker](#474-circuit-breaker)

**Requirements**
- One internal API in front of several model providers and self-hosted models, used by dozens of teams.
- Per-team authentication, allowed models, quotas and monthly budgets; usage and cost reporting per team; PII redaction where required; caching; fallbacks when a provider fails.
- Non-functional: adds < 20 ms of latency; never buffers streamed responses; highly available (if it's down, every AI feature is down).

**Estimates**
- 50 teams, 2K requests/s at peak, a third of them streaming → thousands of concurrent long-lived connections.
- Usage logs: 2K/s × ~1 KB of metadata ≈ 170 GB/day → Kafka → warehouse.

**API**
An OpenAI/Anthropic-compatible surface so teams can use existing SDKs by changing the base URL, plus **model aliases**:
```
POST /v1/chat   {model: "default-chat", messages, stream: true, …}
"default-chat" → provider A / model X today; switch centrally tomorrow
```

**Architecture**
```text
Team services ─► LLM gateway (stateless, autoscaled, multi-AZ)
   ├─ authn (service tokens) + authz (allowed models per team)
   ├─ limits: requests/min and tokens/min per team (Redis token buckets),
   │          monthly spend counters with hard/soft caps
   ├─ request policy: PII redaction, size limits, blocked content
   ├─ routing: alias → provider/model, A/B splits, fallback chain,
   │           circuit breaker per provider
   ├─ cache: exact-match response cache (opt-in per route)
   ├─ streaming proxy: pass chunks through as they arrive
   └─ async usage log ─► Kafka ─► warehouse (cost dashboards, chargeback)
Secrets for provider keys in a vault; never in team code.
```

**Deep dive: budgets and fairness**
Two limits per team: **rate** (tokens/min, so one batch job can't starve interactive traffic — the shared provider quota is the scarce resource) and **spend** (monthly, so a bug that loops doesn't cost a fortune). Output tokens aren't known until the response ends, so reserve an estimate up front and reconcile when usage comes back.

**Deep dive: fallbacks and aliases**
A per-provider circuit breaker ([47.4 Circuit breaker](#474-circuit-breaker)) trips on error rates and sends traffic to the next model in the alias's fallback chain. Aliases also make model migrations a config change instead of 50 code changes — run the new model on a slice of traffic, compare eval scores and cost, then switch.

**Deep dive: streaming proxy**
The gateway must forward each chunk immediately (no buffering, no response-body logging inline), keep timeouts long enough for long generations, and count tokens from the final usage event.

**Failures**
- Gateway instance dies → clients reconnect through the load balancer; in-flight streams are lost and retried by the client.
- Redis (limits) down → fail open with local per-instance limits for a short time rather than blocking all AI traffic.
- Provider outage → circuit breaker + fallback chain; teams that pinned a specific model get a clear error.

**Why not let each team call providers directly?** Every team would reimplement keys, retries, fallbacks, redaction and cost tracking, and nobody could see total spend or switch providers centrally. The gateway is the same idea as an API gateway, applied to model traffic ([6.6 API gateway](#66-api-gateway)).

---

## 71. Problem: Design a recommendation system (embeddings + ranking)

**How often asked:** common · **Builds on:** [53.7 Embeddings](#537-embeddings), [59.4 Embeddings and vector search](#594-embeddings-and-vector-search), [8. Caching](#8-caching), [25.8 Stream processing](#258-stream-processing)

"Recommend products / videos / posts for this user" is asked in both ML-flavoured and general design rounds. The industry-standard shape is a **funnel**: cheap methods narrow millions of items to hundreds, an expensive model ranks the hundreds, and business rules shape the final list.

**Requirements**
- Home-page recommendations for 100M users from a 10M-item catalogue; refreshed as the user interacts; p99 < 200 ms.
- New items and new users must get reasonable recommendations (cold start).
- Business rules: no out-of-stock items, diversity (not 20 phone cases), no repeats of what the user just bought.

**Estimates**
- 100M DAU × 5 page loads = 500M requests/day ≈ 6K QPS average, ~20K peak.
- Item embeddings: 10M × 256 dims × 4 B ≈ 10 GB → fits in memory on each retrieval node.
- Interaction events (views, clicks, purchases): billions/day → Kafka, then a feature pipeline.

**The funnel**
```text
10M items
  │ candidate generation (several sources, ~1–5 ms each)
  │   · ANN search: user embedding → nearest item embeddings
  │   · "people who bought X also bought…" (co-occurrence)
  │   · trending in the user's region; new items to explore
  ▼
~1,000 candidates
  │ ranking: a model scores each (user, item) pair using features
  │   from the feature store → predicted click/purchase probability
  ▼
~100 scored
  │ re-ranking: business rules, diversity, dedupe, ads/sponsored slots
  ▼
20 shown
```

**Embeddings: where the vectors come from**
A **two-tower model** learns a user tower (history, profile) and an item tower (title, category, attributes, image) so that users end up close to items they engage with. Item embeddings are computed in batch and indexed with approximate nearest-neighbour search (HNSW); the user embedding is computed at request time or refreshed every few minutes. Content-based item embeddings (from text and images) also solve **item cold start**: a new product has a vector before anyone has clicked it.

**Feature store**
The ranker needs the same features online (at request time) and offline (for training). A feature store keeps both: a batch pipeline computes slow features (30-day purchase counts) into an offline table and syncs them to a low-latency online store (Redis/DynamoDB); a streaming job updates fast features (items viewed in this session). Using the same definitions for both avoids **training/serving skew**, a classic source of models that look good offline and fail in production.

**Serving path**
```text
request ─► recs service ─► candidate sources (parallel, with timeouts)
                        ─► feature store (batch get for ~1,000 items)
                        ─► ranking model service (GPU/CPU, batched)
                        ─► re-rank rules ─► cache result per user for minutes
```
If ranking times out, fall back to the candidates' own order; if everything fails, show popular items. A recommendation page must never be empty.

**Evaluation**
- Offline: recall@k for candidate generation, nDCG/AUC for ranking, on held-out interaction logs.
- Online: A/B tests on click-through, conversion and long-term metrics (retention), because offline gains often don't translate.
- Watch for feedback loops: the system only learns from what it showed, so reserve a little traffic for exploration.

**Why not one model over all 10M items?** A good ranking model takes about a millisecond per item; 10M items would take hours per request. The funnel spends cheap computation on many items and expensive computation on few.

**Semantic search variant:** replace "user embedding" with "query embedding", combine ANN results with BM25 keyword results ([59.5 Searching: choices and trade-offs](#595-searching-choices-and-trade-offs)), then rank with relevance and business signals — the same funnel.

---

## 72. GenAI interview Q&A

**Q: What is a token, and why does it matter?**
A sub-word unit the model reads/writes. Cost, latency, and context limits are all in tokens; ~4 characters per token in English.

**Q: Why does the model hallucinate, and how do you reduce it?**
It generates plausible text, not looked-up facts. Ground with retrieval/tools, require citations, allow "I don't know", verify claims, evaluate faithfulness.

**Q: RAG vs fine-tuning?**
RAG for knowledge (fresh, private, citable); fine-tuning for behavior/format/specialization or distilling into a cheaper model. Start with prompting + RAG.

**Q: How do you choose chunk size?**
Start ~300–800 tokens with structure-aware splitting, then tune with recall@k on real questions; use parent-child retrieval when you need both precision and context.

**Q: Why hybrid search?**
BM25 nails exact identifiers and rare terms; vectors handle paraphrases. Fusing them (RRF) is robust; add a reranker for ordering.

**Q: What is a reranker and when is it worth it?**
A cross-encoder scoring each (query, passage) pair jointly — much more accurate than embedding similarity. Worth it when the right chunk is retrieved but ranked too low; costs ~50–300 ms.

**Q: How do you evaluate a RAG system?**
Separately: retrieval (recall@k, MRR on labeled question→doc pairs) and generation (correctness, faithfulness, citation accuracy via rubric/LLM judge calibrated on human labels), plus latency and cost. Gate changes in CI.

**Q: How do agents differ from workflows?**
Workflows have code-defined steps; agents let the model choose actions in a loop. Prefer workflows when steps are known; agents for open-ended tasks, with limits and checkpoints.

**Q: How do you make an agent reliable?**
Good tool design, validated arguments, permissions in code, step/cost/time limits, loop detection, checkpoints + idempotent tools, human approval for risky actions, tracing, evals on task success.

**Q: What's MCP?**
A standard protocol (JSON-RPC over stdio/HTTP) for apps to discover and call tools, read resources, and use prompts from external servers — reusable integrations instead of custom adapters per app.

**Q: What is prompt injection? How do you defend?**
Instructions smuggled via user input or retrieved/tool content. Defense in depth: treat content as data, least-privilege tools, authorization in code, confirmations, exfiltration controls, monitoring.

**Q: How do you stream LLM output to a browser?**
Provider streaming API → server forwards chunks over SSE (or WebSocket) → client renders incrementally; handle disconnects, resume with event IDs, save the final message.

**Q: How would you cut LLM costs by 50%?**
Prompt caching, smaller context (better retrieval, summarization), shorter outputs, batch for offline work, response/semantic caching, effort/model routing per route — measured by cost per completed task with quality held constant.

**Q: What's the KV cache?**
Stored attention keys/values for past tokens so each new token doesn't recompute them. Speeds decoding; its memory limits concurrency and context length on a GPU.

**Q: What limits LLM serving throughput?**
GPU memory (weights + KV cache), batching efficiency, prefill vs decode balance. Continuous batching, paged KV cache, quantization, and speculative decoding help.

**Q: How do you handle long conversations?**
Persist everything; build each request from system prompt + memory + rolling summary + recent turns within a budget; compaction; prompt caching for the stable prefix.

**Q: How do you handle structured data questions ("how many orders last week")?**
Don't use vector retrieval — generate/validate a read-only SQL query against an allowlisted schema (or call a metrics API), then let the LLM explain the computed result.

**Q: LLM-as-judge pitfalls?**
Position/verbosity/self-preference bias, vague rubrics, uncalibrated scores. Use specific rubrics, pairwise with swapped order, binary criteria, and measure agreement with humans.

---

## 73. Presenting a system you built

Interviewers often say: *"Design the system you built, at 10× scale."* Prepare 2–3 of your own projects this way, with real numbers.

### 73.1 How to structure the answer

1. **Context (1 min):** what the product does, who uses it, scale (QPS, data size, users).
2. **Architecture (5 min):** draw it — clients, APIs, stores, queues, caches, external dependencies.
3. **Your part:** say clearly what you owned vs what the team or platform owned.
4. **Key decisions and tradeoffs:** for each major choice, the alternative you rejected and why.
5. **Hard problems:** one or two incidents or bottlenecks — how you measured, fixed, and verified.
6. **10× scale:** what breaks first and what you'd change.

### 73.2 Questions to prepare for every project

- **Scale:** requests per second, data size, number of users, growth rate — and how you measured them.
- **Why this design:** for each major choice (database, queue, cache, sync vs async), the alternative you rejected and why.
- **Consistency:** what can be stale, for how long, and what must be exact.
- **Failures:** what happens when each dependency is slow or down; retries, idempotency, timeouts, fallbacks.
- **Bottleneck story:** one performance or reliability problem — how you found it (metrics, traces, profiling), fixed it, and verified the fix.
- **Operations:** deploys, monitoring and alerts, on-call incidents, rollbacks.
- **10× growth:** what breaks first, what you'd change, what you'd keep.
- **Ownership:** what you built yourself vs what the team or platform provided.

### 73.3 Example: walking through an asynchronous processing pipeline

- **Context:** documents arrive from several sources and must be parsed, enriched and indexed for search.
- **Sketch:** source → API stores a job → queue fans out per-document tasks → workers (parse → enrich → index) → fan-in marks the job complete.
- **Decisions:** queue for spike absorption and retries; idempotent workers keyed by document ID + version; dead-letter queue for permanently bad inputs; per-tenant queues so one tenant can't starve others.
- **Failure story:** a malformed document crashed workers repeatedly (poison message) → added per-message error handling + DLQ + alert → pipeline kept flowing while the bad input waited for a fix.
- **10×:** autoscale workers on queue depth, batch index writes, partition the queue by tenant.
- Concepts behind each part: [idempotency](#57-idempotency), [messaging and failure handling](#256-failure-handling-patterns), [backpressure](#1712-backpressure).

---

## 74. Common mistakes and phrases that help

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

---

## 75. GenAI references

A short reading list. Titles are given in full so you can search for them; links change, titles don't.

**Agents and retrieval**
- Anthropic, "Building effective agents" (2024) — workflows vs agents, and the common workflow patterns.
- Anthropic, "Introducing Contextual Retrieval" (2024) — adding context to chunks before indexing, with measurements.
- Lewis et al., "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks" (2020) — the paper that named RAG.
- Yao et al., "ReAct: Synergizing Reasoning and Acting in Language Models" (2022) — interleaving reasoning and tool use.
- Microsoft Azure Architecture Center, "RAG chunking phase", "RAG enrichment phase" and "RAG information-retrieval phase" guides — practical chunking and retrieval design.

**Models and serving**
- Vaswani et al., "Attention Is All You Need" (2017) — the transformer.
- Kwon et al., "Efficient Memory Management for Large Language Model Serving with PagedAttention" (2023) — the vLLM paper.
- Ainslie et al., "GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints" (2023) — grouped-query attention and its KV-cache savings.

**Protocols and safety**
- Model Context Protocol specification — modelcontextprotocol.io.
- OWASP, "Top 10 for Large Language Model Applications" — the standard list of LLM security risks.

**Provider documentation**
- The API documentation of whichever provider you use, especially the pages on tool use, structured outputs, prompt caching and rate limits — these change faster than any book.

---

## 76. Appendix: Calling a real model (Claude Python SDK, as of October 2026)

The rest of this book teaches the concepts with standard-library code and fake models. This appendix shows the same things against a real API, using Anthropic's official Python SDK as it stood in October 2026. **Every listing here needs the `anthropic` package and an API key, so none of them run in this book's test harness.** APIs change often; check the provider's current documentation before copying these into production.

### 76.1 Setup

```text
pip install anthropic
export ANTHROPIC_API_KEY=...        (or sign in once with the `ant` CLI: ant auth login)

Models as of October 2026: claude-opus-5-5 (default), claude-sonnet-5-5 (faster, cheaper),
claude-haiku-5-5 (cheapest, for high-volume simple tasks).
```

### 76.2 Basic call

Concepts: [55.1 Anatomy of a request and response](#551-anatomy-of-a-request-and-response).

```py
import anthropic

# reads ANTHROPIC_API_KEY or a saved `ant auth login` profile
client = anthropic.Anthropic()

response = client.messages.create(
    model="claude-opus-5-5",
    max_tokens=16000,
    # low | medium | high | xhigh | max (default here: medium)
    output_config={"effort": "low"},
    system="You are a concise support assistant for an online store.",
    messages=[{"role": "user", "content": "My payment failed with error E-104. What does that mean?"}],
)
if response.stop_reason == "end_turn":
    print("".join(b.text for b in response.content if b.type == "text"))
print(response.usage.input_tokens, response.usage.output_tokens, response.stop_reason)
```

- `stop_reason` values: `end_turn`, `max_tokens`, `tool_use`, `refusal`, `pause_turn` (a server-side tool paused; send the conversation back to continue).
- The SDK retries 408/409/429/5xx and connection errors itself (2 retries by default).
- Current Claude models choose how much to reason on their own; `effort` is the dial for depth versus cost. `temperature`/`top_p`/`top_k` are rejected.

### 76.3 Streaming

Concepts: [55.2 Streaming (needed for chat UIs)](#552-streaming-needed-for-chat-uis).

```py
with client.messages.stream(
    model="claude-opus-5-5",
    max_tokens=64000,
    messages=[{"role": "user", "content": "Explain our return policy in 5 bullet points."}],
) as stream:
    for text in stream.text_stream:     # forward each piece over SSE to the browser
        print(text, end="", flush=True)
    final = stream.get_final_message()  # full message + usage at the end
```

### 76.4 Structured outputs

Concepts: [55.3 Structured outputs](#553-structured-outputs).

```py
from pydantic import BaseModel

class Ticket(BaseModel):
    category: str
    priority: str
    summary: str

response = client.messages.parse(
    model="claude-opus-5-5",
    max_tokens=16000,
    messages=[{"role": "user", "content": "Classify: 'I was charged twice for order 1042.'"}],
    output_format=Ticket,
)
ticket = response.parsed_output        # a validated Ticket instance
```

On `messages.create()` the equivalent is `output_config={"format": {...json schema...}}`.

### 76.5 Prompt caching

Concepts: [55.4 Prompt caching](#554-prompt-caching).

```py
response = client.messages.create(
    model="claude-opus-5-5",
    max_tokens=16000,
    system=[{
        "type": "text",
        "text": LONG_STABLE_POLICIES_AND_INSTRUCTIONS,
        "cache_control": {"type": "ephemeral"},       # cache everything up to here
    }],
    messages=[{"role": "user", "content": question}],
)
print(response.usage.cache_read_input_tokens)          # > 0 on later calls = cache hit
```

### 76.6 Errors and refusal fallbacks

Concepts: [55.5 Errors, retries, fallbacks](#555-errors-retries-fallbacks).

```py
import anthropic

try:
    response = client.messages.create(model="claude-opus-5-5", max_tokens=1024,
                                      messages=[{"role": "user", "content": "hi"}])
except anthropic.RateLimitError as e:          # 429: back off, honour retry-after
    retry_after = int(e.response.headers.get("retry-after", "30"))
except anthropic.APIStatusError as e:          # other HTTP errors
    if e.status_code >= 500:
        ...                                    # transient: retry later or fall back
    else:
        raise                                  # 4xx: a bug in the request; don't retry
except anthropic.APIConnectionError:
    ...                                        # network: retry with backoff
```

A request declined by safety classifiers returns HTTP 200 with `stop_reason == "refusal"`. A beta option asks the server to retry such requests on a suitable fallback model:

```py
response = client.beta.messages.create(
    model="claude-opus-5-5",
    max_tokens=16000,
    betas=["server-side-fallback-2026-07-01"],
    fallbacks="default",
    messages=[{"role": "user", "content": "..."}],
)
if response.stop_reason == "refusal":
    ...                                        # still declined: show a safe message
```

### 76.7 Tool use: manual loop

Concepts: [56. Tool calling (function calling)](#56-tool-calling-function-calling) and [57.3 The agent loop](#573-the-agent-loop).

```py
import json
import anthropic

client = anthropic.Anthropic()
tools = [{
    "name": "get_order_status",
    "description": "Look up the current status of one of the signed-in customer's orders by order ID.",
    "strict": True,
    "input_schema": {
        "type": "object",
        "properties": {"order_id": {"type": "integer", "description": "Order number, e.g. 1042"}},
        "required": ["order_id"],
        "additionalProperties": False,
    },
}]

def get_order_status(order_id: int) -> dict:
    return {"order_id": order_id, "status": "SHIPPED", "eta": "2026-11-03"}

messages = [{"role": "user", "content": "Where is my order 1042?"}]
for _ in range(10):                                     # always cap the number of steps
    response = client.messages.create(model="claude-opus-5-5", max_tokens=16000,
                                      tools=tools, messages=messages)
    if response.stop_reason != "tool_use":
        break
    # keep the full content
    messages.append({"role": "assistant", "content": response.content})
    results = []
    for block in response.content:
        if block.type == "tool_use":
            try:
                output = get_order_status(**block.input)
                results.append({"type": "tool_result", "tool_use_id": block.id,
                                "content": json.dumps(output)})
            except Exception as exc:
                results.append({"type": "tool_result", "tool_use_id": block.id,
                                "content": f"Error: {exc}", "is_error": True})
    # all results in ONE message
    messages.append({"role": "user", "content": results})

print("".join(b.text for b in response.content if b.type == "text"))
```

### 76.8 Tool use: the SDK tool runner

The SDK can run the same loop for you: it builds the tool schema from the function signature and docstring, calls the tools, and repeats until the model is done.

```py
import anthropic
from anthropic import beta_tool

client = anthropic.Anthropic()

@beta_tool
def get_order_status(order_id: int) -> str:
    """Look up the current status of one of the signed-in customer's orders.

    Args:
        order_id: Numeric order ID, e.g. 1042.
    """
    return '{"status": "SHIPPED", "eta": "2026-11-03"}'

runner = client.beta.messages.tool_runner(
    model="claude-opus-5-5",
    max_tokens=16000,
    tools=[get_order_status],
    messages=[{"role": "user", "content": "Where is my order 1042?"}],
)
for message in runner:                 # one message per model turn
    print(message.stop_reason)
```
