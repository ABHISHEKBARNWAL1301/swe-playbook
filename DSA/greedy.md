# Greedy

Make the locally optimal choice at every step. Works when the problem has the **greedy choice property** — local optimum leads to global optimum — and **optimal substructure**.

Greedy is NOT always correct. If you can construct a counterexample where greedy fails, you need DP. See the robber example in [dp.md](dp.md).

## How to recognize a greedy problem

- "Maximum / minimum number of …"
- "Earliest / latest …"
- Sort + scan solves it
- Interchange argument: swapping two adjacent choices never improves the answer




## Contents

- [Pattern 1: Interval Scheduling](#pattern-1-interval-scheduling)
- [Pattern 2: Sweep Line Algorithm](#pattern-2-sweep-line-algorithm)
- [Pattern 3: Jump Game](#pattern-3-jump-game)
- [Pattern 4: Sorted + Two Pointers](#pattern-4-sorted--two-pointers)
- [Pattern 5: Greedy + Heap](#pattern-5-greedy--heap) (incl. Huffman)

---

## Pattern 1: Interval Scheduling

Sort intervals by start or end time, then scan sequentially to select, merge, or insert intervals.

### Activity Selection — max non-overlapping intervals

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

### Merge overlapping intervals

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

### Insert interval

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

### Common problems

- [Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)
- [Minimum Number of Arrows to Burst Balloons](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/)
- [Merge Intervals](https://leetcode.com/problems/merge-intervals/)
- [Insert Interval](https://leetcode.com/problems/insert-interval/)

---

## Pattern 2: Sweep Line Algorithm

### Basic Knowledge & Core Concept

The **Sweep Line (or Line Sweep)** algorithm conceptualizes a 1D line (or 2D vertical line) sweeping across coordinates/timestamps from left to right (`-∞` to `+∞`).

Instead of comparing every object against every other object ($O(N^2)$), Sweep Line transforms the problem into event-driven processing ($O(N \log N)$):

1. **Decompose into Events**: Break range/interval objects into discrete point events — e.g., an interval `[start, end]` becomes a "Start Event" at `start` and an "End Event" at `end`.
2. **Sort Events**: Sort all events by timestamp/coordinate.
3. **Maintain Active State**: Iterate through sorted events and update a dynamic state (e.g., running total counter, max-heap of active heights, or balanced BST of active segments).

> **Tie-breaking Rule:** When two events occur at the exact same coordinate (e.g., end event of interval A and start event of interval B), event ordering depends on interval boundary rules:
> - **Inclusive intervals `[start, end]`**: process **Start before End** (overlapping at boundaries count as concurrent).
> - **Exclusive intervals `(start, end)`**: process **End before Start** (releasing resources before allocating new ones).

---

### Key Use Cases

#### 1. 1D Concurrency & Max Overlapping (Difference Array / Point Events)

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

#### 2. Two-Pointer Sweep (Separated Start/End Arrays)

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

#### 3. 2D Sweep Line with Dynamic Active Set (Sweep Line + Heap / Sorted Set)

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

### Segregated Problem List

#### 1D Concurrency & Max Overlap (Point Event Counting)
- [Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/) — Min rooms needed for overlapping intervals
- [Car Pooling](https://leetcode.com/problems/car-pooling/) — Capacity checking with point pick-up/drop-off
- [Maximum Population Year](https://leetcode.com/problems/maximum-population-year/) — Max overlapping lifespan range
- [Corporate Flight Bookings](https://leetcode.com/problems/corporate-flight-bookings/) — Prefix sum / difference array on 1D range

#### Multi-Level Overlap & Gap / Free Time Sweep
- [My Calendar I](https://leetcode.com/problems/my-calendar-i/) — Single overlap detection
- [My Calendar II](https://leetcode.com/problems/my-calendar-ii/) — Double booking / triple booking detection
- [Employee Free Time](https://leetcode.com/problems/employee-free-time/) — Finding gap intervals across multiple schedules
- [Describe the Painting](https://leetcode.com/problems/describe-the-painting/) — Sweep line color sum tracking across line segments

#### Advanced 2D / Active Set Sweep Line (Sweep + Heap / BST / Point Queries)
- [The Skyline Problem](https://leetcode.com/problems/the-skyline-problem/) — 2D sweep with active max height heap
- [Number of Flowers in Full Bloom](https://leetcode.com/problems/number-of-flowers-in-full-bloom/) — Point query on interval sweep line
- [Rectangle Area II](https://leetcode.com/problems/rectangle-area-ii/) — 2D sweep line for total union area calculation

---

## Pattern 3: Jump Game

### Can you reach the end?

Track the furthest reachable index. If you ever stand beyond it, return false.

```python
def can_jump(nums):
    reach = 0
    for i, x in enumerate(nums):
        if i > reach: return False
        reach = max(reach, i + x)
    return True
```

### Min jumps to reach end

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

### Common problems

- [Jump Game](https://leetcode.com/problems/jump-game/)
- [Jump Game II](https://leetcode.com/problems/jump-game-ii/)
- [Gas Station](https://leetcode.com/problems/gas-station/)
- [Video Stitching](https://leetcode.com/problems/video-stitching/)

---

## Pattern 4: Sorted + Two Pointers

Sort then greedily pair from both ends.

### Boats to Save People

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

### Common problems

- [Boats to Save People](https://leetcode.com/problems/boats-to-save-people/)
- [Assign Cookies](https://leetcode.com/problems/assign-cookies/)
- [Two City Scheduling](https://leetcode.com/problems/two-city-scheduling/)
- [Minimum Number of Operations to Make Array Continuous](https://leetcode.com/problems/minimum-number-of-operations-to-make-array-continuous/)

---

## Pattern 5: Greedy + Heap

When greedy + sort isn't enough — you need to re-prioritize after each decision.

### Reorganize String

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

### Huffman Coding

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

### Common problems

- [Reorganize String](https://leetcode.com/problems/reorganize-string/)
- [Task Scheduler](https://leetcode.com/problems/task-scheduler/)
- [Maximum Performance of a Team](https://leetcode.com/problems/maximum-performance-of-a-team/)
- [IPO](https://leetcode.com/problems/ipo/)

---

## Quick reference

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
