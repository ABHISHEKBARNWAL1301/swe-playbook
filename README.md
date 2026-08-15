# SWE Playbook

Study notes and curriculum material covering Python, data structures & algorithms, and system design — used as a revision reference and as teaching material.

## Structure

### [DSA/](DSA/) — Data Structures & Algorithms

Concepts, patterns, and curated practice problems. Each file follows: concept intro → patterns → code variants (recursion → memo → tabulation → space-optimized) → practice problems.

1. [linear-data-structures.md](DSA/linear-data-structures.md) — **Linear Data Structures**: *Arrays & Strings* (prefix sums, two pointers, sliding window, Kadane's, 2D matrix, hashing as a tool) · *Stacks & Queues* (brackets, monotonic stack/deque, expression eval, MinStack, BFS, circular queue)
2. [non-linear-data-structures.md](DSA/non-linear-data-structures.md) — **Non-Linear Data Structures**: *Heaps* (Top-K, k-way merge, two-heap median) · *Graphs* (BFS/DFS, topological sort, DSU, shortest paths, MST, SCC) · *Linked Lists* (fast/slow, reverse, merge, doubly linked list) · *Trees & BST* (traversals, LCA, construct/serialize, tree DP) · *Trie* (CRUD, prefix lookup)
3. [algorithms.md](DSA/algorithms.md) — **Algorithms**: *Binary Search* (bounds, search-on-answer, rotated array) · *Sorting* (built-in, counting sort, merge/quick sort, quickselect) · *Dynamic Programming* (linear, LIS, knapsack, string, state-machine, interval, game theory, grid, tree DP) · *Greedy* (intervals, sweep line, jump game, greedy + heap) · *Recursion & Backtracking* (backtracking templates, N-Queens)
4. [scalable-data-structures.md](DSA/scalable-data-structures.md) — **Scalable Data Structures**: Bloom filters, GeoHash, Merkle trees — approximate/space-efficient structures used at scale

Also: [python.md](DSA/python.md) — Python reference: containers, hash-table internals, `functools.cache`, and a language-internals interview section (GIL, generators, decorators, gotchas).

### [SystemDesign/](SystemDesign/) — System Design

- [problems.md](SystemDesign/problems.md) — how to approach a design problem, plus data-structure design patterns (LRU/LFU cache, RandomizedSet, MinStack, Trie design, Twitter-style feed, iterators, streams)
- [dbms.md](SystemDesign/dbms.md) — relational vs. document databases, indexing, full-text search (inverted index, BM25), graph databases, RDF
- [computer_networks.md](SystemDesign/computer_networks.md) — networking foundations: client-server, IP, DNS, proxies, and more
- [oops.md](SystemDesign/oops.md) — OOP design principles (SOLID) *(early draft)*
- [genai.md](SystemDesign/genai.md) — GenAI/LLM system notes (LangChain, RAG) *(early draft, unstructured)*
- `devops.md`, `os.md` — reserved, not yet written

## Usage

Browse topic files directly, or use them as a revision sheet before interviews. Most problems map to one or two of the patterns documented in each file; recognizing the pattern is half the solution.
