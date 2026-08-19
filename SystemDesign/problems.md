## How to Approach a Design Problem



 1: Clarify the Requirements - functional and non-functional.   
 2: Estimate the Scale - BOTE calculation, QPS, data size, etc.   
 3: Identify the Core Components - databases, caches, load balancers, etc.   
 4: Define the API - input, output, and error handling.  
 5: Design the Data Model - schema, relationships, and indexing.  
 6: Write LLD - class diagrams, sequence diagrams, etc (optional).   
 7: Identify Bottlenecks, Failure Modes, Trade-offs.  

## SDE2 design interview loop

Use the first five minutes to turn ambiguity into a contract: state the user, the core user journeys, explicit non-goals, traffic shape, data volume, latency/availability expectations, and consistency requirements. Only then draw components.

Reusable artifact order:

1. Requirements and non-goals.
2. Back-of-the-envelope estimates: requests per second, storage growth, read/write ratio, and hot keys.
3. API surface and data model.
4. High-level architecture and main request flow.
5. Bottlenecks, failure modes, observability, and trade-offs.

For each design decision, say what it optimizes and what it makes worse. SDE2 signal comes from defending constraints, not from naming components.

### Requirements, APIs, and estimates checklist

Before architecture, pin down the contract:

- Requirements: name the primary actor, top two user journeys, explicit non-goals, latency target, availability target, consistency expectation, privacy/security constraint, and operational visibility needed.
- API surface: model nouns first, then standard operations. Use custom verbs only when the action cannot be represented cleanly as create, get, list, update, or delete.
- Estimate loop: state assumption, formula, result, and design consequence for QPS, read/write ratio, storage growth, hot-key risk, bandwidth, and cacheable working set.
- Interview defense: when an assumption is uncertain, give a reasonable range and say which component would change if the high end is true.


# Data Structure Design

Compose existing structures (hashmap + linked list + heap + array) to support a custom interface in optimal time per op. Most of these problems are about picking the **right two structures** that complement each other.

## Patterns covered

- Hashmap + doubly linked list (LRU)
- Hashmap of counters + buckets (LFU, AllOne)
- Array + hashmap (RandomizedSet)
- Stack composition (MinStack, queue from stacks)
- Trie-based design (autocomplete, dictionary)
- Heap + lazy deletion
- Stream / median / windowed structures
- Iterators
- Hashmap bookkeeping (track running state)

---

## Pattern 1: Hashmap + Doubly Linked List

The workhorse for **O(1) get + O(1) update of recency** designs.

- Hashmap: `key → node`. Lookup in O(1).
- DLL: ordered by recency. Move a node to head, remove tail in O(1).

### LRU Cache

Hashmap (`key → node`) for O(1) lookup + a doubly linked list ordered by recency: most-recent at the head, least-recent at the tail. `get` moves a node to the head; `put` inserts at the head and evicts the tail when over capacity. The sentinel head/tail nodes remove all the edge cases.

```python
class Node:
    def __init__(self, k, v):
        self.k, self.v = k, v
        self.prev = self.next = None

class LRUCache:
    def __init__(self, cap):
        self.cap = cap
        self.map = {}
        self.head = Node(0, 0)          # sentinels
        self.tail = Node(0, 0)
        self.head.next = self.tail
        self.tail.prev = self.head

    def _remove(self, n):
        n.prev.next = n.next; n.next.prev = n.prev

    def _add_front(self, n):
        n.next = self.head.next; n.prev = self.head
        self.head.next.prev = n; self.head.next = n

    def get(self, k):
        if k not in self.map: return -1
        n = self.map[k]
        self._remove(n); self._add_front(n)     # mark as most-recent
        return n.v

    def put(self, k, v):
        if k in self.map:
            self._remove(self.map[k])
        n = Node(k, v)
        self.map[k] = n
        self._add_front(n)
        if len(self.map) > self.cap:            # evict least-recent (tail)
            lru = self.tail.prev
            self._remove(lru); del self.map[lru.k]
```

