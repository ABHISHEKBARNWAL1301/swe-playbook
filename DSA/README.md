# DSA

Personal notes on Data Structures & Algorithms — concepts, patterns, and important practice questions. Each topic file follows the same format: short concept intro → patterns → code variants (recursion / memo / tab / space-opt where applicable) → practice problems with LeetCode links.

## Topics

1. [arrays.md](arrays.md) — **Arrays & Strings**: prefix sums, two pointers, sliding window (fixed & variable), Kadane's, 2D matrix, sorting essentials, strings-as-arrays
2. [hashing.md](hashing.md) — **Hashing**: `dict` / `set` / `Counter` cheat sheet, why O(1) (chaining vs open addressing), CPython internals
3. [binary-search.md](binary-search.md) — **Binary Search**: classic, lower/upper bound, search-on-answer, rotated array, peak finding
4. [stacks-queue.md](stacks-queue.md) — **Stacks & Queues**: brackets, monotonic stack/deque, histogram, expression eval, MinStack, BFS, circular queue, queue-from-stacks
5. [heaps.md](heaps.md) — **Heaps**: Top-K, k-way merge, two-heap median, scheduling, Dijkstra heap, custom comparators
6. [greedy.md](greedy.md) — **Greedy**: intervals + sweep line, jump game, sorted + two-pointer, greedy + heap, Huffman
7. [recursion-backtracking-dc.md](recursion-backtracking-dc.md) — **Recursion / Backtracking / D&C**: recursion fundamentals, backtracking templates, N-Queens, merge/quick sort, quickselect
8. [linked-list.md](linked-list.md) — **Linked Lists**: dummy head, fast/slow, reverse (iterative/recursive/k-group), merge, reorder, doubly linked list
9. [trees.md](trees.md) — **Trees & BST**: traversals (BFS/DFS), height/diameter, BST ops, LCA, construct-from-traversal, serialize, tree DP
10. [trie.md](trie.md) — **Trie**: trie node, CRUD operations, prefix lookup
11. [graph.md](graph.md) — **Graphs**: BFS/DFS, topological sort, DSU, cycle detection, shortest paths (Dijkstra / Bellman-Ford / Floyd-Warshall), MST, bipartite, SCC
12. [dp.md](dp.md) — **Dynamic Programming**: linear DP, LIS, knapsack, grid, string, state-machine, interval, game theory, tree DP, and more
13. [design.md](design.md) — **Design**: LRU, LFU, RandomizedSet, MinStack, Trie design, Twitter, iterators, streams, hashmap bookkeeping

> Also: [python.md](python.md) — Python-for-DSA reference (containers, `heapq`, `functools.cache`, gotchas) · [DSA-tracker.xlsx](DSA-tracker.xlsx) — progress tracker across all 429 problems.

## Format per file

- **Concept** — definition and core idea
- **Patterns** — common templates / approaches with flow diagrams
- **Code variants** — recursion → memoization → tabulation → space-optimized (where applicable)
- **Complexity** — time and space tradeoffs
- **Practice problems** — curated LeetCode links

## Usage

Browse topic files directly, or use them as a revision sheet before interviews. Most problems map to one or two of these patterns; recognizing the pattern is half the solution.
