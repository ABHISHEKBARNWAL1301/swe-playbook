# Linear Data Structures

Elements arranged one after another — access is sequential (or index-based via contiguous memory). Covers **arrays/strings** (contiguous, index-addressed) and **stacks & queues** (restricted-access wrappers over that same contiguous storage, or over a deque).

## Contents

- [Arrays](#arrays)
  - [Pattern 0: Warm-up — Classic Array Problems](#pattern-0-warm-up--classic-array-problems)
  - [Pattern 1: Prefix Sums](#pattern-1-prefix-sums)
  - [Pattern 2: Two Pointers](#pattern-2-two-pointers)
  - [Pattern 3: Sliding Window](#pattern-3-sliding-window)
  - [Pattern 4: Kadane's Algorithm](#pattern-4-kadanes-algorithm)
  - [Pattern 5: 2D Matrix](#pattern-5-2d-matrix)
  - [Pattern 6: Sorting Essentials](#pattern-6-sorting-essentials)
  - [Pattern 7: Strings as Arrays](#pattern-7-strings-as-arrays)
  - [Pattern 8: Hashing as a Tool](#pattern-8-hashing-as-a-tool)
- [Stacks & Queues](#stacks--queues)
  - [Stack (LIFO)](#stack-lifo)
  - [Monotonic Stack — Next Greater / Smaller](#monotonic-stack--next-greater--smaller)
  - [Queue (FIFO)](#queue-fifo)
  - [Monotonic Deque — Sliding Window Max/Min](#monotonic-deque--sliding-window-maxmin)

---

## Arrays

Core sequential container. Most patterns reduce to: traverse once, maintain a window or two pointers, accumulate prefix info, or sort and scan.

### Pattern 0: Warm-up — Classic Array Problems

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

### Pattern 1: Prefix Sums

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

---

### Pattern 2: Two Pointers

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

---

### Pattern 3: Sliding Window

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
- [Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/) (also see [Monotonic Queue](#monotonic-deque--sliding-window-maxmin))

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

---

### Pattern 4: Kadane's Algorithm

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

---

### Pattern 5: 2D Matrix

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

### Pattern 6: Sorting Essentials

#### Built-in

`nums.sort()` (Timsort) — O(n log n), stable, in-place.

#### Counting sort — O(n + k)

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

---

### Pattern 7: Strings as Arrays

Strings are immutable char arrays, so **every array pattern above applies directly** — two pointers, sliding window, prefix sums. Below are the string-specific idioms, most built on a char-count map. (General hash-table internals — chaining vs open addressing, why O(1) — live in [python.md](python.md#5-dictionary).)

#### Frequency counting

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

#### Sliding window + hashmap

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

#### Rolling hash (Rabin–Karp)

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

#### Canonical key → hashmap grouping

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

#### Two-pointer string tricks

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

### Pattern 8: Hashing as a Tool

A hashmap turns "have I seen this?" / "how many of this?" / "where was this?" from an O(n) scan into an O(1) lookup. Container mechanics (`dict`/`set`/`Counter`, why O(1), collision handling) live in [python.md](python.md#5-dictionary) — this section is just the array/string problems built on top of it. (Anagram grouping, canonical keys, and sliding-window+`Counter` problems are already covered in [Pattern 7](#pattern-7-strings-as-arrays) above.)

#### Value → index / seen-before map

```python
def two_sum(nums, target):                # value -> index, one pass
    seen = {}
    for i, x in enumerate(nums):
        if target - x in seen:
            return [seen[target - x], i]
        seen[x] = i
```

#### Set for membership / dedup

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

#### Questions

- [Two Sum](https://leetcode.com/problems/two-sum/) — value → index map
- [Contains Duplicate](https://leetcode.com/problems/contains-duplicate/) — set + scan
- [Intersection of Two Arrays](https://leetcode.com/problems/intersection-of-two-arrays/) — set intersection
- [Happy Number](https://leetcode.com/problems/happy-number/) — set to detect cycles (or fast/slow)
- [Roman to Integer](https://leetcode.com/problems/roman-to-integer/) — symbol → value map
- [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/) — `Counter` + heap or bucket sort (see [non-linear-data-structures.md](non-linear-data-structures.md#pattern-1-top-k))
- [Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/) — set, only start runs at sequence heads
- [Insert Delete GetRandom O(1)](https://leetcode.com/problems/insert-delete-getrandom-o1/) — array + `value → index` map
- [4Sum II](https://leetcode.com/problems/4sum-ii/) — split into two pair-sums, count via hashmap
- [Continuous Subarray Sum](https://leetcode.com/problems/continuous-subarray-sum/) — prefix-sum mod k
- [Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/) — `old → new` node map
- [Encode and Decode TinyURL](https://leetcode.com/problems/encode-and-decode-tinyurl/) — id ↔ url map
- [LRU Cache](https://leetcode.com/problems/lru-cache/) — hashmap + doubly linked list (see [SystemDesign/problems.md](../SystemDesign/problems.md#lru-cache))
- [LFU Cache](https://leetcode.com/problems/lfu-cache/) — hashmap + hashmap-of-DLLs + `min_freq`

---

### Quick reference

| Pattern              | When                                       | Complexity   |
|----------------------|--------------------------------------------|--------------|
| Prefix sum           | Many range-sum queries                     | Build O(n), query O(1) |
| Two pointers         | Sorted array, pair/triple sum, in-place    | O(n)         |
| Fixed window         | "Subarray of size k with..."               | O(n)         |
| Variable window      | "Longest/shortest subarray with condition" | O(n)         |
| Kadane               | Max/min contiguous subarray                | O(n)         |
| Sort + scan          | Intervals, custom ordering                 | O(n log n)   |
| Counting sort        | Small int range                            | O(n + k)     |
| Char counting        | Anagrams, freq — `Counter` or `[0]*26`     | O(n)         |
| Rolling hash         | Substring search / dup detection           | O(n)         |
| Expand from center   | Longest palindromic substring              | O(n²)        |
| Hashmap / set lookup | "Seen before?" / "complement exists?"      | O(n)         |

---

## Stacks & Queues

Stack = **LIFO** (use a `list`). Queue = **FIFO** (use `collections.deque`). This section has two parts: the **basic implementations / use cases**, and the **monotonic** variant that shows up for next-greater (stack) and sliding-window extremes (deque).

#### Stack (LIFO)

Push / pop / peek in O(1) — in Python just use a `list`.

```python
st = []
st.append(x)      # push
st.pop()          # pop top   — both O(1)
st[-1]            # peek
while st: ...     # empty check
```

#### Matching Pairs

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

##### Common problems

- [Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)
- [Minimum Remove to Make Valid Parentheses](https://leetcode.com/problems/minimum-remove-to-make-valid-parentheses/)
- [Longest Valid Parentheses](https://leetcode.com/problems/longest-valid-parentheses/)
- [Score of Parentheses](https://leetcode.com/problems/score-of-parentheses/)

#### Expression Evaluation

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

##### Common problems

- [Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/)
- [Basic Calculator](https://leetcode.com/problems/basic-calculator/)
- [Basic Calculator II](https://leetcode.com/problems/basic-calculator-ii/)
- [Decode String](https://leetcode.com/problems/decode-string/)

#### Stack as Recursion Substitute

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

##### Common problems

- [Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/) (iterative)
- [Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/)
- [Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/) (two stacks)
- [Flatten Nested List Iterator](https://leetcode.com/problems/flatten-nested-list-iterator/)

#### Min Stack — O(1) min

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

##### Common problems

- [Min Stack](https://leetcode.com/problems/min-stack/)
- [Max Stack](https://leetcode.com/problems/max-stack/)


#### Monotonic Stack — Next Greater / Smaller

A stack whose values stay in monotonic order. When a new element breaks the order, **pop everything that violates it** — those popped elements just "found" their answer.

**Use it when** you hear "for each element, find the next / previous element that is greater / smaller."

```
push x:
  while stack.top violates monotonicity vs x:
      record answer for stack.pop()
  stack.push(x)
```

##### Example: Next Greater Element

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

##### Example: Largest Rectangle in Histogram

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

##### Common problems

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

#### Queue (FIFO)

Enqueue at the back, dequeue at the front — use `collections.deque` (O(1) at both ends; `list.pop(0)` is O(n)).

```python
from collections import deque
q = deque()
q.append(x)          # enqueue        O(1)
q.popleft()          # dequeue         O(1)
q.appendleft(x)      # push front      O(1)  (deque-only)
q.pop()              # pop back        O(1)  (deque-only)
```

#### BFS

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

> BFS problem sets live where they're used: grids / graphs in [non-linear-data-structures.md](non-linear-data-structures.md#graphs), level-order in [non-linear-data-structures.md](non-linear-data-structures.md#trees--bst).

#### Circular Queue / Ring Buffer

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

##### Common problems

- [Design Circular Queue](https://leetcode.com/problems/design-circular-queue/)
- [Design Circular Deque](https://leetcode.com/problems/design-circular-deque/)

#### Queue from Two Stacks (and vice versa)

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

##### Common problems

- [Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/)
- [Implement Stack using Queues](https://leetcode.com/problems/implement-stack-using-queues/)

---


#### Monotonic Deque — Sliding Window Max/Min

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

##### Common problems

- [Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/)
- [Sliding Window Median](https://leetcode.com/problems/sliding-window-median/) (two heaps; deque doesn't fit)
- [Constrained Subsequence Sum](https://leetcode.com/problems/constrained-subsequence-sum/) (DP + monotonic deque)
- [Shortest Subarray with Sum at Least K](https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/)
- [Jump Game VI](https://leetcode.com/problems/jump-game-vi/)
