# Python for DSA

Built-in containers and the standard-library tools you reach for in interviews. Each section: what it maps to, the operations that matter, complexity, and the gotchas that bite.

## Covered

`list` · `tuple` · `str` · `dict` · `set` · `deque` · `stack/queue` · `Counter` · `defaultdict` · `heapq` · `slicing` · `comprehensions` · `iterables toolkit` · `lambda` 

---

# 1. List

Dynamic array (`std::vector`). Contiguous, indexable, mutable.

```python
nums = []            # list()
nums = [1, 2, 3]
nums = [0] * n       # n zeros
grid = [[0] * c for _ in range(r)]   # r×c — see GOTCHA below
```

| Operation     | Code                | Complexity     |
| ------------- | ------------------- | -------------- |
| Append        | `nums.append(x)`    | O(1) amortized |
| Pop last      | `nums.pop()`        | O(1)           |
| Insert        | `nums.insert(i, x)` | O(n)           |
| Pop index     | `nums.pop(i)`       | O(n)           |
| Remove value  | `nums.remove(x)`    | O(n)           |
| Membership    | `x in nums`         | O(n)           |
| Index of      | `nums.index(x)`     | O(n)           |
| Count         | `nums.count(x)`     | O(n)           |
| Extend        | `nums.extend(lst)`  | O(k)           |
| Sort          | `nums.sort()`       | O(n log n)     |
| Reverse       | `nums.reverse()`    | O(n)           |
| Min/Max/Sum   | `min/max/sum(nums)` | O(n)           |
| Length        | `len(nums)`         | O(1)           |

```python
nums.sort()                       # in place, stable
nums.sort(key=lambda x: -x)       # by key
nums.sort(reverse=True)
b = sorted(nums)                  # returns new list, leaves nums alone
```

> **GOTCHA — `*` aliases references.** `[[0]*3]*3` makes **three references to the same inner list**; writing `grid[0][0]=1` changes every row. Always build 2D with a comprehension: `[[0]*3 for _ in range(3)]`.

---

# 2. Slicing

`a[start:stop:step]` — `stop` exclusive. Always returns a **new** list (copy).

```python
a[2:5]      # indices 2,3,4
a[:k]       # first k
a[-k:]      # last k
a[::-1]     # reversed copy
a[::2]      # every other
a[:]        # shallow copy of whole list
a[i:i] = [x, y]   # insert without removing
del a[i:j]        # delete a range
```

Out-of-range slices are safe (clamped); out-of-range **index** raises `IndexError`.

---

# 3. Tuple

Immutable list. Hashable (if its elements are) → usable as `dict` keys and `set` members.

```python
t = (1, 2, 3)
t = 1, 2, 3          # parens optional
x, y = y, x          # swap via tuple packing
for i, v in enumerate(nums): ...   # tuples everywhere
```

Use for fixed records, multiple return values, and composite keys: `seen.add((r, c))`.

---

# 4. String

Immutable sequence of chars. Every "mutation" builds a new string.

```python
s = "hello"
s[0], s[-1], s[1:4]          # index / slice like a list
"h",   "o",   "ell"
```

| Method            | Returns                          |
| ----------------- | -------------------------------- |
| `s.split(" ")`    | list (split whitespace) |
| `" ".join(lst)`   | string from iterable of strings  |
| `s.strip()`       | trim both ends (`lstrip/rstrip`) |
| `s.lower/upper()` | case-folded copy                 |
| `s.replace(a, b)` | all occurrences                  |
| `s.find(x)`       | index or `-1`                    |
| `s.startswith/endswith(x)` | bool                    |
| `s.isdigit/isalpha/isalnum()` | bool                 |
| `ord(c)` / `chr(n)` | char ↔ codepoint               |


```python
ord('a')                 # 97  — for bucket indexing
chr(97)                  # 'a'
```

> **GOTCHA — building strings in a loop is O(n²).** `s += c` copies the whole string each time. Append chars to a `list`, then `"".join(parts)` once → O(n).

---

# 5. Dictionary

