# SWE Playbook

Study notes and curriculum material covering Python, data structures & algorithms, and system design — used as a revision reference and as teaching material.

## Structure

### [DSA/](DSA/) — Data Structures & Algorithms

Concepts, patterns, and curated practice problems. Each file follows: concept intro → patterns → code variants (recursion → memo → tabulation → space-optimized) → practice problems.

1. [linear-data-structures.md](DSA/linear-data-structures.md) — **Linear Data Structures**: *Arrays* (prefix sums, two pointers, sliding window, Kadane's, 2D matrix) · *Strings* (frequency counting, rolling hash, canonical keys) · *Hashing* (seen-before maps, dedup sets) · *Stacks* (brackets, expression eval, MinStack, monotonic stack) · *Queues* (BFS, circular queue, monotonic deque)
2. [non-linear-data-structures.md](DSA/non-linear-data-structures.md) — **Non-Linear Data Structures**: *Heaps* (Top-K, k-way merge, two-heap median) · *Graphs* (BFS/DFS, topological sort, DSU, shortest paths, MST, SCC) · *Linked Lists* (fast/slow, reverse, merge, doubly linked list) · *Trees & BST* (traversals, LCA, construct/serialize, tree DP) · *Trie* (CRUD, prefix lookup)
3. [algorithms.md](DSA/algorithms.md) — **Algorithms**: *Binary Search* (bounds, search-on-answer, rotated array) · *Sorting* (built-in, counting sort, merge/quick sort, quickselect) · *Dynamic Programming* (linear, LIS, knapsack, string, state-machine, interval, game theory, grid, tree DP) · *Greedy* (intervals, sweep line, jump game, greedy + heap) · *Recursion & Backtracking* (backtracking templates, N-Queens)
4. [scalable-data-structures.md](DSA/scalable-data-structures.md) — **Scalable Data Structures**: Bloom filters, GeoHash, Merkle trees — approximate/space-efficient structures used at scale

Also: [python.md](DSA/python.md) — Python reference: containers, hash-table internals, `functools.cache`, and a language-internals interview section (GIL, generators, decorators, gotchas).

### [SystemDesign/](SystemDesign/) — System Design

- [hld-interview-guide.md](SystemDesign/hld-interview-guide.md) — **High-level design**: interview framework, estimation, networking (OSI/TCP-IP, TLS, DNS), API design, load balancing, CDN, caching, SQL internals, NoSQL modeling, replication, sharding, distributed systems, messaging, search, reliability, security, observability, plus 26 design problems with full solutions
- [lld-interview-guide.md](SystemDesign/lld-interview-guide.md) — **Low-level design / machine coding**: OOP, UML, SOLID, design patterns with code, plus 25 problems with runnable solutions (parking lot, LRU/LFU, BookMyShow, Splitwise, elevator, rate limiter, …)
- [concurrency-interview-guide.md](SystemDesign/concurrency-interview-guide.md) — **Concurrency**: threads, GIL, locks, conditions, deadlock, asyncio, multiprocessing, cross-server coordination, the LeetCode concurrency set, and 12 thread-safe design problems
- [genai.md](SystemDesign/genai.md) — **GenAI engineering**: LLM fundamentals, prompting, tool calling, agents, MCP, RAG, evaluation, serving, cost, safety, and GenAI system design problems
- [devops.md](SystemDesign/devops.md) — reverse proxies, Docker, and related DevOps notes

## Usage

Browse topic files directly, or use them as a revision sheet before interviews. Most problems map to one or two of the patterns documented in each file; recognizing the pattern is half the solution.
