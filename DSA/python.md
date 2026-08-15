# Python

Built-in containers, standard-library tools, and language internals you're expected to know cold in interviews. Each container section: what it maps to, the operations that matter, complexity, and the gotchas that bite. The last section covers language-level questions (GIL, generators, decorators, ...) asked outside of DSA problems.

## Covered

`list` · `tuple` · `str` · `dict` · `set` · `deque` · `stack/queue` · `Counter` · `defaultdict` · `heapq` · `slicing` · `comprehensions` · `iterables toolkit` · `lambda` · `functools cache` · `generators` · `decorators` · `GIL`

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

### Wait — doesn't "jumping to slot `i`" itself take time?

No, and this is the load-bearing fact behind every hash-table claim. An array is a **contiguous block of memory** of fixed-size slots. The address of slot `i` is computed directly:

```
addr(i) = base_address + i * slot_size
```

That's one multiplication and one addition — constant time, **independent of `i` or of `n`**. The CPU then reads the value at that address in one memory operation. There's no "walk to position `i`" step.

```
slot:        0      1      2      3      4      5
memory:   [ ... ][ ... ][ ... ][ ... ][ ... ][ ... ]
          ▲                    ▲
          base                 base + 3 * slot_size   ← jump straight here
```

Contrast with a **linked list**: nodes are scattered in memory, so reaching node `i` means following `i` pointers — O(i). That's why `list[i]` in Python (array-backed) is O(1) but walking a `ListNode` chain to index `i` is O(n).

> **Asymptotically O(1), not "literally free."** A real read still pays for cache misses, page faults, and (for disk-backed hash tables / DB indexes) actual disk seeks. But none of those scale with `n` — they're constants the Big-O hides.

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

```python
c1 + c2    # add counts (drops <= 0 results)
c1 - c2    # subtract, keep only > 0
c1 & c2    # min of each shared key (multiset intersection)
c1 | c2    # max of each (multiset union)
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

# 14. Memoization — `functools.cache` / `lru_cache`

Wrap a pure function so repeated calls with the same args return a cached result instead of recomputing. This is the one-line way to turn an exponential recursion into a polynomial one (top-down DP).

```python
from functools import cache, lru_cache

@cache                      # unbounded cache (Python 3.9+)
def fib(n):
    if n < 2:
        return n
    return fib(n - 1) + fib(n - 2)

@lru_cache(maxsize=None)    # same as @cache; maxsize=N keeps only the N most-recent
def f(args): ...
```

- Arguments must be **hashable** (ints, strings, tuples — not lists or dicts). Convert a list to a `tuple` before passing it in.
- `@cache` is unbounded; `@lru_cache(maxsize=N)` evicts least-recently-used entries once it exceeds `N`.
- Only for **pure** functions (same input → same output, no side effects). Caching something that reads mutable state or the clock gives stale results.
- Inspect/clear with `fib.cache_info()` and `fib.cache_clear()`.

```python
def solve(grid):
    R, C = len(grid), len(grid[0])

    @cache
    def dp(r, c):                       # memoize on the (r, c) state
        if r == R - 1 and c == C - 1:
            return grid[r][c]
        best = float('inf')
        if r + 1 < R: best = min(best, dp(r + 1, c))
        if c + 1 < C: best = min(best, dp(r, c + 1))
        return grid[r][c] + best

    return dp(0, 0)
```

---

# 15. Python Interview — Language Internals & Gotchas

Questions interviewers probe beyond DSA problems — how the language itself works.

### `is` vs `==`

`==` compares **values** (calls `__eq__`); `is` compares **identity** (same object in memory).

```python
a = [1, 2]
b = [1, 2]
a == b        # True  — same value
a is b        # False — different objects
a is a        # True

# small ints (-5..256) and short strings are cached/interned — `is` can look True by accident
x = 256; y = 256
x is y        # True  (cached)
x = 257; y = 257
x is y        # False (usually) — never rely on this
```

> Use `is` only for `None` / `True` / `False` singleton checks (`if x is None`). Use `==` for everything else.

### Iterators vs iterables

An **iterable** has `__iter__` (can hand out an iterator: `list`, `dict`, `str`, ...). An **iterator** has `__next__` (produces values one at a time, remembers position, raises `StopIteration` when done). Every iterator is an iterable; not every iterable is an iterator.

```python
it = iter([1, 2, 3])    # list (iterable) → iterator
next(it)                 # 1
next(it)                 # 2
for x in [1, 2, 3]: ...  # `for` calls iter() then next() under the hood
```

### Generators — `yield`

A function with `yield` returns a **generator** (an iterator) instead of running to completion. Each `next()` call resumes right after the last `yield` — state is preserved between calls without storing the whole sequence.

```python
def count_up_to(n):
    i = 1
    while i <= n:
        yield i
        i += 1

