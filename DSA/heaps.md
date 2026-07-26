# Heaps

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

### Stored as an array, not with pointers

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

### Why an array (and why heaps over BST for priority queues)

- **Height is `log n`** — it's a complete tree, so operations are `O(log n)`.
- **No pointer memory** — no `left`/`right`/`parent` references to store.
- **Cache friendly** — contiguous array → better locality of reference than a pointer-based BST.
- **Build in `O(n)`** — `heapify` builds a heap in linear time vs `O(n log n)` to build a balanced BST.

That combination (fast build, no pointers, cache locality) is why **priority queues use heaps, not BSTs**.

---

## Heaps in Python

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

---

## Core operations (implemented from scratch)

Understanding `heapq` means understanding these. All examples are **min-heaps**.

### 1. Heapify (sift-down) — `O(log n)`

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

### 2. Build heap — `O(n)`

Heapify every internal node, starting from the **last non-leaf** (`n//2 - 1`) up to the root. Leaves are already valid heaps, so they're skipped. The work telescopes to `O(n)`, not `O(n log n)`.

```python
def build_heap(arr):
    n = len(arr)
    for i in range(n // 2 - 1, -1, -1):  # last non-leaf → root
        heapify(arr, n, i)
```

### 3. Insert (sift-up) — `O(log n)`

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

### 4. Extract-min — `O(log n)`

Swap root with the last element, pop it off, then sift the new root down.

```python
def pop_min(arr):
    arr[0], arr[-1] = arr[-1], arr[0]
    mn = arr.pop()
    heapify(arr, len(arr), 0)
    return mn
```

### 5. Delete at index — `O(log n)`

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

### 6. Heap sort — `O(n log n)`

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

### Complexity summary

| Operation      | Time       |
|----------------|------------|
| peek min/max   | `O(1)`     |
| push / insert  | `O(log n)` |
| pop / extract  | `O(log n)` |
| delete         | `O(log n)` |
| build heap     | `O(n)`     |
| heap sort      | `O(n log n)` |

---

## Pattern 1: Top-K

**When:** "k largest / smallest / most frequent / closest" — anything asking for the top `k` out of `n`.

**Key trick:** to keep the **k largest**, use a **min-heap of size k**. The smallest of your top-k sits at the root; whenever the heap exceeds `k`, pop it. Runs in `O(n log k)` — cheaper than sorting (`O(n log n)`) when `k` is small.

> For the **k smallest**, flip it: use a **max-heap of size k** (negate values).

### Example: Top K Frequent Elements

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

### Example: Kth largest element

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

### K-way merge (a Top-K variant)

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

### Problems

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

---

## Pattern 2: Two Heaps

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

### Example: Find Median from a Data Stream

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

### Problems

- [Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/)
- [Sliding Window Median](https://leetcode.com/problems/sliding-window-median/)

---

## Pitfalls

| Pitfall                                        | Fix                                                   |
|------------------------------------------------|-------------------------------------------------------|
| Max-heap with `heapq`                          | Negate the value, or wrap as `(-key, item)`           |
| `heappush` on a list that isn't heap-ordered   | `heapify(list)` first                                 |
| Comparing un-orderable objects on a tie        | Add an index/counter as a tiebreaker: `(key, i, obj)` |
| Two-heaps drifting out of balance              | Always route through one heap, then rebalance sizes   |