Hash map (`unordered_map`). O(1) average ops. Insertion-ordered since 3.7.

```python
d = {}                   # dict()
d = {"a": 1, "b": 2}
```

| Operation     | Code                | Complexity |
| ------------- | ------------------- | ---------- |
| Get / set     | `d[k]` / `d[k]=v`   | O(1)       |
| Safe get      | `d.get(k, default)` | O(1)       |
| Set-if-absent | `d.setdefault(k, default)` | O(1) |
| Delete        | `del d[k]`          | O(1)       |
| Pop           | `d.pop(k, default)` | O(1)       |
| Membership    | `k in d`            | O(1)       |

```python
for k, v in d.items(): ...
for k in d: ...              # iterates keys
d[k] = d.get(k, 0) + 1       # frequency count (or use Counter)
```

> **GOTCHA — `d[k]` on a missing key raises `KeyError`.** Use `.get(k, default)`, `defaultdict`, or `in` first.

## defaultdict — auto-initializes missing keys

```python
from collections import defaultdict

freq  = defaultdict(int)     # freq[x] += 1   (missing → 0)
adj   = defaultdict(list)    # graph: adj[u].append(v)
```

Accessing a missing key **creates** it. To avoid silently growing the dict, read with `.get` instead of `[]`.

## Why get/set is O(1)

A dict is backed by a **hash table**: a flat array of slots. To store `d[k] = v`:

```
1. h = hash(k)             # k → big integer  (O(1) for ints/str/tuples)
2. i = h % capacity        # fold into a slot index
3. store (k, v) at slot i
```

Lookup repeats the same arithmetic to jump straight to slot `i` — no scan. That's the O(1): a hash + a mod + one array access, independent of how many keys exist.

The catch: two different keys can land on the same slot (`h1 % cap == h2 % cap`). That's a **collision**, and how you resolve it defines the table.

### Chaining

Each slot holds a **bucket** (a small list/linked list) of all entries that hashed there. Insert appends to the bucket; lookup hashes to the slot, then scans that one short bucket comparing keys.

```
slot 0 → (k1,v1)
slot 1 → (k4,v4) → (k7,v7)      ← two keys collided here
slot 2 →
slot 3 → (k2,v2)
```

### Open addressing (what CPython uses)

No buckets — every entry lives **inline** in the slot array. On collision, **probe** for the next free slot by a fixed rule (linear: `i+1, i+2…`; CPython uses a pseudo-random sequence derived from the full hash). Lookup probes the same sequence until it finds the key or an empty slot.

```
insert k4 → slot 1 taken → probe → slot 2 free → place k4 there
```

### Why it stays O(1) on average

Both schemes degrade to **O(n)** if everything collides into one chain/cluster. They stay O(1) by keeping the **load factor** (entries ÷ capacity) low — CPython resizes (≈ doubles, rehashing all keys) once the table is ~2/3 full, so buckets/probe-runs stay short.

| Strategy        | Collision goes to            | Used by                     |
| --------------- | ---------------------------- | --------------------------- |
| Chaining        | a list at the same slot      | Java `HashMap`, C++ `unordered_map` |
| Open addressing | the next probed slot, inline | **CPython** `dict` / `set`  |

> **GOTCHA — keys must be hashable & immutable.** Hashing a `list` raises `TypeError`. And if a key's hash changed after insertion, lookup would probe the wrong slot — which is exactly why mutable types are barred as keys.

---

# 6. Set

Hash set (`unordered_set`). O(1) membership. Elements must be hashable (no lists/dicts; tuples OK).

```python
s = set()                # {} is an empty DICT, not a set
s = {1, 2, 3}
seen = set(nums)         # dedup + fast lookup
```

| Operation  | Code           | Complexity                  |
| ---------- | -------------- | --------------------------- |
| Add        | `s.add(x)`     | O(1)                        |
| Discard    | `s.discard(x)` | O(1) — no error if absent   |
| Remove     | `s.remove(x)`  | O(1) — `KeyError` if absent |
| Membership | `x in s`       | O(1)                        |

