# DSA Interview Guide

Everything for the coding rounds in one book: how to approach a problem and analyse its complexity → the core data structures and the patterns built on them → the classic algorithm families (sorting, binary search, recursion, greedy, dynamic programming) → space-efficient structures used at scale → advanced topics for harder rounds (bit tricks, number theory, string matching, range queries, advanced graphs and DP, ordered sets, randomized algorithms) → a pattern-recognition cheat sheet.

Related: [Python interview reference](python.md) (containers, complexity of built-ins, language internals) · [HLD guide](../SystemDesign/hld-interview-guide.md) · [LLD guide](../SystemDesign/lld-interview-guide.md)

**Code conventions:** Python 3.11+, standard library only. Code in the core chapters is written as focused snippets and templates to adapt; code in the advanced chapters is complete, runnable as-is, and ends with assertions.

## Contents

- [1. How to use this book](#1-how-to-use-this-book)
  - [1.1 How the book is organised](#11-how-the-book-is-organised)
  - [1.2 How to approach a problem in the interview](#12-how-to-approach-a-problem-in-the-interview)
  - [1.3 What gets asked, roughly how often](#13-what-gets-asked-roughly-how-often)
- [2. Complexity analysis](#2-complexity-analysis)
  - [2.1 Big-O in one page](#21-big-o-in-one-page)
  - [2.2 Reading the constraints](#22-reading-the-constraints)
  - [2.3 Amortized analysis](#23-amortized-analysis)
  - [2.4 Recursion: recurrences and the master theorem](#24-recursion-recurrences-and-the-master-theorem)
  - [2.5 Space complexity and Python costs](#25-space-complexity-and-python-costs)
- [3. Arrays](#3-arrays)
  - [3.1 Pattern 0: Warm-up — Classic Array Problems](#31-pattern-0-warm-up--classic-array-problems)
  - [3.2 Pattern 1: Prefix Sums](#32-pattern-1-prefix-sums)
  - [3.3 Pattern 2: Two Pointers](#33-pattern-2-two-pointers)
  - [3.4 Pattern 3: Sliding Window](#34-pattern-3-sliding-window)
  - [3.5 Pattern 4: Kadane's Algorithm](#35-pattern-4-kadanes-algorithm)
  - [3.6 Pattern 5: 2D Matrix](#36-pattern-5-2d-matrix)
- [4. Strings](#4-strings)
  - [4.1 Frequency counting](#41-frequency-counting)
  - [4.2 Sliding window + hashmap](#42-sliding-window--hashmap)
  - [4.3 Rolling hash (Rabin–Karp)](#43-rolling-hash-rabinkarp)
  - [4.4 Canonical key → hashmap grouping](#44-canonical-key--hashmap-grouping)
  - [4.5 Two-pointer string tricks](#45-two-pointer-string-tricks)
- [5. Hashing](#5-hashing)
  - [5.1 Value → index / seen-before map](#51-value--index--seen-before-map)
  - [5.2 Set for membership / dedup](#52-set-for-membership--dedup)
  - [5.3 Questions](#53-questions)
  - [5.4 Quick reference](#54-quick-reference)
- [6. Sorting](#6-sorting)
  - [6.1 Built-in](#61-built-in)
  - [6.2 Counting sort — O(n + k)](#62-counting-sort--on--k)
  - [6.3 Merge Sort](#63-merge-sort)
  - [6.4 Quick Sort](#64-quick-sort)
  - [6.5 Quickselect](#65-quickselect)
- [7. Binary search](#7-binary-search)
  - [7.1 Patterns covered](#71-patterns-covered)
  - [7.2 Pattern 1: Classic — Exact Match](#72-pattern-1-classic--exact-match)
  - [7.3 Pattern 2: Lower Bound / Upper Bound](#73-pattern-2-lower-bound--upper-bound)
  - [7.4 Pattern 3: Search on the Answer (Parametric)](#74-pattern-3-search-on-the-answer-parametric)
  - [7.5 Pattern 4: Rotated Sorted Array](#75-pattern-4-rotated-sorted-array)
  - [7.6 Pattern 5: 2D Matrix](#76-pattern-5-2d-matrix)
  - [7.7 Pattern 6: Peak / Valley](#77-pattern-6-peak--valley)
  - [7.8 Common pitfalls](#78-common-pitfalls)
  - [7.9 Quick reference](#79-quick-reference)
- [8. Stacks](#8-stacks)
  - [8.1 Stack (LIFO)](#81-stack-lifo)
  - [8.2 Matching Pairs](#82-matching-pairs)
  - [8.3 Expression Evaluation](#83-expression-evaluation)
  - [8.4 Stack as Recursion Substitute](#84-stack-as-recursion-substitute)
  - [8.5 Min Stack — O(1) min](#85-min-stack--o1-min)
  - [8.6 Monotonic Stack — Next Greater / Smaller](#86-monotonic-stack--next-greater--smaller)
- [9. Queues](#9-queues)
  - [9.1 Queue (FIFO)](#91-queue-fifo)
  - [9.2 BFS](#92-bfs)
  - [9.3 Circular Queue / Ring Buffer](#93-circular-queue--ring-buffer)
  - [9.4 Queue from Two Stacks (and vice versa)](#94-queue-from-two-stacks-and-vice-versa)
  - [9.5 Monotonic Deque — Sliding Window Max/Min](#95-monotonic-deque--sliding-window-maxmin)
- [10. Linked lists](#10-linked-lists)
  - [10.1 Singly Linked List](#101-singly-linked-list)
  - [10.2 Doubly Linked List](#102-doubly-linked-list)
- [11. Recursion and backtracking](#11-recursion-and-backtracking)
  - [11.1 Recursion](#111-recursion)
  - [11.2 Backtracking — Choose / Explore / Unchoose](#112-backtracking--choose--explore--unchoose)
- [12. Trees and BSTs](#12-trees-and-bsts)
  - [12.1 Traversals](#121-traversals)
  - [12.2 BST](#122-bst)
  - [12.3 Advanced Concepts](#123-advanced-concepts)
- [13. Trie](#13-trie)
  - [13.1 Properties](#131-properties)
  - [13.2 Node](#132-node)
  - [13.3 Trie with CRUD](#133-trie-with-crud)
  - [13.4 Usage](#134-usage)
  - [13.5 Common problems](#135-common-problems)
- [14. Heaps](#14-heaps)
  - [14.1 Heaps in Python](#141-heaps-in-python)
  - [14.2 Core operations (implemented from scratch)](#142-core-operations-implemented-from-scratch)
  - [14.3 Pattern 1: Top-K](#143-pattern-1-top-k)
  - [14.4 Pattern 2: Two Heaps](#144-pattern-2-two-heaps)
  - [14.5 Pitfalls](#145-pitfalls)
- [15. Graphs](#15-graphs)
  - [15.1 Representation](#151-representation)
  - [15.2 Traversal](#152-traversal)
  - [15.3 Topological Sorting](#153-topological-sorting)
  - [15.4 Questions](#154-questions)
  - [15.5 Disjoint Set Union (DSU) / Union-Find](#155-disjoint-set-union-dsu--union-find)
  - [15.6 Shortest Path Algorithms](#156-shortest-path-algorithms)
  - [15.7 Cycle Detection](#157-cycle-detection)
  - [15.8 Minimum Spanning Tree (MST)](#158-minimum-spanning-tree-mst)
  - [15.9 Strongly Connected Components](#159-strongly-connected-components)
  - [15.10 Bridges & Articulation Points](#1510-bridges--articulation-points)
  - [15.11 Tarjan's Algorithm (SCC via low-link)](#1511-tarjans-algorithm-scc-via-low-link)
  - [15.12 Must-Do / FAANG Interview Questions](#1512-must-do--faang-interview-questions)
  - [15.13 Quick reference](#1513-quick-reference)
- [16. Greedy](#16-greedy)
  - [16.1 How to recognize a greedy problem](#161-how-to-recognize-a-greedy-problem)
  - [16.2 Pattern 1: Interval Scheduling](#162-pattern-1-interval-scheduling)
  - [16.3 Pattern 2: Sweep Line Algorithm](#163-pattern-2-sweep-line-algorithm)
  - [16.4 Pattern 3: Jump Game](#164-pattern-3-jump-game)
  - [16.5 Pattern 4: Sorted + Two Pointers](#165-pattern-4-sorted--two-pointers)
  - [16.6 Pattern 5: Greedy + Heap](#166-pattern-5-greedy--heap)
  - [16.7 Quick reference](#167-quick-reference)
- [17. Dynamic programming](#17-dynamic-programming)
  - [17.1 Top-down (Memoization)](#171-top-down-memoization)
  - [17.2 Bottom-up (Tabulation)](#172-bottom-up-tabulation)
  - [17.3 DP vs Greedy: When to Use Which?](#173-dp-vs-greedy-when-to-use-which)
  - [17.4 Pattern 1: Linear DP (1D)](#174-pattern-1-linear-dp-1d)
  - [17.5 Pattern 2: Longest Increasing Subsequence (LIS)](#175-pattern-2-longest-increasing-subsequence-lis)
  - [17.6 Pattern 3: Knapsack (0/1, Unbounded, Bounded)](#176-pattern-3-knapsack-01-unbounded-bounded)
  - [17.7 Pattern 4: String DP](#177-pattern-4-string-dp)
  - [17.8 Pattern 5: State Machine DP](#178-pattern-5-state-machine-dp)
  - [17.9 Pattern 6: Interval DP](#179-pattern-6-interval-dp)
  - [17.10 Pattern 7: Game Theory DP (Minimax)](#1710-pattern-7-game-theory-dp-minimax)
  - [17.11 Pattern 8: Grid DP](#1711-pattern-8-grid-dp)
  - [17.12 Pattern 9: Tree DP](#1712-pattern-9-tree-dp)
  - [17.13 Bitmask & Probability DP](#1713-bitmask--probability-dp)
  - [17.14 Must-Do / FAANG DP Questions](#1714-must-do--faang-dp-questions)
- [18. Space-efficient structures at scale](#18-space-efficient-structures-at-scale)
  - [18.1 Bloom Filters](#181-bloom-filters)
  - [18.2 GeoHash](#182-geohash)
  - [18.3 Merkle Trees](#183-merkle-trees)
- [19. Bit manipulation](#19-bit-manipulation)
  - [19.1 Operators and the tricks built on them](#191-operators-and-the-tricks-built-on-them)
  - [19.2 Masks as sets: enumerating subsets](#192-masks-as-sets-enumerating-subsets)
  - [19.3 Python-specific gotchas](#193-python-specific-gotchas)
- [20. Math for coding interviews](#20-math-for-coding-interviews)
  - [20.1 GCD, LCM and fast exponentiation](#201-gcd-lcm-and-fast-exponentiation)
  - [20.2 Modular arithmetic and counting](#202-modular-arithmetic-and-counting)
  - [20.3 Primes: sieve and factorisation](#203-primes-sieve-and-factorisation)
- [21. String matching: KMP, Z-function, Manacher](#21-string-matching-kmp-z-function-manacher)
  - [21.1 Why naive matching is slow, and the KMP idea](#211-why-naive-matching-is-slow-and-the-kmp-idea)
  - [21.2 Z-function](#212-z-function)
  - [21.3 Manacher's algorithm (longest palindrome in O(n))](#213-manachers-algorithm-longest-palindrome-in-on)
- [22. Range queries: Fenwick tree, segment tree, sparse table](#22-range-queries-fenwick-tree-segment-tree-sparse-table)
  - [22.1 Choosing a structure](#221-choosing-a-structure)
  - [22.2 Fenwick tree (binary indexed tree)](#222-fenwick-tree-binary-indexed-tree)
  - [22.3 Segment tree, with lazy propagation](#223-segment-tree-with-lazy-propagation)
  - [22.4 Sparse table: O(1) range minimum on a static array](#224-sparse-table-o1-range-minimum-on-a-static-array)
- [23. Advanced graph algorithms](#23-advanced-graph-algorithms)
  - [23.1 0-1 BFS](#231-0-1-bfs)
  - [23.2 A* search](#232-a-search)
  - [23.3 Euler paths: use every edge exactly once](#233-euler-paths-use-every-edge-exactly-once)
  - [23.4 LCA with binary lifting](#234-lca-with-binary-lifting)
  - [23.5 Maximum flow (know the idea)](#235-maximum-flow-know-the-idea)
- [24. Advanced dynamic programming](#24-advanced-dynamic-programming)
  - [24.1 Digit DP](#241-digit-dp)
  - [24.2 Monotonic-deque optimisation](#242-monotonic-deque-optimisation)
  - [24.3 Meet in the middle](#243-meet-in-the-middle)
  - [24.4 DP over a DAG and memoized DFS](#244-dp-over-a-dag-and-memoized-dfs)
- [25. Ordered sets, balanced BSTs and skip lists](#25-ordered-sets-balanced-bsts-and-skip-lists)
  - [25.1 Why you sometimes need an ordered set](#251-why-you-sometimes-need-an-ordered-set)
  - [25.2 Python workarounds](#252-python-workarounds)
  - [25.3 A skip list from scratch](#253-a-skip-list-from-scratch)
- [26. Randomized and streaming algorithms](#26-randomized-and-streaming-algorithms)
  - [26.1 Shuffling and weighted picks](#261-shuffling-and-weighted-picks)
  - [26.2 Reservoir sampling](#262-reservoir-sampling)
  - [26.3 Majority vote and O(1)-random sets](#263-majority-vote-and-o1-random-sets)
- [27. Pattern recognition cheat sheet](#27-pattern-recognition-cheat-sheet)

---

## 1. How to use this book


### 1.1 How the book is organised

- **Fundamentals first.** Complexity analysis, then recursion — every later chapter leans on both.
- **Linear structures** — arrays, strings, hashing, sorting, binary search, stacks, queues. Elements sit one after another (or are reached by index through contiguous memory). Strings are arrays of characters, so every array technique applies; hashing is the O(1) lookup layer built on top of both; stacks and queues are restricted-access wrappers (LIFO vs FIFO) over the same storage.
- **Pointer-based structures** — linked lists, trees, tries, heaps, graphs. Elements connect through references rather than position: each node can chain to one `next` (linked list), branch (tree, trie) or point anywhere (graph). A heap sits between the two worlds: array-backed, but logically a tree.
- **Algorithm families** — greedy and dynamic programming, which work over any structure: make the locally best choice, or break the problem into overlapping subproblems and remember their answers.
- **Space-efficient structures at scale** — Bloom filters, geohashes, Merkle trees: structures that trade exactness for space, used in front of expensive operations in real systems.
- **Advanced topics** — bit manipulation, number theory, string matching, range queries, advanced graph and DP techniques, ordered sets, randomized algorithms. These separate a "hire" from a "strong hire" at companies with harder rounds, and they appear regularly in online assessments.
- **Pattern recognition cheat sheet** — the last chapter maps problem signals ("sorted array", "k largest", "all subsets") to the technique and chapter.

Each pattern section follows the same arc: the idea → a template → variants → practice problems. Solve the problems; reading templates alone doesn't stick.

### 1.2 How to approach a problem in the interview

1. **Clarify.** Input size and ranges, duplicates, negatives, empty input, sorted or not, what to return when there's no answer. The constraints often name the intended complexity (see [Reading the constraints](#22-reading-the-constraints)).
2. **Work an example by hand**, including an edge case. Many patterns become visible here.
3. **Say the brute force** and its complexity in one sentence. It shows you understand the problem and gives you a fallback.
4. **Find the bottleneck** — the repeated work in the brute force — and match it to a pattern: repeated range sums → prefix sums; "have I seen this?" → hash set; nested loop over a sorted array → two pointers; overlapping subproblems → DP.
5. **Code it cleanly**, narrating as you go. Name things well; small helper functions beat one long block.
6. **Test** with your example, then the edge cases (empty, one element, all equal, maximum size).
7. **State time and space complexity**, including recursion stack and output size.

### 1.3 What gets asked, roughly how often

| Frequency | Topics |
|---|---|
| Almost every loop | Arrays and strings (two pointers, sliding window, prefix sums), hashing, trees (DFS/BFS), graphs (BFS/DFS, topological sort), binary search, heaps |
| Very common | Dynamic programming (1D, 2D, knapsack, strings), backtracking, linked lists, stacks (monotonic), intervals and greedy, union-find |
| Common at harder companies / OAs | Tries, Dijkstra, segment/Fenwick trees, bit manipulation, KMP and rolling hash, bitmask and digit DP |
| Rare | Max flow, suffix arrays, Manacher, heavy geometry |

---

## 2. Complexity analysis


### 2.1 Big-O in one page

Big-O describes how the running time (or memory) **grows** as the input grows, ignoring constant factors and smaller terms. `3n² + 10n + 7` is O(n²): for large n, the n² term dominates.

| Complexity | Name | Typical source | n = 10⁶ is… |
|---|---|---|---|
| O(1) | Constant | Hash lookup, array index | Instant |
| O(log n) | Logarithmic | Binary search, balanced tree, heap push/pop | ~20 steps |
| O(n) | Linear | One pass, two pointers, sliding window | Fast |
| O(n log n) | Linearithmic | Sorting, n heap operations | Fast |
| O(n²) | Quadratic | Nested loops over the input | Too slow (10¹² steps) |
| O(2ⁿ) | Exponential | All subsets | Only for n ≲ 20–25 |
| O(n!) | Factorial | All permutations | Only for n ≲ 10 |

- **Big-O** is an upper bound, **Big-Θ** a tight bound, **Big-Ω** a lower bound. In interviews "O" usually means the tight bound of the worst case.
- **Best, average and worst case** can differ: quicksort is O(n log n) on average, O(n²) in the worst case; hash lookups are O(1) on average, O(n) in the worst case.
- **Two inputs, two variables:** merging lists of sizes m and n is O(m + n), not O(n). A grid is O(rows × cols).

### 2.2 Reading the constraints

Interview problems are sized so the intended solution runs in about a second, which in Python is roughly **10⁷ simple operations**. The input limits therefore hint at the intended complexity:

| n up to | Intended complexity | Usual techniques |
|---|---|---|
| 10–12 | O(n!), O(2ⁿ · n) | Permutations, backtracking |
| 20–25 | O(2ⁿ) | Subsets, bitmask DP, meet in the middle (for ~40) |
| 100–500 | O(n³) | Interval DP, Floyd–Warshall |
| 1,000–5,000 | O(n²) | 2D DP, pairwise comparisons |
| 10⁵–10⁶ | O(n log n) or O(n) | Sorting, heaps, binary search, two pointers, hashing |
| 10⁹ and beyond | O(log n) or O(1) | Binary search on the answer, math |

### 2.3 Amortized analysis

Some operations are occasionally expensive but cheap **on average over a sequence**. A Python list doubles its capacity when full: an append that triggers a resize copies every element, but resizes happen so rarely that n appends cost O(n) in total — **O(1) amortized** per append.

```python
def appends_with_doubling(n: int) -> int:
    """Count element copies made by n appends into a dynamic array that doubles when full."""
    capacity, size, copies = 1, 0, 0
    for _ in range(n):
        if size == capacity:
            copies += size              # resize: copy everything into a twice-as-big array
            capacity *= 2
        size += 1
    return copies

for n in (10, 1_000, 1_000_000):
    assert appends_with_doubling(n) < 2 * n     # total work stays linear → O(1) amortized each
```

The same argument explains why the monotonic stack is O(n) despite a `while` loop inside a `for` loop: each element is pushed once and popped at most once, so the inner loop runs at most n times **in total**.

### 2.4 Recursion: recurrences and the master theorem

A recursive algorithm's cost is a recurrence. Most interview recursions match one of these:

| Recurrence | Example | Complexity |
|---|---|---|
| T(n) = T(n/2) + O(1) | Binary search | O(log n) |
| T(n) = T(n−1) + O(1) | Linked-list recursion, factorial | O(n) |
| T(n) = 2T(n/2) + O(n) | Merge sort | O(n log n) |
| T(n) = 2T(n/2) + O(1) | Tree traversal | O(n) |
| T(n) = 2T(n−1) + O(1) | Naive Fibonacci, all subsets | O(2ⁿ) |
| T(n) = T(n−1) + O(n) | Selection sort, naive quicksort worst case | O(n²) |

**Master theorem** (for T(n) = a·T(n/b) + O(nᵈ)): compare d with log_b(a).
- d > log_b(a): the top level dominates → O(nᵈ).
- d = log_b(a): every level costs the same → O(nᵈ log n) (merge sort: a = 2, b = 2, d = 1).
- d < log_b(a): the leaves dominate → O(n^log_b(a)).

A quicker rule for recursion trees: **total work = (number of calls) × (work per call)**. Memoized DP is (number of distinct states) × (work per state).

### 2.5 Space complexity and Python costs

- Count **auxiliary** space (extra memory you allocate) and say whether the output is included.
- **Recursion uses stack space**: a DFS on a path-shaped tree of 10⁵ nodes is O(n) space — and exceeds Python's default recursion limit of 1,000. Use an explicit stack, or `sys.setrecursionlimit` with care.
- Hidden costs in Python that interviewers notice:
  - `x in some_list` is O(n); `x in some_set` is O(1) on average.
  - Slicing `a[i:j]` copies → O(j − i). Passing slices into recursion silently adds a factor of n.
  - `s += ch` in a loop can be O(n²) overall; collect pieces in a list and `"".join` once.
  - `list.pop(0)` and `list.insert(0, x)` are O(n); use `collections.deque`.
  - Sorting is O(n log n) even when you only need the minimum — use `min()` or a heap.

The [Python interview reference](python.md) lists the cost of every built-in container operation.

---

## 3. Arrays

Core sequential container. Most patterns reduce to: traverse once, maintain a window or two pointers, accumulate prefix info, or sort and scan.

### 3.1 Pattern 0: Warm-up — Classic Array Problems

Single-pass tricks, in-place rewrites, and clever index/value math. Good for building the reflex of "scan once, track a counter, swap in place."

#### Easy

- [Move Zeroes](https://leetcode.com/problems/move-zeroes/) — two-pointer write/read
- [Max Consecutive Ones](https://leetcode.com/problems/max-consecutive-ones/) — running counter, reset on 0
- [Majority Element](https://leetcode.com/problems/majority-element/) — Boyer–Moore voting
- [Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) — track min price + max profit so far

#### Medium

- [Majority Element II](https://leetcode.com/problems/majority-element-ii/) — Boyer–Moore for two candidates (`⌊n/3⌋` threshold)
- [Rotate Array](https://leetcode.com/problems/rotate-array/)
- [Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/) — write pointer lags read pointer

#### Hard

- [First Missing Positive](https://leetcode.com/problems/first-missing-positive/)
- [Next Permutation](https://leetcode.com/problems/next-permutation/)
- [Find All Numbers Disappeared in an Array](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/) — in-place index marking (negate `nums[abs(x)-1]`)

### 3.2 Pattern 1: Prefix Sums

`prefix[i]` = sum of `nums[0..i-1]`. Range sum `nums[l..r]` = `prefix[r+1] - prefix[l]` in O(1).

```
nums   = [3, 1, 4, 1, 5, 9, 2]
prefix = [0, 3, 4, 8, 9, 14, 23, 25]
                   ▲           ▲
        sum(2..5) = prefix[6] - prefix[2] = 23 - 4 = 19
```

```python
prefix = [0] * (n + 1)
for i in range(n):
    prefix[i+1] = prefix[i] + nums[i]

def range_sum(l, r):           # inclusive
    return prefix[r+1] - prefix[l]
```

#### Variants

- **Suffix sum**: build right-to-left for "from i to end" queries.
- **Prefix XOR / product**: same idea with `^` or `*`.
- **2D prefix sum**: `psum[r][c] = nums[r][c] + psum[r-1][c] + psum[r][c-1] - psum[r-1][c-1]`.

#### Common problems

- [Range Sum Query — Immutable](https://leetcode.com/problems/range-sum-query-immutable/)
- [Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/)
- [Contiguous Array](https://leetcode.com/problems/contiguous-array/)
- [Range Sum Query 2D — Immutable](https://leetcode.com/problems/range-sum-query-2d-immutable/)
- [Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)
- [Subarray Sums Divisible by K](https://leetcode.com/problems/subarray-sums-divisible-by-k/) — count prefix remainders mod k

### 3.3 Pattern 2: Two Pointers

Two indices walking the array — usually one of:

- **Opposite ends** moving inward (sorted array, pair sum)
- **Same direction** at different speeds (in-place rewrite, dedup)
- **Fast / slow** (cycle / midpoint)

#### Example: Two Sum II (sorted array)

```
nums = [2, 7, 11, 15], target = 9
        L            R       L=2, R=15, sum=17 > 9 → R--
        L     R              L=2, R=11, sum=13 > 9 → R--
        L  R                 L=2, R=7,  sum=9  → answer (1, 2)
```

```python
def two_sum_sorted(nums, target):
    l, r = 0, len(nums) - 1
    while l < r:
        s = nums[l] + nums[r]
        if s == target: return [l, r]
        if s < target: l += 1
        else: r -= 1
    return [-1, -1]
```

#### Same-direction (in-place rewrite)

`write` lags `read`. Used for dedup, partition, remove element.

```python
def remove_duplicates(nums):           # sorted input
    write = 0
    for read in range(len(nums)):
        if read == 0 or nums[read] != nums[read-1]:
            nums[write] = nums[read]
            write += 1
    return write
```

#### Common problems

- [Two Sum II](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)
- [3Sum](https://leetcode.com/problems/3sum/)
- [Container With Most Water](https://leetcode.com/problems/container-with-most-water/)
- [Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)
- [Move Zeroes](https://leetcode.com/problems/move-zeroes/)
- [Sort Colors](https://leetcode.com/problems/sort-colors/) (Dutch national flag — 3 pointers)
- [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/) (optional)
- [3Sum Closest](https://leetcode.com/problems/3sum-closest/)
- [Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/) — fill from the back
- [Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/) — Floyd's cycle on `nums[i]` as pointers

### 3.4 Pattern 3: Sliding Window

#### Fixed Size

Window of exactly `k`. Slide one step → add new element, drop oldest. O(n) instead of O(n·k).

```
nums = [2, 1, 5, 1, 3, 2], k = 3

[2 1 5] 1 3 2     sum = 8
 2 [1 5 1] 3 2    sum = 7    (add 1, drop 2)
 2  1 [5 1 3] 2   sum = 9    (add 3, drop 1)
 2  1  5 [1 3 2]  sum = 6    (add 2, drop 5)

max = 9
```

```python
def max_sum_k(nums, k):
    window = sum(nums[:k])
    best = window
    for i in range(k, len(nums)):
        window += nums[i] - nums[i-k]
        best = max(best, window)
    return best
```

#### Common problems

- [Maximum Average Subarray I](https://leetcode.com/problems/maximum-average-subarray-i/)
- [Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/)
- [Permutation in String](https://leetcode.com/problems/permutation-in-string/)
- [Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/) (also see [Monotonic Queue](#95-monotonic-deque--sliding-window-maxmin))

---

#### Variable Size

Window grows/shrinks based on a condition. Right pointer expands; left pointer shrinks while the window is invalid.

```
                grow ──▶
   [L ─────────── R]
        shrink ◀──    while invalid
```

#### Template

```python
def variable_window(nums, condition):
    l = 0
    best = 0
    state = init_state()
    for r in range(len(nums)):
        update(state, nums[r])              # include nums[r]
        while not condition(state):
            update_remove(state, nums[l])   # drop nums[l]
            l += 1
        best = max(best, r - l + 1)         # window is now valid
    return best
```

#### Example: Longest Substring Without Repeating Characters

```python
def length_of_longest_substring(s):
    seen = {}            # char -> last index
    l = 0
    best = 0
    for r, ch in enumerate(s):
        if ch in seen and seen[ch] >= l:
            l = seen[ch] + 1
        seen[ch] = r
        best = max(best, r - l + 1)
    return best
```

#### Common problems

- [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
- [Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/)
- [Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/)
- [Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/)
- [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)
- [Subarrays with K Different Integers](https://leetcode.com/problems/subarrays-with-k-different-integers/) (exactly-K = atMost(K) - atMost(K-1))
- [Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/)

### 3.5 Pattern 4: Kadane's Algorithm

Max sum contiguous subarray in O(n). At each index, decide: extend previous subarray or restart at current.

```
nums    = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
curr    = [-2, 1, -2, 4,  3, 5, 6,  1, 5]
best    = [-2, 1,  1, 4,  4, 5, 6,  6, 6]

answer = 6   ([4, -1, 2, 1])
```

```python
def max_subarray(nums):
    curr = best = nums[0]
    for x in nums[1:]:
        curr = max(x, curr + x)        # extend or restart
        best = max(best, curr)
    return best
```

#### Variant: Max Product Subarray

Track both max and min (negative × negative flips).

```python
def max_product(nums):
    curr_max = curr_min = best = nums[0]
    for x in nums[1:]:
        if x < 0:
            curr_max, curr_min = curr_min, curr_max
        curr_max = max(x, curr_max * x)
        curr_min = min(x, curr_min * x)
        best = max(best, curr_max)
    return best
```

#### Variant: Max Circular Subarray Sum

Answer is `max(kadane(nums), total - kadane_min(nums))`. The second term handles the wrap-around case (remove min subarray). Edge case: if all negative, fall back to `kadane(nums)`.

```python
def max_circular(nums):
    total = sum(nums)
    max_kadane = kadane(nums, mode='max')
    min_kadane = kadane(nums, mode='min')
    if max_kadane < 0:
        return max_kadane
    return max(max_kadane, total - min_kadane)
```

#### Common problems

- [Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)
- [Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/)
- [Maximum Sum Circular Subarray](https://leetcode.com/problems/maximum-sum-circular-subarray/)
- [Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) (Kadane on diffs)

### 3.6 Pattern 5: 2D Matrix

#### Traverse

Row-major, column-major, diagonals, spiral. Indices: `(r, c)` with `0 <= r < n, 0 <= c < m`.

#### Common ops

```python
# transpose in-place (square matrix)
for r in range(n):
    for c in range(r+1, n):
        M[r][c], M[c][r] = M[c][r], M[r][c]

# rotate 90° clockwise = transpose + reverse each row
for row in M: row.reverse()

# spiral order
def spiral(M):
    res = []
    top, bottom, left, right = 0, len(M)-1, 0, len(M[0])-1
    while top <= bottom and left <= right:
        for c in range(left, right+1): res.append(M[top][c])
        top += 1
        for r in range(top, bottom+1): res.append(M[r][right])
        right -= 1
        if top <= bottom:
            for c in range(right, left-1, -1): res.append(M[bottom][c])
            bottom -= 1
        if left <= right:
            for r in range(bottom, top-1, -1): res.append(M[r][left])
            left += 1
    return res
```

#### Search a sorted matrix (rows + cols sorted)

Start from top-right (or bottom-left). Move left if too big, down if too small. O(n + m).

```python
def search_matrix(M, target):
    r, c = 0, len(M[0]) - 1
    while r < len(M) and c >= 0:
        if M[r][c] == target: return True
        if M[r][c] > target: c -= 1
        else: r += 1
    return False
```

#### Common problems

- [Rotate Image](https://leetcode.com/problems/rotate-image/)
- [Spiral Matrix](https://leetcode.com/problems/spiral-matrix/)
- [Set Matrix Zeroes](https://leetcode.com/problems/set-matrix-zeroes/)
- [Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/)
- [Search a 2D Matrix II](https://leetcode.com/problems/search-a-2d-matrix-ii/)
- [Word Search](https://leetcode.com/problems/word-search/) (DFS + backtracking)
- [Game of Life](https://leetcode.com/problems/game-of-life/)

---

## 4. Strings

Strings are immutable char arrays, so **every array pattern above applies directly** — two pointers, sliding window, prefix sums. Below are the string-specific idioms, most built on a char-count map. (General hash-table internals — chaining vs open addressing, why O(1) — live in [python.md](python.md#5-dictionary). Sorting itself — built-in, counting sort, merge/quick sort, quickselect — lives in [6. Sorting](#6-sorting).)

### 4.1 Frequency counting

A hashmap (`char → count`) or a fixed-size array (`[0]*26` for lowercase, `[0]*128` for ASCII) answers "is this char count what we need?" in O(1).

```python
from collections import Counter

cnt = Counter("aabbc")        # {'a':2, 'b':2, 'c':1}
cnt['z']                       # 0  (defaults, no KeyError)

def char_count(s):             # fixed-size array — faster for lowercase ASCII
    cnt = [0] * 26
    for ch in s:
        cnt[ord(ch) - ord('a')] += 1
    return cnt
```

Problems: [First Unique Character in a String](https://leetcode.com/problems/first-unique-character-in-a-string/), [Ransom Note](https://leetcode.com/problems/ransom-note/), [Find the Difference](https://leetcode.com/problems/find-the-difference/).

### 4.2 Sliding window + hashmap

Most "find substring with property X" problems = a variable window over `s` with a `Counter` tracking the window's char counts.

```python
def window_substring(s, target):
    need = Counter(target)
    have = Counter()
    matched = 0                                     # chars that meet need
    l, best = 0, ""
    for r, ch in enumerate(s):
        have[ch] += 1
        if ch in need and have[ch] == need[ch]:
            matched += 1
        while matched == len(need):                  # valid → shrink
            if best == "" or r - l + 1 < len(best):
                best = s[l:r+1]
            have[s[l]] -= 1
            if s[l] in need and have[s[l]] < need[s[l]]:
                matched -= 1
            l += 1
    return best
```

Problems: [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/), [Longest Substring with At Most K Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/), [Permutation in String](https://leetcode.com/problems/permutation-in-string/), [Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/), [Substring with Concatenation of All Words](https://leetcode.com/problems/substring-with-concatenation-of-all-words/).

### 4.3 Rolling hash (Rabin–Karp)

Hash a fixed-size window in O(1) amortized — for substring search, duplicate detection, repeated-DNA. Slide right by dropping the leading char and adding the trailing one:

```
hash_new = (hash_old - s[l]·b^(k-1)) · b + s[r+1]     # all mod a large prime
```

```python
def rabin_karp(s, t):                  # find t in s
    n, m = len(s), len(t)
    if m > n: return -1
    BASE, MOD = 256, (1 << 61) - 1
    pow_m = pow(BASE, m, MOD)
    th = sh = 0
    for i in range(m):
        th = (th * BASE + ord(t[i])) % MOD
        sh = (sh * BASE + ord(s[i])) % MOD
    if sh == th and s[:m] == t: return 0
    for i in range(m, n):
        sh = (sh * BASE + ord(s[i]) - ord(s[i-m]) * pow_m) % MOD
        if sh == th and s[i-m+1:i+1] == t: return i - m + 1
    return -1
```

Problems: [Repeated DNA Sequences](https://leetcode.com/problems/repeated-dna-sequences/), [Longest Duplicate Substring](https://leetcode.com/problems/longest-duplicate-substring/), [Find the Index of the First Occurrence in a String](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/).

### 4.4 Canonical key → hashmap grouping

Map each string to a key so strings sharing structure land in the same bucket. **Anagram grouping is the classic case** — the key is the sorted string (or a 26-int count tuple).

| Problem | Canonical key |
|---|---|
| Anagrams | sorted string / count tuple |
| Group shifted strings | tuple of diffs `(s[i+1]-s[i]) mod 26` |
| Isomorphic strings | pattern of first-occurrence indices |
| Word pattern | bijection between pattern char and word |

```python
from collections import defaultdict

def group_anagrams(strs):
    groups = defaultdict(list)
    for s in strs:
        groups[tuple(sorted(s))].append(s)       # anagrams share a sorted key
    return list(groups.values())

def is_isomorphic(s, t):
    if len(s) != len(t): return False
    s2t, t2s = {}, {}
    for a, b in zip(s, t):
        if s2t.get(a, b) != b or t2s.get(b, a) != a:
            return False
        s2t[a], t2s[b] = b, a
    return True
```

> Two strings are anagrams iff `Counter(s) == Counter(t)` — the O(1)-check special case of the same idea.

Problems: [Group Anagrams](https://leetcode.com/problems/group-anagrams/), [Valid Anagram](https://leetcode.com/problems/valid-anagram/), [Isomorphic Strings](https://leetcode.com/problems/isomorphic-strings/), [Word Pattern](https://leetcode.com/problems/word-pattern/), [Group Shifted Strings](https://leetcode.com/problems/group-shifted-strings/).

### 4.5 Two-pointer string tricks

```python
def is_palindrome(s):                       # ignore non-alphanumerics, case
    s = [c.lower() for c in s if c.isalnum()]
    l, r = 0, len(s) - 1
    while l < r:
        if s[l] != s[r]: return False
        l += 1; r -= 1
    return True

def longest_palindrome(s):                  # expand from each center
    def expand(l, r):
        while l >= 0 and r < len(s) and s[l] == s[r]:
            l -= 1; r += 1
        return s[l+1:r]
    best = ""
    for i in range(len(s)):
        for cand in (expand(i, i), expand(i, i+1)):   # odd + even centers
            if len(cand) > len(best): best = cand
    return best
```

Problems: [Valid Palindrome](https://leetcode.com/problems/valid-palindrome/), [Valid Palindrome II](https://leetcode.com/problems/valid-palindrome-ii/), [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/), [Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/), [Reverse Words in a String](https://leetcode.com/problems/reverse-words-in-a-string/), [Encode and Decode Strings](https://leetcode.com/problems/encode-and-decode-strings/).

---

## 5. Hashing

A hashmap turns "have I seen this?" / "how many of this?" / "where was this?" from an O(n) scan into an O(1) lookup. Container mechanics (`dict`/`set`/`Counter`, why O(1), collision handling) live in [python.md](python.md#5-dictionary) — this section is just the array/string problems built on top of it. (Anagram grouping, canonical keys, and sliding-window+`Counter` problems are already covered in [Strings](#4-strings) above.)

### 5.1 Value → index / seen-before map

```python
def two_sum(nums, target):                # value -> index, one pass
    seen = {}
    for i, x in enumerate(nums):
        if target - x in seen:
            return [seen[target - x], i]
        seen[x] = i
```

### 5.2 Set for membership / dedup

```python
def longest_consecutive(nums):             # only start counting at run heads
    s = set(nums)
    best = 0
    for x in s:
        if x - 1 not in s:                 # x is the start of a run
            y = x
            while y + 1 in s: y += 1
            best = max(best, y - x + 1)
    return best
```

### 5.3 Questions

- [Two Sum](https://leetcode.com/problems/two-sum/) — value → index map
- [Contains Duplicate](https://leetcode.com/problems/contains-duplicate/) — set + scan
- [Intersection of Two Arrays](https://leetcode.com/problems/intersection-of-two-arrays/) — set intersection
- [Happy Number](https://leetcode.com/problems/happy-number/) — set to detect cycles (or fast/slow)
- [Roman to Integer](https://leetcode.com/problems/roman-to-integer/) — symbol → value map
- [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/) — `Counter` + heap or bucket sort (see [14.3 Pattern 1: Top-K](#143-pattern-1-top-k))
- [Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/) — set, only start runs at sequence heads
- [Insert Delete GetRandom O(1)](https://leetcode.com/problems/insert-delete-getrandom-o1/) — array + `value → index` map
- [4Sum II](https://leetcode.com/problems/4sum-ii/) — split into two pair-sums, count via hashmap
- [Continuous Subarray Sum](https://leetcode.com/problems/continuous-subarray-sum/) — prefix-sum mod k
- [Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/) — `old → new` node map
- [Encode and Decode TinyURL](https://leetcode.com/problems/encode-and-decode-tinyurl/) — id ↔ url map
- [LRU Cache](https://leetcode.com/problems/lru-cache/) — hashmap + doubly linked list (see [LLD guide: LRU cache](../SystemDesign/lld-interview-guide.md#35-problem-lru-cache-and-lfu-cache-with-ttl))
- [LFU Cache](https://leetcode.com/problems/lfu-cache/) — hashmap + hashmap-of-DLLs + `min_freq`

### 5.4 Quick reference

| Pattern              | When                                       | Complexity   |
|----------------------|--------------------------------------------|--------------|
| Prefix sum           | Many range-sum queries                     | Build O(n), query O(1) |
| Two pointers         | Sorted array, pair/triple sum, in-place    | O(n)         |
| Fixed window         | "Subarray of size k with..."               | O(n)         |
| Variable window      | "Longest/shortest subarray with condition" | O(n)         |
| Kadane               | Max/min contiguous subarray                | O(n)         |
| Char counting        | Anagrams, freq — `Counter` or `[0]*26`     | O(n)         |
| Rolling hash         | Substring search / dup detection           | O(n)         |
| Expand from center   | Longest palindromic substring              | O(n²)        |
| Hashmap / set lookup | "Seen before?" / "complement exists?"      | O(n)         |

---

## 6. Sorting

Ordering the input turns a lot of problems into a scan or a two-pointer walk. Three flavors below: the built-in comparison sort, a non-comparison sort for small integer ranges, and the two classic divide-and-conquer sorts — quickselect rides along with Quick Sort since it's the same partition step, just recursing into one side instead of both.

### 6.1 Built-in

`nums.sort()` (Timsort) — O(n log n), stable, in-place.

### 6.2 Counting sort — O(n + k)

When values are small ints in `[0, k]`. Stable, non-comparison.

```python
def counting_sort(nums, k):
    count = [0] * (k + 1)
    for x in nums: count[x] += 1
    out = []
    for v, c in enumerate(count):
        out.extend([v] * c)
    return out
```

#### Common sort-then-scan problems

- [Merge Intervals](https://leetcode.com/problems/merge-intervals/)
- [Insert Interval](https://leetcode.com/problems/insert-interval/)
- [Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)
- [Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)
- [Largest Number](https://leetcode.com/problems/largest-number/) (custom comparator)
- [H-Index](https://leetcode.com/problems/h-index/)

### 6.3 Merge Sort

`O(n log n)` time, stable, `O(n)` space. Split in half, sort each half, merge.

```python
def merge_sort(nums):
    if len(nums) <= 1:
        return nums
    mid = len(nums) // 2
    left  = merge_sort(nums[:mid])
    right = merge_sort(nums[mid:])
    return merge(left, right)

def merge(a, b):
    out, i, j = [], 0, 0
    while i < len(a) and j < len(b):
        if a[i] <= b[j]:
            out.append(a[i]); i += 1
        else:
            out.append(b[j]); j += 1
    out.extend(a[i:]); out.extend(b[j:])
    return out
```

Prefer it when stability matters, for linked lists, or external sorts.

### 6.4 Quick Sort

`O(n log n)` average, `O(n²)` worst, in-place. Pick a pivot, partition around it, recurse on both sides.

```python
def quick_sort(nums, lo=0, hi=None):
    if hi is None:
        hi = len(nums) - 1
    if lo >= hi:
        return
    p = partition(nums, lo, hi)
    quick_sort(nums, lo, p - 1)
    quick_sort(nums, p + 1, hi)

def partition(nums, lo, hi):          # Lomuto: pivot = last element
    pivot = nums[hi]
    i = lo
    for j in range(lo, hi):
        if nums[j] <= pivot:
            nums[i], nums[j] = nums[j], nums[i]
            i += 1
    nums[i], nums[hi] = nums[hi], nums[i]
    return i
```

Avoid the `O(n²)` worst case by randomizing the pivot or using median-of-three.

### 6.5 Quickselect

`O(n)` average. Find the k-th smallest without fully sorting — partition, then recurse into **one** side only.

```python
def quickselect(nums, k):             # k is 0-indexed
    lo, hi = 0, len(nums) - 1
    while lo <= hi:
        p = partition(nums, lo, hi)
        if p == k:
            return nums[p]
        if p < k:
            lo = p + 1
        else:
            hi = p - 1
```

> **Count inversions** is a merge-sort variant: while merging, each element taken from the right half forms an inversion with every element still left in the left half.

#### Common problems

- [Sort an Array](https://leetcode.com/problems/sort-an-array/)
- [Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/) (quickselect or heap)
- [Count of Smaller Numbers After Self](https://leetcode.com/problems/count-of-smaller-numbers-after-self/) (merge-sort)
- [Reverse Pairs](https://leetcode.com/problems/reverse-pairs/)
- [Maximum Subarray](https://leetcode.com/problems/maximum-subarray/) (D&C version)
- [Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/) (pairwise merge, or heap — see [14. Heaps](#14-heaps))
- [Sort List](https://leetcode.com/problems/sort-list/) (merge sort on a linked list)
- [Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/) (binary-search partition)
- [Different Ways to Add Parentheses](https://leetcode.com/problems/different-ways-to-add-parentheses/) (split at each operator)
- [Longest Substring with At Least K Repeating Characters](https://leetcode.com/problems/longest-substring-with-at-least-k-repeating-characters/) (split on rare chars)
- [The Skyline Problem](https://leetcode.com/problems/the-skyline-problem/) (merge half-skylines)
- [Count of Range Sum](https://leetcode.com/problems/count-of-range-sum/) (merge-sort on prefix sums)

---

## 7. Binary search

Halve the search space each step. Works when the space is sorted **or** has a monotonic predicate.

```
T T T T F F F F F          (predicate flips exactly once)
        ▲
   find the boundary
```

### 7.1 Patterns covered

- Classic binary search (exact match)
- Lower bound / upper bound
- Search on the answer (parametric search)
- Binary search in a rotated array
- Binary search on a 2D matrix
- Find peak / find min in rotated

### 7.2 Pattern 1: Classic — Exact Match

Sorted array, find `target` index or `-1`.

```python
def binary_search(nums, target):
    lo, hi = 0, len(nums) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if nums[mid] == target: return mid
        if nums[mid] < target: lo = mid + 1
        else: hi = mid - 1
    return -1
```

**Invariant:** target, if it exists, is in `nums[lo..hi]`. Loop exits when range is empty.

> Use `mid = lo + (hi - lo) // 2` if you're worried about overflow (not an issue in Python, but a habit for C++/Java).

### 7.3 Pattern 2: Lower Bound / Upper Bound

The most useful BS form. Find a **boundary** instead of an exact match.

- **Lower bound**: smallest index `i` such that `nums[i] >= target`.
- **Upper bound**: smallest index `i` such that `nums[i] > target`.

```python
def lower_bound(nums, target):
    lo, hi = 0, len(nums)                        # [lo, hi)
    while lo < hi:
        mid = (lo + hi) // 2
        if nums[mid] < target: lo = mid + 1
        else: hi = mid
    return lo                                    # in [0, n]

def upper_bound(nums, target):
    lo, hi = 0, len(nums)
    while lo < hi:
        mid = (lo + hi) // 2
        if nums[mid] <= target: lo = mid + 1
        else: hi = mid
    return lo
```

Count of `target` = `upper_bound - lower_bound`.

#### Why `lo < hi` and `hi = len(nums)`?

- Half-open `[lo, hi)` avoids the `lo <= hi` infinite-loop trap with `hi = mid`.
- Result can be `n` if `target` is greater than everything.

#### Common problems

- [Search Insert Position](https://leetcode.com/problems/search-insert-position/)
- [First Bad Version](https://leetcode.com/problems/first-bad-version/)
- [Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)
- [Number of Occurrences](https://leetcode.com/problems/number-of-occurrences-of-an-element-in-an-array/)
- [Guess Number Higher or Lower](https://leetcode.com/problems/guess-number-higher-or-lower/)

### 7.4 Pattern 3: Search on the Answer (Parametric)

The array isn't sorted, but the **answer space** is. Binary-search the answer; use a `check(x)` predicate that's monotonic in `x`.

#### Template

```python
def min_feasible(lo, hi, check):
    while lo < hi:
        mid = (lo + hi) // 2
        if check(mid): hi = mid                  # mid works → maybe smaller works
        else:          lo = mid + 1
    return lo
```

#### Example: Koko Eating Bananas

Min eating-speed `k` such that Koko finishes all piles within `h` hours. Slower speed → more hours; faster → fewer. Monotonic ⇒ BS on `k`.

```python
def min_eating_speed(piles, h):
    def can_finish(k):
        hours = sum((p + k - 1) // k for p in piles)
        return hours <= h
    lo, hi = 1, max(piles)
    while lo < hi:
        mid = (lo + hi) // 2
        if can_finish(mid): hi = mid
        else: lo = mid + 1
    return lo
```

#### Common problems

- [Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/)
- [Capacity To Ship Packages Within D Days](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/)
- [Split Array Largest Sum](https://leetcode.com/problems/split-array-largest-sum/)
- [Minimum Number of Days to Make m Bouquets](https://leetcode.com/problems/minimum-number-of-days-to-make-m-bouquets/)
- [Find the Smallest Divisor Given a Threshold](https://leetcode.com/problems/find-the-smallest-divisor-given-a-threshold/)
- [Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/) (BS on partition)
- [Aggressive Cows / Magnetic Force](https://leetcode.com/problems/magnetic-force-between-two-balls/)

### 7.5 Pattern 4: Rotated Sorted Array

Array was sorted, then rotated at some pivot.

```
nums = [4, 5, 6, 7, 0, 1, 2]
        ─────────  ─────────
         sorted L   sorted R
```

At each step, one half is sorted. Check which half is sorted, then decide whether `target` is in it.

```python
def search_rotated(nums, target):
    lo, hi = 0, len(nums) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if nums[mid] == target: return mid
        if nums[lo] <= nums[mid]:                # left half sorted
            if nums[lo] <= target < nums[mid]:
                hi = mid - 1
            else:
                lo = mid + 1
        else:                                    # right half sorted
            if nums[mid] < target <= nums[hi]:
                lo = mid + 1
            else:
                hi = mid - 1
    return -1
```

#### Find min in rotated sorted array

```python
def find_min(nums):
    lo, hi = 0, len(nums) - 1
    while lo < hi:
        mid = (lo + hi) // 2
        if nums[mid] > nums[hi]: lo = mid + 1
        else: hi = mid
    return nums[lo]
```

#### Common problems

- [Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)
- [Search in Rotated Sorted Array II](https://leetcode.com/problems/search-in-rotated-sorted-array-ii/) (duplicates)
- [Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)
- [Find Minimum in Rotated Sorted Array II](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array-ii/)

### 7.6 Pattern 5: 2D Matrix

#### Rows + cols sorted, and `M[i][m-1] < M[i+1][0]` — full sorted

Treat as a flat sorted array of length `n*m`, map index `k` → `(k // m, k % m)`. BS in O(log nm).

```python
def search_matrix(M, target):
    n, m = len(M), len(M[0])
    lo, hi = 0, n * m - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        r, c = divmod(mid, m)
        if M[r][c] == target: return True
        if M[r][c] < target: lo = mid + 1
        else: hi = mid - 1
    return False
```

#### Only rows sorted, only cols sorted (no row-row ordering)

Start at top-right (or bottom-left). O(n + m). See [3.6 Pattern 5: 2D Matrix](#36-pattern-5-2d-matrix).

#### Common problems

- [Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/)
- [Search a 2D Matrix II](https://leetcode.com/problems/search-a-2d-matrix-ii/)
- [Find K-th Smallest Pair Distance](https://leetcode.com/problems/find-k-th-smallest-pair-distance/) (search on answer)

### 7.7 Pattern 6: Peak / Valley

Find any peak (`nums[i-1] < nums[i] > nums[i+1]`) in O(log n) using "which side goes uphill."

```python
def find_peak(nums):
    lo, hi = 0, len(nums) - 1
    while lo < hi:
        mid = (lo + hi) // 2
        if nums[mid] < nums[mid+1]: lo = mid + 1
        else: hi = mid
    return lo
```

- [Find Peak Element](https://leetcode.com/problems/find-peak-element/)
- [Peak Index in a Mountain Array](https://leetcode.com/problems/peak-index-in-a-mountain-array/)
- [Find in Mountain Array](https://leetcode.com/problems/find-in-mountain-array/)

### 7.8 Common pitfalls

| Pitfall                                         | Fix                                                                    |
| ----------------------------------------------- | ---------------------------------------------------------------------- |
| Infinite loop with`hi = mid` and `lo <= hi` | Use`lo < hi` + `hi = len(nums)` (half-open)                        |
| Off-by-one (`mid - 1` vs `mid`)             | Match`hi` init to interval style (closed vs open)                    |
| Overflow (C++/Java)                             | `mid = lo + (hi - lo) // 2`                                          |
| Missing duplicates                              | When`nums[lo] == nums[mid] == nums[hi]`, `lo++; hi--` (rotated II) |
| Predicate not monotonic                         | Search-on-answer needs`check(x)` to be monotonic                     |

### 7.9 Quick reference

| Pattern           | Search space        | Predicate              |
| ----------------- | ------------------- | ---------------------- |
| Exact match       | Sorted array        | `==`                 |
| Lower/upper bound | Sorted array        | `>=` / `>`         |
| Search on answer  | Answer range        | `check(x)` monotonic |
| Rotated array     | Sorted-then-rotated | Which half is sorted   |
| 2D matrix         | `n*m` flat        | Same as 1D             |
| Peak              | Unsorted            | Slope direction        |

---

## 8. Stacks

**LIFO** — use a plain `list`. This section has two parts: the **basic implementations / use cases**, and the **monotonic** variant that shows up for next-greater / previous-smaller queries.

### 8.1 Stack (LIFO)

Push / pop / peek in O(1) — in Python just use a `list`.

```python
st = []
st.append(x)      # push
st.pop()          # pop top   — both O(1)
st[-1]            # peek
while st: ...     # empty check
```

### 8.2 Matching Pairs

Stack tracks "what we expect to see next." Push openers, pop on closers and check.

```python
def is_valid(s):
    stack = []
    pairs = {')': '(', ']': '[', '}': '{'}
    for ch in s:
        if ch in pairs:                          # closer
            if not stack or stack.pop() != pairs[ch]:
                return False
        else:
            stack.append(ch)
    return not stack
```

#### Common problems

- [Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)
- [Minimum Remove to Make Valid Parentheses](https://leetcode.com/problems/minimum-remove-to-make-valid-parentheses/)
- [Longest Valid Parentheses](https://leetcode.com/problems/longest-valid-parentheses/)
- [Score of Parentheses](https://leetcode.com/problems/score-of-parentheses/)

### 8.3 Expression Evaluation

**Postfix (RPN)** — straightforward stack eval:

```python
def eval_rpn(tokens):
    stack = []
    ops = {'+': lambda a,b:a+b, '-': lambda a,b:a-b,
           '*': lambda a,b:a*b, '/': lambda a,b:int(a/b)}
    for t in tokens:
        if t in ops:
            b, a = stack.pop(), stack.pop()
            stack.append(ops[t](a, b))
        else:
            stack.append(int(t))
    return stack[0]
```

**Infix with `+ - * /` and parentheses** — one stack for partial sums plus a `sign`; apply `*`/`/` to the top immediately:

```python
def calculate(s):
    stack, num, sign, res = [], 0, 1, 0
    for ch in s + '+':
        if ch.isdigit():
            num = num*10 + int(ch)
        elif ch in '+-':
            res += sign * num
            sign, num = (1 if ch == '+' else -1), 0
        elif ch == '(':
            stack.append(res); stack.append(sign)
            res, sign = 0, 1
        elif ch == ')':
            res += sign * num
            num = 0
            res *= stack.pop()                   # sign
            res += stack.pop()                   # previous result
    return res + sign * num
```

#### Common problems

- [Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/)
- [Basic Calculator](https://leetcode.com/problems/basic-calculator/)
- [Basic Calculator II](https://leetcode.com/problems/basic-calculator-ii/)
- [Decode String](https://leetcode.com/problems/decode-string/)

### 8.4 Stack as Recursion Substitute

Iterative DFS. Push the root; while stack: pop, process, push children.

```python
def dfs_iter(root):
    if not root: return
    stack = [root]
    while stack:
        node = stack.pop()
        visit(node)
        if node.right: stack.append(node.right)
        if node.left:  stack.append(node.left)
```

#### Common problems

- [Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/) (iterative)
- [Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/)
- [Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/) (two stacks)
- [Flatten Nested List Iterator](https://leetcode.com/problems/flatten-nested-list-iterator/)

### 8.5 Min Stack — O(1) min

Store `(value, current_min)` pairs so the running minimum is always one peek away.

```python
class MinStack:
    def __init__(self):
        self.stack = []                           # (val, min_so_far)
    def push(self, x):
        cur_min = x if not self.stack else min(x, self.stack[-1][1])
        self.stack.append((x, cur_min))
    def pop(self):    self.stack.pop()
    def top(self):    return self.stack[-1][0]
    def getMin(self): return self.stack[-1][1]
```

#### Common problems

- [Min Stack](https://leetcode.com/problems/min-stack/)
- [Max Stack](https://leetcode.com/problems/max-stack/)

### 8.6 Monotonic Stack — Next Greater / Smaller

A stack whose values stay in monotonic order. When a new element breaks the order, **pop everything that violates it** — those popped elements just "found" their answer.

**Use it when** you hear "for each element, find the next / previous element that is greater / smaller."

```
push x:
  while stack.top violates monotonicity vs x:
      record answer for stack.pop()
  stack.push(x)
```

#### Example: Next Greater Element

For each `nums[i]`, find the next index `j > i` with `nums[j] > nums[i]`, else `-1`. Maintain a **decreasing** stack of indices (top = smallest still waiting).

```python
def next_greater(nums):
    n = len(nums)
    ans = [-1] * n
    stack = []                                   # indices, values decreasing
    for i, x in enumerate(nums):
        while stack and nums[stack[-1]] < x:
            ans[stack.pop()] = x
        stack.append(i)
    return ans
```

```
nums = [2, 1, 2, 4, 3]

i=0 push 0           stack=[0]   values=[2]
i=1 push 1           stack=[0,1] values=[2,1]
i=2 pop 1 → ans[1]=2 ; pop 0 → ans[0]=2 ; push 2   stack=[2]
i=3 pop 2 → ans[2]=4 ; push 3                       stack=[3]
i=4 push 4                                          stack=[3,4]

ans = [2, 2, 4, -1, -1]
```

**Circular variant** — for "next greater in a circular array," iterate `2n` times and use `i % n`:

```python
for i in range(2*n):
    x = nums[i % n]
    while stack and nums[stack[-1]] < x:
        ans[stack.pop()] = x
    if i < n: stack.append(i)
```

#### Example: Largest Rectangle in Histogram

Maintain an **increasing** stack of indices; when a shorter bar arrives, pop and measure the rectangle each popped bar can form.

```
heights = [2, 1, 5, 6, 2, 3]

           ┌──┐
           │6 │
        ┌──┤  │
        │5 │  │
        │  │  │      ┌──┐
   ┌──┐ │  │  │ ┌──┐ │3 │
   │2 │ │  │  │ │2 │ │  │
   │  │1│  │  │ │  │ │  │
   └──┴─┴──┴──┴─┴──┴─┴──┘

max area = 10  (bars 5, 6 → height 5 × width 2)
```

```python
def largest_rectangle(heights):
    stack = []                                   # indices, heights increasing
    heights.append(0)                            # sentinel flushes the stack
    best = 0
    for i, h in enumerate(heights):
        while stack and heights[stack[-1]] > h:
            top = stack.pop()
            left = stack[-1] if stack else -1
            width = i - left - 1
            best = max(best, heights[top] * width)
        stack.append(i)
    heights.pop()                                # restore
    return best
```

#### Common problems

- [Next Greater Element I](https://leetcode.com/problems/next-greater-element-i/)
- [Next Greater Element II](https://leetcode.com/problems/next-greater-element-ii/) (circular)
- [Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)
- [Online Stock Span](https://leetcode.com/problems/online-stock-span/)
- [Remove K Digits](https://leetcode.com/problems/remove-k-digits/)
- [132 Pattern](https://leetcode.com/problems/132-pattern/)
- [Sum of Subarray Minimums](https://leetcode.com/problems/sum-of-subarray-minimums/)
- [Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/)
- [Maximal Rectangle](https://leetcode.com/problems/maximal-rectangle/) (reduce to histogram per row)
- [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/) (also doable with stack)

---

## 9. Queues

**FIFO** — use `collections.deque`. Same two-part split as Stacks: basic implementations / use cases, then the **monotonic deque** variant for sliding-window extremes.

### 9.1 Queue (FIFO)

Enqueue at the back, dequeue at the front — use `collections.deque` (O(1) at both ends; `list.pop(0)` is O(n)).

```python
from collections import deque
q = deque()
q.append(x)          # enqueue        O(1)
q.popleft()          # dequeue         O(1)
q.appendleft(x)      # push front      O(1)  (deque-only)
q.pop()              # pop back        O(1)  (deque-only)
```

### 9.2 BFS

Level-by-level traversal of a tree or graph — the classic shortest-path-by-edges algorithm on unweighted graphs.

```python
def bfs(start):
    q = deque([start])
    seen = {start}
    while q:
        node = q.popleft()
        for nei in neighbors(node):
            if nei not in seen:
                seen.add(nei)
                q.append(nei)
```

> BFS problem sets live where they're used: grids / graphs in [15. Graphs](#15-graphs), level-order in [12. Trees and BSTs](#12-trees-and-bsts).

### 9.3 Circular Queue / Ring Buffer

Fixed-capacity queue using an array + a head index and a size.

```python
class MyCircularQueue:
    def __init__(self, k):
        self.buf = [0] * k
        self.head = 0
        self.size = 0
        self.cap = k

    def enQueue(self, x):
        if self.size == self.cap: return False
        tail = (self.head + self.size) % self.cap
        self.buf[tail] = x
        self.size += 1
        return True

    def deQueue(self):
        if self.size == 0: return False
        self.head = (self.head + 1) % self.cap
        self.size -= 1
        return True

    def Front(self): return -1 if self.size == 0 else self.buf[self.head]
    def Rear(self):  return -1 if self.size == 0 else self.buf[(self.head + self.size - 1) % self.cap]
    def isEmpty(self): return self.size == 0
    def isFull(self):  return self.size == self.cap
```

#### Common problems

- [Design Circular Queue](https://leetcode.com/problems/design-circular-queue/)
- [Design Circular Deque](https://leetcode.com/problems/design-circular-deque/)

### 9.4 Queue from Two Stacks (and vice versa)

**Queue using two stacks** — `in_stack` for pushes; `out_stack` for pops (when empty, pour `in_stack` into it, reversing order). Amortized O(1).

```python
class MyQueue:
    def __init__(self):
        self.in_s, self.out_s = [], []

    def push(self, x):
        self.in_s.append(x)

    def _shift(self):
        if not self.out_s:
            while self.in_s:
                self.out_s.append(self.in_s.pop())

    def pop(self):
        self._shift()
        return self.out_s.pop()

    def peek(self):
        self._shift()
        return self.out_s[-1]

    def empty(self):
        return not self.in_s and not self.out_s
```

**Stack using two queues** — push: enqueue to `q1`, move everything from `q2` into `q1`, swap. Now `q1` front = stack top.

#### Common problems

- [Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/)
- [Implement Stack using Queues](https://leetcode.com/problems/implement-stack-using-queues/)

### 9.5 Monotonic Deque — Sliding Window Max/Min

A deque kept monotonically increasing or decreasing. The front holds the answer for the current window in O(1).

**Use it when** you hear "sliding window of size k, return the max/min in each window" — O(n) total.

Maintain a **decreasing** deque of **indices**. For each new `i`:

1. Pop from back while `nums[back] <= nums[i]` (dominated — useless).
2. Append `i`.
3. Pop from front if it fell out of the window (`front <= i - k`).
4. Front of the deque is the current window max.

```python
def max_sliding_window(nums, k):
    q = deque()                                  # indices, nums[q] decreasing
    out = []
    for i, x in enumerate(nums):
        while q and nums[q[-1]] <= x:
            q.pop()
        q.append(i)
        if q[0] <= i - k:
            q.popleft()
        if i >= k - 1:
            out.append(nums[q[0]])
    return out
```

```
nums = [1, 3, -1, -3, 5, 3, 6, 7], k = 3

i=0 deque=[0]
i=1 pop 0 (1<3), deque=[1]                 — window not full
i=2 deque=[1,2]                            window=[1,3,-1] max=3
i=3 deque=[1,2,3]                          window=[3,-1,-3] max=3
i=4 pop 3,2,1 all ≤ 5, deque=[4]           window=[-1,-3,5] max=5
i=5 deque=[4,5]                            window=[-3,5,3] max=5
i=6 pop 5,4 (≤ 6), deque=[6]               window=[5,3,6] max=6
i=7 deque=[6,7]                            window=[3,6,7] max=7

out = [3, 3, 5, 5, 6, 7]
```

For the **minimum**, flip it: keep the deque **increasing** — pop from back while `nums[back] >= nums[i]`.

#### Common problems

- [Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/)
- [Sliding Window Median](https://leetcode.com/problems/sliding-window-median/) (two heaps; deque doesn't fit)
- [Constrained Subsequence Sum](https://leetcode.com/problems/constrained-subsequence-sum/) (DP + monotonic deque)
- [Shortest Subarray with Sum at Least K](https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/)
- [Jump Game VI](https://leetcode.com/problems/jump-game-vi/)

---

## 10. Linked lists

Each node holds a value and a pointer to the next. No random access — every position needs a walk from `head`. O(1) insert/delete at a known node (no shifting), but index lookup is O(n).

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```

### 10.1 Singly Linked List

One `next` pointer per node. Every technique below is a variation on three core moves.

#### Core operations

```python
# traverse
node = head
while node:
    node = node.next

# insert `new` after `prev`
new.next  = prev.next
prev.next = new

# delete the node after `prev`
prev.next = prev.next.next
```

#### Dummy Head (Sentinel)

Add a fake node before `head`. Eliminates "what if I delete the head?" / "what if the list is empty?" edge cases.

```python
dummy = ListNode(0, head)
prev = dummy
# ... mutate ...
return dummy.next
```

Use it for: remove nth from end, partition, merge, deletion.

#### Fast / Slow Pointers

`slow` moves 1 step, `fast` moves 2.

| Need              | Trick                                             |
|-------------------|---------------------------------------------------|
| Middle of list    | When `fast` hits end, `slow` is at middle         |
| Detect cycle      | If they meet, there's a cycle (Floyd's)           |
| Find cycle entry  | After meeting, reset one to head; move both by 1  |
| nth from end      | Move `fast` n steps first, then advance both      |

```
                ┌───────────────┐
                ▼               │
 head → a → b → c → d → e → f → g (cycle back to c)

slow and fast meet inside the cycle → cycle exists.
Reset slow to head; move both 1 step → they meet at cycle entry.
```

```python
def detect_cycle(head):                          # Floyd's — returns cycle entry
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            slow = head
            while slow is not fast:
                slow = slow.next
                fast = fast.next
            return slow
    return None

def middle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow

def remove_nth(head, n):
    dummy = ListNode(0, head)
    slow = fast = dummy
    for _ in range(n): fast = fast.next
    while fast.next:
        slow = slow.next
        fast = fast.next
    slow.next = slow.next.next
    return dummy.next
```

#### Reverse

Walk the list, flipping each node's `next` to point at the previous one.

```python
def reverse(head):                               # iterative
    prev, cur = None, head
    while cur:
        nxt = cur.next
        cur.next = prev
        prev, cur = cur, nxt
    return prev
```

```
prev = None, cur = 1 → 2 → 3 → 4
after one step:  1 → None  ,  cur = 2 → 3 → 4
after two:       2 → 1 → None ,  cur = 3 → 4
final:           4 → 3 → 2 → 1 → None
```

```python
def reverse_rec(head):                           # recursive
    if not head or not head.next: return head
    new_head = reverse_rec(head.next)
    head.next.next = head
    head.next = None
    return new_head

def reverse_between(head, l, r):                 # reverse sublist [l, r]
    dummy = ListNode(0, head)
    prev = dummy
    for _ in range(l - 1): prev = prev.next
    cur = prev.next
    for _ in range(r - l):                        # move next-pointer in front
        nxt = cur.next
        cur.next = nxt.next
        nxt.next = prev.next
        prev.next = nxt
    return dummy.next

def reverse_k_group(head, k):                    # reverse in groups of k
    node = head
    for _ in range(k):                            # check k nodes ahead
        if not node: return head
        node = node.next
    prev, cur = None, head
    for _ in range(k):
        nxt = cur.next; cur.next = prev
        prev, cur = cur, nxt
    head.next = reverse_k_group(cur, k)
    return prev
```

#### Merge

```python
def merge_two(a, b):                             # two sorted lists
    dummy = ListNode(0)
    tail = dummy
    while a and b:
        if a.val <= b.val:
            tail.next = a; a = a.next
        else:
            tail.next = b; b = b.next
        tail = tail.next
    tail.next = a or b
    return dummy.next
```

For **k sorted lists**: divide-and-conquer pairwise merge, or a min-heap of the heads — both O(N log k). See [Heaps](#14-heaps).

#### Reorder / Partition

**Reorder List** (`L0 → Ln → L1 → Ln-1 → …`): find middle → reverse second half → interleave.

```python
def reorder(head):
    if not head or not head.next: return
    slow = fast = head                            # 1. middle
    while fast.next and fast.next.next:
        slow = slow.next; fast = fast.next.next
    prev, cur = None, slow.next                    # 2. reverse second half
    slow.next = None
    while cur:
        nxt = cur.next; cur.next = prev
        prev, cur = cur, nxt
    a, b = head, prev                              # 3. interleave
    while b:
        a_next, b_next = a.next, b.next
        a.next = b; b.next = a_next
        a, b = a_next, b_next
```

**Partition** around `x`: build two dummy lists (less / greater-eq), then stitch.

```python
def partition(head, x):
    less, greater = ListNode(0), ListNode(0)
    lt, gt = less, greater
    while head:
        if head.val < x:
            lt.next = head; lt = lt.next
        else:
            gt.next = head; gt = gt.next
        head = head.next
    gt.next = None                                # terminate the tail
    lt.next = greater.next                        # stitch less → greater
    return less.next
```

#### Intersection of Two Lists

Two pointers, each walks its own list then swaps to the other. They meet at the intersection (or at `None`) after at most `len(a) + len(b)` steps.

```python
def intersection(a, b):
    pa, pb = a, b
    while pa is not pb:
        pa = pa.next if pa else b
        pb = pb.next if pb else a
    return pa
```

#### Deep Copy with Random Pointer

Node has `next` **and** `random`. Interleave clones into the original list so each clone's `random` is reachable as `orig.random.next` — O(1) extra space.

```python
def copy_random(head):
    if not head: return None
    cur = head                                   # 1. A → A' → B → B' → ...
    while cur:
        cur.next = Node(cur.val, cur.next)
        cur = cur.next.next
    cur = head                                   # 2. set clone.random
    while cur:
        if cur.random:
            cur.next.random = cur.random.next
        cur = cur.next.next
    cur, clone_head = head, head.next            # 3. unweave
    while cur:
        clone = cur.next
        cur.next = clone.next
        clone.next = clone.next.next if clone.next else None
        cur = cur.next
    return clone_head
```

#### Questions

- [Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)
- [Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/)
- [Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/)
- [Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/)
- [Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/)
- [Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)
- [Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/)
- [Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/)
- [Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)
- [Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)
- [Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)
- [Sort List](https://leetcode.com/problems/sort-list/)
- [Reorder List](https://leetcode.com/problems/reorder-list/)
- [Partition List](https://leetcode.com/problems/partition-list/)
- [Odd Even Linked List](https://leetcode.com/problems/odd-even-linked-list/)
- [Rotate List](https://leetcode.com/problems/rotate-list/)
- [Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/)
- [Remove Linked List Elements](https://leetcode.com/problems/remove-linked-list-elements/)
- [Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/)

### 10.2 Doubly Linked List

Each node carries **two** pointers, `prev` and `next`. That buys two things a singly list can't do:

- **O(1) delete given only the node** — a singly list needs the predecessor; here `node.prev` is right there.
- **Bidirectional traversal** — walk forward or backward.

The cost is an extra pointer per node. Pair it with **sentinel head/tail** nodes and every insert/delete becomes branch-free (no null checks at the ends).

```python
class DNode:
    def __init__(self, val=0):
        self.val = val
        self.prev = self.next = None

class DoublyLinkedList:
    def __init__(self):
        self.head = DNode()                       # sentinels — never removed
        self.tail = DNode()
        self.head.next = self.tail
        self.tail.prev = self.head

    def _link(self, node, after):                 # insert `node` right after `after`
        nxt = after.next
        after.next = node; node.prev = after
        node.next = nxt;   nxt.prev = node

    def push_front(self, val):                    # O(1)
        self._link(DNode(val), self.head)

    def push_back(self, val):                     # O(1)
        self._link(DNode(val), self.tail.prev)

    def remove(self, node):                       # O(1) unlink, given the node
        node.prev.next = node.next
        node.next.prev = node.prev
```

**Where it's used:** the O(1) "move a node to the front / evict from the back" combo is exactly what an [LRU cache](../SystemDesign/lld-interview-guide.md#35-problem-lru-cache-and-lfu-cache-with-ttl) (hashmap + DLL) needs. Deques, browser history, and Python's own `collections.OrderedDict` are built on the same idea.

#### Questions

- [Design Linked List](https://leetcode.com/problems/design-linked-list/)
- [Flatten a Multilevel Doubly Linked List](https://leetcode.com/problems/flatten-a-multilevel-doubly-linked-list/)
- [Design Browser History](https://leetcode.com/problems/design-browser-history/)
- [LRU Cache](https://leetcode.com/problems/lru-cache/) (hashmap + DLL — full design in [LLD guide: LRU cache](../SystemDesign/lld-interview-guide.md#35-problem-lru-cache-and-lfu-cache-with-ttl))
- [Convert Binary Search Tree to Sorted Doubly Linked List](https://leetcode.com/problems/convert-binary-search-tree-to-sorted-doubly-linked-list/)
- [All O`one Data Structure](https://leetcode.com/problems/all-oone-data-structure/) (DLL of frequency buckets)
- [Design Circular Deque](https://leetcode.com/problems/design-circular-deque/)

---

## 11. Recursion and backtracking

Recursion = define `f(input)` in terms of `f(smaller_input)` + base case.  
Backtracking = recursion + "undo choice and try another."

### 11.1 Recursion

```
                   ┌─ Base case → return
recursive call ────┤
                   └─ Decompose → recurse → combine
```

Every recursive function needs:

1. **Base case** — when to stop.
2. **Recursive case** — call self with smaller input.
3. **Combine** — use the recursive result.

#### Example: factorial

```python
def fact(n):
    if n <= 1: return 1
    return n * fact(n - 1)
```

#### Example: reverse a string

```python
def rev(s):
    if len(s) <= 1: return s
    return rev(s[1:]) + s[0]
```

### 11.2 Backtracking — Choose / Explore / Unchoose

Build a solution incrementally in a shared `path`. At each step: **choose** a candidate, **explore** deeper, then **unchoose** (undo) so the next candidate starts from a clean state.

```python
def backtrack(path):
    if is_solution(path):
        record(path)
        return
    for choice in choices(path):
        path.append(choice)      # choose
        backtrack(path)          # explore
        path.pop()               # unchoose
```

#### Combination Sum

Explore with `i` (not `i + 1`) so the same element can be reused, and let the base case check the running total.

```python
def combination_sum(candidates, target):
    res, path = [], []

    def backtrack(start, remaining):
        if remaining == 0:
            res.append(path[:])
            return
        if remaining < 0:                # prune: overshot the target
            return
        for i in range(start, len(candidates)):
            path.append(candidates[i])                   # choose
            backtrack(i, remaining - candidates[i])      # explore (i → reuse allowed)
            path.pop()                                   # unchoose

    backtrack(0, target)
    return res
```

#### Permutations

Order matters, so every step may pick any element not already in `path` — track a `used` array instead of a start index.

```python
def permute(nums):
    res, path = [], []
    used = [False] * len(nums)

    def backtrack():
        if len(path) == len(nums):       # record only full-length permutations
            res.append(path[:])
            return
        for i in range(len(nums)):
            if used[i]:
                continue
            used[i] = True               # choose
            path.append(nums[i])
            backtrack()                  # explore
            path.pop()                   # unchoose
            used[i] = False

    backtrack()
    return res
```

#### Pruning duplicates

Sort the input first, then skip a candidate equal to its left neighbour **within the same recursion level**:

```python
for i in range(start, len(nums)):
    if i > start and nums[i] == nums[i - 1]:   # skip duplicate at this level
        continue
    ...
```

For permutations, the "same level" check becomes: skip `nums[i]` if `nums[i] == nums[i-1] and not used[i-1]` (its identical twin hasn't been placed yet on this path).

#### Common problems

- [Subsets](https://leetcode.com/problems/subsets/)
- [Subsets II](https://leetcode.com/problems/subsets-ii/)
- [Permutations](https://leetcode.com/problems/permutations/)
- [Permutations II](https://leetcode.com/problems/permutations-ii/)
- [Combinations](https://leetcode.com/problems/combinations/)
- [Combination Sum](https://leetcode.com/problems/combination-sum/)
- [Combination Sum II](https://leetcode.com/problems/combination-sum-ii/)
- [Letter Combinations of a Phone Number](https://leetcode.com/problems/letter-combinations-of-a-phone-number/)
- [Generate Parentheses](https://leetcode.com/problems/generate-parentheses/)
- [Palindrome Partitioning](https://leetcode.com/problems/palindrome-partitioning/)
- [N-Queens](https://leetcode.com/problems/n-queens/)
- [N-Queens II](https://leetcode.com/problems/n-queens-ii/)
- [Sudoku Solver](https://leetcode.com/problems/sudoku-solver/)
- [Remove Invalid Parentheses](https://leetcode.com/problems/remove-invalid-parentheses/)
- [Restore IP Addresses](https://leetcode.com/problems/restore-ip-addresses/)

---

## 12. Trees and BSTs

A tree is a connected acyclic graph with `n` nodes and `n-1` edges. A **binary tree** has at most 2 children per node. A **BST** maintains `left.val < node.val < right.val`.

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

### 12.1 Traversals

```
        1
       / \
      2   3
     / \   \
    4   5   6

Pre-order  (root, L, R):  1 2 4 5 3 6
In-order   (L, root, R):  4 2 5 1 3 6     ← BST gives sorted output
Post-order (L, R, root):  4 5 2 6 3 1
Level-order (BFS)      :  1 2 3 4 5 6
```

#### BFS (level-order)

A queue processes the tree level by level. Snapshot `len(q)` at the start of each level to group nodes.

```python
from collections import deque

def level_order(root):
    if not root: return []
    q, out = deque([root]), []
    while q:
        level = []
        for _ in range(len(q)):
            n = q.popleft()
            level.append(n.val)
            if n.left:  q.append(n.left)
            if n.right: q.append(n.right)
        out.append(level)
    return out
```

#### DFS (pre / in / post order)

```python
def inorder(node, out):                          # recursive — swap the order for pre/post
    if not node: return
    inorder(node.left, out)
    out.append(node.val)
    inorder(node.right, out)
```

```python
def inorder_iter(root):                          # iterative inorder — explicit stack
    stack, out, node = [], [], root
    while stack or node:
        while node:
            stack.append(node)
            node = node.left
        node = stack.pop()
        out.append(node.val)
        node = node.right
    return out

def preorder_iter(root):                         # iterative preorder
    if not root: return []
    stack, out = [root], []
    while stack:
        node = stack.pop()
        out.append(node.val)
        if node.right: stack.append(node.right)  # right first → left pops first
        if node.left:  stack.append(node.left)
    return out
```

- **Postorder (iterative):** run preorder pushing left before right, then reverse the output.
- **Morris traversal — O(1) space:** thread each leaf's null-right pointer to its in-order successor. Rarely required.

#### Operations (height, diameter, path sum)

Two DFS styles cover almost every tree computation:

**Return-style** — each node returns info upward (height, sum, "is balanced").

```python
def max_depth(root):
    if not root: return 0
    return 1 + max(max_depth(root.left), max_depth(root.right))
```

**Diameter** — longest path through a node = `leftHeight + rightHeight`; track a global max while computing heights.

```python
def diameter(root):
    best = [0]
    def height(node):
        if not node: return 0
        l, r = height(node.left), height(node.right)
        best[0] = max(best[0], l + r)
        return 1 + max(l, r)
    height(root)
    return best[0]
```

**Accumulator-style** — pass state *down* the path (running sum, depth, path so far).

```python
def has_path_sum(root, target):
    if not root: return False
    if not root.left and not root.right:
        return root.val == target
    rest = target - root.val
    return has_path_sum(root.left, rest) or has_path_sum(root.right, rest)
```

#### Questions

- [Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)
- [Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/)
- [Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/)
- [Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)
- [Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)
- [Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/)
- [Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)
- [Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)
- [Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/)
- [Same Tree](https://leetcode.com/problems/same-tree/)
- [Symmetric Tree](https://leetcode.com/problems/symmetric-tree/)
- [Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/)
- [Path Sum](https://leetcode.com/problems/path-sum/)
- [Path Sum II](https://leetcode.com/problems/path-sum-ii/)
- [Path Sum III](https://leetcode.com/problems/path-sum-iii/) (prefix-sum hashmap)

### 12.2 BST

In-order traversal of a BST is strictly increasing — that ordering is what makes search/insert/delete O(h).

#### Search

```python
def bst_search(root, target):
    while root and root.val != target:
        root = root.left if target < root.val else root.right
    return root
```

#### Insert

```python
def bst_insert(root, val):
    if not root: return TreeNode(val)
    if val < root.val: root.left  = bst_insert(root.left, val)
    else:              root.right = bst_insert(root.right, val)
    return root
```

#### Delete

Three cases for the node to remove:

1. **No children** → return `None`.
2. **One child** → return that child.
3. **Two children** → replace with in-order successor (smallest in right subtree), then delete the successor.

```python
def bst_delete(root, key):
    if not root: return None
    if key < root.val:
        root.left = bst_delete(root.left, key)
    elif key > root.val:
        root.right = bst_delete(root.right, key)
    else:
        if not root.left:  return root.right
        if not root.right: return root.left
        succ = root.right
        while succ.left: succ = succ.left
        root.val = succ.val
        root.right = bst_delete(root.right, succ.val)
    return root
```

#### Validate

Pass min/max bounds down (or check that an in-order traversal is strictly increasing).

```python
def is_valid_bst(root, lo=float('-inf'), hi=float('inf')):
    if not root: return True
    if not (lo < root.val < hi): return False
    return (is_valid_bst(root.left, lo, root.val) and
            is_valid_bst(root.right, root.val, hi))
```

#### Questions

- [Search in a Binary Search Tree](https://leetcode.com/problems/search-in-a-binary-search-tree/)
- [Insert into a Binary Search Tree](https://leetcode.com/problems/insert-into-a-binary-search-tree/)
- [Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst/)
- [Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/)
- [Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/) (in-order)
- [Convert Sorted Array to BST](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)
- [Inorder Successor in BST](https://leetcode.com/problems/inorder-successor-in-bst/)
- [Recover Binary Search Tree](https://leetcode.com/problems/recover-binary-search-tree/)

### 12.3 Advanced Concepts

#### Lowest Common Ancestor (LCA)

**Binary tree (general):** if `p` or `q` is `root`, return `root`; else recurse both sides. If both come back non-null, `root` is the LCA.

```python
def lca(root, p, q):
    if not root or root is p or root is q: return root
    L = lca(root.left, p, q)
    R = lca(root.right, p, q)
    if L and R: return root
    return L or R
```

**BST (faster):** use the ordering — both values smaller → go left; both larger → go right; else `root` is the split point.

```python
def lca_bst(root, p, q):
    while root:
        if p.val < root.val and q.val < root.val:   root = root.left
        elif p.val > root.val and q.val > root.val: root = root.right
        else: return root
```

#### Construct Tree from Traversals

Root = `preorder[0]`. Its index in `inorder` splits the remaining values into the left and right subtrees.

```python
def build(preorder, inorder):
    idx = {v: i for i, v in enumerate(inorder)}
    self_pre = [0]                               # pointer into preorder
    def go(lo, hi):
        if lo > hi: return None
        val = preorder[self_pre[0]]; self_pre[0] += 1
        node = TreeNode(val)
        m = idx[val]
        node.left  = go(lo, m - 1)
        node.right = go(m + 1, hi)
        return node
    return go(0, len(inorder) - 1)
```

#### Serialize / Deserialize

Pre-order with a sentinel (`#`) for null children works for any binary tree.

```python
def serialize(root):
    out = []
    def go(node):
        if not node: out.append('#'); return
        out.append(str(node.val))
        go(node.left); go(node.right)
    go(root)
    return ','.join(out)

def deserialize(s):
    it = iter(s.split(','))
    def go():
        v = next(it)
        if v == '#': return None
        node = TreeNode(int(v))
        node.left  = go()
        node.right = go()
        return node
    return go()
```

#### Tree DP

Each node returns a tuple of "states" to its parent. See [17.12 Pattern 9: Tree DP](#1712-pattern-9-tree-dp) (Tree DP) for the full pattern.

```python
def rob(root):                                   # House Robber III
    def dp(n):
        if not n: return (0, 0)                   # (rob this node, skip it)
        lr, ls = dp(n.left)
        rr, rs = dp(n.right)
        return (n.val + ls + rs, max(lr, ls) + max(rr, rs))
    return max(dp(root))
```

#### Questions

- [LCA of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)
- [LCA of a BST](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)
- [LCA II](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-ii/) (nodes may not exist)
- [LCA III](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-iii/) (parent pointers)
- [Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)
- [Construct Binary Tree from Inorder and Postorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)
- [Construct Binary Tree from Preorder and Postorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-postorder-traversal/)
- [Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/)
- [Serialize and Deserialize BST](https://leetcode.com/problems/serialize-and-deserialize-bst/)
- [House Robber III](https://leetcode.com/problems/house-robber-iii/)
- [Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/)
- [Binary Tree Cameras](https://leetcode.com/problems/binary-tree-cameras/)
- [Sum of Distances in Tree](https://leetcode.com/problems/sum-of-distances-in-tree/) (re-rooting)

---

## 13. Trie

A tree where each node represents a character. Paths from root spell out stored strings. Used for prefix lookup, autocomplete, dictionary, longest common prefix.

### 13.1 Properties

- Each node has up to `k` children (k = alphabet size, 26 for lowercase).
- A node is marked `end` if some inserted word terminates there.
- Insert / search / delete / prefix-search: `O(L)` where `L` = word length.
- Space: `O(N * L * k)` worst case (N = number of words).

### 13.2 Node

```python
class TrieNode:
    def __init__(self):
        self.children = {}   # char -> TrieNode
        self.end = False     # True if a word ends here
```

### 13.3 Trie with CRUD

```python
class Trie:
    def __init__(self):
        self.root = TrieNode()

    # CREATE / UPDATE — insert a word
    def insert(self, word: str) -> None:
        node = self.root
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
        node.end = True

    # READ — exact word lookup
    def search(self, word: str) -> bool:
        node = self.root
        for ch in word:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return node.end

    # READ — prefix lookup
    def starts_with(self, prefix: str) -> bool:
        node = self.root
        for ch in prefix:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return True

    # DELETE — remove a word, prune dead branches
    def delete(self, word: str) -> bool:

        def helper(node: TrieNode, word: str, index: int) -> bool:
            if index == len(word):
                if not node.end:
                    return False        # word not present
                node.end = False
                return len(node.children) == 0   # prune if leaf
            ch = word[index]
            child = node.children.get(ch)
            if child is None:
                return False
            should_prune = helper(child, word, index + 1)
            if should_prune:
                del node.children[ch]
                return not node.end and len(node.children) == 0
            return False

        return helper(self.root, word, 0)
```

### 13.4 Usage

```python
t = Trie()
t.insert("apple")
t.insert("app")

t.search("apple")      # True
t.search("appl")       # False
t.starts_with("appl")  # True

t.delete("apple")
t.search("apple")      # False
t.search("app")        # True   (still present)
```

### 13.5 Common problems

- [Implement Trie (Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree/)
- [Design Add and Search Words Data Structure](https://leetcode.com/problems/design-add-and-search-words-data-structure/) — wildcard `.`
- [Word Search II](https://leetcode.com/problems/word-search-ii/) — trie + DFS on board
- [Replace Words](https://leetcode.com/problems/replace-words/)
- [Longest Word in Dictionary](https://leetcode.com/problems/longest-word-in-dictionary/)
- [Maximum XOR of Two Numbers in an Array](https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/) — bit trie

---

## 14. Heaps

A **heap** is a **complete binary tree** (every level full except possibly the last, which fills left to right) that satisfies the **heap property**:

- **Min-heap:** every parent ≤ its children → smallest element at the root
- **Max-heap:** every parent ≥ its children → largest element at the root

```
      Min-heap                         Max-heap
         5                                30
       /   \                            /    \
      11    12                        15      18
     / \   / \                       / \     / \
    20 25 30 18                     10 12   9   8

   root = minimum                  root = maximum
```

#### Stored as an array, not with pointers

A complete binary tree maps perfectly onto an array — index math replaces child/parent pointers.

```
        5(0)
       /    \                  index:  0   1   2   3   4   5   6
    11(1)   12(2)             value:  [ 5, 11, 12, 20, 25, 30, 18 ]
    /  \    /  \
 20(3) 25(4) 30(5) 18(6)

  left(i)   = 2*i + 1
  right(i)  = 2*i + 2
  parent(i) = (i - 1) // 2
```

#### Why an array (and why heaps over BST for priority queues)

- **Height is `log n`** — it's a complete tree, so operations are `O(log n)`.
- **No pointer memory** — no `left`/`right`/`parent` references to store.
- **Cache friendly** — contiguous array → better locality of reference than a pointer-based BST.
- **Build in `O(n)`** — `heapify` builds a heap in linear time vs `O(n log n)` to build a balanced BST.

That combination (fast build, no pointers, cache locality) is why **priority queues use heaps, not BSTs**.

### 14.1 Heaps in Python

Python's `heapq` module is a **min-heap** over a plain list.

```python
import heapq

pq = []
heapq.heappush(pq, 5)        # push        O(log n)
heapq.heappush(pq, 1)
heapq.heappush(pq, 3)
pq[0]                        # peek min    O(1)   → 1
heapq.heappop(pq)            # pop min     O(log n) → 1

arr = [5, 1, 4, 2, 30, 25]
heapq.heapify(arr)           # build heap in-place, O(n)
```

**Max-heap:** `heapq` has no max-heap, so **negate the values** (or wrap as `(-key, item)`).

```python
heapq.heappush(pq, -x)       # push
top = -heapq.heappop(pq)     # pop max
```

For reference, in C++ it's the opposite default:

```cpp
priority_queue<int> pq;                                // max-heap
priority_queue<int, vector<int>, greater<int>> pq;     // min-heap
```

### 14.2 Core operations (implemented from scratch)

Understanding `heapq` means understanding these. All examples are **min-heaps**.

#### 1. Heapify (sift-down) — `O(log n)`

Fix a single violation by pushing the value at `i` down to its correct spot. Assumes the subtrees below `i` are already valid heaps.

```python
def heapify(arr, n, i):                  # min-heapify subtree rooted at i
    smallest = i
    l, r = 2*i + 1, 2*i + 2
    if l < n and arr[l] < arr[smallest]:
        smallest = l
    if r < n and arr[r] < arr[smallest]:
        smallest = r
    if smallest != i:
        arr[i], arr[smallest] = arr[smallest], arr[i]
        heapify(arr, n, smallest)        # recurse into the affected subtree
```

#### 2. Build heap — `O(n)`

Heapify every internal node, starting from the **last non-leaf** (`n//2 - 1`) up to the root. Leaves are already valid heaps, so they're skipped. The work telescopes to `O(n)`, not `O(n log n)`.

```python
def build_heap(arr):
    n = len(arr)
    for i in range(n // 2 - 1, -1, -1):  # last non-leaf → root
        heapify(arr, n, i)
```

#### 3. Insert (sift-up) — `O(log n)`

Append at the end, then bubble up while smaller than the parent.

```python
def push(arr, x):
    arr.append(x)
    i = len(arr) - 1
    while i > 0 and arr[(i - 1) // 2] > arr[i]:
        p = (i - 1) // 2
        arr[p], arr[i] = arr[i], arr[p]
        i = p
```

#### 4. Extract-min — `O(log n)`

Swap root with the last element, pop it off, then sift the new root down.

```python
def pop_min(arr):
    arr[0], arr[-1] = arr[-1], arr[0]
    mn = arr.pop()
    heapify(arr, len(arr), 0)
    return mn
```

#### 5. Delete at index — `O(log n)`

Set the key to `-inf`, sift it up to the root, then extract-min.

```python
def delete(arr, i):
    arr[i] = float('-inf')
    while i > 0 and arr[(i - 1) // 2] > arr[i]:
        p = (i - 1) // 2
        arr[p], arr[i] = arr[i], arr[p]
        i = p
    pop_min(arr)
```

#### 6. Heap sort — `O(n log n)`

Build a **max-heap**, then repeatedly move the root (largest) to the end and shrink the heap. Sorts ascending in place.

```python
def max_heapify(arr, n, i):
    largest = i
    l, r = 2*i + 1, 2*i + 2
    if l < n and arr[l] > arr[largest]: largest = l
    if r < n and arr[r] > arr[largest]: largest = r
    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        max_heapify(arr, n, largest)

def heap_sort(arr):
    n = len(arr)
    for i in range(n // 2 - 1, -1, -1):  # build max-heap
        max_heapify(arr, n, i)
    for end in range(n - 1, 0, -1):
        arr[0], arr[end] = arr[end], arr[0]   # largest to the back
        max_heapify(arr, end, 0)              # restore on the shrunk heap
```

#### Complexity summary

| Operation      | Time       |
|----------------|------------|
| peek min/max   | `O(1)`     |
| push / insert  | `O(log n)` |
| pop / extract  | `O(log n)` |
| delete         | `O(log n)` |
| build heap     | `O(n)`     |
| heap sort      | `O(n log n)` |

### 14.3 Pattern 1: Top-K

**When:** "k largest / smallest / most frequent / closest" — anything asking for the top `k` out of `n`.

**Key trick:** to keep the **k largest**, use a **min-heap of size k**. The smallest of your top-k sits at the root; whenever the heap exceeds `k`, pop it. Runs in `O(n log k)` — cheaper than sorting (`O(n log n)`) when `k` is small.

> For the **k smallest**, flip it: use a **max-heap of size k** (negate values).

#### Example: Top K Frequent Elements

Return the `k` most frequent values in `nums`. Three ways, from brute to optimal:

```python
from collections import Counter
import heapq

# 1) Sort by frequency — O(n log n)
def topKFrequent(nums, k):
    freq = Counter(nums)
    return [x for x, _ in freq.most_common(k)]

# 2) Max-heap of ALL items, pop k times — O(n log n)
def topKFrequent(nums, k):
    freq = Counter(nums)
    heap = [(-cnt, x) for x, cnt in freq.items()]
    heapq.heapify(heap)
    return [heapq.heappop(heap)[1] for _ in range(k)]

# 3) Min-heap of size k — O(n log k)   ← best when k << n
def topKFrequent(nums, k):
    freq = Counter(nums)
    heap = []                              # holds (count, value)
    for x, cnt in freq.items():
        heapq.heappush(heap, (cnt, x))
        if len(heap) > k:                  # drop the least frequent so far
            heapq.heappop(heap)
    return [x for _, x in heap]
```

Approach 3 is the pattern: **push everything, but never let the heap grow past k.** What survives is your answer.

#### Example: Kth largest element

Same idea — a min-heap of size `k`, and the root *is* the k-th largest.

```python
def kth_largest(nums, k):
    heap = nums[:k]
    heapq.heapify(heap)
    for x in nums[k:]:
        if x > heap[0]:
            heapq.heapreplace(heap, x)     # pop + push in one sift
    return heap[0]
```

#### K-way merge (a Top-K variant)

Same machinery when merging `k` sorted streams: seed the heap with the head of each stream, pop the min, push its successor.

```python
def merge_k_lists(lists):
    heap = []
    for i, node in enumerate(lists):
        if node:
            heapq.heappush(heap, (node.val, i, node))   # i breaks val ties
    dummy = tail = ListNode(0)
    while heap:
        val, i, node = heapq.heappop(heap)
        tail.next = node; tail = node
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))
    return dummy.next
```

#### Problems

- [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)
- [Top K Frequent Words](https://leetcode.com/problems/top-k-frequent-words/)
- [Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/)
- [Kth Smallest Element in a Sorted Matrix](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/)
- [K Closest Points to Origin](https://leetcode.com/problems/k-closest-points-to-origin/)
- [Find K Pairs with Smallest Sums](https://leetcode.com/problems/find-k-pairs-with-smallest-sums/)
- [Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)
- [Smallest Range Covering Elements from K Lists](https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists/)
- [Ugly Number II](https://leetcode.com/problems/ugly-number-ii/)
- [Reorganize String](https://leetcode.com/problems/reorganize-string/)
- [Task Scheduler](https://leetcode.com/problems/task-scheduler/)
- [Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)
- [Single-Threaded CPU](https://leetcode.com/problems/single-threaded-cpu/)
- [Maximum Performance of a Team](https://leetcode.com/problems/maximum-performance-of-a-team/)

### 14.4 Pattern 2: Two Heaps

**When:** you need the **middle** of a stream, or to balance two groups by size/priority. Split the data into a smaller half and a larger half, each in its own heap.

**Setup:** a **max-heap `lo`** for the smaller half + a **min-heap `hi`** for the larger half. The two roots sit right at the boundary — so the median is always one pop away.

```
       hi  (min-heap, larger half)
        │   root = smallest of the big numbers
     median
        │   root = largest of the small numbers
       lo  (max-heap, smaller half)
```

Keep them balanced: `len(lo) == len(hi)` or `len(lo) == len(hi) + 1`.

#### Example: Find Median from a Data Stream

```python
class MedianFinder:
    def __init__(self):
        self.lo = []                       # max-heap (store negatives)
        self.hi = []                       # min-heap

    def addNum(self, x):
        heapq.heappush(self.lo, -x)                        # add to lo
        heapq.heappush(self.hi, -heapq.heappop(self.lo))   # move lo's max to hi
        if len(self.hi) > len(self.lo):                    # rebalance
            heapq.heappush(self.lo, -heapq.heappop(self.hi))

    def findMedian(self):
        if len(self.lo) > len(self.hi):
            return -self.lo[0]                             # odd count
        return (-self.lo[0] + self.hi[0]) / 2              # even count
```

Every `addNum` funnels the value through `lo → hi`, then rebalances so `lo` never falls behind — `O(log n)` per insert, `O(1)` median.

#### Problems

- [Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/)
- [Sliding Window Median](https://leetcode.com/problems/sliding-window-median/)

### 14.5 Pitfalls

| Pitfall                                        | Fix                                                   |
|------------------------------------------------|-------------------------------------------------------|
| Max-heap with `heapq`                          | Negate the value, or wrap as `(-key, item)`           |
| `heappush` on a list that isn't heap-ordered   | `heapify(list)` first                                 |
| Comparing un-orderable objects on a tie        | Add an index/counter as a tiebreaker: `(key, i, obj)` |
| Two-heaps drifting out of balance              | Always route through one heap, then rebalance sizes   |

---

## 15. Graphs

A graph is a nonlinear data structure consisting of nodes and edges. Every algorithm below builds on two primitives — BFS and DFS.

### 15.1 Representation

```python

from collections import defaultdict, deque
# Unweighted graph
graph = defaultdict(list)
graph['A'].append('B')
graph['A'].append('C')

# Weighted graph
weighted_graph = defaultdict(list)
weighted_graph['A'].append(('B', 2))
weighted_graph['A'].append(('C', 4))
```

The `graph['A'].append('B')` calls above build this — each key maps to a list of its neighbours (an **adjacency list**):

```
Unweighted                 Weighted                 Adjacency list (dict)
                                                     graph = {
      A                        A                       'A': ['B', 'C'],
     / \                      / \                       'B': [],
    B   C                  2 /   \ 4                     'C': [],
                            B     C                    }
   directed A→B, A→C      edge labels = weights
```

To make it **undirected**, add the edge both ways — `graph['B'].append('A')` as well. A fuller example and its picture:

```python
graph = defaultdict(list)
for u, v in [('A','B'), ('A','C'), ('B','D'), ('C','D')]:
    graph[u].append(v)
    graph[v].append(u)          # drop this line for a directed graph
```

```
        A                graph = {
       / \                 'A': ['B', 'C'],
      B   C                'B': ['A', 'D'],
       \ /                 'C': ['A', 'D'],
        D                  'D': ['B', 'C'],
                         }
   undirected: every edge stored on both endpoints
```

### 15.2 Traversal

#### BFS

```python
def bfs_unweighted(graph, start, visited):
    queue = deque([start])
    visited.add(start)
    
    while queue:
        node = queue.popleft()
        print(node, end=" ")  # Process the node
        
        # Visit all unvisited neighbors
        for neighbor in graph[node]:
            if neighbor not in visited:
                queue.append(neighbor)
                visited.add(neighbor)

def bfs(graph):
    visited = set()
    for i in graph:
        if i not in visited:
            bfs_unweighted(graph, i, visited)
```    
#### Questions

- [Rotting Oranges](https://leetcode.com/problems/rotting-oranges/) — multi-source BFS: seed the queue with every rotten orange, then expand level by level.
- [Word Ladder](https://leetcode.com/problems/word-ladder/) — BFS over an implicit graph where words one letter apart are neighbors.

#### DFS

```python
def helper(u, graph, visited):
    visited.add(u)
    print(u)
    for v in graph[u]:
        if v not in visited:
          helper(v,graph, visited)

def dfs(graph):
    visited = set()
    for u in graph:
        if u not in visited:
            helper(u, graph, visited)
```

#### Questions

- [Number of Islands](https://leetcode.com/problems/number-of-islands/) — flood-fill each unvisited land cell; the number of flood-fills you start is the answer.
- [Clone Graph](https://leetcode.com/problems/clone-graph/) — DFS while mapping original→copy in a hashmap so each node is cloned once.

#### Bipartite Check (BFS/DFS 2-coloring)

Just a traversal that colors each node the opposite of its parent. A graph is bipartite iff it's 2-colorable iff it has no odd cycles — if you ever reach a neighbor already colored the same as the current node, it isn't bipartite.

```python
def is_bipartite(graph, n):
    color = [-1] * n
    for s in range(n):
        if color[s] != -1: continue
        color[s] = 0
        q = deque([s])
        while q:
            u = q.popleft()
            for v in graph[u]:
                if color[v] == -1:
                    color[v] = 1 - color[u]     # opposite color
                    q.append(v)
                elif color[v] == color[u]:      # same color = odd cycle
                    return False
    return True
```

- [Is Graph Bipartite?](https://leetcode.com/problems/is-graph-bipartite/) — 2-color via BFS/DFS; a same-colored neighbor means it isn't bipartite.

### 15.3 Topological Sorting

Topological sorting for Directed Acyclic Graph (DAG) is a linear ordering of vertices such that for every directed edge (u, v), vertex u comes before v in the ordering.

Topological sorting can be performed using both:
 - Kahn's Algorithm (BFS + in-degree) — the approach used in your findOrder solution.
 - Depth-First Search (DFS) — a very elegant and widely used approach.

Both produce a valid topological order for a Directed Acyclic Graph (DAG).

#### Kahn's Algorithm (BFS + in-degree)

```python
# Kahn's BFS

from collections import defaultdict, deque

def topological_sort(n, edges):
    """
    n: number of nodes (0 to n-1)
    edges: list of directed edges [u, v] meaning u -> v

    Returns:
        A topological ordering if the graph is a DAG,
        otherwise returns [] if a cycle exists.
    """

    # Step 1: Build graph and compute in-degree
    graph = defaultdict(list)
    in_degree = [0] * n

    for u, v in edges:
        graph[u].append(v)
        in_degree[v] += 1

    # Step 2: Initialize queue with all nodes having in-degree 0
    queue = deque()
    for node in range(n):
        if in_degree[node] == 0:
            queue.append(node)

    # Step 3: Process nodes in BFS order
    topo_order = []

    while queue:
        u = queue.popleft()
        topo_order.append(u)

        for v in graph[u]:
            in_degree[v] -= 1
            if in_degree[v] == 0:
                queue.append(v)

    # Step 4: Check for cycle
    if len(topo_order) == n:
        return topo_order

    # Cycle detected
    return []
```

#### DFS-based Topological Sort

```python

# DFS-Based Topological Sort

def topo_sort_dfs(n, edges):
    graph = [[] for _ in range(n)]
    for u, v in edges:
        graph[u].append(v)

    state = [0] * n   # 0=unvisited, 1=visiting, 2=visited
    result = []

    def dfs(u):
        if state[u] == 1:
            return False      # cycle found
        if state[u] == 2:
            return True       # already processed

        state[u] = 1          # visiting

        for v in graph[u]:
            if not dfs(v):
                return False

        state[u] = 2          # fully processed
        result.append(u)
        return True

    for i in range(n):
        if state[i] == 0:
            if not dfs(i):
                return []     # cycle exists

    return result[::-1]
```

### 15.4 Questions

- [Course Schedule II](https://leetcode.com/problems/course-schedule-ii/) — return any valid order; an empty result means a cycle exists.
- [Alien Dictionary](https://leetcode.com/problems/alien-dictionary/) — derive precedence edges from adjacent words, then topological sort.

### 15.5 Disjoint Set Union (DSU) / Union-Find

Disjoint Set Union (DSU), also known as Union-Find, is a data structure used to efficiently maintain a collection of disjoint (non-overlapping) sets.

It supports two fundamental operations:
- Find(x) → Returns the representative (root) of the set containing x.
- Union(x, y) → Merges the sets containing x and y.

**Time complexity:** with both optimizations (**path compression** + **union by rank/size**), each `Find` and `Union` runs in **O(α(n))** amortized — where α is the inverse Ackermann function, effectively constant (α(n) ≤ 4 for any practical n). Without the optimizations it degrades to **O(n)** per operation in the worst case (a chain-like tree). Space is **O(n)**.

| Optimization | Find / Union |
|---|---|
| Naive (no optimization) | O(n) |
| Union by rank/size only | O(log n) |
| Path compression only | O(log n) amortized |
| Both (standard DSU) | O(α(n)) ≈ O(1) |

#### Intuition
Suppose we have the elements:

``` 
{0}, {1}, {2}, {3}, {4}
```

Initially, every element is in its own set.

After performing:
```
union(0, 1)
union(3, 4)
union(1, 4)
```
The sets become:

``` 
{0, 1, 3, 4}, {2}
``` 
Each set is represented by a single root node.

```python
class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # Path compression
        return self.parent[x]

    def union(self, x, y):
        root_x = self.find(x)
        root_y = self.find(y)

        if root_x == root_y:
            return False   # Already connected

        if self.rank[root_x] < self.rank[root_y]:
            self.parent[root_x] = root_y
        elif self.rank[root_x] > self.rank[root_y]:
            self.parent[root_y] = root_x
        else:
            self.parent[root_y] = root_x
            self.rank[root_x] += 1

        return True
```

#### Questions

- [Redundant Connection](https://leetcode.com/problems/redundant-connection/) — the edge whose `union` returns `False` is the one that closes a cycle.
- [Accounts Merge](https://leetcode.com/problems/accounts-merge/) — union accounts that share any email, then group members by their root.

### 15.6 Shortest Path Algorithms

Find the minimum-cost path from a source to one/all other vertices. Every algorithm here is built on **edge relaxation** — "if going through `u` reaches `v` cheaper than the best known, update `v`." They differ only in the **order** they relax edges, and that order is dictated by the graph's shape (weights, signs, DAG or not).

| Algorithm | Handles | Source→ | Negative edges | Negative cycle | Time |
|---|---|---|---|---|---|
| BFS | unweighted | single | — | — | O(V + E) |
| Dijkstra | non-negative weights | single | ✗ | ✗ | O((V + E) log V) |
| Bellman-Ford | any weights | single | ✓ | detects | O(V · E) |
| Floyd-Warshall | any weights | all pairs | ✓ | detects (diagonal < 0) | O(V³) |
| DAG relax | any weights, DAG only | single | ✓ | impossible (acyclic) | O(V + E) |

#### BFS (unweighted shortest path)

**Use when** every edge has the same cost (grids, "minimum number of steps/moves"). The first time BFS reaches a node, it's via a shortest path, because BFS explores in order of distance.

```python
def bfs_dist(graph, src):
    dist = {src: 0}
    q = deque([src])
    while q:
        u = q.popleft()
        for v in graph[u]:
            if v not in dist:
                dist[v] = dist[u] + 1
                q.append(v)
    return dist
```

#### Dijkstra (non-negative weights)

**Use when** edges have varying **non-negative** weights and you want the single-source shortest paths. A min-heap of `(dist, node)` always pops the closest unprocessed node; once popped it's settled (final). This greedy step is exactly why negative edges break it — a later cheaper detour can't be reconsidered.

```python
import heapq

def dijkstra(graph, src):                       # graph: {u: [(v, w), ...]}
    dist = {src: 0}
    h = [(0, src)]
    while h:
        d, u = heapq.heappop(h)
        if d > dist[u]: continue                 # stale entry
        for v, w in graph[u]:
            nd = d + w
            if nd < dist.get(v, float('inf')):
                dist[v] = nd
                heapq.heappush(h, (nd, v))
    return dist
```

#### Bellman-Ford (handles negative edges, detects negative cycles)

**Use when** the graph has **negative-weight edges**, or you need to detect a negative cycle. Relax every edge `V - 1` times (any shortest path has at most `V - 1` edges). If a relaxation still succeeds on the V-th pass → a negative cycle exists.

```python
def bellman_ford(n, edges, src):                 # edges: [(u, v, w), ...]
    dist = [float('inf')] * n
    dist[src] = 0
    for _ in range(n - 1):
        for u, v, w in edges:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
    for u, v, w in edges:                        # negative cycle check
        if dist[u] + w < dist[v]:
            return None
    return dist
```

#### Floyd-Warshall (all-pairs)

**Use when** you need shortest paths between **every pair** of vertices, and the graph is small (`V ≤ ~400`, since it's `O(V³)`). `dist[i][j]` is improved by trying each vertex `k` as an intermediate. A negative value on the diagonal afterward means a negative cycle.

```python
def floyd_warshall(n, edges):
    dist = [[float('inf')] * n for _ in range(n)]
    for i in range(n): dist[i][i] = 0
    for u, v, w in edges: dist[u][v] = w
    for k in range(n):
        for i in range(n):
            for j in range(n):
                if dist[i][k] + dist[k][j] < dist[i][j]:
                    dist[i][j] = dist[i][k] + dist[k][j]
    return dist
```

#### DAG (topological order + relax)

On a DAG there are no cycles, so process vertices in **topological order** and relax each one's outgoing edges. Every node is finalized before its successors are touched — one linear pass, and it handles **negative weights** too (no cycles to break the assumption).

```python
def dag_shortest_path(n, edges, src):            # edges: [(u, v, w), ...]
    graph = [[] for _ in range(n)]
    indeg = [0] * n
    for u, v, w in edges:
        graph[u].append((v, w))
        indeg[v] += 1

    q = deque(u for u in range(n) if indeg[u] == 0)
    topo = []
    while q:                                     # Kahn's topological sort
        u = q.popleft()
        topo.append(u)
        for v, w in graph[u]:
            indeg[v] -= 1
            if indeg[v] == 0:
                q.append(v)

    dist = [float('inf')] * n
    dist[src] = 0
    for u in topo:                               # relax in topological order
        if dist[u] == float('inf'): continue
        for v, w in graph[u]:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
    return dist
```

#### Questions

- [Network Delay Time](https://leetcode.com/problems/network-delay-time/) — single source to all nodes; works with **Dijkstra, Bellman-Ford, or Floyd-Warshall** — a great problem to implement all three and compare.
- [Cheapest Flights Within K Stops](https://leetcode.com/problems/cheapest-flights-within-k-stops/) — Bellman-Ford with exactly k+1 relaxation rounds (the stop limit caps path length).

### 15.7 Cycle Detection

The method depends on whether the graph is **undirected** or **directed** — the two need different rules.

| Graph | Techniques |
|---|---|
| Undirected | DFS/BFS with a **parent** check, or **DSU** (cycle if an edge's endpoints are already connected) |
| Directed | DFS with a **recursion-stack** check (back edge = cycle), or **Kahn's** (leftover nodes = cycle) |

---

#### Undirected — DFS / BFS (parent check)

Traverse the graph. If you reach an already-visited neighbor that **isn't the node you came from** (the parent), it's reachable by a second route → a cycle. The parent check is essential: without it the edge `u–v` looks like a cycle the instant you see `u` again from `v`.

```python
def has_cycle(graph):                     # DFS version
    visited = set()
    for node in graph:
        if node not in visited:
            if dfs_cycle(graph, node, visited, parent=-1):
                return True
    return False

def dfs_cycle(graph, u, visited, parent):
    visited.add(u)
    for v in graph[u]:
        if v not in visited:
            if dfs_cycle(graph, v, visited, u):   # u becomes the parent
                return True
        elif v != parent:                          # visited & not parent → cycle
            return True
    return False
```

```python
def bfs_cycle(graph, start, visited):     # BFS version
    queue = deque([(start, -1)])          # (node, parent)
    visited.add(start)
    while queue:
        u, parent = queue.popleft()
        for v in graph[u]:
            if v not in visited:
                visited.add(v)
                queue.append((v, u))
            elif v != parent:             # visited & not parent → cycle
                return True
    return False
```

#### Undirected — DSU (Union-Find)

For each edge `(u, v)`: if `u` and `v` are **already in the same set**, this edge closes a cycle. Otherwise union them. `O(α(n))` per edge.

```python
def has_cycle_dsu(n, edges):
    dsu = DSU(n)
    for u, v in edges:
        if dsu.find(u) == dsu.find(v):    # already connected → cycle
            return True
        dsu.union(u, v)
    return False
```

---

#### Directed graphs → it's just topological sort

Directed cycle detection is the **same algorithm** as [Topological Sorting](#153-topological-sorting) — no new code needed:

- **DFS-based** — the 3-state (unvisited / on-stack / done) DFS already returns a cycle flag when it revisits an on-stack node (a back edge). See [DFS-based Topological Sort](#dfs-based-topological-sort).
- **Kahn's BFS** — if the topo order can't place every node (`len(topo_order) != n`), the leftover nodes form a cycle. See [Kahn's Algorithm](#kahns-algorithm-bfs--in-degree).

---

#### Questions

- [Course Schedule](https://leetcode.com/problems/course-schedule/) — directed cycle detection = "can every node be topologically sorted?"
- [Detect Cycles in 2D Grid](https://leetcode.com/problems/detect-cycles-in-2d-grid/) — undirected cycle on a grid via a DFS parent check (or DSU).

### 15.8 Minimum Spanning Tree (MST)

Connect all `V` nodes with `V - 1` edges, minimizing total weight. Undirected, weighted, connected graph.

#### Kruskal (sort edges + Union-Find)

```python
def kruskal(n, edges):                           # edges: [(w, u, v), ...]
    edges.sort()
    parent = list(range(n))
    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x
    total, used = 0, 0
    for w, u, v in edges:
        ru, rv = find(u), find(v)
        if ru != rv:
            parent[ru] = rv
            total += w; used += 1
            if used == n - 1: break
    return total
```

#### Prim (heap-based)

Like Dijkstra but tracks edge weights, not path sums.

```python
def prim(graph, n):                              # graph: {u: [(v, w), ...]}
    visited = {0}
    h = [(w, v) for v, w in graph[0]]
    heapq.heapify(h)
    total = 0
    while h and len(visited) < n:
        w, u = heapq.heappop(h)
        if u in visited: continue
        visited.add(u); total += w
        for v, w2 in graph[u]:
            if v not in visited:
                heapq.heappush(h, (w2, v))
    return total
```

#### Questions

- [Min Cost to Connect All Points](https://leetcode.com/problems/min-cost-to-connect-all-points/) — MST over a complete graph of Manhattan distances (Prim or Kruskal).

### 15.9 Strongly Connected Components

A **strongly connected component (SCC)** is a maximal set of vertices in a **directed** graph where every vertex is reachable from every other vertex in the set. (Undirected graphs just have "connected components" — reachability is automatically mutual there.)

```
   1 ──▶ 0 ◀──▶ 3        SCCs: {0, 1, 2}  (0→2→1→0 cycle)
   ▲     │      │              {3}
   │     ▼      ▼              {4}
   2 ◀───┘      4        collapse each SCC to a node ⇒ a DAG (the "condensation")
```

#### Kosaraju's Algorithm

Two DFS passes, `O(V + E)`:

1. DFS the original graph, pushing each vertex onto a stack **when it finishes** (post-order).
2. **Reverse** every edge (transpose the graph).
3. Pop vertices off the stack; each DFS on the reversed graph collects exactly one SCC.

Why it works: the stack orders vertices by finish time, so the vertex on top belongs to a "source" SCC of the condensation. Reversing the edges makes that source a sink — so a DFS from it can't escape its own SCC, cleanly carving out one component at a time.

```python
def kosaraju(n, adj):                     # adj: adjacency list over 0..n-1
    visited = [False] * n
    order = []

    # Pass 1: record vertices by finish time
    def dfs1(u):
        visited[u] = True
        for v in adj[u]:
            if not visited[v]:
                dfs1(v)
        order.append(u)                   # pushed once fully explored

    for i in range(n):
        if not visited[i]:
            dfs1(i)

    # Pass 2: transpose the graph
    radj = [[] for _ in range(n)]
    for u in range(n):
        for v in adj[u]:
            radj[v].append(u)

    # Pass 3: DFS on the transpose in reverse finish order
    visited = [False] * n
    sccs = []

    def dfs2(u, comp):
        visited[u] = True
        comp.append(u)
        for v in radj[u]:
            if not visited[v]:
                dfs2(v, comp)

    for u in reversed(order):
        if not visited[u]:
            comp = []
            dfs2(u, comp)
            sccs.append(comp)

    return sccs                           # len(sccs) = number of SCCs
```

#### Questions

Pure SCC problems are uncommon on LeetCode; the Kosaraju/Tarjan machinery usually shows up as bridges, articulation points, or reachability.

- [Critical Connections in a Network](https://leetcode.com/problems/critical-connections-in-a-network/) — find all bridges with Tarjan's low-link DFS.

### 15.10 Bridges & Articulation Points

Both are found by a single DFS that tracks two timestamps per vertex — this is the **low-link** idea that Tarjan's SCC (next) reuses.

- `disc[u]` — **discovery time**: the order in which DFS first reaches `u`.
- `low[u]`  — the **lowest `disc`** reachable from `u`'s subtree using tree edges plus **at most one back edge**.

A **bridge** is an edge whose removal disconnects the graph. An **articulation point** is a vertex whose removal disconnects it. Both come out of comparing a child's `low` against the parent's `disc`, in `O(V + E)`.

**Bridge:** the tree edge `u → v` is a bridge when `low[v] > disc[u]` — nothing in `v`'s subtree can reach `u` or anything above it except through this edge.

```python
def find_bridges(n, adj):
    disc = [-1] * n
    low  = [0] * n
    timer = [0]
    bridges = []

    def dfs(u, parent):
        disc[u] = low[u] = timer[0]; timer[0] += 1
        for v in adj[u]:
            if v == parent:
                continue
            if disc[v] == -1:                 # tree edge
                dfs(v, u)
                low[u] = min(low[u], low[v])
                if low[v] > disc[u]:          # v can't reach u or above
                    bridges.append((u, v))
            else:                             # back edge
                low[u] = min(low[u], disc[v])

    for i in range(n):
        if disc[i] == -1:
            dfs(i, -1)
    return bridges
```

**Articulation point:** a non-root `u` is an AP if some child `v` has `low[v] >= disc[u]` (that subtree has no back edge climbing above `u`). The **root** is an AP only if it has **more than one** DFS child.

```python
def articulation_points(n, adj):
    disc = [-1] * n
    low  = [0] * n
    timer = [0]
    ap = set()

    def dfs(u, parent):
        disc[u] = low[u] = timer[0]; timer[0] += 1
        child = 0
        for v in adj[u]:
            if v == parent:
                continue
            if disc[v] == -1:
                child += 1
                dfs(v, u)
                low[u] = min(low[u], low[v])
                if parent != -1 and low[v] >= disc[u]:
                    ap.add(u)
            else:
                low[u] = min(low[u], disc[v])
        if parent == -1 and child > 1:        # root with 2+ subtrees
            ap.add(u)

    for i in range(n):
        if disc[i] == -1:
            dfs(i, -1)
    return ap
```

- [Critical Connections in a Network](https://leetcode.com/problems/critical-connections-in-a-network/) — return all bridges

### 15.11 Tarjan's Algorithm (SCC via low-link)

Tarjan finds SCCs in a **single DFS** using the same `disc` / `low` timestamps, plus a **stack of the current path** and an `on_stack` flag. Only edges to vertices *still on the stack* update `low` (they're part of the current, unfinished SCC). When a vertex satisfies `low[u] == disc[u]`, it's the **root** of an SCC — pop the stack down to it to emit that component.

```python
def tarjan_scc(n, adj):
    disc = [-1] * n
    low  = [0] * n
    on_stack = [False] * n
    timer = [0]
    stack = []
    sccs = []

    def dfs(u):
        disc[u] = low[u] = timer[0]; timer[0] += 1
        stack.append(u); on_stack[u] = True
        for v in adj[u]:
            if disc[v] == -1:                 # tree edge
                dfs(v)
                low[u] = min(low[u], low[v])
            elif on_stack[v]:                 # edge into the current SCC
                low[u] = min(low[u], disc[v])
        if low[u] == disc[u]:                 # u is an SCC root
            comp = []
            while True:
                w = stack.pop(); on_stack[w] = False
                comp.append(w)
                if w == u:
                    break
            sccs.append(comp)

    for i in range(n):
        if disc[i] == -1:
            dfs(i)
    return sccs
```

> Tarjan vs Kosaraju: same `O(V + E)`, but Tarjan does it in **one** DFS (no transpose) at the cost of the extra stack bookkeeping.

### 15.12 Must-Do / FAANG Interview Questions

The graph problems that show up most in big-tech interviews. Figuring out which technique each needs is part of the practice — concepts and worked hints live in the sections above.

- [Number of Islands](https://leetcode.com/problems/number-of-islands/)
- [Clone Graph](https://leetcode.com/problems/clone-graph/)
- [Course Schedule](https://leetcode.com/problems/course-schedule/)
- [Course Schedule II](https://leetcode.com/problems/course-schedule-ii/)
- [Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/)
- [Rotting Oranges](https://leetcode.com/problems/rotting-oranges/)
- [Word Ladder](https://leetcode.com/problems/word-ladder/)
- [Accounts Merge](https://leetcode.com/problems/accounts-merge/)
- [Network Delay Time](https://leetcode.com/problems/network-delay-time/)
- [Cheapest Flights Within K Stops](https://leetcode.com/problems/cheapest-flights-within-k-stops/)
- [Flood Fill](https://leetcode.com/problems/flood-fill/)
- [Max Area of Island](https://leetcode.com/problems/max-area-of-island/)
- [Surrounded Regions](https://leetcode.com/problems/surrounded-regions/)
- [Shortest Bridge](https://leetcode.com/problems/shortest-bridge/)
- [01 Matrix](https://leetcode.com/problems/01-matrix/)
- [Shortest Path in Binary Matrix](https://leetcode.com/problems/shortest-path-in-binary-matrix/)
- [As Far from Land as Possible](https://leetcode.com/problems/as-far-from-land-as-possible/)
- [Open the Lock](https://leetcode.com/problems/open-the-lock/)
- [Keys and Rooms](https://leetcode.com/problems/keys-and-rooms/)
- [All Paths From Source to Target](https://leetcode.com/problems/all-paths-from-source-to-target/)
- [Number of Provinces](https://leetcode.com/problems/number-of-provinces/)
- [Number of Connected Components in an Undirected Graph](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/)
- [Redundant Connection](https://leetcode.com/problems/redundant-connection/)
- [Graph Valid Tree](https://leetcode.com/problems/graph-valid-tree/)
- [Number of Operations to Make Network Connected](https://leetcode.com/problems/number-of-operations-to-make-network-connected/)
- [Satisfiability of Equality Equations](https://leetcode.com/problems/satisfiability-of-equality-equations/)
- [Evaluate Division](https://leetcode.com/problems/evaluate-division/)
- [Smallest String With Swaps](https://leetcode.com/problems/smallest-string-with-swaps/)
- [Most Stones Removed with Same Row or Column](https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/)
- [Number of Islands II](https://leetcode.com/problems/number-of-islands-ii/)
- [Redundant Connection II](https://leetcode.com/problems/redundant-connection-ii/)
- [Course Schedule IV](https://leetcode.com/problems/course-schedule-iv/)
- [Alien Dictionary](https://leetcode.com/problems/alien-dictionary/)
- [Find Eventual Safe States](https://leetcode.com/problems/find-eventual-safe-states/)
- [Minimum Height Trees](https://leetcode.com/problems/minimum-height-trees/)
- [Parallel Courses](https://leetcode.com/problems/parallel-courses/)
- [Find All Possible Recipes from Given Supplies](https://leetcode.com/problems/find-all-possible-recipes-from-given-supplies/)
- [Sort Items by Groups Respecting Dependencies](https://leetcode.com/problems/sort-items-by-groups-respecting-dependencies/)
- [Build a Matrix With Conditions](https://leetcode.com/problems/build-a-matrix-with-conditions/)
- [Path with Minimum Effort](https://leetcode.com/problems/path-with-minimum-effort/)
- [Swim in Rising Water](https://leetcode.com/problems/swim-in-rising-water/)
- [Path with Maximum Probability](https://leetcode.com/problems/path-with-maximum-probability/)
- [The Maze II](https://leetcode.com/problems/the-maze-ii/)
- [Minimum Cost to Make at Least One Valid Path in a Grid](https://leetcode.com/problems/minimum-cost-to-make-at-least-one-valid-path-in-a-grid/)
- [Minimum Cost to Convert String I](https://leetcode.com/problems/minimum-cost-to-convert-string-i/)
- [Find Minimum Time to Reach Last Room I](https://leetcode.com/problems/find-minimum-time-to-reach-last-room-i/)
- [Minimum Cost Path with Edge Reversals](https://leetcode.com/problems/minimum-cost-path-with-edge-reversals/)
- [Minimum Weighted Subgraph With the Required Paths](https://leetcode.com/problems/minimum-weighted-subgraph-with-the-required-paths/)
- [Find the City With the Smallest Number of Neighbors at a Threshold Distance](https://leetcode.com/problems/find-the-city-with-the-smallest-number-of-neighbors-at-a-threshold-distance/)
- [Is Graph Bipartite?](https://leetcode.com/problems/is-graph-bipartite/)
- [Possible Bipartition](https://leetcode.com/problems/possible-bipartition/)
- [Min Cost to Connect All Points](https://leetcode.com/problems/min-cost-to-connect-all-points/)
- [Connecting Cities With Minimum Cost](https://leetcode.com/problems/connecting-cities-with-minimum-cost/)
- [Detect Cycles in 2D Grid](https://leetcode.com/problems/detect-cycles-in-2d-grid/)
- [Critical Connections in a Network](https://leetcode.com/problems/critical-connections-in-a-network/)

### 15.13 Quick reference

| Need                                | Algorithm                    |
|-------------------------------------|------------------------------|
| Connected components                | DFS / BFS / DSU              |
| Shortest path, unweighted           | BFS                          |
| Shortest path, non-neg weights      | Dijkstra                     |
| Shortest path, neg weights          | Bellman-Ford                 |
| Shortest path, all pairs            | Floyd-Warshall               |
| Topological order                   | Kahn's BFS or DFS post-order |
| Cycle in undirected                 | DFS with parent, or DSU      |
| Cycle in directed                   | DFS rec-stack, or Kahn's     |
| MST                                 | Kruskal or Prim              |
| Bipartite check                     | 2-coloring via BFS/DFS       |
| Strongly connected components (dir) | Kosaraju (2 DFS) or Tarjan    |
| Bridges / articulation              | Tarjan (DFS low-link)        |

---

## 16. Greedy

Make the locally optimal choice at every step. Works when the problem has the **greedy choice property** — local optimum leads to global optimum — and **optimal substructure**.

Greedy is NOT always correct. If you can construct a counterexample where greedy fails, you need DP. See the robber example in [DP vs Greedy](#173-dp-vs-greedy-when-to-use-which).

### 16.1 How to recognize a greedy problem

- "Maximum / minimum number of …"
- "Earliest / latest …"
- Sort + scan solves it
- Interchange argument: swapping two adjacent choices never improves the answer

### 16.2 Pattern 1: Interval Scheduling

Sort intervals by start or end time, then scan sequentially to select, merge, or insert intervals.

#### Activity Selection — max non-overlapping intervals

Sort by **end time**. Greedily pick each interval whose start is `≥` the last selected end.

```python
def max_non_overlap(intervals):
    intervals.sort(key=lambda x: x[1])
    count = 0
    last_end = float('-inf')
    for s, e in intervals:
        if s >= last_end:
            count += 1
            last_end = e
    return count
```

**Why end time?** Picking the interval that ends earliest leaves the most room for the rest. Minimum intervals to remove = `n - max_non_overlap`.

#### Merge overlapping intervals

Sort by **start time**. Scan and merge.

```python
def merge(intervals):
    intervals.sort()
    out = [intervals[0]]
    for s, e in intervals[1:]:
        if s <= out[-1][1]:
            out[-1][1] = max(out[-1][1], e)
        else:
            out.append([s, e])
    return out
```

#### Insert interval

Insert a new interval into sorted non-overlapping intervals and merge if necessary.

```python
def insert(intervals, newInterval):
    out = []
    i, n = 0, len(intervals)
    # Add all intervals ending before newInterval starts
    while i < n and intervals[i][1] < newInterval[0]:
        out.append(intervals[i])
        i += 1
    # Merge overlapping intervals with newInterval
    while i < n and intervals[i][0] <= newInterval[1]:
        newInterval[0] = min(newInterval[0], intervals[i][0])
        newInterval[1] = max(newInterval[1], intervals[i][1])
        i += 1
    out.append(newInterval)
    # Add remaining intervals
    while i < n:
        out.append(intervals[i])
        i += 1
    return out
```

#### Common problems

- [Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)
- [Minimum Number of Arrows to Burst Balloons](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/)
- [Merge Intervals](https://leetcode.com/problems/merge-intervals/)
- [Insert Interval](https://leetcode.com/problems/insert-interval/)

### 16.3 Pattern 2: Sweep Line Algorithm

#### Basic Knowledge & Core Concept

The **Sweep Line (or Line Sweep)** algorithm conceptualizes a 1D line (or 2D vertical line) sweeping across coordinates/timestamps from left to right (`-∞` to `+∞`).

Instead of comparing every object against every other object ($O(N^2)$), Sweep Line transforms the problem into event-driven processing ($O(N \log N)$):

1. **Decompose into Events**: Break range/interval objects into discrete point events — e.g., an interval `[start, end]` becomes a "Start Event" at `start` and an "End Event" at `end`.
2. **Sort Events**: Sort all events by timestamp/coordinate.
3. **Maintain Active State**: Iterate through sorted events and update a dynamic state (e.g., running total counter, max-heap of active heights, or balanced BST of active segments).

> **Tie-breaking Rule:** When two events occur at the exact same coordinate (e.g., end event of interval A and start event of interval B), event ordering depends on interval boundary rules:
> - **Inclusive intervals `[start, end]`**: process **Start before End** (overlapping at boundaries count as concurrent).
> - **Exclusive intervals `(start, end)`**: process **End before Start** (releasing resources before allocating new ones).

---

#### Key Use Cases

##### 1. 1D Concurrency & Max Overlapping (Difference Array / Point Events)

**Use case:** Finding maximum concurrent items at any point in time, checking total capacity, or calculating overlap metrics across continuous ranges.

**Approach:** Turn each interval `(s, e, val)` into `(s, +val)` and `(e, -val)`. Sort by time and accumulate `current_capacity`.

```python
def car_pooling(trips, capacity):
    events = []
    for n, s, e in trips:
        events.append((s, n))    # Pick up passengers
        events.append((e, -n))   # Drop off passengers
    
    # Sort events by time; if times equal, drop-offs (-n) come before pick-ups (+n)
    events.sort(key=lambda x: (x[0], x[1]))
    
    cur = 0
    for _, delta in events:
        cur += delta
        if cur > capacity:
            return False
    return True
```

##### 2. Two-Pointer Sweep (Separated Start/End Arrays)

**Use case:** When you only care about overlap counts (like room allocation) and don't need custom event payloads or multi-attribute state.

**Approach:** Extract and sort `starts` and `ends` independently. Use two pointers to simulate the sweep line advancing.

```python
def min_meeting_rooms(intervals):
    starts = sorted(s for s, _ in intervals)
    ends   = sorted(e for _, e in intervals)
    rooms = used = i = j = 0
    while i < len(starts):
        if starts[i] < ends[j]:  # Meeting starts before earliest ending meeting
            used += 1; i += 1
        else:                     # A room frees up
            used -= 1; j += 1
        rooms = max(rooms, used)
    return rooms
```

##### 3. 2D Sweep Line with Dynamic Active Set (Sweep Line + Heap / Sorted Set)

**Use case:** Geometrical sweep line problems where active elements have varying properties (e.g., building heights, overlapping rectangles, skyline contour).

**Approach:** Sweep along X-axis. As the sweep line crosses X, maintain active entities along Y-axis in a Heap or Balanced BST.

```python
import heapq

def get_skyline(buildings):
    # Events: (x, -height, right) for start, (x, 0, 0) for end
    # Using -height so starts process taller buildings first
    events = []
    for l, r, h in buildings:
        events.append((l, -h, r))  # Start of building
        events.append((r, 0, 0))   # End marker
    events.sort()

    res = [[0, 0]]
    # Max-heap: (-height, end_x)
    hp = [(0, float('inf'))]

    for x, neg_h, r in events:
        if neg_h != 0:
            heapq.heappush(hp, (neg_h, r))
        
        # Lazy deletion of expired buildings from heap top
        while hp[0][1] <= x:
            heapq.heappop(hp)
            
        cur_max = -hp[0][0]
        if res[-1][1] != cur_max:
            res.append([x, cur_max])

    return res[1:]
```

---

#### Segregated Problem List

##### 1D Concurrency & Max Overlap (Point Event Counting)
- [Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/) — Min rooms needed for overlapping intervals
- [Car Pooling](https://leetcode.com/problems/car-pooling/) — Capacity checking with point pick-up/drop-off
- [Maximum Population Year](https://leetcode.com/problems/maximum-population-year/) — Max overlapping lifespan range
- [Corporate Flight Bookings](https://leetcode.com/problems/corporate-flight-bookings/) — Prefix sum / difference array on 1D range

##### Multi-Level Overlap & Gap / Free Time Sweep
- [My Calendar I](https://leetcode.com/problems/my-calendar-i/) — Single overlap detection
- [My Calendar II](https://leetcode.com/problems/my-calendar-ii/) — Double booking / triple booking detection
- [Employee Free Time](https://leetcode.com/problems/employee-free-time/) — Finding gap intervals across multiple schedules
- [Describe the Painting](https://leetcode.com/problems/describe-the-painting/) — Sweep line color sum tracking across line segments

##### Advanced 2D / Active Set Sweep Line (Sweep + Heap / BST / Point Queries)
- [The Skyline Problem](https://leetcode.com/problems/the-skyline-problem/) — 2D sweep with active max height heap
- [Number of Flowers in Full Bloom](https://leetcode.com/problems/number-of-flowers-in-full-bloom/) — Point query on interval sweep line
- [Rectangle Area II](https://leetcode.com/problems/rectangle-area-ii/) — 2D sweep line for total union area calculation

### 16.4 Pattern 3: Jump Game

#### Can you reach the end?

Track the furthest reachable index. If you ever stand beyond it, return false.

```python
def can_jump(nums):
    reach = 0
    for i, x in enumerate(nums):
        if i > reach: return False
        reach = max(reach, i + x)
    return True
```

#### Min jumps to reach end

BFS-like: at each step, expand the current "level" of reachable indices.

```python
def jump(nums):
    jumps = cur_end = farthest = 0
    for i in range(len(nums) - 1):
        farthest = max(farthest, i + nums[i])
        if i == cur_end:
            jumps += 1
            cur_end = farthest
    return jumps
```

#### Common problems

- [Jump Game](https://leetcode.com/problems/jump-game/)
- [Jump Game II](https://leetcode.com/problems/jump-game-ii/)
- [Gas Station](https://leetcode.com/problems/gas-station/)
- [Video Stitching](https://leetcode.com/problems/video-stitching/)

### 16.5 Pattern 4: Sorted + Two Pointers

Sort then greedily pair from both ends.

#### Boats to Save People

**Problem:** each boat carries at most **2 people** with combined weight `≤ limit`. Return the minimum number of boats.

**Approach:** sort, then two pointers from heaviest and lightest. Pair them if they fit; otherwise the boat takes only the heaviest.

```python
def num_rescue_boats(people, limit):
    people.sort()
    l, r = 0, len(people) - 1
    boats = 0
    while l <= r:
        if people[l] + people[r] <= limit:
            l += 1
        r -= 1
        boats += 1
    return boats
```

#### Common problems

- [Boats to Save People](https://leetcode.com/problems/boats-to-save-people/)
- [Assign Cookies](https://leetcode.com/problems/assign-cookies/)
- [Two City Scheduling](https://leetcode.com/problems/two-city-scheduling/)
- [Minimum Number of Operations to Make Array Continuous](https://leetcode.com/problems/minimum-number-of-operations-to-make-array-continuous/)

### 16.6 Pattern 5: Greedy + Heap

When greedy + sort isn't enough — you need to re-prioritize after each decision.

#### Reorganize String

**Problem:** rearrange `s` so that **no two adjacent characters are the same**; return `""` if impossible (a char appearing more than `⌈n/2⌉` times can't be spaced out).

**Approach:** always place the most-frequent *remaining* char, but hold the one you just used aside for a turn so it can't be picked twice in a row — then push it back. Max-heap on counts.

```python
def reorganize(s):
    cnt = Counter(s)
    if max(cnt.values()) > (len(s) + 1) // 2: return ""
    h = [(-c, ch) for ch, c in cnt.items()]
    heapq.heapify(h)
    out = []
    prev = (0, '')
    while h:
        c, ch = heapq.heappop(h)
        out.append(ch)
        if prev[0] < 0:
            heapq.heappush(h, prev)
        prev = (c + 1, ch)
    return ''.join(out)
```

#### Huffman Coding

Build a prefix-free binary code of minimum total length. Min-heap of frequencies; pop the two smallest, merge, push back. Classic greedy with provable optimality via the interchange argument.

```python
def huffman(freqs):
    h = [(f, ch) for ch, f in freqs.items()]
    heapq.heapify(h)
    while len(h) > 1:
        f1, _ = heapq.heappop(h)
        f2, _ = heapq.heappop(h)
        heapq.heappush(h, (f1 + f2, None))
    return h[0][0]                               # total cost
```

#### Common problems

- [Reorganize String](https://leetcode.com/problems/reorganize-string/)
- [Task Scheduler](https://leetcode.com/problems/task-scheduler/)
- [Maximum Performance of a Team](https://leetcode.com/problems/maximum-performance-of-a-team/)
- [IPO](https://leetcode.com/problems/ipo/)

### 16.7 Quick reference

| Problem family                 | Sort by                           | Then…                          |
|-------------------------------|-----------------------------------|--------------------------------|
| Max non-overlap intervals      | End time                          | Pick if `start ≥ last_end`     |
| Merge intervals                | Start time                        | Extend if `start ≤ last_end`   |
| 1D Sweep Line (Concurrency)    | Event coordinate (time/x)         | Accumulate `+delta`/`-delta`   |
| 2D Sweep Line (Skyline/Union)  | X-coordinate                      | Track active state in Heap/BST |
| Jump game (reach end)          | —                                 | Track `max reach` while iterating |
| Two-pointer pairing            | Sort, scan from both ends         | Pair greedily                  |
| Stream re-prioritization       | Heap                              | Pop, decide, push back         |
| Huffman                        | Heap of frequencies               | Combine two smallest           |

---

## 17. Dynamic programming

Dynamic programming is a method for solving complex problems by breaking them down into simpler subproblems. It is applicable when the problem has **overlapping subproblems** and **optimal substructure** properties. The main idea is to store the results of subproblems to avoid redundant work, which can significantly reduce the time complexity of algorithms.

### 17.1 Top-down (Memoization)

In the top-down approach, we start with the original problem and recursively break it down into smaller subproblems. We use a cache (often a dictionary) to store the results of these subproblems. If we encounter the same subproblem again, we can simply return the cached result instead of recomputing it.

### 17.2 Bottom-up (Tabulation)

In the bottom-up approach, we start with the smallest subproblems and iteratively build up the solution to the original problem. We typically use a table (like an array) to store the results of subproblems. This approach often involves filling out a table based on the relationships between subproblems, and it can be more space-efficient than the top-down approach.

### 17.3 DP vs Greedy: When to Use Which?

Before diving into patterns, let's understand the fundamental difference between DP and Greedy.

**Problem:** You're a robber with two streets to rob. Each house has some money. You can rob houses from both streets, but you cannot rob two adjacent houses from the same street (alarms will trigger). What's the maximum money you can steal?

```
Street A: [2, 7, 9, 3, 1]
Street B: [3, 2, 6, 8, 2]
```

**Greedy Robber:** "I'll always rob the house with most money available!"

- Rob B[0] = 3 (greedy)
- Rob A[1] = 7
- Rob B[2] = 6
- Rob A[3] = 3
- Rob B[4] = 2
- **Total: 21**

**DP Robber:** "Let me consider all valid combinations!"

- Option 1: A[0]=2, B[1]=2, A[2]=9, B[3]=8, A[4]=1 → Total = **22**
- Option 2: B[0]=3, A[1]=7, B[2]=6, A[3]=3, B[4]=2 → Total = 21
- **Best: 22**

**Why Greedy Failed:** By greedily picking B[0] first, it blocked access to the optimal combination that includes both 9 and 8 from different streets.

- **When Greedy Works:** Problems with the "greedy choice property" where local optimal leads to global optimal (e.g., Activity Selection, Huffman Coding).
- **When DP is Needed:** Problems requiring exploring multiple choices where greedy fails (e.g., most optimization problems, counting problems).

### 17.4 Pattern 1: Linear DP (1D)

**Why this pattern matters:** Foundation of DP. Teaches the core idea — break problem into subproblems, build the answer incrementally.

#### Example: House Robber

`nums[i]` = money in house `i`. Cannot rob two adjacent houses. Return max money.

```
nums = [2,7,9,3,1]  →  12   (rob 2 + 9 + 1)
```

#### Recurrence

At each house `i`, pick or skip:

```
f(i) = max(nums[i] + f(i+2),  f(i+1))
f(i) = 0  for i >= n
```

`f(2)` and `f(3)` recur in different branches → overlapping subproblems → memoize.

#### Recursion — O(2^n)

```python
def f(i):
    if i >= n: return 0
    return max(nums[i] + f(i+2), f(i+1))
```

#### Memoization — O(n) time, O(n) space

```python
memo = {}
def f(i):
    if i >= n: return 0
    if i in memo: return memo[i]
    memo[i] = max(nums[i] + f(i+2), f(i+1))
    return memo[i]
```

#### Tabulation — O(n) time, O(n) space

`dp[i]` = max money from index `i` onward. Fill right to left.

```python
dp = [0] * (n + 2)
for i in range(n - 1, -1, -1):
    dp[i] = max(nums[i] + dp[i+2], dp[i+1])
return dp[0]
```

#### Space-optimized — O(n) time, O(1) space

Only `dp[i+1]` and `dp[i+2]` are needed.

```python
next1 = next2 = 0
for i in range(n - 1, -1, -1):
    curr = max(nums[i] + next2, next1)
    next2, next1 = next1, curr
return next1
```

#### Pattern

Standard **Pick vs Not Pick** DP. State `f(i)`, choices = take/skip, recurrence `f(i) = max(nums[i] + f(i+2), f(i+1))`.

**Practice problems:**
- [House Robber](https://leetcode.com/problems/house-robber/) — pick vs skip; can't take two adjacent houses.
- [Word Break](https://leetcode.com/problems/word-break/) — `dp[i]` = can `s[:i]` be segmented into dictionary words.

### 17.5 Pattern 2: Longest Increasing Subsequence (LIS)

**Why this pattern matters:** Appears in version control diffs, patience sorting, box stacking. The `O(n log n)` binary-search variant is a must-know.

#### Example: Longest Increasing Subsequence

Given `nums`, return the length of the longest strictly increasing subsequence (not necessarily contiguous).

```
nums = [10, 9, 2, 5, 3, 7, 101, 18]  →  4   (2, 3, 7, 101)
```

#### Recurrence (pick vs not pick)

State `f(i, prev)` = LIS length starting at index `i`, where `prev` is the index of the last picked element (`-1` if none):

```
f(i, prev) = max(
    1 + f(i+1, i)        if prev == -1 or nums[i] > nums[prev],   # pick
    f(i+1, prev)                                                  # skip
)
f(i, prev) = 0  for i == n
```

#### Recursion — O(2^n)

```python
def f(i, prev):
    if i == n: return 0
    skip = f(i+1, prev)
    take = 0
    if prev == -1 or nums[i] > nums[prev]:
        take = 1 + f(i+1, i)
    return max(take, skip)
```

#### Memoization — O(n²) time, O(n²) space

Shift `prev` by `+1` so it indexes into a 2D table.

```python
memo = {}
def f(i, prev):
    if i == n: return 0
    if (i, prev) in memo: return memo[(i, prev)]
    skip = f(i+1, prev)
    take = 0
    if prev == -1 or nums[i] > nums[prev]:
        take = 1 + f(i+1, i)
    memo[(i, prev)] = max(take, skip)
    return memo[(i, prev)]
```

#### Tabulation — O(n²) time, O(n) space

`dp[i]` = LIS length **ending at** index `i`. Every element alone is length 1.

```python
dp = [1] * n
for i in range(n):
    for j in range(i):
        if nums[j] < nums[i]:
            dp[i] = max(dp[i], dp[j] + 1)
return max(dp)
```

#### Binary search — O(n log n) time, O(n) space

Maintain `tails`, where `tails[k]` = smallest possible tail of an increasing subsequence of length `k+1`. For each `x`, find the leftmost index in `tails` with value `>= x` and overwrite it; append if no such index. Final length of `tails` is the answer.

> `tails` is **not** an actual LIS — just a tracker of best tails.

```python
def lower_bound(arr, target):
    # leftmost index i such that arr[i] >= target, else len(arr)
    lo, hi = 0, len(arr)
    while lo < hi:
        mid = (lo + hi) // 2
        if arr[mid] < target:
            lo = mid + 1
        else:
            hi = mid
    return lo

tails = []
for x in nums:
    i = lower_bound(tails, x)
    if i == len(tails):
        tails.append(x)
    else:
        tails[i] = x
return len(tails)
```

#### Pattern

State `f(i, prev)`, choices = take/skip with order constraint `nums[i] > nums[prev]`. The `prev`-index trick generalizes to any "subsequence with a monotonic constraint" problem.

**Practice problems:**

- [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/) — the template; O(n log n) via patience sorting.
- [Russian Doll Envelopes](https://leetcode.com/problems/russian-doll-envelopes/) — sort by width, run LIS on height (2D LIS).

### 17.6 Pattern 3: Knapsack (0/1, Unbounded, Bounded)

**Why this pattern matters:** Versatile pattern for resource allocation and subset selection. Two flavors: each item used **once** (0/1) vs. **unlimited** times (unbounded). The only structural difference is one index in the recurrence.

#### Example: 0/1 Knapsack

`weights[i]`, `values[i]` for `n` items, capacity `W`. Pick a subset (each item at most once) maximizing total value with total weight `<= W`.

```
weights = [1, 3, 4, 5]
values  = [1, 4, 5, 7]
W = 7
        →  9   (items 1 + 2: weight 3+4, value 4+5)
```

#### Recurrence — 0/1

State `f(i, w)` = max value using items `i..n-1` with remaining capacity `w`:

```
f(i, w) = max(
    f(i+1, w),                                       # skip
    values[i] + f(i+1, w - weights[i])  if w >= weights[i]    # take, move to i+1
)
f(n, w) = 0
```

Key bit: after taking item `i`, we move to `i+1` — it cannot be reused.

#### Recursion — O(2^n)

```python
def f(i, w):
    if i == n: return 0
    skip = f(i+1, w)
    take = 0
    if w >= weights[i]:
        take = values[i] + f(i+1, w - weights[i])
    return max(take, skip)
```

#### Memoization — O(n·W) time and space

```python
memo = {}
def f(i, w):
    if i == n: return 0
    if (i, w) in memo: return memo[(i, w)]
    skip = f(i+1, w)
    take = 0
    if w >= weights[i]:
        take = values[i] + f(i+1, w - weights[i])
    memo[(i, w)] = max(take, skip)
    return memo[(i, w)]
```

#### Tabulation — O(n·W) time, O(n·W) space

`dp[i][w]` = max value using items `i..n-1` with capacity `w`. Fill bottom-up from `i = n` down.

```python
dp = [[0] * (W + 1) for _ in range(n + 1)]
for i in range(n - 1, -1, -1):
    for w in range(W + 1):
        skip = dp[i+1][w]
        take = 0
        if w >= weights[i]:
            take = values[i] + dp[i+1][w - weights[i]]
        dp[i][w] = max(take, skip)
return dp[0][W]
```

#### Space-optimized — O(n·W) time, O(W) space

Each row depends only on the row below. Use a 1D `dp[w]` and iterate `w` from **high to low** so each item is used at most once.

```python
dp = [0] * (W + 1)
for i in range(n):
    for w in range(W, weights[i] - 1, -1):     # reverse — prevents reuse
        dp[w] = max(dp[w], values[i] + dp[w - weights[i]])
return dp[W]
```

#### Extension: Unbounded Knapsack

Same problem, but each item can be picked **unlimited** times.

Only change in the recurrence: when we take item `i`, we stay at `i` instead of moving to `i+1`.

```
f(i, w) = max(
    f(i+1, w),                                       # skip
    values[i] + f(i, w - weights[i])    if w >= weights[i]    # take, stay at i
)
```

Space-optimized version: iterate `w` from **low to high** so the updated `dp[w - weights[i]]` (which already includes item `i`) is reused.

```python
dp = [0] * (W + 1)
for i in range(n):
    for w in range(weights[i], W + 1):         # forward — allows reuse
        dp[w] = max(dp[w], values[i] + dp[w - weights[i]])
return dp[W]
```

#### Pattern

| Variant   | Recurrence on take | 1D loop direction |
|-----------|--------------------|--------------------|
| 0/1       | `f(i+1, w - wt)`   | `w` high → low     |
| Unbounded | `f(i,   w - wt)`   | `w` low  → high    |

Same skeleton. Loop direction is the whole trick.

**0/1 Knapsack** (each item used once):

- [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/) — 0/1 subset-sum: can a subset hit total/2?

**Unbounded Knapsack** (items can be used multiple times):

- [Coin Change](https://leetcode.com/problems/coin-change/) — min coins to make an amount; each coin reusable (forward loop).

### 17.7 Pattern 4: String DP

**Why this pattern matters:** Two-string problems (diff, edit distance, alignment) all reduce to a 2D DP on indices `(i, j)`. LCS is the template — most others are 1–2 line tweaks of it.

#### Example: Longest Common Subsequence

Given strings `s1`, `s2`, return the length of the longest subsequence present in both (not necessarily contiguous).

```
s1 = "abcde"
s2 = "ace"
                →  3   ("ace")
```

#### Recurrence

State `f(i, j)` = LCS length of the prefixes `s1[:i]` and `s2[:j]` (first `i` chars of `s1`, first `j` chars of `s2`):

```
f(i, j) = 1 + f(i-1, j-1)              if s1[i-1] == s2[j-1]
        = max(f(i-1, j), f(i, j-1))    otherwise
f(i, j) = 0  if i == 0 or j == 0
```

Match → consume both. Mismatch → drop one character from either side, take the best. The answer is `f(n, m)`.

#### Recursion — O(2^(n+m))

```python
def f(i, j):
    if i == 0 or j == 0: return 0
    if s1[i-1] == s2[j-1]:
        return 1 + f(i-1, j-1)
    return max(f(i-1, j), f(i, j-1))
```

#### Memoization — O(n·m) time and space

```python
memo = {}
def f(i, j):
    if i == 0 or j == 0: return 0
    if (i, j) in memo: return memo[(i, j)]
    if s1[i-1] == s2[j-1]:
        memo[(i, j)] = 1 + f(i-1, j-1)
    else:
        memo[(i, j)] = max(f(i-1, j), f(i, j-1))
    return memo[(i, j)]
```

#### Tabulation — O(n·m) time, O(n·m) space

`dp[i][j]` = LCS of the prefixes `s1[:i]` and `s2[:j]`. Fill from `i = 1, j = 1` upward; row/col `0` are the empty-prefix base cases.

```python
    n, m = len(text1), len(text2)
    dp = [[0]*(m+1) for _ in range(n+1)]
    
    for i in range(1, n+1):
        for j in range(1, m+1):
            if text1[i-1] == text2[j-1]:
                dp[i][j] = 1 + dp[i-1][j-1]
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
        
    return dp[n][m]
```

#### Space-optimized — O(n·m) time, O(m) space

Each row depends only on the row below (`dp[i+1]`) plus one already-computed cell to the right in the current row. Keep two rows.

```python
    n, m = len(text1), len(text2)
    prev = [0]*(m+1)
    
    for i in range(1, n+1):
        curr = [0]*(m+1)
        for j in range(1, m+1):
            if text1[i-1] == text2[j-1]:
                curr[j] = 1 + prev[j-1]
            else:
                curr[j] = max(prev[j], curr[j-1])
        prev = curr

    return prev[m]
```

#### Reconstructing the LCS

Walk the `dp` table backward from `(n, m)`: on match, take the char and step to `(i-1, j-1)`; otherwise move toward the larger of `dp[i-1][j]` / `dp[i][j-1]`. Reverse at the end since we collect from the back.

```python
i, j, out = n, m, []
while i > 0 and j > 0:
    if s1[i-1] == s2[j-1]:
        out.append(s1[i-1]); i -= 1; j -= 1
    elif dp[i-1][j] >= dp[i][j-1]:
        i -= 1
    else:
        j -= 1
return "".join(reversed(out))
```

#### Pattern

State `f(i, j)` on two index pointers, branch on `s1[i] == s2[j]`. Most string DP problems (Edit Distance, SCS, Delete Operation, Palindromic Subsequence as `LCS(s, reversed(s))`) are LCS with the recurrence's recombination step swapped.

**Practice problems:**

- [Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/) — the 2D `(i, j)` template every other string-DP tweaks.
- [Edit Distance](https://leetcode.com/problems/edit-distance/) — LCS-shaped with insert / delete / replace transitions.

### 17.8 Pattern 5: State Machine DP

**Why this pattern matters:** Carry an extra state (holding / not holding, cooldown, k transactions left, etc.) alongside the index. Stock problems are the canonical family — once you nail the state diagram, the recurrence writes itself.

#### Example: Best Time to Buy and Sell Stock II

`prices[i]` = price on day `i`. Unlimited transactions allowed, but cannot hold more than one share at a time. Return max profit.

```
prices = [7, 1, 5, 3, 6, 4]  →  7   (buy 1, sell 5) + (buy 3, sell 6)
```

#### State diagram

Two states per day: `0` = not holding, `1` = holding.

```
        buy (-price)
   0 ─────────────────▶ 1
   ▲                    │
   │   sell (+price)    │
   └────────────────────┘

   self-loops on both: do nothing
```

#### Recurrence

State `f(i, holding)` = max profit from day `i` onward:

```
f(i, 0) = max(
    f(i+1, 0),                   # rest
    -prices[i] + f(i+1, 1)       # buy
)
f(i, 1) = max(
    f(i+1, 1),                   # hold
    prices[i] + f(i+1, 0)        # sell
)
f(n, *) = 0
```

#### Recursion — O(2^n)

```python
def f(i, holding):
    if i == n: return 0
    if holding:
        return max(f(i+1, 1), prices[i] + f(i+1, 0))
    return max(f(i+1, 0), -prices[i] + f(i+1, 1))
```

#### Memoization — O(n) time, O(n) space

```python
memo = {}
def f(i, holding):
    if i == n: return 0
    if (i, holding) in memo: return memo[(i, holding)]
    if holding:
        memo[(i, holding)] = max(f(i+1, 1), prices[i] + f(i+1, 0))
    else:
        memo[(i, holding)] = max(f(i+1, 0), -prices[i] + f(i+1, 1))
    return memo[(i, holding)]
```

#### Tabulation — O(n) time, O(n) space

`dp[i][s]` = max profit from day `i` onward in state `s`.

```python
dp = [[0, 0] for _ in range(n + 1)]
for i in range(n - 1, -1, -1):
    dp[i][0] = max(dp[i+1][0], -prices[i] + dp[i+1][1])
    dp[i][1] = max(dp[i+1][1],  prices[i] + dp[i+1][0])
return dp[0][0]
```

#### Space-optimized — O(n) time, O(1) space

Each day only needs the next day's two values.

```python
not_hold, hold = 0, 0
for i in range(n - 1, -1, -1):
    new_not_hold = max(not_hold, -prices[i] + hold)
    new_hold     = max(hold,      prices[i] + not_hold)
    not_hold, hold = new_not_hold, new_hold
return not_hold
```

#### Extension: extra states

The pattern scales by adding dimensions to the state:

| Variant            | Extra state              | Recurrence tweak |
|--------------------|--------------------------|------------------|
| At most `k` txns   | `k` transactions left    | decrement `k` on buy (or sell) |
| Cooldown 1 day     | after sell → skip 1 day  | sell transitions to `f(i+2, 0)` |
| Transaction fee    | constant `fee`           | subtract `fee` on sell |

#### Pattern

State = `(index, current_state)`. Transitions are the state-machine edges; recurrence picks the max over all outgoing edges from `(i, s)`. Add a dimension for every independent quantity you must track (`k`, cooldown flag, fee already paid, etc.).

**Practice problems:**

- [Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) — two states: holding / not holding.
- [Best Time to Buy and Sell Stock IV](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/) — add a "transactions left" (k) dimension to the state.

### 17.9 Pattern 6: Interval DP

**The idea:** the subproblem is a contiguous range `[i, j]`. To solve it you choose a **partition point** `k` inside the range, solve the two pieces independently, and combine them. Because every range depends only on **shorter** ranges, you solve small intervals first and build up.

```
solve(i, j):
    if base case: return ...
    best = optimum over all k in [i, j) of:
              solve(i, k) + solve(k+1, j) + cost_of_combining
    return best
```

Two things change from problem to problem: **what the partition `k` means** (a split position, the last element removed, the root of a tree...) and **the combine cost**. Everything else is boilerplate.

> **Evaluation order (tabulation):** fill by increasing interval length, so all shorter intervals are ready when you need them.

---

#### Worked example: Matrix Chain Multiplication

**Problem:** You have `n` matrices to multiply in order. Matrix `i` has shape `dims[i] x dims[i+1]`. Matrix multiplication is associative — the *result* is the same regardless of how you parenthesise — but the *cost* differs. Find the cheapest parenthesisation.

Multiplying A(10×30), B(30×5), C(5×60):
- `(AB)C` → 10·30·5 + 10·5·60 = **4500**
- `A(BC)` → 30·5·60 + 10·30·60 = **27000**

Here `k` = where the chain splits. `solve(i, j)` = min cost to multiply matrices `i..j`. Splitting at `k` produces one matrix of shape `dims[i] x dims[k+1]` and another of `dims[k+1] x dims[j+1]`; combining them costs `dims[i] * dims[k+1] * dims[j+1]`.

#### Recursion

```python
def f(i, j):
    if i == j:                       # single matrix, nothing to multiply
        return 0
    best = float('inf')
    for k in range(i, j):            # split between k and k+1
        cost = f(i, k) + f(k+1, j) + dims[i] * dims[k+1] * dims[j+1]
        best = min(best, cost)
    return best
```

#### Memoization — O(n³) time, O(n²) space

```python
memo = {}
def f(i, j):
    if i == j:
        return 0
    if (i, j) in memo:
        return memo[(i, j)]
    best = float('inf')
    for k in range(i, j):
        cost = f(i, k) + f(k+1, j) + dims[i] * dims[k+1] * dims[j+1]
        best = min(best, cost)
    memo[(i, j)] = best
    return best
```

#### Tabulation — O(n³) time, O(n²) space

```python
dp = [[0] * n for _ in range(n)]
for length in range(2, n + 1):           # interval length
    for i in range(n - length + 1):
        j = i + length - 1
        dp[i][j] = float('inf')
        for k in range(i, j):
            cost = dp[i][k] + dp[k+1][j] + dims[i] * dims[k+1] * dims[j+1]
            dp[i][j] = min(dp[i][j], cost)
return dp[0][n - 1]
```

---

#### Variant: the "last operation" trick (Burst Balloons)

Sometimes splitting *forward* breaks down because each choice reshapes the problem — bursting a balloon changes its neighbours. The fix: let `k` be the **last** thing done in `[i, j]`. At that moment `i` and `j` are guaranteed to be its neighbours (everything between is gone), so the two sides become independent.

```python
# pad with 1s; dp[i][j] = max coins from the open interval (i, j)
nums = [1] + nums + [1]
n = len(nums)
dp = [[0] * n for _ in range(n)]
for length in range(2, n):
    for i in range(n - length):
        j = i + length
        for k in range(i + 1, j):        # k = last balloon burst in (i, j)
            dp[i][j] = max(dp[i][j],
                           dp[i][k] + nums[i] * nums[k] * nums[j] + dp[k][j])
return dp[0][n - 1]
```

---

#### Variant: Palindrome Partitioning II (min cuts)

**Problem:** Partition string `s` so every part is a palindrome, using the **fewest cuts**.

Here the "interval" thinking is on prefixes: `cuts[i]` = min cuts for `s[:i]`. Precompute `isPal[a][b]` (itself an interval DP), then for each end `i` try every start `j` of the last palindrome piece.

```python
n = len(s)
isPal = [[False] * n for _ in range(n)]
for i in range(n - 1, -1, -1):
    for j in range(i, n):
        if s[i] == s[j] and (j - i < 2 or isPal[i+1][j-1]):
            isPal[i][j] = True

cuts = [0] * (n + 1)
for i in range(1, n + 1):
    cuts[i] = float('inf')
    for j in range(i):
        if isPal[j][i-1]:                 # s[j:i] is a palindrome
            cuts[i] = min(cuts[i], (0 if j == 0 else cuts[j] + 1))
return cuts[n]
```

---

#### Variant: Egg Drop

**Problem:** `k` eggs, `n` floors. Find the highest safe floor with the **minimum number of trials in the worst case**. Dropping an egg from a floor either breaks it (search below with one fewer egg) or not (search above with same eggs).

`dp[e][f]` = min worst-case trials with `e` eggs and `f` floors. For each floor `x` you drop from, the worst case is `1 + max(broken, survived)`; minimise over `x`. (The naive split is O(k·n²); a binary-search-on-`x` or the "moves" reformulation makes it faster.)

```python
def f(eggs, floors):
    if floors == 0 or eggs == 1:
        return floors
    best = float('inf')
    for x in range(1, floors + 1):
        broke   = f(eggs - 1, x - 1)      # egg breaks → below
        survive = f(eggs, floors - x)     # egg survives → above
        best = min(best, 1 + max(broke, survive))
    return best
```

---

#### Recognition checklist

- Subproblem is a **contiguous range** and depends only on strictly smaller ranges
- You choose a **partition / split / last-element** inside the range and combine the pieces
- Problem involves **merging, removing, cutting, or building** something over a range

#### Complexity

Standard interval DP is **O(n³) time, O(n²) space** — two loops for the interval `(i, j)`, one for the split `k`.

#### Practice problems

- [Burst Balloons](https://leetcode.com/problems/burst-balloons/) — think about which balloon is burst *last* in a range.
- [Palindrome Partitioning II](https://leetcode.com/problems/palindrome-partitioning-ii/) — min cuts; precompute palindrome table, then interval DP.

### 17.10 Pattern 7: Game Theory DP (Minimax)

**The idea:** two players alternate turns and **both play optimally**. The trick is to track the score from the **current player's** perspective at every state. Whatever the opponent gains on their turn is your loss, so you flip the sign as you recurse.

`dp[i][j]` = best **score advantage** (my total − opponent's total) the player to move can guarantee on the interval `[i, j]`.

#### Worked example: Predict the Winner

**Problem:** Players take turns taking a number from **either end** of `nums`. Both play to maximise their own total. Can player 1 win (or tie)?

If I take `nums[i]`, the opponent then faces `[i+1, j]` and plays optimally — their guaranteed advantage there becomes *my* deficit. So I subtract the subproblem's result:

```
dp[i][j] = max(nums[i] - dp[i+1][j],   # take left end
               nums[j] - dp[i][j-1])   # take right end
```

#### Recursion

```python
def f(i, j):
    if i == j:
        return nums[i]
    return max(nums[i] - f(i + 1, j),
               nums[j] - f(i, j - 1))

return f(0, n - 1) >= 0                  # >= 0 means player 1 wins / ties
```

#### Memoization — O(n²) time, O(n²) space

```python
memo = {}
def f(i, j):
    if i == j:
        return nums[i]
    if (i, j) in memo:
        return memo[(i, j)]
    memo[(i, j)] = max(nums[i] - f(i + 1, j),
                       nums[j] - f(i, j - 1))
    return memo[(i, j)]

return f(0, n - 1) >= 0
```

#### Tabulation — O(n²) time, O(n²) space

```python
dp = [[0] * n for _ in range(n)]
for i in range(n):
    dp[i][i] = nums[i]
for length in range(2, n + 1):
    for i in range(n - length + 1):
        j = i + length - 1
        dp[i][j] = max(nums[i] - dp[i+1][j],
                       nums[j] - dp[i][j-1])
return dp[0][n - 1] >= 0
```

#### Practice problems

- [Predict the Winner](https://leetcode.com/problems/predict-the-winner/) — `dp[i][j]` = best score difference the current player can force.
- [Stone Game](https://leetcode.com/problems/stone-game/) — same score-difference minimax, picking from either end.

### 17.11 Pattern 8: Grid DP

**Why this pattern matters:** 2D state space `(r, c)` with moves in fixed directions. Common in pathfinding, counting paths, min/max cost grids. Once you have the recurrence, the table fill is mechanical.

#### Example: Minimum Path Sum

Grid of non-negative ints. Start at top-left, end at bottom-right. Each step goes **right** or **down**. Return the minimum path sum.

```
grid = [[1, 3, 1],
        [1, 5, 1],
        [4, 2, 1]]
                    →  7   (1 → 3 → 1 → 1 → 1)
```

#### Recurrence

State `f(r, c)` = min path sum from `(r, c)` to `(n-1, m-1)`:

```
f(r, c) = grid[r][c] + min(f(r+1, c), f(r, c+1))
f(n-1, m-1) = grid[n-1][m-1]
f(r, c) = +∞   if r == n or c == m   (out of bounds)
```

#### Recursion — O(2^(n+m))

```python
def f(r, c):
    if r == n or c == m: return float('inf')
    if r == n - 1 and c == m - 1: return grid[r][c]
    return grid[r][c] + min(f(r+1, c), f(r, c+1))
```

#### Memoization — O(n·m) time and space

```python
memo = {}
class Solution:
    def minPathSum(self, grid: List[List[int]]) -> int:
        def dfs(i, j):
            if  0 <= i < n and 0 <= j < m:
                if i == n-1 and j == m-1:
                    return grid[i][j]
                
                if (i, j) in memo:
                    return memo[(i, j)]
            
                memo[(i, j)] = grid[i][j] + min(dfs(i+1, j), dfs(i, j+1))
                return memo[(i, j)]
            
            return float(inf)

        memo = {}
        n, m = len(grid), len(grid[0])
        return dfs(0, 0)
```

#### Tabulation — O(n·m) time, O(n·m) space

`dp[r][c]` = min path sum from `(r, c)` onward. Fill bottom-right to top-left.

```python
    n, m = len(grid), len(grid[0])
    dp = [[float(inf)] * (m+1) for _ in range(n+1)]
    dp[n-1][m-1] = grid[n - 1][m - 1]

    for r in range(n - 1, -1, -1):
        for c in range(m - 1, -1, -1):
            if r == n - 1 and c == m - 1:
                continue

            dp[r][c] = grid[r][c] + min(dp[r + 1][c], dp[r][c + 1])

    return dp[0][0]
```

#### Space-optimized — O(n·m) time, O(m) space

Each row only needs the row below it. Keep a single 1D array and update in-place right-to-left.

```python
dp = [float('inf')] * (m + 1)
dp[m - 1] = 0  # sentinel: stepping "out of bounds right" = inf, handled by init

for r in range(n - 1, -1, -1):
    new_dp = [float('inf')] * (m + 1)
    for c in range(m - 1, -1, -1):
        down  = dp[c]           # row below, same column
        right = new_dp[c + 1]   # same row, column to the right
        new_dp[c] = grid[r][c] + min(down, right)
    dp = new_dp

return dp[0]
```

#### Variant: Unique Paths (counting)

Same grid skeleton, but count paths instead of summing costs. Recurrence becomes addition, base is `1`:

```
f(r, c) = f(r+1, c) + f(r, c+1)
f(n-1, m-1) = 1
```

```python
dp = [[1] * m for _ in range(n)]
for r in range(n - 2, -1, -1):
    for c in range(m - 2, -1, -1):
        dp[r][c] = dp[r+1][c] + dp[r][c+1]
return dp[0][0]
```

#### Pattern

State `(r, c)` + fixed move set (right/down, or 4-dir, etc.). Recurrence aggregates over allowed moves (`min` / `max` / sum / count). 1D space-opt works when transitions only touch the next row + same row.

**Practice problems:**

- [Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/) — the template: `dp[r][c] = grid + min(up, left)`.
- [Maximal Square](https://leetcode.com/problems/maximal-square/) — `dp[r][c] = 1 + min` of three neighbors (square side length).

### 17.12 Pattern 9: Tree DP

**Why this pattern matters:** The tree structure enforces a natural subproblem order — children before parents (post-order). Every node becomes a DP state, and you propagate answers bottom-up. The key skill is deciding **what to return from each node** so the parent has exactly what it needs.

---

#### Core idea

```
dfs(node) -> (some state tuple)
    if node is None: return base case
    left  = dfs(node.left)
    right = dfs(node.right)
    # combine left, right, node.val → answer for this subtree
    return result
```

The global answer is often updated **inside** dfs, not just at the root, because the optimal solution might be rooted at any node.

---

#### Sub-pattern A: Single value return (path/diameter problems)

The function returns one number — the best value achievable going **downward** from this node. The answer that **passes through** this node is computed locally and updates a global variable.

**Template:**
```python
ans = float('-inf')

def dfs(node):
    if not node:
        return 0
    left  = max(dfs(node.left),  0)  # discard negative branches
    right = max(dfs(node.right), 0)
    ans = max(ans, left + right + node.val)  # path through this node
    return max(left, right) + node.val       # best single arm upward

dfs(root)
```

**Problems:**
- [Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/)
- [Longest ZigZag Path in a Binary Tree](https://leetcode.com/problems/longest-zigzag-path-in-a-binary-tree/)
- [Difference Between Maximum and Minimum Price Sum](https://leetcode.com/problems/difference-between-maximum-and-minimum-price-sum/)

---

#### Sub-pattern B: State tuple return (choose/reject at each node)

Each node has discrete states (e.g., rob or skip, camera or no camera). Return a value per state; parent picks the best combination.

**Template (House Robber III):**
```python
def dfs(node):
    if not node:
        return (0, 0)  # (robbed, skipped)
    l_rob, l_skip = dfs(node.left)
    r_rob, r_skip = dfs(node.right)

    robbed  = node.val + l_skip + r_skip   # rob this → children must be skipped
    skipped = max(l_rob, l_skip) + max(r_rob, r_skip)  # skip this → children free
    return (robbed, skipped)

l_rob, l_skip = dfs(root)
return max(l_rob, l_skip)
```

**Problems:**
- [House Robber III](https://leetcode.com/problems/house-robber-iii/)

- [Binary Tree Cameras](https://leetcode.com/problems/binary-tree-cameras/)

- [Maximum Sum BST in Binary Tree](https://leetcode.com/problems/maximum-sum-bst-in-binary-tree/)


---

#### Sub-pattern C: Rerooting (answer changes with root choice)

Two-pass technique. First DFS computes subtree answers (bottom-up). Second DFS propagates answers **down** so every node knows the answer if it were the root. Used when the problem asks for something like "sum of distances from every node to all others."

**Template:**
```python
# Pass 1: compute subtree sizes / subtree-sums rooted at node 0
def dfs1(u, parent):
    for v in graph[u]:
        if v != parent:
            dfs1(v, u)
            subtree_size[u] += subtree_size[v]
            dp[u] += dp[v] + subtree_size[v]  # e.g. sum of distances

# Pass 2: reroot — push parent's contribution down to child
def dfs2(u, parent):
    for v in graph[u]:
        if v != parent:
            # moving root from u to v: nodes outside v's subtree get 1 farther
            dp[v] = dp[u] - subtree_size[v] + (n - subtree_size[v])
            dfs2(v, u)
```

**Problems:**
- [Sum of Distances in Tree](https://leetcode.com/problems/sum-of-distances-in-tree/)

---

#### Sub-pattern D: DP on tree paths / knapsack on tree

Merge children's DP tables at each node. Often O(n²) but tight — each pair of nodes is merged exactly once.

**Problems:**
- [Distribute Coins in Binary Tree](https://leetcode.com/problems/distribute-coins-in-binary-tree/)
- [Maximum Product of Splitted Binary Tree](https://leetcode.com/problems/maximum-product-of-splitted-binary-tree/)


---

#### What to return checklist

| What the parent needs | Return from dfs |
|---|---|
| Best value going down | single int (height / gain) |
| Multiple exclusive states | tuple of ints |
| Subtree aggregate (sum, size, min, max) | tuple with all aggregates |
| Whether subtree satisfies a property | bool + relevant value |

---

#### Common pitfalls

- **Forgetting to clamp negatives** — if a subtree has negative contribution, return 0 (discard it), not the raw value
- **Updating global answer at wrong place** — update it inside dfs at each node, not just at the root
- **Wrong base case for None** — match the return type: if you return a tuple, None should return a tuple of neutral values

### 17.13 Bitmask & Probability DP

#### Bitmask DP

Use when n ≤ 20 and you need to track which elements have been used. A bitmask integer represents the subset.

```python
dp = [INF] * (1 << n)
dp[0] = 0
for mask in range(1 << n):
    for i in range(n):
        if mask & (1 << i):
            prev = mask ^ (1 << i)
            dp[mask] = min(dp[mask], dp[prev] + cost(prev, i))
```

Bit tricks: `mask & (1<<i)` check, `mask | (1<<i)` add, `mask ^ (1<<i)` remove, `(1<<n)-1` full set.

- [Partition to K Equal Sum Subsets](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/) — mask = which elements are used so far.
- [Shortest Path Visiting All Nodes](https://leetcode.com/problems/shortest-path-visiting-all-nodes/) — TSP-style state `(mask, node)`.

---

#### Probability DP

`dp[state]` = probability of reaching that state. Transition multiplies by probability of each choice. Watch for floating point errors on large inputs.

```python
dp[start] = 1.0
for state in all_states:
    for each outcome with probability p:
        dp[next_state] += dp[state] * p
```

- [Knight Probability in Chessboard](https://leetcode.com/problems/knight-probability-in-chessboard/) — `dp[step][r][c]` = prob of still being on the board.
- [New 21 Game](https://leetcode.com/problems/new-21-game/) — probability DP with a sliding-window sum.

### 17.14 Must-Do / FAANG DP Questions

Problems **not** already used as examples above — a flat practice bank. Figuring out which pattern each needs is part of the drill.

- [Fibonacci Number](https://leetcode.com/problems/fibonacci-number/)
- [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)
- [Min Cost Climbing Stairs](https://leetcode.com/problems/min-cost-climbing-stairs/)
- [Decode Ways](https://leetcode.com/problems/decode-ways/)
- [House Robber II](https://leetcode.com/problems/house-robber-ii/)
- [Number of Longest Increasing Subsequence](https://leetcode.com/problems/number-of-longest-increasing-subsequence/)
- [Longest String Chain](https://leetcode.com/problems/longest-string-chain/)
- [Maximum Length of Pair Chain](https://leetcode.com/problems/maximum-length-of-pair-chain/)
- [Find the Longest Valid Obstacle Course at Each Position](https://leetcode.com/problems/find-the-longest-valid-obstacle-course-at-each-position/)
- [Arithmetic Slices II - Subsequence](https://leetcode.com/problems/arithmetic-slices-ii-subsequence/)
- [Target Sum](https://leetcode.com/problems/target-sum/)
- [Last Stone Weight II](https://leetcode.com/problems/last-stone-weight-ii/)
- [Ones and Zeroes](https://leetcode.com/problems/ones-and-zeroes/)
- [Partition Array Into Two Arrays to Minimize Sum Difference](https://leetcode.com/problems/partition-array-into-two-arrays-to-minimize-sum-difference/)
- [Coin Change 2](https://leetcode.com/problems/coin-change-2/)
- [Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/)
- [Perfect Squares](https://leetcode.com/problems/perfect-squares/)
- [Minimum Cost For Tickets](https://leetcode.com/problems/minimum-cost-for-tickets/)
- [Delete Operation for Two Strings](https://leetcode.com/problems/delete-operation-for-two-strings/)
- [Minimum ASCII Delete Sum for Two Strings](https://leetcode.com/problems/minimum-ascii-delete-sum-for-two-strings/)
- [Shortest Common Supersequence](https://leetcode.com/problems/shortest-common-supersequence/)
- [Longest Palindromic Subsequence](https://leetcode.com/problems/longest-palindromic-subsequence/)
- [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)
- [Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/)
- [Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching/)
- [Wildcard Matching](https://leetcode.com/problems/wildcard-matching/)
- [Distinct Subsequences](https://leetcode.com/problems/distinct-subsequences/)
- [Distinct Subsequences II](https://leetcode.com/problems/distinct-subsequences-ii/)
- [Number of Unique Good Subsequences](https://leetcode.com/problems/number-of-unique-good-subsequences/)
- [Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)
- [Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/)
- [Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)
- [Best Time to Buy and Sell Stock with Transaction Fee](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)
- [Maximum Profit from Trading Stocks](https://leetcode.com/problems/maximum-profit-from-trading-stocks/)
- [Cherry Pickup](https://leetcode.com/problems/cherry-pickup/)
- [Maximum Alternating Subsequence Sum](https://leetcode.com/problems/maximum-alternating-subsequence-sum/)
- [Minimum Swaps to Make Sequences Increasing](https://leetcode.com/problems/minimum-swaps-to-make-sequences-increasing/)
- [Flip String to Monotone Increasing](https://leetcode.com/problems/flip-string-to-monotone-increasing/)
- [Solving Questions With Brainpower](https://leetcode.com/problems/solving-questions-with-brainpower/)
- [Knight Dialer](https://leetcode.com/problems/knight-dialer/)
- [Student Attendance Record II](https://leetcode.com/problems/student-attendance-record-ii/)
- [Paint House](https://leetcode.com/problems/paint-house/)
- [Paint House II](https://leetcode.com/problems/paint-house-ii/)
- [Paint House III](https://leetcode.com/problems/paint-house-iii/)
- [Paint Fence](https://leetcode.com/problems/paint-fence/)
- [Decode Ways II](https://leetcode.com/problems/decode-ways-ii/)
- [Minimum Cost to Merge Stones](https://leetcode.com/problems/minimum-cost-to-merge-stones/)
- [Minimum Cost Tree From Leaf Values](https://leetcode.com/problems/minimum-cost-tree-from-leaf-values/)
- [Strange Printer](https://leetcode.com/problems/strange-printer/)
- [Minimum Score Triangulation of Polygon](https://leetcode.com/problems/minimum-score-triangulation-of-polygon/)
- [Remove Boxes](https://leetcode.com/problems/remove-boxes/)
- [Super Egg Drop](https://leetcode.com/problems/super-egg-drop/)
- [Unique Binary Search Trees](https://leetcode.com/problems/unique-binary-search-trees/)
- [Different Ways to Add Parentheses](https://leetcode.com/problems/different-ways-to-add-parentheses/)
- [Stone Game II](https://leetcode.com/problems/stone-game-ii/)
- [Stone Game III](https://leetcode.com/problems/stone-game-iii/)
- [Stone Game IV](https://leetcode.com/problems/stone-game-iv/)
- [Can I Win](https://leetcode.com/problems/can-i-win/)
- [Guess Number Higher or Lower II](https://leetcode.com/problems/guess-number-higher-or-lower-ii/)
- [Zuma Game](https://leetcode.com/problems/zuma-game/)
- [Unique Paths](https://leetcode.com/problems/unique-paths/)
- [Unique Paths II](https://leetcode.com/problems/unique-paths-ii/)
- [Maximal Rectangle](https://leetcode.com/problems/maximal-rectangle/)
- [Minimum Falling Path Sum](https://leetcode.com/problems/minimum-falling-path-sum/)
- [Count Square Submatrices with All Ones](https://leetcode.com/problems/count-square-submatrices-with-all-ones/)
- [Triangle](https://leetcode.com/problems/triangle/)
- [Find the Shortest Superstring](https://leetcode.com/problems/find-the-shortest-superstring/)
- [Smallest Sufficient Team](https://leetcode.com/problems/smallest-sufficient-team/)
- [Number of Ways to Wear Different Hats to Each Other](https://leetcode.com/problems/number-of-ways-to-wear-different-hats-to-each-other/)
- [Minimum Number of Work Sessions to Finish the Tasks](https://leetcode.com/problems/minimum-number-of-work-sessions-to-finish-the-tasks/)
- [Soup Servings](https://leetcode.com/problems/soup-servings/)
- [Toss Strange Coins](https://leetcode.com/problems/toss-strange-coins/)
- [Probability of a Two Boxes Having The Same Number of Distinct Balls](https://leetcode.com/problems/probability-of-a-two-boxes-having-the-same-number-of-distinct-balls/)
- [Maximum Profit in Job Scheduling](https://leetcode.com/problems/maximum-profit-in-job-scheduling/)
- [Constrained Subsequence Sum](https://leetcode.com/problems/constrained-subsequence-sum/)
- [Number of Dice Rolls With Target Sum](https://leetcode.com/problems/number-of-dice-rolls-with-target-sum/)
- [Tallest Billboard](https://leetcode.com/problems/tallest-billboard/)
- [Painting the Walls](https://leetcode.com/problems/painting-the-walls/)
- [Count Ways to Build Good Strings](https://leetcode.com/problems/count-ways-to-build-good-strings/)
- [Minimum Number of Removals to Make Mountain Array](https://leetcode.com/problems/minimum-number-of-removals-to-make-mountain-array/)
- [Longest Arithmetic Subsequence of Given Difference](https://leetcode.com/problems/longest-arithmetic-subsequence-of-given-difference/)

---

## 18. Space-efficient structures at scale

Data structures that trade exactness or strict ordering for space/time efficiency at scale — used as cheap first checks or approximations in front of expensive operations.

The same structures appear from the system-design side in the HLD guide's [special data structures chapter](../SystemDesign/hld-interview-guide.md#35-special-data-structures).

### 18.1 Bloom Filters

Bloom filters are compact filters that answer "definitely not present" or "probably present."

Used correctly, they have false positives but no false negatives for items that were added. Sizing matters: choose the expected item count and false-positive rate up front, then derive the bit-array size and number of hash functions.


#### How it works


- A bit array of size `m`, all initialized to 0, plus `k` independent hash functions.
- **Add**: hash the item with all `k` functions, set the resulting `k` bit positions to 1.
- **Check**: hash the item with all `k` functions — if *any* of those bits is 0, the item was definitely never added; if all are 1, it's probably present (could be a false positive from bit overlap with other items).

```
add("apple")  → hash1=2, hash2=5, hash3=9  → set bits 2,5,9
check("apple")→ bits 2,5,9 all 1           → "probably present" ✓
check("pear") → hash1=2, hash2=7, hash3=9  → bit 7 is 0          → "definitely not present"
```

![Bloom filter bit array — add and check](../static/images/bloom-filter-mechanics.svg)


#### Sizing


Pick `n` (expected item count) and `p` (acceptable false-positive rate) upfront, then derive:

```
m = -(n * ln(p)) / (ln 2)^2      # bits needed
k = (m / n) * ln(2)              # optimal number of hash functions
```

Undersizing `m` for the actual `n` inserted is the most common mistake — as the filter fills up, the false-positive rate climbs well past the target `p`.


#### Simple Implementation (Python)


Only needs a bit array and hash functions. Instead of running `k` independent hash functions, this uses **double hashing** (Kirsch-Mitzenmacher) — derive `k` positions from just two hashes (`h1`, `h2`) via `h1 + i*h2`, which is cheaper and works just as well in practice.

⚠️ **Never hash with a language's default/built-in hash function** (Python's `hash()`, etc.) if the filter is saved to disk, shared between services, or reused after a restart. `hash()` on `str`/`bytes` is randomized per-process (`PYTHONHASHSEED`) as a DoS mitigation — the same item hashes to different bit positions in a different process, so loading a persisted filter elsewhere silently corrupts lookups (including false negatives, which breaks the core guarantee). Use a fixed, deterministic hash — `hashlib` here, or MurmurHash/xxHash in production — so the same item always maps to the same bits everywhere.

```python
import math
import hashlib


class BloomFilter:
    def __init__(self, n: int, p: float):
        """n = expected item count, p = target false-positive rate."""
        self.m = self._bit_array_size(n, p)
        self.k = self._hash_count(self.m, n)
        self.bits = bytearray(math.ceil(self.m / 8))  # m bits, packed into bytes

    @staticmethod
    def _bit_array_size(n: int, p: float) -> int:
        return math.ceil(-(n * math.log(p)) / (math.log(2) ** 2))

    @staticmethod
    def _hash_count(m: int, n: int) -> int:
        return max(1, round((m / n) * math.log(2)))

    def _hashes(self, item: str) -> list[int]:
        """Derive k bit positions from two base hashes (double hashing)."""
        h1 = int(hashlib.md5(item.encode()).hexdigest(), 16)
        h2 = int(hashlib.sha1(item.encode()).hexdigest(), 16)
        return [(h1 + i * h2) % self.m for i in range(self.k)]

    def _set_bit(self, pos: int) -> None:
        self.bits[pos // 8] |= 1 << (pos % 8)

    def _get_bit(self, pos: int) -> bool:
        return bool(self.bits[pos // 8] & (1 << (pos % 8)))

    def add(self, item: str) -> None:
        for pos in self._hashes(item):
            self._set_bit(pos)

    def check(self, item: str) -> bool:
        """False -> definitely not present. True -> probably present."""
        return all(self._get_bit(pos) for pos in self._hashes(item))


# ── Usage ────────────────────────────────────────────────────
bf = BloomFilter(n=1000, p=0.01)

bf.add("apple")
bf.add("banana")

print(bf.check("apple"))   # True  — probably present
print(bf.check("pear"))    # False — definitely not present
print(bf.m, bf.k)          # e.g. 9586 bits, 7 hash functions
```

Not production-grade (`md5`/`sha1` are slower than purpose-built hashes like MurmurHash, and there's no serialization), but it shows the mechanics end to end. For real use, reach for a library — `pybloom-live` (Python), Guava's `BloomFilter` (Java), or RedisBloom (`BF.ADD`/`BF.EXISTS`).


#### Pros


- O(k) constant-time add/lookup, independent of item count.
- Very space-efficient — stores bits, not the actual keys.
- No false negatives — safe to skip an expensive lookup whenever the filter says "not present."


#### Cons


- False positives are inherent — must size for an acceptable rate.
- No deletion in the standard version — clearing a bit can silently break membership for other items that hash to it.
- Can't enumerate or retrieve items — membership test only.
- Not suitable where exact answers matter (legal, security, financial decisions).


#### Where it's used


- **Databases** — LSM-tree engines (RocksDB, Cassandra, HBase) keep a bloom filter per SSTable to skip disk reads for keys that don't exist.
- **Caches** — check the filter before hitting cache/DB to avoid cache penetration from repeated lookups of nonexistent keys.
- **Crawlers** — dedupe visited URLs without storing the full URL set.
- **Distributed systems** — reduce cross-node calls, e.g., a node checks its bloom filter before asking a peer "do you have this key?"


#### Real-world examples


| Application                       | How it uses a Bloom filter                                                                                                          |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| **Google Chrome** (Safe Browsing) | Downloads a local bloom filter of known-malicious URL hashes — checks it before every page load, only calls Google's API on a hit, so most safe browsing never leaves the device. |
| **Apache Cassandra**              | Each SSTable has a bloom filter of its row keys — skips a disk read entirely if the filter says the key isn't in that SSTable.       |
| **Apache HBase**                  | Same idea per `StoreFile` — avoids opening files that can't contain the requested row/column.                                       |
| **RocksDB / LevelDB**             | Per-SST-file filter block, checked before a disk seek — the backbone of the "skip the read" pattern used above by Cassandra/HBase.  |
| **Bitcoin (BIP 37, SPV wallets)** | Lightweight wallets send a bloom filter of their addresses to a full node, which replies only with matching transactions — filters out irrelevant blocks without the wallet revealing exact addresses. |
| **Ethereum**                      | Every block header carries a bloom filter over event-log topics — light clients check it to decide if a block is even worth downloading for logs matching a query. |
| **Akamai CDN**                    | Uses a bloom filter to detect "one-hit-wonders" (objects requested only once) so they're not cached, avoiding cache pollution.       |
| **Medium**                        | Used a bloom filter to avoid re-recommending articles a user has already read/seen, without storing full read-history per user.     |
| **Squid proxy**                   | Cache digests (bloom-filter-based summaries) let proxies ask peers "do you have this object?" without a full protocol round trip.    |
| **Redis (RedisBloom module)**     | Ships probabilistic data structures — `BF.ADD` / `BF.EXISTS` — as a first-class Redis command, for app-level use without hand-rolling one. |
| **PostgreSQL (`bloom` extension)**| A contrib index type using a bloom filter per row for fast equality checks across many columns at once, when no single column is selective enough for a B-tree. |


#### Scaling it


A single fixed-size filter degrades as item count grows past its sized `n` — fill ratio rises and so does the false-positive rate. Options:

- **Scalable Bloom Filter** — chain multiple filters; when one fills up, add a new one (typically with a tighter `p`), and check membership across the whole chain (OR across filters). Avoids a full rebuild.
- **Counting Bloom Filter** — replace each bit with a small counter (e.g., 4 bits) so deletion becomes decrementing instead of clearing a shared bit. Costs more memory for the ability to delete.
- **Shard by key** — in distributed systems, give each shard/node its own filter sized for its slice of the keyspace, rather than one giant shared filter.
- **Periodic rebuild** — once actual `n` outgrows the original estimate by a wide margin, recompute `m`/`k` and re-insert all known items rather than letting `p` silently drift.

![Bloom filter scaling — chained filters vs sharding by key](../static/images/bloom-filter-scaling.svg)

**Default advice:** size generously for expected growth up front — resizing/rebuilding a bloom filter isn't a cheap in-place operation like resizing a hash table, since bit positions depend on `m`.

### 18.2 GeoHash

Location systems keep needing to answer one question fast: *what's near this point?* Scanning every lat/lng row and computing distance one by one doesn't work once there are millions of restaurants, drivers, or devices.

Geohash turns a location into a short string, where nearby places usually share a prefix. That lets an ordinary ordered index (a B-tree) fetch a small set of candidates before doing any real distance math.

Geohash is not an exact distance calculator — it's a first-pass filter. Real systems still check neighboring cells, compute actual distance, and pick precision deliberately.


#### How it works


Three steps: repeatedly halve the longitude and latitude ranges, interleave the resulting bits (longitude first), then base32-encode every 5 bits.

**Splitting the range** — at each step, ask "is the target in the lower or upper half of the current range?" `0` for lower, `1` for upper, then recurse into that half.

Encoding `(19.0760, 72.8777)` — Mumbai:

| # | Longitude range | Mid | Value vs mid | Bit |
|---|---|---|---|---|
| 1 | [-180, 180] | 0 | ≥ | 1 |
| 2 | [0, 180] | 90 | < | 0 |
| 3 | [0, 90] | 45 | ≥ | 1 |
| 4 | [45, 90] | 67.5 | ≥ | 1 |
| 5 | [67.5, 90] | 78.75 | < | 0 |

| # | Latitude range | Mid | Value vs mid | Bit |
|---|---|---|---|---|
| 1 | [-90, 90] | 0 | ≥ | 1 |
| 2 | [0, 90] | 45 | < | 0 |
| 3 | [0, 45] | 22.5 | < | 0 |
| 4 | [0, 22.5] | 11.25 | ≥ | 1 |
| 5 | [11.25, 22.5] | 16.875 | ≥ | 1 |

**Interleave** (longitude bit, latitude bit, repeat): `1 1 0 0 1 0 1 1 0 1` → grouped into 5-bit chunks → `11001` (=25) and `01101` (=13).

**Base32-encode** each chunk with the geohash alphabet `0123456789bcdefghjkmnpqrstuvwxyz` (`a`, `i`, `l`, `o` skipped to avoid misreads) → index 25 = `t`, index 13 = `e` → geohash starts `"te…"`, and continues to `"te7ud2ev"` at 8 characters.

![Geohash encoding — split range, interleave bits, base32 encode](../static/images/geohash-encoding.svg)

Same input always produces the same string — that's what makes a geohash usable as a plain, sortable index key.

Points sharing a prefix are in the same rectangular cell; longer shared prefixes mean closer points. But the reverse isn't guaranteed — two nearby points can fall on opposite sides of a cell boundary and get completely different prefixes (see the boundary problem below).


#### Precision


Precision is just string length — each extra character adds 5 bits, so the cell shrinks fast. Approximate cell size **near the equator**:

| Length | Cell width | Cell height | Typical use |
|---|---|---|---|
| 4 | 39.1 km | 19.5 km | City-level grouping |
| 5 | 4.9 km | 4.9 km | Neighborhood search |
| 6 | 1.2 km | 0.61 km | Nearby candidates |
| 7 | 153 m | 153 m | Block-level candidates |
| 8 | 38 m | 19 m | Building-level candidates |
| 9 | 4.8 m | 4.8 m | Fine outdoor positioning |

Cell height stays fairly constant; cell **width shrinks toward the poles** because longitude lines converge — a 6-char cell in San Francisco (~38°N) is narrower east-west than the same cell at the equator.

Rule of thumb: 5 chars for broad discovery ("restaurants nearby"), 6–7 for nearby drivers/couriers/stores, 8+ only when GPS accuracy and data density justify it. Don't guess — measure candidate counts in dense areas (Manhattan) and sparse ones (rural) separately; they behave very differently at the same precision.


#### Nearby search


1. Convert the query point to a geohash at the chosen precision.
2. Fetch candidates via a prefix query (`LIKE '9q8yy%'`, or the equivalent range scan `WHERE geohash >= '9q8yy' AND geohash < '9q8yz'`).
3. **Also query the 8 neighboring cells** — a point 10m from the query center can sit just across a cell boundary and get skipped by a single-prefix query. This is the most common geohash bug: works in a demo, silently drops edge results in production.
4. Apply an exact distance filter (haversine) on the combined candidate set — the grid gives rectangles, the real search area is a circle, so corners bring in extra false candidates that must be filtered out.
5. Rank and page (ETA, availability, price, relevance — business logic, not geohash's job).

![Geohash nesting and the 9-cell neighbor query](../static/images/geohash-neighbors.svg)


#### Simple Implementation (Python)


```python
import math

BASE32 = "0123456789bcdefghjkmnpqrstuvwxyz"
BASE32_INDEX = {c: i for i, c in enumerate(BASE32)}


def encode(lat: float, lon: float, precision: int = 8) -> str:
    lat_range, lon_range = [-90.0, 90.0], [-180.0, 180.0]
    geohash, bit, ch, even = [], 0, 0, True  # longitude bit goes first

    while len(geohash) < precision:
        rng = lon_range if even else lat_range
        val = lon if even else lat
        mid = (rng[0] + rng[1]) / 2
        if val >= mid:
            ch, rng[0] = (ch << 1) | 1, mid
        else:
            ch, rng[1] = ch << 1, mid
        even, bit = not even, bit + 1
        if bit == 5:
            geohash.append(BASE32[ch])
            bit = ch = 0

    return "".join(geohash)


def decode(geohash: str) -> tuple[float, float, float, float]:
    """Returns (lat, lon, lat_error, lon_error) — error is the half-height/width of the cell."""
    lat_range, lon_range, even = [-90.0, 90.0], [-180.0, 180.0], True

    for char in geohash:
        for shift in (4, 3, 2, 1, 0):
            bit = (BASE32_INDEX[char] >> shift) & 1
            rng = lon_range if even else lat_range
            mid = (rng[0] + rng[1]) / 2
            rng[0 if bit else 1] = mid
            even = not even

    lat = (lat_range[0] + lat_range[1]) / 2
    lon = (lon_range[0] + lon_range[1]) / 2
    return lat, lon, (lat_range[1] - lat_range[0]) / 2, (lon_range[1] - lon_range[0]) / 2


def neighbors(geohash: str) -> dict[str, str]:
    """Approximate the 8 surrounding cells by nudging one cell-width/height in each direction."""
    lat, lon, lat_err, lon_err = decode(geohash)
    deltas = {"n": (1, 0), "s": (-1, 0), "e": (0, 1), "w": (0, -1),
              "ne": (1, 1), "nw": (1, -1), "se": (-1, 1), "sw": (-1, -1)}
    return {
        name: encode(lat + dlat * 2 * lat_err, lon + dlon * 2 * lon_err, len(geohash))
        for name, (dlat, dlon) in deltas.items()
    }


def haversine_km(lat1, lon1, lat2, lon2) -> float:
    r = 6371.0
    p1, p2 = math.radians(lat1), math.radians(lat2)
    dphi, dlmb = math.radians(lat2 - lat1), math.radians(lon2 - lon1)
    a = math.sin(dphi / 2) ** 2 + math.cos(p1) * math.cos(p2) * math.sin(dlmb / 2) ** 2
    return 2 * r * math.asin(math.sqrt(a))


# ── Usage ────────────────────────────────────────────────────
gh = encode(19.0760, 72.8777, precision=8)
print(gh)                              # te7ud2ev
print(decode(gh))                      # (19.0759..., 72.8776..., ~8.6e-05, ~1.7e-04)
print(neighbors(gh))                   # {'n': 'te7ud2ey', 's': 'te7ud2eu', ...}
print(haversine_km(19.0760, 72.8777, *decode(gh)[:2]))  # ~0.008 km round-trip error
```

Verified: `encode(19.0760, 72.8777, 2)` → `"te"`, matching the hand trace above, and `decode("ezs42")` reproduces the well-known geohash.org example (~42.605, -5.603).

Not production-grade — no Date Line/pole handling, and the neighbor function approximates via re-encoding rather than direct bit manipulation. For real use: `python-geohash`, `pygeohash`, or a database's native geo type.


#### Pros


- Plain string — sorts, indexes (B-tree), and stores in any normal column, no special database feature required.
- Prefix search doubles as coarse-to-fine spatial search: shorter prefix = wider area, longer = narrower.
- Cheap to compute and compare — good as a sharding or cache key, not just a query filter.


#### Cons


- **Boundary problem** — nearby points can land in different cells (and totally different prefixes) if they straddle a cell edge. Always the top source of "missing nearby results" bugs.
- **Rectangular cells, not circles** — a radius search over a grid always pulls in extra corner candidates that need filtering.
- **Uneven cell size** — longitude degrees shrink toward the poles, so the same precision means a narrower cell at higher latitudes.
- **Date Line / poles** — points at ±180° longitude are physically adjacent but can have very different prefixes; very high latitudes behave poorly since longitude lines converge.
- Not a distance function — always needs a second, exact pass (haversine or the DB's distance function) before showing results to a user.


#### Where it's used


| Application | How it uses Geohash |
|---|---|
| **Redis** (`GEOADD`/`GEOSEARCH`) | Stores each member in a sorted set using a 52-bit interleaved geohash-style score — geo commands are a thin layer over that ordering. |
| **Elasticsearch** | `geohash_grid` aggregation buckets documents by geohash prefix for map heatmaps and tile rendering. |
| **MongoDB** (legacy `2d` index) | Pre-`2dsphere` index type built on a geohash-like space-filling curve; `2dsphere` has since replaced it for most new code. |
| **Firebase / GeoFire** | Stores a geohash string alongside lat/lng in Firestore/Realtime DB so "nearby" queries can run as plain range queries — no native geo index needed. |
| **Uber** (early marketplace) | Used geohash-based cells for driver/rider matching and surge pricing regions before moving to H3 for better neighbor uniformity. |
| **BigQuery / Snowflake** | Expose a `ST_GEOHASH`/`GEOHASH` SQL function for bucketing rows into cells directly in analytics queries. |


#### Scaling it


- **Store raw coordinates separately** — the geohash is an index key, not the source of truth. Keep `lat`/`lon` columns for exact distance checks, map display, and rebuilding the geohash if precision changes.
- **Combine with business filters** — real queries filter by status, category, or tenant too (`WHERE geohash LIKE '9q8yy%' AND status = 'open'`). Index/partition to match the actual query shape, not location alone.
- **Moving objects need a different store** — don't update a relational row on every GPS ping. Put live location (drivers, couriers, devices) in Redis/DynamoDB/Cassandra with TTLs or heartbeats, and keep it separate from historical location events.
- **Hot cells** — a busy downtown cell can dwarf the read/write volume of many rural cells combined. Shard by region-plus-load, cache heavy cells, and keep live-location writes off the slow path.
- **Reach for a native geo index first** — PostGIS (GiST/SP-GiST), MongoDB `2dsphere`, Elasticsearch `geo_point`/`geo_shape`, or Redis `GEO*` all handle the boundary/pole/Date-Line edge cases properly. Geohash is still worth it as a simple sharding/cache key even when a real spatial index handles the actual queries.
- **H3 (hexagonal) or S2 (spherical)** are the usual next step when neighbor uniformity, polygon covering, or global edge cases (Date Line, poles) start to matter more than a simple string key.

### 18.3 Merkle Trees

To check whether a large file or dataset arrived correctly, you could compare one checksum for the whole thing. That works when it matches — but when it doesn't, all it tells you is "something changed." It doesn't say *what*, so the system may have to re-fetch or re-compare everything.

A Merkle tree fixes that by hashing data in chunks: hash each block, hash those hashes in pairs, keep going until one root hash remains. The root tells you if anything changed at all; the tree beneath it tells you exactly which block.


#### How it works


- **Leaves** — hash of each individual data block.
- **Parents** — hash of the concatenation of their two children's hashes.
- **Root** — one hash that commits to the entire dataset.
- **Odd number of blocks** — duplicate the last hash so it has a pairing partner; keeps the tree shape simple.

```
Root = H(H12 + H34)
H12  = H(H1 + H2)          H34  = H(H3 + H4)
H1 = H(Block1)  H2 = H(Block2)   H3 = H(Block3)  H4 = H(Block4)
```

Change Block 3, and only the hashes on its path to the root recompute — `H3 → H34 → Root`. `H1`, `H2`, and `H12` are untouched, because they never depended on Block 3.

![Merkle tree — a single block change cascades to the root along one path](../static/images/merkle-tree-structure.svg)


#### Two things it enables


**Merkle proof — verify one block cheaply.** To prove Block 3 is correct against a trusted root, you don't need the whole tree — just the sibling hashes along its path (here, `H4` and `H12`): recompute `H3 = H(Block3)`, then `H34 = H(H3+H4)`, then `Root = H(H12+H34)`, and compare to the trusted root. `log₂(n)` hashes instead of `n`.

**Tree diff — find what differs between two copies.** Two nodes holding the same data range compare root hashes first. Match → done, no further work. Differ → compare children; whichever child hash matches gets skipped entirely, whichever differs gets walked into recursively — repeat until you land on the actual mismatched block(s). Finding one bad block among ~1M takes ~20 comparisons, not a million.

![Merkle proof and tree-diff sync](../static/images/merkle-proof-sync.svg)


#### Simple Implementation (Python)


```python
import hashlib


class MerkleTree:
    def __init__(self, data_blocks):
        self.leaves = [self._hash(block) for block in data_blocks]
        self.tree = self._build_tree(self.leaves)

    def _hash(self, data):
        if isinstance(data, str):
            data = data.encode()
        return hashlib.sha256(data).hexdigest()

    def _build_tree(self, leaves):
        if not leaves:
            return []
        tree = [leaves]
        current_level = leaves
        while len(current_level) > 1:
            next_level = []
            for i in range(0, len(current_level), 2):
                left = current_level[i]
                right = current_level[i + 1] if i + 1 < len(current_level) else left  # duplicate odd tail
                next_level.append(self._hash(left + right))
            tree.append(next_level)
            current_level = next_level
        return tree

    def get_root(self):
        return self.tree[-1][0] if self.tree else None

    def get_proof(self, index):
        """Sibling hashes needed to verify the leaf at `index`."""
        if index >= len(self.leaves):
            raise IndexError("Index out of range")
        proof = []
        current_index = index
        for level in self.tree[:-1]:  # exclude root level
            if current_index % 2 == 0:
                sibling_index, direction = current_index + 1, "right"
            else:
                sibling_index, direction = current_index - 1, "left"
            sibling_hash = level[sibling_index] if sibling_index < len(level) else level[current_index]
            proof.append((direction, sibling_hash))
            current_index //= 2
        return proof

    def verify_proof(self, data, proof, root):
        current_hash = self._hash(data)
        for direction, sibling_hash in proof:
            current_hash = self._hash(sibling_hash + current_hash) if direction == "left" \
                else self._hash(current_hash + sibling_hash)
        return current_hash == root


# ── Usage ────────────────────────────────────────────────────
transactions = ["tx1: Alice->Bob $50", "tx2: Bob->Carol $30", "tx3: Carol->Dave $20", "tx4: Dave->Eve $10"]
tree = MerkleTree(transactions)
root = tree.get_root()

proof = tree.get_proof(2)                                  # only 2 sibling hashes for 4 leaves
print(tree.verify_proof(transactions[2], proof, root))     # True
print(tree.verify_proof("tx3: Carol->Dave $999", proof, root))  # False — tampered data fails
```

Verified against the odd-leaf case too (5 blocks → last one duplicated) — every leaf's proof still round-trips correctly.


#### Pros


- Verify one item in O(log n) instead of O(n) — a small proof, not the whole dataset.
- Detect *and* localize changes — a plain checksum only tells you "different," a Merkle tree tells you "block 3 is different."
- Comparing two trees skips every matching branch — sync work is proportional to what actually changed, not total data size.


#### Cons


- Doesn't prevent tampering by itself — it only detects it, and only if you already have a trusted root from somewhere else (out-of-band, or signed).
- **Second-preimage / node-confusion attack** on naive implementations: if a leaf hash `H(block)` and an internal-node hash `H(left+right)` live in the same hash space, an attacker can craft a leaf whose content equals some `left+right` pair and pass it off as an internal node (or vice versa), forging a valid-looking proof for data that was never in the tree. The standard fix (RFC 6962 / Certificate Transparency) is **domain separation** — prefix leaf hashes with `0x00` and internal-node hashes with `0x01` before hashing, so the two can never collide. The reference implementation above skips this for clarity — don't skip it in anything real.
- Tree must be rebuilt (or incrementally updated) whenever blocks change — costly for highly mutable data. Append-only logs solve this with a **Merkle Mountain Range** (a forest of perfect binary trees instead of one, so appending is O(log n) instead of a full rebuild) — this is what Certificate Transparency and some blockchains actually use under the hood.
- Not a membership/lookup structure like a Bloom filter — it verifies data you already have a block index for, it doesn't answer "is X in this set" cheaply on its own.


#### Where it's used


| Application | How it uses a Merkle tree |
|---|---|
| **Git** | Commit hashes depend on a tree of file/directory hashes plus the parent commit hash — changing any historical file changes every hash after it, which is how Git detects corruption or history tampering. |
| **Bitcoin** | Each block header stores the Merkle root of its transaction list — light (SPV) clients verify a transaction is in a block via a small proof, without downloading every transaction. |
| **Ethereum** | Goes further with a **Merkle Patricia Trie** — a Merkle tree merged with a prefix trie — for state, storage, and receipts. This is a common interview gotcha: it's not the same plain binary tree Bitcoin uses for its tx list; the trie structure also gives efficient key-based lookups and proofs-of-absence, not just proofs-of-inclusion. |
| **Cassandra / DynamoDB-style stores** | Each replica builds a Merkle tree over its data range; nodes exchange roots and walk down only the mismatched branches to repair drift (anti-entropy / read repair). |
| **Certificate Transparency logs** | Append-only Merkle tree (Merkle Mountain Range) of issued TLS certificates — lets auditors verify a certificate was logged and catch mis-issued ones without trusting the log operator blindly. |
| **IPFS / content-addressed storage** | Files are chunked and hashed into a Merkle DAG — identical chunks across different files are naturally deduplicated and independently verifiable. |


#### Merkle tree vs. hash list vs. Bloom filter


| | Hash list | Merkle tree | Bloom filter |
|---|---|---|---|
| Verify one item | O(n) | O(log n) | O(k) |
| Detect changed data | Yes | Yes | No |
| Find *what* changed | O(n) | O(log n) | N/A |
| Space | O(n) | O(n) | O(m), sub-linear |
| Best for | simple whole-file checks | data sync, proofs, tamper detection | fast membership checks |

Reach for a Merkle tree when you need both "did anything change?" and "which part?" — a checksum alone only answers the first, a Bloom filter answers neither (it answers "have I seen this item," a different question).

---

## 19. Bit manipulation


### 19.1 Operators and the tricks built on them

An integer is a row of bits, and bitwise operators work on all of them at once.

| Operation | Code | Example (x = 12 = `1100`) |
|---|---|---|
| Is bit i set? | `(x >> i) & 1` | bit 2 → 1 |
| Set bit i | `x \| (1 << i)` | i = 0 → `1101` (13) |
| Clear bit i | `x & ~(1 << i)` | i = 3 → `0100` (4) |
| Toggle bit i | `x ^ (1 << i)` | i = 1 → `1110` (14) |
| Drop the lowest set bit | `x & (x - 1)` | `1000` (8) |
| Keep only the lowest set bit | `x & -x` | `0100` (4) |
| Power of two? | `x > 0 and x & (x - 1) == 0` | 12 → False |
| Count set bits | `x.bit_count()` (3.10+) or `bin(x).count("1")` | 2 |
| Number of bits needed | `x.bit_length()` | 4 |

**Why `x & (x - 1)` works:** subtracting 1 flips the lowest set bit to 0 and every bit below it to 1; AND-ing with the original clears exactly that lowest set bit. Repeating it counts set bits in O(number of set bits) — Kernighan's trick.

**XOR is the star of bit problems** because `a ^ a = 0`, `a ^ 0 = a`, and order doesn't matter. XOR-ing a whole list cancels every value that appears twice.

```python
from functools import reduce
from operator import xor

def single_number(nums: list[int]) -> int:
    """Every value appears twice except one."""
    return reduce(xor, nums, 0)

def missing_number(nums: list[int]) -> int:
    """nums has n distinct values from 0..n with one missing: XOR indices and values together."""
    return reduce(xor, range(len(nums) + 1), 0) ^ reduce(xor, nums, 0)

def count_bits_upto(n: int) -> list[int]:
    """bits[i] = bits[i >> 1] + lowest bit — a one-line DP."""
    bits = [0] * (n + 1)
    for i in range(1, n + 1):
        bits[i] = bits[i >> 1] + (i & 1)
    return bits

def kernighan(x: int) -> int:
    count = 0
    while x:
        x &= x - 1
        count += 1
    return count

assert single_number([4, 1, 2, 1, 2]) == 4
assert missing_number([3, 0, 1]) == 2
assert count_bits_upto(5) == [0, 1, 1, 2, 1, 2]
assert kernighan(0b1011_0000) == 3 == (0b1011_0000).bit_count()
```

### 19.2 Masks as sets: enumerating subsets

With n ≤ 20 items, an integer mask of n bits represents a subset: bit i set means item i is in. That turns "all subsets" into a loop from 0 to 2ⁿ − 1, and is the basis of bitmask DP ([Bitmask & Probability DP](#1713-bitmask--probability-dp)).

```python
def all_subsets(items: list) -> list[list]:
    n = len(items)
    return [[items[i] for i in range(n) if mask >> i & 1] for mask in range(1 << n)]

def submasks(mask: int) -> list[int]:
    """Every submask of `mask`, largest first — O(2^popcount) instead of O(2^n)."""
    out, sub = [], mask
    while True:
        out.append(sub)
        if sub == 0:
            return out
        sub = (sub - 1) & mask

assert sorted(map(tuple, all_subsets(["a", "b"]))) == [(), ("a",), ("a", "b"), ("b",)]
assert submasks(0b101) == [0b101, 0b100, 0b001, 0]
```

### 19.3 Python-specific gotchas

- Python integers are **unbounded**: there's no overflow and no fixed 32-bit width, so `~5 == -6` and right-shifting a negative number keeps it negative forever. Problems written for 32-bit languages need an explicit mask.
- "Add two integers without `+`" (LeetCode 371) loops forever on negatives in Python unless you mask to 32 bits and convert back:

```python
def add_without_plus(a: int, b: int) -> int:
    MASK, MAX = 0xFFFFFFFF, 0x7FFFFFFF
    while b & MASK:
        a, b = (a ^ b) & MASK, ((a & b) << 1) & MASK   # sum without carry, carry
    a &= MASK
    return a if a <= MAX else ~(a ^ MASK)              # back to a signed Python int

assert add_without_plus(2, 3) == 5
assert add_without_plus(-7, 3) == -4
assert add_without_plus(-1, 1) == 0
```

**Maximum XOR of two numbers** is solved with a binary trie over the bits (greedily choose the opposite bit at each level) — see [Trie](#13-trie).

**Practice:** [Single Number](https://leetcode.com/problems/single-number/) · [Single Number II](https://leetcode.com/problems/single-number-ii/) (count bits mod 3) · [Counting Bits](https://leetcode.com/problems/counting-bits/) · [Missing Number](https://leetcode.com/problems/missing-number/) · [Reverse Bits](https://leetcode.com/problems/reverse-bits/) · [Sum of Two Integers](https://leetcode.com/problems/sum-of-two-integers/) · [Subsets](https://leetcode.com/problems/subsets/) (bitmask version) · [Maximum XOR of Two Numbers in an Array](https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/)

---

## 20. Math for coding interviews


### 20.1 GCD, LCM and fast exponentiation

- **GCD** by Euclid: `gcd(a, b) = gcd(b, a % b)`, O(log min(a, b)). Python has `math.gcd` and `math.lcm`; know the algorithm anyway.
- **LCM** = a × b / gcd(a, b) — divide first to keep numbers small in fixed-width languages.
- **Fast power** (binary exponentiation): square the base and halve the exponent → O(log e) multiplications. Python's `pow(b, e, mod)` does it built in.

```python
def gcd(a: int, b: int) -> int:
    while b:
        a, b = b, a % b
    return abs(a)

def fast_pow(base: int, exp: int, mod: int | None = None) -> int:
    result = 1
    while exp:
        if exp & 1:
            result = result * base if mod is None else result * base % mod
        base = base * base if mod is None else base * base % mod
        exp >>= 1
    return result

def my_pow(x: float, n: int) -> float:
    """LeetCode 50 — handles negative exponents."""
    if n < 0:
        x, n = 1 / x, -n
    result = 1.0
    while n:
        if n & 1:
            result *= x
        x *= x
        n >>= 1
    return result

assert gcd(84, 36) == 12 and 84 * 36 // gcd(84, 36) == 252
assert fast_pow(3, 200, 10**9 + 7) == pow(3, 200, 10**9 + 7)
assert abs(my_pow(2.0, -2) - 0.25) < 1e-12
```

### 20.2 Modular arithmetic and counting

Answers that "may be large" are asked **modulo 10⁹ + 7** (a prime). The rules:
- `(a + b) % m`, `(a * b) % m` and `(a - b) % m` can be reduced at every step (Python's `%` is always non-negative for a positive m; in Java/C++ add m before taking `%` of a difference).
- **Division** needs a modular inverse. For a prime m, Fermat's little theorem gives `inverse(a) = a^(m−2) mod m`.
- **nCr mod p:** precompute factorials and inverse factorials once, then each query is O(1).

```python
MOD = 10**9 + 7

def make_ncr(limit: int):
    fact = [1] * (limit + 1)
    for i in range(1, limit + 1):
        fact[i] = fact[i - 1] * i % MOD
    inv = [1] * (limit + 1)
    inv[limit] = pow(fact[limit], MOD - 2, MOD)          # Fermat inverse
    for i in range(limit, 0, -1):
        inv[i - 1] = inv[i] * i % MOD
    return lambda n, r: 0 if r < 0 or r > n else fact[n] * inv[r] % MOD * inv[n - r] % MOD

ncr = make_ncr(1000)
assert ncr(5, 2) == 10 and ncr(10, 0) == 1 and ncr(3, 5) == 0
assert ncr(1000, 500) == __import__("math").comb(1000, 500) % MOD
assert ncr(18, 9) == 48620                       # lattice paths in a 10×10 grid (Unique Paths)
```

Counting identities that come up: paths in an m × n grid = C(m+n−2, m−1); **Catalan numbers** C(2n, n)/(n+1) count valid parenthesis strings, BST shapes with n nodes, and ways to triangulate a polygon; stars and bars: ways to put n identical balls into k boxes = C(n+k−1, k−1).

### 20.3 Primes: sieve and factorisation

```python
def sieve(n: int) -> list[int]:
    """All primes ≤ n in O(n log log n): cross out multiples of each prime, starting at p²."""
    is_prime = bytearray([1]) * (n + 1)
    is_prime[0:2] = b"\x00\x00"
    for p in range(2, int(n ** 0.5) + 1):
        if is_prime[p]:
            is_prime[p * p::p] = bytearray(len(range(p * p, n + 1, p)))
    return [i for i in range(n + 1) if is_prime[i]]

def factorise(n: int) -> dict[int, int]:
    """Trial division up to √n — O(√n)."""
    factors, d = {}, 2
    while d * d <= n:
        while n % d == 0:
            factors[d] = factors.get(d, 0) + 1
            n //= d
        d += 1
    if n > 1:
        factors[n] = factors.get(n, 0) + 1
    return factors

assert sieve(30) == [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
assert len(sieve(10**6)) == 78498
assert factorise(360) == {2: 3, 3: 2, 5: 1}
```

**Other numeric habits:** use `math.isqrt(n)` instead of `int(n ** 0.5)` for big integers (floating-point square roots are off for large n); compare floats with a tolerance; for geometry, the **cross product** `(b−a) × (c−a)` tells you whether three points turn left, right, or are collinear, without any division.

**Practice:** [Pow(x, n)](https://leetcode.com/problems/powx-n/) · [Count Primes](https://leetcode.com/problems/count-primes/) · [Unique Paths](https://leetcode.com/problems/unique-paths/) (combinatorics version) · [Greatest Common Divisor of Strings](https://leetcode.com/problems/greatest-common-divisor-of-strings/) · [Excel Sheet Column Title](https://leetcode.com/problems/excel-sheet-column-title/) · [Fraction to Recurring Decimal](https://leetcode.com/problems/fraction-to-recurring-decimal/) · [Super Pow](https://leetcode.com/problems/super-pow/)

---

## 21. String matching: KMP, Z-function, Manacher


### 21.1 Why naive matching is slow, and the KMP idea

Finding a pattern of length m in a text of length n by trying every start position is O(n·m) in the worst case (text `aaaa…ab`, pattern `aaab`): after a mismatch, the naive approach throws away everything it just learned.

**KMP** keeps that knowledge in a **prefix function** (also called the failure function or LPS array): `pi[i]` = length of the longest proper prefix of `pattern[:i+1]` that is also a suffix of it. On a mismatch, instead of restarting, jump the pattern pointer back to `pi[j−1]` — the longest part that's still known to match. Total time O(n + m).

```text
pattern = a b a b c
pi      = 0 0 1 2 0     ("abab" ends with "ab", which is also its prefix → 2)
```

```python
def prefix_function(s: str) -> list[int]:
    pi = [0] * len(s)
    k = 0
    for i in range(1, len(s)):
        while k and s[i] != s[k]:
            k = pi[k - 1]                 # fall back to the next-longest border
        if s[i] == s[k]:
            k += 1
        pi[i] = k
    return pi

def kmp_search(text: str, pattern: str) -> list[int]:
    """All start indices of pattern in text, O(n + m)."""
    if not pattern:
        return list(range(len(text) + 1))
    pi, out, k = prefix_function(pattern), [], 0
    for i, ch in enumerate(text):
        while k and ch != pattern[k]:
            k = pi[k - 1]
        if ch == pattern[k]:
            k += 1
        if k == len(pattern):
            out.append(i - k + 1)
            k = pi[k - 1]                 # keep going to find overlapping matches
    return out

assert prefix_function("ababc") == [0, 0, 1, 2, 0]
assert kmp_search("abababc", "abab") == [0, 2]
assert kmp_search("aaaa", "b") == []
# Shortest palindrome trick: the longest palindromic prefix of s is pi[-1] of s + "#" + reversed(s)
s = "aacecaaa"
assert prefix_function(s + "#" + s[::-1])[-1] == 7       # "aacecaa"
```

### 21.2 Z-function

`z[i]` = length of the longest substring starting at i that matches a prefix of the string. Matching a pattern = compute Z on `pattern + "$" + text` and look for values equal to len(pattern). Same O(n + m) as KMP; some people find it easier to reason about.

```python
def z_function(s: str) -> list[int]:
    n = len(s)
    z, left, right = [0] * n, 0, 0
    for i in range(1, n):
        if i < right:
            z[i] = min(right - i, z[i - left])           # reuse the box [left, right)
        while i + z[i] < n and s[z[i]] == s[i + z[i]]:
            z[i] += 1
        if i + z[i] > right:
            left, right = i, i + z[i]
    return z

def z_search(text: str, pattern: str) -> list[int]:
    z, m = z_function(pattern + "\x00" + text), len(pattern)
    return [i - m - 1 for i in range(m + 1, len(z)) if z[i] >= m]

assert z_function("aabxaab") == [0, 1, 0, 0, 3, 1, 0]
assert z_search("abababc", "abab") == [0, 2]          # same answer as KMP
```

### 21.3 Manacher's algorithm (longest palindrome in O(n))

Expanding around every centre is O(n²) and is the expected answer for [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/). Manacher reaches O(n) by reusing the mirror image of palindromes already found, like the Z-function's box. It's rarely required; know that it exists and what it reuses.

```python
def longest_palindrome(s: str) -> str:
    t = "^#" + "#".join(s) + "#$"        # sentinels + separators: odd and even cases become one
    p, centre, right = [0] * len(t), 0, 0
    for i in range(1, len(t) - 1):
        if i < right:
            p[i] = min(right - i, p[2 * centre - i])     # mirror around the current centre
        while t[i + p[i] + 1] == t[i - p[i] - 1]:
            p[i] += 1
        if i + p[i] > right:
            centre, right = i, i + p[i]
    length, i = max((v, i) for i, v in enumerate(p))
    start = (i - length) // 2
    return s[start:start + length]

assert longest_palindrome("babad") in ("bab", "aba")
assert longest_palindrome("cbbd") == "bb"
assert longest_palindrome("forgeeksskeegfor") == "geeksskeeg"
```

**Which one?** Rolling hash ([Rolling hash (Rabin–Karp)](#43-rolling-hash-rabinkarp)) is the most flexible (compare any two substrings in O(1)) but probabilistic; KMP and Z are exact and linear. **Suffix arrays and suffix automata** answer harder queries (number of distinct substrings, longest repeated substring) and are almost never expected in interviews — name them if asked.

**Practice:** [Find the Index of the First Occurrence in a String](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/) · [Repeated Substring Pattern](https://leetcode.com/problems/repeated-substring-pattern/) · [Shortest Palindrome](https://leetcode.com/problems/shortest-palindrome/) · [Longest Happy Prefix](https://leetcode.com/problems/longest-happy-prefix/) · [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)

---

## 22. Range queries: Fenwick tree, segment tree, sparse table


### 22.1 Choosing a structure

Prefix sums ([Pattern 1: Prefix Sums](#32-pattern-1-prefix-sums)) answer range-sum queries in O(1) — **as long as the array never changes**. One update forces an O(n) rebuild. When updates and queries are mixed, you need a tree:

| Structure | Update | Query | Supports | Use when |
|---|---|---|---|---|
| Prefix sums | O(n) | O(1) | Sums (invertible ops) | No updates |
| **Fenwick tree (BIT)** | O(log n) | O(log n) | Prefix sums / counts | Point updates + range sums; counting inversions |
| **Segment tree** | O(log n) | O(log n) | Any associative op: sum, min, max, gcd | Range min/max with updates; range updates with lazy propagation |
| **Sparse table** | — (static) | O(1) | Idempotent ops: min, max, gcd | Many range-min queries on a static array |

### 22.2 Fenwick tree (binary indexed tree)

Each index i stores the sum of a block of elements ending at i, whose length is the lowest set bit of i (`i & -i`). A prefix query walks down by removing that bit; an update walks up by adding it — both touch O(log n) cells. About ten lines of code, which is why it's the interview favourite.

```python
class Fenwick:
    def __init__(self, n: int):
        self.tree = [0] * (n + 1)                     # 1-indexed

    def add(self, i: int, delta: int) -> None:        # a[i] += delta (i is 0-indexed)
        i += 1
        while i < len(self.tree):
            self.tree[i] += delta
            i += i & -i

    def prefix(self, i: int) -> int:                  # sum of a[0..i-1]
        total = 0
        while i:
            total += self.tree[i]
            i -= i & -i
        return total

    def range_sum(self, lo: int, hi: int) -> int:     # sum of a[lo..hi] inclusive
        return self.prefix(hi + 1) - self.prefix(lo)

def count_smaller_after_self(nums: list[int]) -> list[int]:
    """LeetCode 315: walk from the right; the BIT counts how many values seen so far are smaller."""
    ranks = {v: r for r, v in enumerate(sorted(set(nums)))}   # coordinate compression
    bit, out = Fenwick(len(ranks)), []
    for v in reversed(nums):
        out.append(bit.prefix(ranks[v]))                     # values with a smaller rank
        bit.add(ranks[v], 1)
    return out[::-1]

f = Fenwick(5)
for i, v in enumerate([5, 2, 7, 1, 3]):
    f.add(i, v)
assert f.range_sum(1, 3) == 10
f.add(2, -7)                                          # a[2] = 0
assert f.range_sum(0, 4) == 11
assert count_smaller_after_self([5, 2, 6, 1]) == [2, 1, 1, 0]
```

**Coordinate compression** (map values to their rank) is the usual companion: it lets a BIT index values up to 10⁹ using only as many cells as there are distinct values.

### 22.3 Segment tree, with lazy propagation

A segment tree stores, for each node, the answer for a segment of the array; children split the segment in half. Any associative operation works. The iterative version below handles point updates and range-min queries; the recursive one adds **lazy propagation** for "add v to every element in [l, r]" in O(log n), by postponing updates to children until someone needs them.

```python
class MinSegTree:
    """Iterative, bottom-up: leaves at [n, 2n), parent of i is i // 2."""
    def __init__(self, data: list[int]):
        self.n = len(data)
        self.t = [0] * self.n + list(data)
        for i in range(self.n - 1, 0, -1):
            self.t[i] = min(self.t[2 * i], self.t[2 * i + 1])

    def update(self, i: int, value: int) -> None:
        i += self.n
        self.t[i] = value
        while i > 1:
            i //= 2
            self.t[i] = min(self.t[2 * i], self.t[2 * i + 1])

    def query(self, lo: int, hi: int) -> int:          # min over [lo, hi)
        res, lo, hi = float("inf"), lo + self.n, hi + self.n
        while lo < hi:
            if lo & 1:
                res, lo = min(res, self.t[lo]), lo + 1
            if hi & 1:
                hi -= 1
                res = min(res, self.t[hi])
            lo, hi = lo // 2, hi // 2
        return res

class LazySumTree:
    """Range add + range sum, recursive with lazy tags."""
    def __init__(self, n: int):
        self.n, self.sum, self.lazy = n, [0] * (4 * n), [0] * (4 * n)

    def _push(self, node: int, lo: int, hi: int) -> None:
        if self.lazy[node]:
            mid = (lo + hi) // 2
            for child, l, r in ((2 * node, lo, mid), (2 * node + 1, mid + 1, hi)):
                self.sum[child] += self.lazy[node] * (r - l + 1)
                self.lazy[child] += self.lazy[node]
            self.lazy[node] = 0

    def add(self, l: int, r: int, v: int, node: int = 1, lo: int = 0, hi: int | None = None) -> None:
        hi = self.n - 1 if hi is None else hi
        if r < lo or hi < l:
            return
        if l <= lo and hi <= r:                       # fully covered: tag it and stop
            self.sum[node] += v * (hi - lo + 1)
            self.lazy[node] += v
            return
        self._push(node, lo, hi)
        mid = (lo + hi) // 2
        self.add(l, r, v, 2 * node, lo, mid)
        self.add(l, r, v, 2 * node + 1, mid + 1, hi)
        self.sum[node] = self.sum[2 * node] + self.sum[2 * node + 1]

    def total(self, l: int, r: int, node: int = 1, lo: int = 0, hi: int | None = None) -> int:
        hi = self.n - 1 if hi is None else hi
        if r < lo or hi < l:
            return 0
        if l <= lo and hi <= r:
            return self.sum[node]
        self._push(node, lo, hi)
        mid = (lo + hi) // 2
        return self.total(l, r, 2 * node, lo, mid) + self.total(l, r, 2 * node + 1, mid + 1, hi)

st = MinSegTree([5, 2, 7, 1, 3])
assert st.query(0, 3) == 2 and st.query(2, 5) == 1
st.update(3, 9)
assert st.query(2, 5) == 3

lz, ref = LazySumTree(8), [0] * 8
for l, r, v in [(0, 3, 5), (2, 7, 1), (4, 4, 10)]:
    lz.add(l, r, v)
    for i in range(l, r + 1):
        ref[i] += v
assert all(lz.total(l, r) == sum(ref[l:r + 1]) for l in range(8) for r in range(l, 8))
```

### 22.4 Sparse table: O(1) range minimum on a static array

Precompute `table[k][i]` = min of the 2ᵏ elements starting at i. Any range [l, r] is covered by two overlapping power-of-two blocks; because min is **idempotent** (overlap doesn't matter), the answer is the min of those two. O(n log n) build, O(1) query, no updates.

```python
def build_sparse(a: list[int]) -> list[list[int]]:
    table = [list(a)]
    k = 1
    while (1 << k) <= len(a):
        prev, half = table[-1], 1 << (k - 1)
        table.append([min(prev[i], prev[i + half]) for i in range(len(a) - (1 << k) + 1)])
        k += 1
    return table

def range_min(table: list[list[int]], l: int, r: int) -> int:
    k = (r - l + 1).bit_length() - 1
    return min(table[k][l], table[k][r - (1 << k) + 1])

a = [5, 2, 7, 1, 3, 8, 6]
sp = build_sparse(a)
assert all(range_min(sp, l, r) == min(a[l:r + 1]) for l in range(len(a)) for r in range(l, len(a)))
```

**Practice:** [Range Sum Query – Mutable](https://leetcode.com/problems/range-sum-query-mutable/) · [Count of Smaller Numbers After Self](https://leetcode.com/problems/count-of-smaller-numbers-after-self/) · [Reverse Pairs](https://leetcode.com/problems/reverse-pairs/) · [Count of Range Sum](https://leetcode.com/problems/count-of-range-sum/) · [Falling Squares](https://leetcode.com/problems/falling-squares/) (segment tree with coordinate compression)

---

## 23. Advanced graph algorithms


### 23.1 0-1 BFS

When edge weights are only 0 or 1 (e.g., "free to move along an arrow, cost 1 to change it"), Dijkstra works but a **deque** is simpler and O(V + E): push 0-weight neighbours to the **front** and 1-weight neighbours to the **back**. The deque always holds at most two distance values, so it stays sorted like Dijkstra's heap.

```python
from collections import deque

def min_cost_grid(grid: list[list[int]]) -> int:
    """LeetCode 1368: each cell points 1=right 2=left 3=down 4=up; changing a sign costs 1."""
    rows, cols = len(grid), len(grid[0])
    dirs = {1: (0, 1), 2: (0, -1), 3: (1, 0), 4: (-1, 0)}
    dist = [[float("inf")] * cols for _ in range(rows)]
    dist[0][0], dq = 0, deque([(0, 0)])
    while dq:
        r, c = dq.popleft()
        for sign, (dr, dc) in dirs.items():
            nr, nc = r + dr, c + dc
            if 0 <= nr < rows and 0 <= nc < cols:
                w = 0 if grid[r][c] == sign else 1
                if dist[r][c] + w < dist[nr][nc]:
                    dist[nr][nc] = dist[r][c] + w
                    (dq.appendleft if w == 0 else dq.append)((nr, nc))
    return dist[-1][-1]

assert min_cost_grid([[1, 1, 1, 1], [2, 2, 2, 2], [1, 1, 1, 1], [2, 2, 2, 2]]) == 3
assert min_cost_grid([[1, 1, 3], [3, 2, 2], [1, 1, 4]]) == 0
```

### 23.2 A* search

Dijkstra explores outward in every direction. **A\*** orders the frontier by `distance so far + h(node)`, where `h` is an **admissible** heuristic (never overestimates the remaining distance — e.g., Manhattan distance on a 4-direction grid). It finds the same shortest path while exploring far fewer nodes when the target is far away; with h = 0 it *is* Dijkstra. Used in games and maps; in interviews, mostly a follow-up to "can you make the grid search faster?".

```python
import heapq

def astar_grid(grid: list[str], start: tuple, goal: tuple) -> int:
    """Shortest path length on a grid of '.' and '#', 4 directions."""
    h = lambda p: abs(p[0] - goal[0]) + abs(p[1] - goal[1])
    best, heap = {start: 0}, [(h(start), 0, start)]
    while heap:
        _, g, (r, c) = heapq.heappop(heap)
        if (r, c) == goal:
            return g
        if g > best[(r, c)]:
            continue
        for nr, nc in ((r + 1, c), (r - 1, c), (r, c + 1), (r, c - 1)):
            if 0 <= nr < len(grid) and 0 <= nc < len(grid[0]) and grid[nr][nc] == "." \
                    and g + 1 < best.get((nr, nc), float("inf")):
                best[(nr, nc)] = g + 1
                heapq.heappush(heap, (g + 1 + h((nr, nc)), g + 1, (nr, nc)))
    return -1

maze = ["....#",
        ".##.#",
        "...#.",
        "#...."]
assert astar_grid(maze, (0, 0), (3, 4)) == 7
assert astar_grid([".#", "#."], (0, 0), (1, 1)) == -1
```

### 23.3 Euler paths: use every edge exactly once

A path that uses **every edge** once exists in a directed graph when at most one node has out-degree − in-degree = 1 (the start), at most one has −1 (the end), and the rest are balanced. **Hierholzer's algorithm** builds it in O(E): walk unused edges until stuck, and add each node to the answer as you back out of it.

```python
from collections import defaultdict

def find_itinerary(tickets: list[list[str]]) -> list[str]:
    """LeetCode 332: use every ticket once, starting at JFK, lexically smallest itinerary."""
    graph = defaultdict(list)
    for src, dst in sorted(tickets, reverse=True):
        graph[src].append(dst)                   # reversed sort → pop() gives the smallest
    route, stack = [], ["JFK"]
    while stack:
        while graph[stack[-1]]:
            stack.append(graph[stack[-1]].pop())
        route.append(stack.pop())                # stuck: this node is finished
    return route[::-1]

assert find_itinerary([["MUC", "LHR"], ["JFK", "MUC"], ["SFO", "SJC"], ["LHR", "SFO"]]) == \
    ["JFK", "MUC", "LHR", "SFO", "SJC"]
assert find_itinerary([["JFK", "SFO"], ["JFK", "ATL"], ["SFO", "ATL"], ["ATL", "JFK"], ["ATL", "SFO"]]) == \
    ["JFK", "ATL", "JFK", "SFO", "ATL", "SFO"]
```

(A path visiting every **node** once — a Hamiltonian path — is NP-hard; for n ≤ 20 it's a bitmask DP.)

### 23.4 LCA with binary lifting

For many "lowest common ancestor" or "k-th ancestor" queries on a large tree, precompute `up[j][v]` = the 2ʲ-th ancestor of v. Any jump of k steps is a sum of powers of two, so it takes O(log n); LCA lifts the deeper node to the same depth, then lifts both while their ancestors differ. O(n log n) build, O(log n) per query.

```python
def build_lifting(parent: list[int]) -> tuple[list[list[int]], list[int]]:
    """parent[root] = root. Assumes nodes are numbered so that parent[v] < v (e.g., BFS order)."""
    n, log = len(parent), max(1, len(parent).bit_length())
    up = [parent[:]]
    for _ in range(1, log):
        prev = up[-1]
        up.append([prev[prev[v]] for v in range(n)])
    depth = [0] * n
    for v in range(1, n):
        depth[v] = depth[parent[v]] + 1
    return up, depth

def kth_ancestor(up, v: int, k: int) -> int:
    for j in range(len(up)):
        if k >> j & 1:
            v = up[j][v]
    return v

def lca(up, depth, a: int, b: int) -> int:
    if depth[a] < depth[b]:
        a, b = b, a
    a = kth_ancestor(up, a, depth[a] - depth[b])
    if a == b:
        return a
    for j in reversed(range(len(up))):
        if up[j][a] != up[j][b]:
            a, b = up[j][a], up[j][b]
    return up[0][a]

#        0
#      /   \
#     1     2
#    / \     \
#   3   4     5
#   |
#   6
parent = [0, 0, 0, 1, 1, 2, 3]
up, depth = build_lifting(parent)
assert kth_ancestor(up, 6, 2) == 1 and kth_ancestor(up, 6, 3) == 0
assert lca(up, depth, 6, 4) == 1 and lca(up, depth, 6, 5) == 0 and lca(up, depth, 3, 6) == 3
```

### 23.5 Maximum flow (know the idea)

Max flow asks how much can be pushed from a source to a sink through edges with capacities. **Ford–Fulkerson**: while there's a path with spare capacity in the **residual graph** (which includes reverse edges that let you undo earlier choices), push the bottleneck amount along it. Using BFS to pick paths (**Edmonds–Karp**) gives O(V·E²). The **max-flow = min-cut** theorem says the answer equals the cheapest set of edges whose removal separates source and sink. Bipartite matching (assign workers to jobs) is max flow with capacity-1 edges. Rare in interviews; recognising "this is a matching / flow problem" is usually enough.

```python
from collections import deque

def max_flow(cap: list[list[int]], s: int, t: int) -> int:
    n, flow = len(cap), 0
    residual = [row[:] for row in cap]
    while True:
        parent = [-1] * n
        parent[s] = s
        q = deque([s])
        while q and parent[t] == -1:                 # BFS for an augmenting path
            u = q.popleft()
            for v in range(n):
                if parent[v] == -1 and residual[u][v] > 0:
                    parent[v] = u
                    q.append(v)
        if parent[t] == -1:
            return flow
        bottleneck, v = float("inf"), t
        while v != s:
            bottleneck, v = min(bottleneck, residual[parent[v]][v]), parent[v]
        v = t
        while v != s:
            residual[parent[v]][v] -= bottleneck
            residual[v][parent[v]] += bottleneck     # reverse edge: lets later paths undo this
            v = parent[v]
        flow += bottleneck

capacity = [[0, 10, 10, 0, 0, 0],
            [0, 0, 2, 4, 8, 0],
            [0, 0, 0, 0, 9, 0],
            [0, 0, 0, 0, 0, 10],
            [0, 0, 0, 6, 0, 10],
            [0, 0, 0, 0, 0, 0]]
assert max_flow(capacity, 0, 5) == 19
```

**Practice:** [Minimum Cost to Make at Least One Valid Path in a Grid](https://leetcode.com/problems/minimum-cost-to-make-at-least-one-valid-path-in-a-grid/) · [Minimum Obstacle Removal to Reach Corner](https://leetcode.com/problems/minimum-obstacle-removal-to-reach-corner/) · [Shortest Path in Binary Matrix](https://leetcode.com/problems/shortest-path-in-binary-matrix/) · [Reconstruct Itinerary](https://leetcode.com/problems/reconstruct-itinerary/) · [Cracking the Safe](https://leetcode.com/problems/cracking-the-safe/) (Euler path on a de Bruijn graph) · [Kth Ancestor of a Tree Node](https://leetcode.com/problems/kth-ancestor-of-a-tree-node/)

---

## 24. Advanced dynamic programming


### 24.1 Digit DP

"Count the integers in [0, N] whose digits satisfy some property" — N can be 10¹⁸, so you can't loop. Build the number **digit by digit from the left**, tracking: the position, whatever the property needs (sum so far, last digit, a mask of used digits), and a **`tight`** flag meaning "every digit so far equals N's digit, so the next digit is capped by N's". Memoize on that state. States ≈ digits × property values × 2.

```python
from functools import cache

def count_digit_sum(n: int, target: int) -> int:
    """How many integers in [0, n] have digit sum == target."""
    digits = list(map(int, str(n)))

    @cache
    def go(pos: int, total: int, tight: bool) -> int:
        if total > target:
            return 0
        if pos == len(digits):
            return int(total == target)
        limit = digits[pos] if tight else 9
        return sum(go(pos + 1, total + d, tight and d == limit) for d in range(limit + 1))

    return go(0, 0, True)

def brute(n: int, target: int) -> int:
    return sum(1 for x in range(n + 1) if sum(map(int, str(x))) == target)

assert all(count_digit_sum(n, t) == brute(n, t) for n in (0, 9, 57, 1000, 4321) for t in range(0, 15))
from math import comb
assert count_digit_sum(10**18, 9) == comb(26, 9)              # instant, despite N = 10^18
# (stars and bars: 9 units spread over 18 digit positions = C(9 + 17, 17))
```

For "count in [L, R]", compute `f(R) − f(L − 1)`. Leading zeros need an extra `started` flag when the property depends on them (e.g., "no repeated digits").

### 24.2 Monotonic-deque optimisation

When a DP transition is `dp[i] = best of dp[j] for j in a sliding window + something`, scanning the window makes it O(n·k). Keep the window's candidates in a **monotonic deque** ([Monotonic Deque — Sliding Window Max/Min](#95-monotonic-deque--sliding-window-maxmin)) and each step becomes O(1) amortized.

```python
from collections import deque

def max_result(nums: list[int], k: int) -> int:
    """LeetCode 1696 (Jump Game VI): dp[i] = nums[i] + max(dp[i-k..i-1])."""
    dp, dq = [0] * len(nums), deque([0])           # dq holds indices, dp values decreasing
    dp[0] = nums[0]
    for i in range(1, len(nums)):
        if dq[0] < i - k:
            dq.popleft()                           # fell out of the window
        dp[i] = nums[i] + dp[dq[0]]
        while dq and dp[dq[-1]] <= dp[i]:
            dq.pop()                               # dominated: older and not better
        dq.append(i)
    return dp[-1]

assert max_result([1, -1, -2, 4, -7, 3], 2) == 7
assert max_result([10, -5, -2, 4, 0, 3], 3) == 17
```

Other transition speed-ups worth naming (rare in interviews): convex hull trick (linear functions), divide-and-conquer optimisation, Knuth's optimisation for some interval DPs.

### 24.3 Meet in the middle

Subsets of 40 items is 2⁴⁰ ≈ 10¹² — too many. Split the items in half, enumerate the 2²⁰ subset sums of each half, sort one side, and for each sum on the other side **binary-search** for the best partner. O(2^(n/2) · n).

```python
from bisect import bisect_left

def closest_subsequence_sum(nums: list[int], goal: int) -> int:
    """LeetCode 1755: min |subset sum − goal|."""
    def sums(arr):
        out = [0]
        for x in arr:
            out += [s + x for s in out]
        return out
    left, right = sums(nums[: len(nums) // 2]), sorted(sums(nums[len(nums) // 2:]))
    best = abs(goal)
    for s in left:
        i = bisect_left(right, goal - s)
        for j in (i - 1, i):
            if 0 <= j < len(right):
                best = min(best, abs(s + right[j] - goal))
    return best

assert closest_subsequence_sum([5, -7, 3, 5], 6) == 0
assert closest_subsequence_sum([7, -9, 15, -2], -5) == 1
assert closest_subsequence_sum([1, 2, 3], -7) == 7
```

### 24.4 DP over a DAG and memoized DFS

Any DP is a shortest/longest path problem on the DAG of its states. When the dependency order isn't obvious (a grid where you can move to any larger neighbour), **memoized DFS** computes states in the right order automatically — no need to find a topological order by hand.

```python
from functools import cache

def longest_increasing_path(matrix: list[list[int]]) -> int:
    rows, cols = len(matrix), len(matrix[0])

    @cache
    def from_cell(r: int, c: int) -> int:
        best = 1
        for nr, nc in ((r + 1, c), (r - 1, c), (r, c + 1), (r, c - 1)):
            if 0 <= nr < rows and 0 <= nc < cols and matrix[nr][nc] > matrix[r][c]:
                best = max(best, 1 + from_cell(nr, nc))
        return best

    return max(from_cell(r, c) for r in range(rows) for c in range(cols))

assert longest_increasing_path([[9, 9, 4], [6, 6, 8], [2, 1, 1]]) == 4      # 1 → 2 → 6 → 9
assert longest_increasing_path([[3, 4, 5], [3, 2, 6], [2, 2, 1]]) == 4
```

"Strictly increasing" is what makes the state graph acyclic. With `>=` the recursion would loop — a quick check for any memoized DFS.

**Practice:** [Numbers At Most N Given Digit Set](https://leetcode.com/problems/numbers-at-most-n-given-digit-set/) · [Number of Digit One](https://leetcode.com/problems/number-of-digit-one/) · [Count Numbers with Unique Digits](https://leetcode.com/problems/count-numbers-with-unique-digits/) · [Jump Game VI](https://leetcode.com/problems/jump-game-vi/) · [Constrained Subsequence Sum](https://leetcode.com/problems/constrained-subsequence-sum/) · [Closest Subsequence Sum](https://leetcode.com/problems/closest-subsequence-sum/) · [Partition Array Into Two Arrays to Minimize Sum Difference](https://leetcode.com/problems/partition-array-into-two-arrays-to-minimize-sum-difference/) · [Longest Increasing Path in a Matrix](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/)

---

## 25. Ordered sets, balanced BSTs and skip lists


### 25.1 Why you sometimes need an ordered set

A hash set answers "is x present?"; a heap answers "what's the smallest?". Some problems need **both dynamic updates and order queries**: "the largest value ≤ x", "how many values lie in [a, b]", "the median of a changing window". That's an **ordered set**: Java's `TreeMap`/`TreeSet`, C++'s `std::set`/`std::map`, both balanced BSTs with O(log n) insert, delete, floor and ceiling.

| Structure | How it stays balanced | Notes |
|---|---|---|
| AVL tree | Heights of children differ by ≤ 1; rotations after each change | Strictly balanced → fastest lookups |
| Red-black tree | Colouring rules keep the longest path ≤ 2× the shortest | Fewer rotations; behind Java `TreeMap`, C++ `std::map`, Linux's scheduler |
| Treap | BST by key + heap by a random priority | Simple to code; balanced in expectation |
| Skip list | Sorted linked lists in levels; each node promoted with probability ½ | Balanced in expectation; easy to make concurrent — used by Redis sorted sets and Java's `ConcurrentSkipListMap` |
| B-tree | Many keys per node, all leaves at the same depth | Built for disks — database indexes |

You won't be asked to code a red-black tree. You may be asked **which structure** to use, and in Python you need a workaround, because the standard library has no ordered set.

### 25.2 Python workarounds

- **`bisect` on a sorted list**: lookups are O(log n), but inserts and deletes are O(n) because elements shift. Fine up to ~10⁵ operations in practice (the shifting is a fast memory move).
- **`sortedcontainers.SortedList`** (third-party, allowed on LeetCode): O(log n)-ish inserts and deletes, plus `bisect_left`, indexing and `irange`. Say if you're relying on it.
- **Two heaps** for running medians ([Pattern 2: Two Heaps](#144-pattern-2-two-heaps)), with lazy deletion for sliding windows.
- **Fenwick tree over compressed values** for "how many ≤ x" with updates ([Fenwick tree (binary indexed tree)](#222-fenwick-tree-binary-indexed-tree)).

```python
from bisect import bisect_left, bisect_right, insort

class MyCalendar:
    """LeetCode 729: book [start, end) if it doesn't overlap — floor/ceiling on a sorted list."""
    def __init__(self):
        self.starts, self.ends = [], []

    def book(self, start: int, end: int) -> bool:
        i = bisect_right(self.starts, start)
        if i > 0 and self.ends[i - 1] > start:           # previous booking runs into us
            return False
        if i < len(self.starts) and self.starts[i] < end:  # next booking starts before we end
            return False
        self.starts.insert(i, start)
        self.ends.insert(i, end)
        return True

cal = MyCalendar()
assert [cal.book(*b) for b in [(10, 20), (15, 25), (20, 30), (5, 10), (5, 11)]] == \
    [True, False, True, True, False]

window = []
for x in [5, 1, 4]:
    insort(window, x)
assert window == [1, 4, 5] and bisect_left(window, 4) == 1      # rank of 4
```

### 25.3 A skip list from scratch

Asked occasionally on its own (LeetCode 1206) and a good way to show you understand "balanced in expectation". Each node gets a random height; searching starts at the top level and drops down whenever the next node would overshoot. Expected O(log n) for search, insert and delete.

```python
import random

class Node:
    __slots__ = ("val", "next")
    def __init__(self, val, height):
        self.val, self.next = val, [None] * height

class Skiplist:
    MAX_LEVEL = 16

    def __init__(self, seed: int = 0):
        self.head = Node(float("-inf"), self.MAX_LEVEL)
        self.level = 1
        self.rng = random.Random(seed)

    def _random_height(self) -> int:
        h = 1
        while h < self.MAX_LEVEL and self.rng.random() < 0.5:
            h += 1
        return h

    def _predecessors(self, target) -> list[Node]:
        """For each level, the last node with val < target."""
        update, node = [self.head] * self.MAX_LEVEL, self.head
        for lvl in reversed(range(self.level)):
            while node.next[lvl] and node.next[lvl].val < target:
                node = node.next[lvl]
            update[lvl] = node
        return update

    def search(self, target) -> bool:
        nxt = self._predecessors(target)[0].next[0]
        return nxt is not None and nxt.val == target

    def add(self, num) -> None:
        update, h = self._predecessors(num), self._random_height()
        self.level = max(self.level, h)
        node = Node(num, h)
        for lvl in range(h):
            node.next[lvl], update[lvl].next[lvl] = update[lvl].next[lvl], node

    def erase(self, num) -> bool:
        update = self._predecessors(num)
        node = update[0].next[0]
        if node is None or node.val != num:
            return False
        for lvl in range(len(node.next)):
            if update[lvl].next[lvl] is node:
                update[lvl].next[lvl] = node.next[lvl]
        return True

    def items(self) -> list:
        out, node = [], self.head.next[0]
        while node:
            out.append(node.val)
            node = node.next[0]
        return out

sl = Skiplist()
for x in [30, 10, 20, 10, 50]:
    sl.add(x)
assert sl.items() == [10, 10, 20, 30, 50]
assert sl.search(20) and not sl.search(25)
assert sl.erase(10) and sl.items() == [10, 20, 30, 50]
assert not sl.erase(99)

big = Skiplist(seed=1)
values = random.Random(2).sample(range(10**6), 5000)
for v in values:
    big.add(v)
assert big.items() == sorted(values) and big.level <= Skiplist.MAX_LEVEL
```

**Practice:** [My Calendar I](https://leetcode.com/problems/my-calendar-i/) · [Design Skiplist](https://leetcode.com/problems/design-skiplist/) · [Sliding Window Median](https://leetcode.com/problems/sliding-window-median/) · [Contains Duplicate III](https://leetcode.com/problems/contains-duplicate-iii/) · [Data Stream as Disjoint Intervals](https://leetcode.com/problems/data-stream-as-disjoint-intervals/)

---

## 26. Randomized and streaming algorithms


### 26.1 Shuffling and weighted picks

- **Fisher–Yates shuffle:** for i from the end down, swap `a[i]` with a random index in `[0, i]`. Every permutation is equally likely, O(n). The common bug — swapping with any index in `[0, n)` — produces n^n equally likely swap sequences, which can't divide evenly into n! permutations, so some orders come up more often.
- **Weighted random pick:** build prefix sums of the weights, draw a random number in `[0, total)`, and binary-search the prefix sums.

```python
import random
from bisect import bisect_right
from collections import Counter
from itertools import accumulate

def fisher_yates(a: list, rng: random.Random) -> list:
    a = a[:]
    for i in range(len(a) - 1, 0, -1):
        j = rng.randint(0, i)
        a[i], a[j] = a[j], a[i]
    return a

class WeightedPicker:
    def __init__(self, weights: list[int], rng: random.Random):
        self.prefix, self.rng = list(accumulate(weights)), rng

    def pick(self) -> int:
        return bisect_right(self.prefix, self.rng.random() * self.prefix[-1])

rng = random.Random(42)
counts = Counter(tuple(fisher_yates([1, 2, 3], rng)) for _ in range(60_000))
assert len(counts) == 6 and all(9_000 < c < 11_000 for c in counts.values())    # all 6 orders ~equally
picks = Counter(WeightedPicker([1, 3], rng).pick() for _ in range(40_000))
assert 0.72 < picks[1] / 40_000 < 0.78                                           # ≈ 3/4
```

### 26.2 Reservoir sampling

Pick k items uniformly from a stream of **unknown length** in one pass and O(k) memory: keep the first k; for the i-th item (1-based, i > k), replace a random slot with probability k/i. Every item ends up in the sample with probability k/n. Used for "random node from a linked list" and for sampling logs or events in real systems.

```python
import random
from collections import Counter

def reservoir_sample(stream, k: int, rng: random.Random) -> list:
    sample = []
    for i, item in enumerate(stream, 1):
        if i <= k:
            sample.append(item)
        else:
            j = rng.randint(1, i)
            if j <= k:
                sample[j - 1] = item
    return sample

rng = random.Random(7)
hits = Counter(x for _ in range(20_000) for x in reservoir_sample(range(10), 3, rng))
assert all(5_400 < hits[x] < 6_600 for x in range(10))     # each item chosen ≈ 3/10 of the time
```

### 26.3 Majority vote and O(1)-random sets

- **Boyer–Moore majority vote:** find the element appearing more than n/2 times in O(n) time and O(1) space by pairing off different elements — whatever survives is the only possible majority (verify with a second pass if a majority isn't guaranteed). The generalisation with k − 1 candidates finds everything appearing more than n/k times.
- **Insert, delete, getRandom in O(1):** a list for random access plus a dict from value to index; delete swaps the element with the last one before popping.

```python
import random

def majority(nums: list[int]) -> int:
    candidate, count = None, 0
    for x in nums:
        if count == 0:
            candidate = x
        count += 1 if x == candidate else -1
    return candidate

class RandomizedSet:
    def __init__(self, rng: random.Random):
        self.items, self.index, self.rng = [], {}, rng

    def insert(self, val) -> bool:
        if val in self.index:
            return False
        self.index[val] = len(self.items)
        self.items.append(val)
        return True

    def remove(self, val) -> bool:
        if val not in self.index:
            return False
        i, last = self.index[val], self.items[-1]
        self.items[i], self.index[last] = last, i          # move the last element into the hole
        self.items.pop()
        del self.index[val]
        return True

    def get_random(self):
        return self.rng.choice(self.items)

assert majority([2, 2, 1, 1, 1, 2, 2]) == 2
rs = RandomizedSet(random.Random(0))
assert rs.insert(1) and not rs.insert(1) and rs.insert(2) and rs.remove(1) and not rs.remove(1)
assert rs.get_random() == 2 and rs.items == [2] and rs.index == {2: 0}
```

**Randomization as a defence:** randomized quickselect and quicksort ([Quickselect](#65-quickselect)) pick random pivots so no fixed input triggers the O(n²) worst case; hash tables in production use randomized hash seeds for the same reason. The streaming sketches in [Space-efficient structures at scale](#18-space-efficient-structures-at-scale) (Bloom filters) and in the HLD guide (HyperLogLog, Count-Min) trade a little accuracy for tiny memory.

**Practice:** [Shuffle an Array](https://leetcode.com/problems/shuffle-an-array/) · [Random Pick with Weight](https://leetcode.com/problems/random-pick-with-weight/) · [Linked List Random Node](https://leetcode.com/problems/linked-list-random-node/) · [Random Pick Index](https://leetcode.com/problems/random-pick-index/) · [Majority Element](https://leetcode.com/problems/majority-element/) · [Majority Element II](https://leetcode.com/problems/majority-element-ii/) · [Insert Delete GetRandom O(1)](https://leetcode.com/problems/insert-delete-getrandom-o1/)

---

## 27. Pattern recognition cheat sheet

Read the problem, spot the signal, reach for the technique. When two rows match, the constraints ([Reading the constraints](#22-reading-the-constraints)) usually decide.

| Signal in the problem | Technique | Where |
|---|---|---|
| Sorted array; pair or triple with a target sum | Two pointers | [Pattern 2: Two Pointers](#33-pattern-2-two-pointers) |
| Longest/shortest subarray or substring with a condition | Sliding window | [Pattern 3: Sliding Window](#34-pattern-3-sliding-window) |
| Many range-sum queries; "subarray sum equals k" | Prefix sums (+ hash map of counts) | [Pattern 1: Prefix Sums](#32-pattern-1-prefix-sums) |
| Range queries **with updates** | Fenwick tree / segment tree | [Range queries: Fenwick tree, segment tree, sparse table](#22-range-queries-fenwick-tree-segment-tree-sparse-table) |
| "Have I seen this?", counts, grouping by a key | Hash map / set | [Hashing](#5-hashing) |
| Sorted input, or "minimum value such that…" monotonic answer | Binary search (on the answer) | [Binary search](#7-binary-search) |
| Next greater/smaller element; histogram areas | Monotonic stack | [Monotonic Stack — Next Greater / Smaller](#86-monotonic-stack--next-greater--smaller) |
| Sliding window max/min | Monotonic deque | [Monotonic Deque — Sliding Window Max/Min](#95-monotonic-deque--sliding-window-maxmin) |
| k largest/smallest, merge k sorted, running median | Heap | [Heaps](#14-heaps) |
| All subsets / permutations / combinations; n ≤ 20 | Backtracking | [Recursion and backtracking](#11-recursion-and-backtracking) |
| Tree depth, paths, subtree properties | DFS (recursion), tree DP | [Trees and BSTs](#12-trees-and-bsts) |
| Level order, shortest path in an unweighted graph or grid | BFS | [Graphs](#15-graphs) |
| Dependencies, ordering, "can all courses be finished?" | Topological sort | [Topological Sorting](#153-topological-sorting) |
| Connected groups, merging sets, cycle in an undirected graph | Union-find | [Disjoint Set Union (DSU) / Union-Find](#155-disjoint-set-union-dsu--union-find) |
| Weighted shortest path | Dijkstra (0-1 BFS if weights are 0/1) | [Shortest Path Algorithms](#156-shortest-path-algorithms), [0-1 BFS](#231-0-1-bfs) |
| Prefix matching, autocomplete, word search on a board | Trie | [Trie](#13-trie) |
| Intervals: merge, schedule, minimum rooms | Sort + sweep or greedy | [Pattern 1: Interval Scheduling](#162-pattern-1-interval-scheduling) |
| "Number of ways", "min/max cost", overlapping subproblems | Dynamic programming | [Dynamic programming](#17-dynamic-programming) |
| Count integers ≤ N with a digit property | Digit DP | [Digit DP](#241-digit-dp) |
| n ≤ 20 with a "visited set" in the state | Bitmask DP | [Bitmask & Probability DP](#1713-bitmask--probability-dp) |
| n ≈ 40, subset sums | Meet in the middle | [Meet in the middle](#243-meet-in-the-middle) |
| Pattern occurrence in a text, repeated prefixes | KMP / Z-function / rolling hash | [String matching: KMP, Z-function, Manacher](#21-string-matching-kmp-z-function-manacher) |
| Pairs that cancel, single unpaired value, subsets as integers | Bit manipulation | [Bit manipulation](#19-bit-manipulation) |
| Answer "modulo 10⁹ + 7", counting arrangements | Modular arithmetic, nCr | [Modular arithmetic and counting](#202-modular-arithmetic-and-counting) |
| Floor/ceiling with inserts and deletes | Ordered set (or bisect) | [Ordered sets, balanced BSTs and skip lists](#25-ordered-sets-balanced-bsts-and-skip-lists) |
| Random sample from a stream; uniform shuffle | Reservoir sampling, Fisher–Yates | [Randomized and streaming algorithms](#26-randomized-and-streaming-algorithms) |
| Linked list cycle or middle | Fast and slow pointers | [Linked lists](#10-linked-lists) |
