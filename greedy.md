# Greedy

Make the locally optimal choice at every step. Works when the problem has the **greedy choice property** — local optimum leads to global optimum — and **optimal substructure**.

Greedy is NOT always correct. If you can construct a counterexample where greedy fails, you need DP. See the robber example in [dp.md](dp.md).

## How to recognize a greedy problem

- "Maximum / minimum number of …"
- "Earliest / latest …"
- Sort + scan solves it
- Interchange argument: swapping two adjacent choices never improves the answer


## How to prove greedy is correct (informal)

1. **Greedy choice property**: there's an optimal solution that starts with the greedy choice.
2. **Optimal substructure**: after making the greedy choice, the rest is the same problem at smaller scale.
3. **Interchange (swap) argument**: take any optimal solution; show swapping a non-greedy choice for the greedy one doesn't make it worse.

If you can't prove it, suspect DP.


## Contents

- [Pattern 1: Intervals](#pattern-1-intervals) (scheduling + sweep line)
- [Pattern 2: Jump Game](#pattern-2-jump-game)
- [Pattern 3: Sorted + Two Pointers](#pattern-3-sorted--two-pointers)
- [Pattern 4: Greedy + Heap](#pattern-4-greedy--heap) (incl. Huffman)

---

## Pattern 1: Intervals

The most common greedy family. Sort by something, then scan.

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

Sort by **start**. Scan and merge.

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

### Min meeting rooms — two-pointer sweep

Sort starts and ends separately. Walk both: a start before the next end opens a room, otherwise one frees up. Max concurrent = answer.

```python
def min_meeting_rooms(intervals):
    starts = sorted(s for s, _ in intervals)
    ends   = sorted(e for _, e in intervals)
    rooms = used = i = j = 0
    while i < len(starts):
        if starts[i] < ends[j]:
            used += 1; i += 1
        else:
            used -= 1; j += 1
        rooms = max(rooms, used)
    return rooms
```

### Sweep line / event ordering

Generalizes the sweep above: turn each interval into `(time, +delta)` / `(time, -delta)` events, sort by time, and scan a running total. Handles weighted overlaps (car pooling, population).

```python
def car_pooling(trips, capacity):
    events = []
    for n, s, e in trips:
        events.append((s, n))
        events.append((e, -n))
    events.sort()
    cur = 0
    for _, delta in events:
        cur += delta
        if cur > capacity: return False
    return True
```

### Common problems

- [Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)
- [Minimum Number of Arrows to Burst Balloons](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/)
- [Merge Intervals](https://leetcode.com/problems/merge-intervals/)
- [Insert Interval](https://leetcode.com/problems/insert-interval/)
- [Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)
- [Car Pooling](https://leetcode.com/problems/car-pooling/)
- [My Calendar II](https://leetcode.com/problems/my-calendar-ii/)
- [Employee Free Time](https://leetcode.com/problems/employee-free-time/)
- [Maximum Population Year](https://leetcode.com/problems/maximum-population-year/)
- [The Skyline Problem](https://leetcode.com/problems/the-skyline-problem/)

---

## Pattern 2: Jump Game

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

## Pattern 3: Sorted + Two Pointers

Sort then greedily pair from both ends.

### Boats to Save People

Two pointers from heaviest and lightest. Pair them if they fit; otherwise the lightest boat takes only the heaviest.

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

## Pattern 4: Greedy + Heap

When greedy + sort isn't enough — you need to re-prioritize after each decision.

### Reorganize String

Always place the most-frequent remaining char. Max-heap on counts.

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
| Min meeting rooms / overlap    | Start time                        | Sweep with running count       |
| Merge intervals                | Start time                        | Extend if `start ≤ last_end`   |
| Jump game (reach end)          | —                                 | Track `max reach` while iterating |
| Two-pointer pairing            | Sort, scan from both ends         | Pair greedily                  |
| Stream re-prioritization       | Heap                              | Pop, decide, push back         |
| Huffman                        | Heap of frequencies               | Combine two smallest           |