```python
a | b    # union          a & b    # intersection
a - b    # difference     a ^ b    # symmetric difference
a <= b   # subset
frozenset(x)   # immutable, hashable set — usable as dict key
```

A `set` is a `dict` with keys but no values — same hash table, same open-addressing collision handling (see [Why get/set is O(1)](#why-getset-is-o1) above). So `x in s` is the O(1) slot lookup, and membership in a list (`x in lst`, O(n)) is the trap it replaces.

---

# 7. Deque

Double-ended queue (`collections.deque`). O(1) push/pop at **both** ends — the list's `pop(0)`/`insert(0, x)` are O(n), so use deque for queues.

```python
from collections import deque
dq = deque()             # deque(iterable), deque(maxlen=k)
```

| Operation    | Code               | Complexity |
| ------------ | ------------------ | ---------- |
| Push right   | `dq.append(x)`     | O(1)       |
| Push left    | `dq.appendleft(x)` | O(1)       |
| Pop right    | `dq.pop()`         | O(1)       |
| Pop left     | `dq.popleft()`     | O(1)       |
| Rotate       | `dq.rotate(k)`     | O(k)       |
| Peek         | `dq[0]` / `dq[-1]` | O(1)       |

Used in: **BFS**, **sliding-window maximum** (monotonic queue), bounded history (`maxlen`).

---

# 8. Stack & Queue

**Stack (LIFO)** — just a list:

```python
st = []
st.append(x)      # push
st.pop()          # pop top   — both O(1)
st[-1]            # peek
while st: ...     # empty check
```

**Queue (FIFO)** — use a deque, never `list.pop(0)`:

```python
q = deque()
q.append(x)       # enqueue
q.popleft()       # dequeue   — both O(1)
```

---

# 9. Counter

Frequency map built on `dict`. Missing keys read as `0` (no `KeyError`).

```python
from collections import Counter
cnt = Counter("aabbbc")      # {'b':3,'a':2,'c':1}
cnt = Counter(nums)
```

```python
cnt[x]                  # count (0 if absent)
cnt.most_common(k)      # top-k as [(val, count), ...]
cnt.most_common()[-1]   # least common
list(cnt.elements())    # expand back to elements
```

Anagram check in one line: `Counter(s) == Counter(t)`.

---

# 10. Heap (priority queue)

`heapq` — a **min-heap** over a plain list. `heap[0]` is the smallest.

```python
import heapq
h = []
heapq.heappush(h, x)    # O(log n)
heapq.heappop(h)        # smallest — O(log n)
h[0]                    # peek min — O(1)
heapq.heapify(nums)     # list → heap in place, O(n)
```

| Helper                        | Use                     |
| ----------------------------- | ----------------------- |
| `heapq.heappushpop(h, x)`     | push then pop (cheaper) |
| `heapq.nlargest(k, it)`       | top-k                   |
| `heapq.nsmallest(k, it)`      | bottom-k                |

```python
heapq.heappush(h, -x); -heapq.heappop(h)     # MAX-heap: negate
heapq.heappush(h, (priority, item))          # order by tuple
```



> **Top-k pattern:** keep a min-heap of size `k`; push, and `heappop` when `len > k`. The k largest survive in O(n log k).

---

# 11. Iterables toolkit

Work on any iterable — used constantly:

```python
len, sum, min, max, abs, round
sorted(it, key=..., reverse=...)   # new list
reversed(seq)                      # iterator
enumerate(it, start=0)             # (index, value)
zip(a, b)                          # pair up; stops at shortest
map(fn, it)  /  filter(fn, it)     # lazy iterators
all(it)  /  any(it)                # bool over iterable
```

```python
for i, v in enumerate(nums): ...
for a, b in zip(arr1, arr2): ...
total = sum(x*x for x in nums)          # generator — no temp list
mx = max(words, key=len)                # arg-max by key
a, b = zip(*pairs)                       # unzip
```

> `min`/`max` raise on empty input — pass `default=`: `max(nums, default=0)`.

### map / filter — transform & keep

Both return **lazy iterators** — wrap in `list()` to materialize. In practice a comprehension is more readable; reach for these when piping into another consumer.

```python
squares = map(lambda x: x*x, nums)       # lazy; iterate or list() it
evens = filter(lambda x: x % 2 == 0, nums)
nonempty = filter(None, items)           # filter(None, ...) drops falsy values
```

### all / any — short-circuit boolean over an iterable

`all` → True if every element is truthy (vacuously True on empty). `any` → True if at least one is. Both **stop early** at the first decisive element.

```python
all(x > 0 for x in nums)                 # all positive?
any(x < 0 for x in nums)                 # any negative?
all(grid[r][c] == 0 for r in range(R))   # column all zero?
if any(word in s for word in banned): ...
```

> Pass a **generator** (no brackets), not a list comp — `any(x < 0 for x in huge)` bails on the first hit instead of building the whole list.

---

# 12. Comprehensions

Faster and clearer than manual loops; the Pythonic default.

```python
[x*x for x in nums]                       # list
[x for x in nums if x > 0]                # filter
[x if x > 0 else 0 for x in nums]         # transform
{x for x in nums}                         # set
{k: v for k, v in pairs}                  # dict
[(r, c) for r in range(R) for c in range(C)]   # nested
```

---

# 13. Lambda

A `lambda` is an **anonymous function** of one expression — no `def`, no name, no `return` (the expression's value is returned). These two are equivalent:

```python
f = lambda x: x * 2          # f(3) → 6
def f(x): return x * 2       # same thing
```

Syntax: `lambda <args>: <single expression>`. It can take any args (`lambda x, y: x + y`, `lambda: 0`) but the body must be **one expression** — no statements, loops, or assignments.

## The main use: `key=`

A `key` function maps each element to the value you want to **sort/compare by**. `lambda` lets you write that inline instead of defining a throwaway named function.

```python
nums.sort(key=lambda x: abs(x))              # order by magnitude: [0,-1,2,-3]
max(items, key=lambda it: it.score)          # element with the largest .score
min(points, key=lambda p: p[0]**2 + p[1]**2) # point closest to origin
sorted(words, key=len)                       # by length (len is already a fn — no lambda needed)
```

**Tuple key = multi-level sort.** Python compares tuples left-to-right, so return a tuple to sort by several fields. Negate a numeric field to flip that field's direction:

```python
sorted(pairs, key=lambda p: (p[1], -p[0]))   # by p[1] ascending, ties broken by p[0] descending
sorted(people, key=lambda x: (x.age, x.name))# age asc, then name A→Z
```

Same `key=` works in `sorted`, `list.sort`, `min`, `max`, `heapq.nlargest/nsmallest`.

## Also seen with map / filter

```python
list(map(lambda x: x*x, nums))               # but a comprehension is clearer: [x*x for x in nums]
list(filter(lambda x: x % 2 == 0, nums))
```

> **When NOT to use it.** If the logic spans multiple lines, is reused, or needs a name to be readable, write a real `def`. A lambda assigned to a variable (`f = lambda x: ...`) is just a worse `def` — use `def`.

---

# Gotchas worth memorizing

```python
# Integer vs float division
7 // 2      # 3   floor (note: -7 // 2 == -4, floors toward -∞)
7 / 2       # 3.5 always float
7 % 3       # 1   ; -7 % 3 == 2 (result takes sign of divisor)
divmod(7, 3)        # (2, 1)

# Truthiness — empty containers are falsy
if not nums: ...            # empty list/str/dict/set, 0, None

# Chained comparison
if 0 <= i < n: ...

# Swap, no temp
a, b = b, a

# Unbounded ints — no overflow
2 ** 100

# float infinity for init
best = float('inf'); worst = float('-inf')
```

> **GOTCHA — mutable default argument.** `def f(acc=[])` reuses the *same* list across calls. Use `def f(acc=None): acc = acc or []`.

> **GOTCHA — copy semantics.** `b = a` aliases; `b = a[:]` / `a.copy()` is a **shallow** copy (nested objects still shared). For nested structures use `copy.deepcopy(a)`.

