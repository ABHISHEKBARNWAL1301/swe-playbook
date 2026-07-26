# Hashing — Dict & Set

Hash map (`unordered_map`). O(1) average ops. Insertion-ordered since Python 3.7.

```python
d = {}                   # dict()
d = {"a": 1, "b": 2}
```

| Operation     | Code                       | Complexity |
| ------------- | -------------------------- | ---------- |
| Get / set     | `d[k]` / `d[k]=v`          | O(1)       |
| Safe get      | `d.get(k, default)`        | O(1)       |
| Set-if-absent | `d.setdefault(k, default)` | O(1)       |
| Delete        | `del d[k]`                 | O(1)       |
| Pop           | `d.pop(k, default)`        | O(1)       |
| Membership    | `k in d`                   | O(1)       |

```python
for k, v in d.items(): ...
for k in d: ...              # iterates keys
d[k] = d.get(k, 0) + 1       # frequency count (or use Counter)
```

> **GOTCHA — `d[k]` on a missing key raises `KeyError`.** Use `.get(k, default)`, `defaultdict`, or `in` first.

---

## defaultdict — auto-initializes missing keys

```python
from collections import defaultdict

freq  = defaultdict(int)     # freq[x] += 1   (missing → 0)
adj   = defaultdict(list)    # graph: adj[u].append(v)
```

Accessing a missing key **creates** it. To avoid silently growing the dict, read with `.get` instead of `[]`.

---

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

Each slot holds a **bucket** (a small list / linked list) of all entries that hashed there. Insert appends to the bucket; lookup hashes to the slot, then scans that one short bucket comparing keys.

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

Both schemes degrade to **O(n)** if everything collides into one chain/cluster. They stay O(1) by keeping the **load factor** (entries ÷ capacity) low — CPython resizes (≈ doubles, rehashing all keys) once the table is ~2/3 full, so buckets / probe-runs stay short.

| Strategy        | Collision goes to            | Used by                              |
| --------------- | ---------------------------- | ------------------------------------ |
| Chaining        | a list at the same slot      | Java `HashMap`, C++ `unordered_map`  |
| Open addressing | the next probed slot, inline | **CPython** `dict` / `set`           |

> **GOTCHA — keys must be hashable & immutable.** Hashing a `list` raises `TypeError`. And if a key's hash changed after insertion, lookup would probe the wrong slot — which is exactly why mutable types are barred as keys.

---

## Set

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

## Counter — multiset helper

```python
from collections import Counter

c = Counter("aabbc")         # {'a': 2, 'b': 2, 'c': 1}
c.most_common(2)             # [('a', 2), ('b', 2)]
c1 + c2                      # add counts
c1 - c2                      # subtract, keep >= 0
c1 & c2                      # min of each
c1 | c2                      # max of each
```

---

## Practice problems — FAANG favorites

Curated for what actually shows up in Meta / Google / Amazon / Apple / Microsoft / Bloomberg / Netflix screens and onsites. Hash-table is the core trick in every one.

### Easy — warm-ups & phone screens

- [Two Sum](https://leetcode.com/problems/two-sum/) — the most-asked LC question, period. `value → index` map.
- [Contains Duplicate](https://leetcode.com/problems/contains-duplicate/) — set + scan
- [Valid Anagram](https://leetcode.com/problems/valid-anagram/) — `Counter` equality
- [First Unique Character in a String](https://leetcode.com/problems/first-unique-character-in-a-string/) — count then re-scan
- [Ransom Note](https://leetcode.com/problems/ransom-note/) — counter subtract
- [Intersection of Two Arrays](https://leetcode.com/problems/intersection-of-two-arrays/) — set intersection
- [Happy Number](https://leetcode.com/problems/happy-number/) — set to detect cycles (or fast/slow)
- [Roman to Integer](https://leetcode.com/problems/roman-to-integer/) — symbol → value map
- [Isomorphic Strings](https://leetcode.com/problems/isomorphic-strings/) — bijection via two maps

### Medium — the FAANG bread-and-butter

- [Group Anagrams](https://leetcode.com/problems/group-anagrams/) — sorted-string or count-tuple key. **Meta, Amazon, Google.**
- [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/) — counter + heap (or bucket sort). **Amazon, Meta.**
- [Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/) — prefix-sum + `{sum → count}`. **Meta, Google.**
- [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/) — sliding window + `char → last index`. **Asked everywhere.**
- [Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/) — set + only start runs at sequence heads. **Google, Meta.**
- [LRU Cache](https://leetcode.com/problems/lru-cache/) — hashmap + doubly linked list. **Must-know for FAANG.** (Full code in [design.md](design.md))
- [Insert Delete GetRandom O(1)](https://leetcode.com/problems/insert-delete-getrandom-o1/) — array + `value → index`. **Google, Amazon.**
- [Encode and Decode TinyURL](https://leetcode.com/problems/encode-and-decode-tinyurl/) — design + hashmap. **Bloomberg, Amazon.**
- [Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/) — fixed-size sliding window of counts. **Meta.**
- [4Sum II](https://leetcode.com/problems/4sum-ii/) — split into two pair-sums, count via hashmap. **Amazon.**
- [Continuous Subarray Sum](https://leetcode.com/problems/continuous-subarray-sum/) — prefix-sum mod k. **Meta.**
- [Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/) — `old → new` map. **Meta, Amazon.**

### Hard — onsite-level

- [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/) — variable window + `need` / `have` counters. **Meta, Amazon, Google.**
- [Substring with Concatenation of All Words](https://leetcode.com/problems/substring-with-concatenation-of-all-words/) — sliding window of word-counts
- [First Missing Positive](https://leetcode.com/problems/first-missing-positive/) — array used as a hash via cyclic placement
- [LFU Cache](https://leetcode.com/problems/lfu-cache/) — hashmap + hashmap-of-DLLs + `min_freq`
