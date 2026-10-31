# SWE Playbook

Study notes and curriculum material covering Python, data structures & algorithms, and system design — used as a revision reference and as teaching material.

## Structure

### [DSA/](DSA/) — Data Structures & Algorithms

- [dsa-interview-guide.md](DSA/dsa-interview-guide.md) — **DSA book**: how to approach a problem, complexity analysis and reading constraints, recursion and backtracking, *arrays* (prefix sums, two pointers, sliding window, Kadane's, 2D matrix), *strings*, *hashing*, *sorting*, *binary search*, *stacks and queues* (monotonic stack/deque), *linked lists*, *trees and BSTs*, *tries*, *heaps*, *graphs* (BFS/DFS, topological sort, union-find, shortest paths, MST, SCC), *greedy*, *dynamic programming* (linear, LIS, knapsack, string, state machine, interval, game, grid, tree, bitmask), space-efficient structures (Bloom filters, GeoHash, Merkle trees), and **advanced topics**: bit manipulation, math and number theory, KMP/Z/Manacher, Fenwick and segment trees, sparse tables, 0-1 BFS, A*, Euler paths, binary lifting, max flow, digit DP, meet in the middle, ordered sets and skip lists, randomized and streaming algorithms — ending with a pattern-recognition cheat sheet
- [python.md](DSA/python.md) — **Python interview reference**: containers and their complexity, hash-table internals, `functools.cache`, and language internals (GIL, generators, decorators, gotchas)

### [SystemDesign/](SystemDesign/) — System Design

- [hld-interview-guide.md](SystemDesign/hld-interview-guide.md) — **High-level design**: interview framework, estimation, networking (OSI/TCP-IP, TLS, DNS), API design, load balancing, CDN, caching, SQL internals, NoSQL modeling, replication, sharding, distributed systems (incl. cross-server concurrency), messaging, search, reliability, security, observability, 26 design problems with full solutions, and **GenAI engineering** (LLM fundamentals, prompting, tool calling, agents, MCP, RAG, evaluation, serving, cost, safety, GenAI system design)
- [lld-interview-guide.md](SystemDesign/lld-interview-guide.md) — **Low-level design / machine coding**: OOP, UML, SOLID, design patterns with code, 25 problems with runnable solutions (parking lot, LRU/LFU, BookMyShow, Splitwise, elevator, rate limiter, …), and **concurrency** (threads, GIL, locks, deadlock, asyncio, multiprocessing, the LeetCode concurrency set, 12 thread-safe design problems)
- [devops.md](SystemDesign/devops.md) — reverse proxies, Docker, and related DevOps notes

## Usage

Browse topic files directly, or use them as a revision sheet before interviews. Most problems map to one or two of the patterns documented in each file; recognizing the pattern is half the solution.