- [LRU Cache](https://leetcode.com/problems/lru-cache/)
- [Design Browser History](https://leetcode.com/problems/design-browser-history/) (two stacks also work)

---

## Pattern 2: LFU Cache

Hashmap of `freq → DLL` plus `key → (value, freq, node)`. Track `min_freq` so eviction is O(1).

```
freq=1 → [keyA] [keyB]            (oldest at tail)
freq=2 → [keyC]
freq=3 → [keyD] [keyE]
```

On `get(k)`: bump `k`'s frequency, move it from `freq[f]` to `freq[f+1]` (add at head). If `freq[min_freq]` is now empty and `min_freq == f`, increment `min_freq`.

On evict: remove tail of `freq[min_freq]`.

- [LFU Cache](https://leetcode.com/problems/lfu-cache/)
- [All O`one Data Structure](https://leetcode.com/problems/all-oone-data-structure/) (DLL of buckets, each bucket = set of keys with same count)

---

## Pattern 3: Array + Hashmap — RandomizedSet

Insert / remove / `getRandom` all in O(1).

- Array `vals` stores elements.
- Hashmap `pos`: `value → index in vals`.

`remove(x)`: swap `vals[pos[x]]` with `vals[-1]`, pop, fix `pos`.

```python
import random

class RandomizedSet:
    def __init__(self):
        self.vals = []
        self.pos = {}

    def insert(self, x):
        if x in self.pos: return False
        self.pos[x] = len(self.vals)
        self.vals.append(x)
        return True

    def remove(self, x):
        if x not in self.pos: return False
        i = self.pos[x]
        last = self.vals[-1]
        self.vals[i] = last
        self.pos[last] = i
        self.vals.pop()
        del self.pos[x]
        return True

    def getRandom(self):
        return random.choice(self.vals)
```

- [Insert Delete GetRandom O(1)](https://leetcode.com/problems/insert-delete-getrandom-o1/)
- [Insert Delete GetRandom O(1) — Duplicates allowed](https://leetcode.com/problems/insert-delete-getrandom-o1-duplicates-allowed/) (value → set of indices)

---

## Pattern 4: Stack Composition

### MinStack — O(1) min

Pair each value with the running min, or maintain two stacks. See [linear-data-structures.md](../DSA/linear-data-structures.md#stacks).

- [Min Stack](https://leetcode.com/problems/min-stack/)
- [Max Stack](https://leetcode.com/problems/max-stack/) (use sorted container or two stacks + lazy delete)

### Stack with increment

Keep a lazy `inc[i]` array — `increment(k, v)` marks `inc[k-1] += v`; `pop` applies and pushes the carry down. O(1) per op.

- [Design a Stack With Increment Operation](https://leetcode.com/problems/design-a-stack-with-increment-operation/)

### Queue from stacks / Stack from queues

See [linear-data-structures.md](../DSA/linear-data-structures.md#stacks).

---

## Pattern 5: Trie-based Design

Trie powers fast prefix queries. See [non-linear-data-structures.md](../DSA/non-linear-data-structures.md#trie).

### Use cases

- Autocomplete / typeahead
- Word dictionary with wildcard search (`.`)
- Search history sorted by frequency
- Replace words

### Common design problems

- [Implement Trie (Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree/)
- [Add and Search Word — Data Structure](https://leetcode.com/problems/design-add-and-search-words-data-structure/)
- [Design Search Autocomplete System](https://leetcode.com/problems/design-search-autocomplete-system/)
- [Magic Dictionary](https://leetcode.com/problems/implement-magic-dictionary/)

---

## Pattern 6: Heap + Lazy Deletion

When you can't directly remove an arbitrary heap element, mark it deleted in a side dict and pop stale entries on `peek` / `pop`.

```python
class LazyHeap:
    def __init__(self):
        self.h = []
        self.removed = Counter()

    def push(self, x):
        heapq.heappush(self.h, x)

    def remove(self, x):
        self.removed[x] += 1

    def _clean(self):
        while self.h and self.removed[self.h[0]] > 0:
            self.removed[self.h[0]] -= 1
            heapq.heappop(self.h)

    def top(self):
        self._clean()
        return self.h[0]
```

- [Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/)
- [Sliding Window Median](https://leetcode.com/problems/sliding-window-median/)
- [Maximum Frequency Stack](https://leetcode.com/problems/maximum-frequency-stack/) (hashmap of stacks by frequency)

---

## Pattern 7: Twitter-style / Newsfeed

`user → list of (timestamp, tweet)`. For `getNewsFeed(user)`:

- Collect heads of own + followed tweet lists.
- Heap-merge by timestamp, take top 10.

```python
class Twitter:
    def __init__(self):
        self.time = 0
        self.tweets = defaultdict(list)             # user -> [(time, tid), ...]
        self.follows = defaultdict(set)

    def postTweet(self, u, tid):
        self.tweets[u].append((self.time, tid))
        self.time += 1

    def follow(self, u, v):    self.follows[u].add(v)
    def unfollow(self, u, v):  self.follows[u].discard(v)

    def getNewsFeed(self, u):
        users = self.follows[u] | {u}
        h = []
        for v in users:
            for t, tid in self.tweets[v][-10:]:
                heapq.heappush(h, (-t, tid))
        return [tid for _, tid in heapq.nsmallest(10, h)]
```

- [Design Twitter](https://leetcode.com/problems/design-twitter/)

---

## Pattern 8: Iterators

Wrap an existing data source; expose `next()` / `hasNext()`.

### Peeking Iterator

Cache the next value when peeked.

```python
class PeekingIterator:
    def __init__(self, it):
        self.it = it
        self.cached = None
        self.has_cached = False

    def peek(self):
        if not self.has_cached:
            self.cached = next(self.it)
            self.has_cached = True
        return self.cached

    def next(self):
        if self.has_cached:
            self.has_cached = False
            return self.cached
        return next(self.it)

    def hasNext(self):
        if self.has_cached: return True
        try:
            self.cached = next(self.it); self.has_cached = True
            return True
        except StopIteration:
            return False
```

- [Peeking Iterator](https://leetcode.com/problems/peeking-iterator/)
- [Flatten Nested List Iterator](https://leetcode.com/problems/flatten-nested-list-iterator/) (stack of iterators)
- [Binary Search Tree Iterator](https://leetcode.com/problems/binary-search-tree-iterator/) (in-order via stack)
- [Zigzag Iterator](https://leetcode.com/problems/zigzag-iterator/)

---

## Pattern 9: Stream Structures

Process elements one at a time; answer running queries.

### Moving average

Deque of last `k` values; running sum.

### Hit Counter (last 5 min)

Deque of `(timestamp, count)`. Pop stale entries on each call.

```python
from collections import deque

class HitCounter:
    def __init__(self):
        self.q = deque()                            # (ts, count)
        self.total = 0

    def hit(self, ts):
        if self.q and self.q[-1][0] == ts:
            self.q[-1] = (ts, self.q[-1][1] + 1)
        else:
            self.q.append((ts, 1))
        self.total += 1
        self._evict(ts)

    def getHits(self, ts):
        self._evict(ts)
        return self.total

    def _evict(self, ts):
        while self.q and self.q[0][0] <= ts - 300:
            self.total -= self.q[0][1]
            self.q.popleft()
```

- [Moving Average from Data Stream](https://leetcode.com/problems/moving-average-from-data-stream/)
- [Design Hit Counter](https://leetcode.com/problems/design-hit-counter/)
- [Logger Rate Limiter](https://leetcode.com/problems/logger-rate-limiter/)
- [Snapshot Array](https://leetcode.com/problems/snapshot-array/)
- [Time Based Key-Value Store](https://leetcode.com/problems/time-based-key-value-store/) (hashmap + sorted list + BS)

---

## Pattern 10: Hashmap Bookkeeping

Many design problems are just "keep one or two hashmaps in sync so each query reads a value directly." The skill is choosing keys that match the query you're asked.

### Design Underground System

Two maps: `checkin[id] = (station, t)` for in-flight trips, and `totals[(start, end)] = [sum_time, count]` for averages.

```python
class UndergroundSystem:
    def __init__(self):
        self.checkin = {}                            # id -> (station, t)
        self.totals  = defaultdict(lambda: [0, 0])   # (a, b) -> [sum, count]

    def checkIn(self, id, station, t):
        self.checkin[id] = (station, t)

    def checkOut(self, id, station, t):
        start, t0 = self.checkin.pop(id)
        agg = self.totals[(start, station)]
        agg[0] += t - t0; agg[1] += 1

    def getAverageTime(self, start, end):
        total, count = self.totals[(start, end)]
        return total / count
```

- [Design Underground System](https://leetcode.com/problems/design-underground-system/)
- [Design a Leaderboard](https://leetcode.com/problems/design-a-leaderboard/) (hashmap `player → score`; sort or heap for top-K)
- [Design Authentication Manager](https://leetcode.com/problems/design-authentication-manager/) (hashmap `token → expiry`)
- [Design Tic-Tac-Toe](https://leetcode.com/problems/design-tic-tac-toe/) (per-row/col/diagonal counters, ±1 per move)
- [Design HashMap](https://leetcode.com/problems/design-hashmap/) / [Design HashSet](https://leetcode.com/problems/design-hashset/) (implement the primitive — see [python.md](../DSA/python.md#5-dictionary))

---

## Pattern 11: Union-Find (Disjoint Set Union)

Not strictly a "design" problem, but commonly required for design questions about dynamic connectivity. See [non-linear-data-structures.md](../DSA/non-linear-data-structures.md#graphs).

- [Number of Connected Components](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/)
- [Accounts Merge](https://leetcode.com/problems/accounts-merge/)
- [Most Stones Removed with Same Row or Column](https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/)

---

## More Design Problems

Common enough to know, but each is really a one-off combo rather than a reusable pattern.

| Problem | Core combo |
|---|---|
| [Design Snake Game](https://leetcode.com/problems/design-snake-game/) | deque (body cells) + set (occupied cells) for O(1) collision check |
| [Design a Text Editor](https://leetcode.com/problems/design-a-text-editor/) | two stacks around the cursor (chars left / chars right) |
| [Design File System](https://leetcode.com/problems/design-file-system/) | hashmap `path → value` (or a trie of path components) |
| [Design a Number Container System](https://leetcode.com/problems/design-a-number-container-system/) | `index → number` map + `number → min-heap of indices` (lazy delete) |
| [Design a Food Rating System](https://leetcode.com/problems/design-a-food-rating-system/) | per-cuisine max-heap of `(-rating, food)` + `food → rating` map (lazy delete) |
| [Design Parking System](https://leetcode.com/problems/design-parking-system/) | three counters, one per slot size |
| [Design Movie Rental System](https://leetcode.com/problems/design-movie-rental-system/) | hashmaps + sorted heaps of `(price, shop, movie)` |

---

## How to attack a design problem

1. **List all operations + required complexity.**
2. **For each op, pick the data structure that gives it natively.**
3. **Find the combination that makes them mutually O(1) (or O(log n)) — usually a hashmap + one of {linked list, heap, array, BST}.**
4. **Watch the invariant.** When two structures point at each other, every mutation must update both.
5. **Build it incrementally** — start with `get` working, then add `put`, then eviction.

### Common pairings

| Need                                  | Combo                                |
|---------------------------------------|--------------------------------------|
| O(1) get + recency order              | Hashmap + Doubly Linked List         |
| O(1) get + frequency order            | Hashmap + Hashmap of DLLs            |
| O(1) random + insert/delete           | Array + Hashmap                      |
| Prefix queries                        | Trie                                 |
| Top-K / running extremes              | Heap (+ lazy delete)                 |
| Range queries                         | BIT / Segment Tree (see [segment-tree.md](segment-tree.md)) |
| Track running state / averages        | One or two hashmaps (bookkeeping)    |
| Dynamic connectivity                  | Union-Find                           |
| Versioned key-value                   | Hashmap of sorted lists + binary search |

---

## Quick reference

| Problem               | Core combo                                        |
|-----------------------|---------------------------------------------------|
| LRU                   | Hashmap + DLL                                     |
| LFU / AllOne          | Hashmap + Hashmap of DLLs + `min_freq`            |
| RandomizedSet         | Array + Hashmap (swap-with-last on delete)        |
| MinStack              | Stack of (val, running min)                       |
| FreqStack             | Hashmap of stacks per frequency + `max_freq`      |
| Twitter feed          | Per-user list + heap merge                        |
| Hit Counter           | Deque of (timestamp, count) + running total       |
| Snapshot Array        | Per-index list of (version, value) + BS           |