for x in count_up_to(3): print(x)   # 1 2 3, one at a time
```

- **Lazy** — values are produced on demand, so `count_up_to(10**9)` runs in O(1) memory, not O(n).
- A **generator expression** `(x*x for x in nums)` is the lazy counterpart of a list comprehension — same syntax minus the brackets, one value at a time instead of the whole list built up front. Reach for it when you'll iterate once and don't need a list (e.g. piping straight into `sum`/`any`/`all` — see [Iterables toolkit](#11-iterables-toolkit)).
- Once exhausted, a generator can't be restarted — call the function again for a fresh one.

### `*args` / `**kwargs`

Collect a variable number of positional / keyword arguments into a `tuple` / `dict`.

```python
def f(*args, **kwargs):
    print(args)       # tuple of positional args
    print(kwargs)      # dict of keyword args

f(1, 2, a=3, b=4)      # args=(1, 2), kwargs={'a': 3, 'b': 4}
```

The same `*` / `**` **unpack** at a call site — the reverse direction:

```python
nums = [1, 2, 3]
print(*nums)           # unpacks: print(1, 2, 3)
d = {'a': 1, 'b': 2}
f(**d)                 # unpacks: f(a=1, b=2)
```

### Decorators

A decorator wraps a function to add behavior without changing its code. `@dec` above `def f` is sugar for `f = dec(f)`.

```python
def timer(fn):
    def wrapper(*args, **kwargs):
        import time
        start = time.time()
        result = fn(*args, **kwargs)
        print(f"{fn.__name__} took {time.time()-start:.4f}s")
        return result
    return wrapper

@timer
def slow(): ...
```

`@cache` / `@lru_cache` (section 14 above) are the decorators you'll actually reach for in interviews.

### The GIL (Global Interpreter Lock)

CPython's GIL lets only **one thread execute Python bytecode at a time**, even on a multi-core machine.

| Workload  | Use                       | Why                                                    |
| --------- | -------------------------- | ------------------------------------------------------- |
| I/O-bound | `threading` / `asyncio`   | a thread releases the GIL while waiting on network/disk |
| CPU-bound | `multiprocessing`         | separate processes → separate GILs → true parallelism   |

`threading` alone does **not** speed up CPU-bound work — every thread still fights over the one GIL.

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

## Pass by value or reference?

Neither, exactly — Python passes **the reference, by value** (often called *call by object reference* or *call by sharing*). Every variable is a **name** pointing at an object; assignment binds a name to an object, it never copies the object. When you call `f(x)`, the parameter becomes a *second name* for the very same object `x` points to.

What the function can do then depends entirely on whether that object is **mutable**:

```python
# Immutable (int, str, tuple, float, bool) — rebinding inside doesn't escape
def bump(n):
    n += 1          # rebinds local name to a NEW int; caller's x untouched
    return n
x = 5
bump(x)             # → 6, but
x                   # still 5

# Mutable (list, dict, set) — mutating the object IS visible to the caller
def push(lst):
    lst.append(99)  # mutates the SAME object both names point to
arr = [1, 2]
push(arr)
arr                 # [1, 2, 99]   ← changed!
```

The dividing line is **mutate vs rebind**, not the type:

```python
def f(lst):
    lst.append(1)   # mutates caller's object        → visible outside
    lst = [0, 0]    # rebinds the LOCAL name only     → caller unaffected
```

After `lst = [0, 0]`, the parameter name points at a brand-new list; the caller's variable still points at the original. So even with a mutable argument, **reassigning the parameter** never reaches the caller — only in-place mutation does.

> **To protect a caller's list, copy at the boundary:** `def f(lst): lst = lst[:]` (shallow) or `copy.deepcopy(lst)` for nested data.

> **GOTCHA — mutable default argument.** A default value is evaluated **once**, when the function is defined — not per call. So `def f(acc=[])` creates a single list that every call without an argument shares and keeps mutating. Use the `None` sentinel:
> ```python
> def f(acc=None):
>     if acc is None:
>         acc = []        # fresh list each call
> ```

> **GOTCHA — copy semantics.** `b = a` aliases; `b = a[:]` / `a.copy()` is a **shallow** copy (nested objects still shared). For nested structures use `copy.deepcopy(a)`.

