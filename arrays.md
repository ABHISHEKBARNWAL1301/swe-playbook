# Arrays

Core sequential container. Most patterns reduce to: traverse once, maintain a window or two pointers, accumulate prefix info, or sort and scan.

## Contents

- [Pattern 0: Warm-up — Classic Array Problems](#pattern-0-warm-up--classic-array-problems)
- [Pattern 1: Prefix Sums](#pattern-1-prefix-sums)
- [Pattern 2: Two Pointers](#pattern-2-two-pointers)
- [Pattern 3: Sliding Window](#pattern-3-sliding-window) (fixed + variable)
- [Pattern 4: Kadane's Algorithm](#pattern-4-kadanes-algorithm)
- [Pattern 5: 2D Matrix](#pattern-5-2d-matrix)
- [Pattern 6: Sorting Essentials](#pattern-6-sorting-essentials)
- [Pattern 7: Strings as Arrays](#pattern-7-strings-as-arrays)


---

## Pattern 0: Warm-up — Classic Array Problems

Single-pass tricks, in-place rewrites, and clever index/value math. Good for building the reflex of "scan once, track a counter, swap in place."

### Easy

- [Move Zeroes](https://leetcode.com/problems/move-zeroes/) — two-pointer write/read
- [Max Consecutive Ones](https://leetcode.com/problems/max-consecutive-ones/) — running counter, reset on 0
- [Majority Element](https://leetcode.com/problems/majority-element/) — Boyer–Moore voting
- [Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) — track min price + max profit so far

### Medium

- [Majority Element II](https://leetcode.com/problems/majority-element-ii/) — Boyer–Moore for two candidates (`⌊n/3⌋` threshold)
- [Rotate Array](https://leetcode.com/problems/rotate-array/)
- [Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/) — write pointer lags read pointer

### Hard

- [First Missing Positive](https://leetcode.com/problems/first-missing-positive/)
- [Next Permutation](https://leetcode.com/problems/next-permutation/)
- [Find All Numbers Disappeared in an Array](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/) — in-place index marking (negate `nums[abs(x)-1]`)

## Pattern 1: Prefix Sums

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

### Variants

- **Suffix sum**: build right-to-left for "from i to end" queries.
- **Prefix XOR / product**: same idea with `^` or `*`.
- **2D prefix sum**: `psum[r][c] = nums[r][c] + psum[r-1][c] + psum[r][c-1] - psum[r-1][c-1]`.

### Common problems

- [Range Sum Query — Immutable](https://leetcode.com/problems/range-sum-query-immutable/)
- [Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/)
- [Contiguous Array](https://leetcode.com/problems/contiguous-array/)
- [Range Sum Query 2D — Immutable](https://leetcode.com/problems/range-sum-query-2d-immutable/)
- [Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)
- [Subarray Sums Divisible by K](https://leetcode.com/problems/subarray-sums-divisible-by-k/) — count prefix remainders mod k

---

## Pattern 2: Two Pointers

Two indices walking the array — usually one of:

- **Opposite ends** moving inward (sorted array, pair sum)
- **Same direction** at different speeds (in-place rewrite, dedup)
- **Fast / slow** (cycle / midpoint)

### Example: Two Sum II (sorted array)

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

### Same-direction (in-place rewrite)

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

### Common problems

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

## Pattern 3: Sliding Window

### Fixed Size

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

### Common problems

- [Maximum Average Subarray I](https://leetcode.com/problems/maximum-average-subarray-i/)
- [Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/)
- [Permutation in String](https://leetcode.com/problems/permutation-in-string/)
- [Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/) (also see [Monotonic Queue](stacks-queue.md))

---

### Variable Size

Window grows/shrinks based on a condition. Right pointer expands; left pointer shrinks while the window is invalid.

```
                grow ──▶
   [L ─────────── R]
        shrink ◀──    while invalid
```

### Template

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

### Example: Longest Substring Without Repeating Characters

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

### Common problems

- [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
- [Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/)
- [Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/)
- [Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/)
- [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)
- [Subarrays with K Different Integers](https://leetcode.com/problems/subarrays-with-k-different-integers/) (exactly-K = atMost(K) - atMost(K-1))
- [Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/)

---

## Pattern 4: Kadane's Algorithm

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

### Variant: Max Product Subarray

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

### Variant: Max Circular Subarray Sum

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

### Common problems

- [Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)
- [Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/)
- [Maximum Sum Circular Subarray](https://leetcode.com/problems/maximum-sum-circular-subarray/)
- [Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) (Kadane on diffs)

---

## Pattern 5: 2D Matrix

### Traverse

Row-major, column-major, diagonals, spiral. Indices: `(r, c)` with `0 <= r < n, 0 <= c < m`.

### Common ops

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

### Search a sorted matrix (rows + cols sorted)

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

### Common problems

- [Rotate Image](https://leetcode.com/problems/rotate-image/)
- [Spiral Matrix](https://leetcode.com/problems/spiral-matrix/)
- [Set Matrix Zeroes](https://leetcode.com/problems/set-matrix-zeroes/)
- [Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/)
- [Search a 2D Matrix II](https://leetcode.com/problems/search-a-2d-matrix-ii/)
- [Word Search](https://leetcode.com/problems/word-search/) (DFS + backtracking)
- [Game of Life](https://leetcode.com/problems/game-of-life/)

---

## Pattern 6: Sorting Essentials

### Built-in

`nums.sort()` (Timsort) — O(n log n), stable, in-place.

### Counting sort — O(n + k)

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

### Common sort-then-scan problems

- [Merge Intervals](https://leetcode.com/problems/merge-intervals/)
- [Insert Interval](https://leetcode.com/problems/insert-interval/)
- [Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)
- [Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)
- [Largest Number](https://leetcode.com/problems/largest-number/) (custom comparator)
- [H-Index](https://leetcode.com/problems/h-index/)

---

## Pattern 7: Strings as Arrays

Strings are immutable char arrays, so **every array pattern above applies directly** — two pointers, sliding window, prefix sums. Below are the string-specific idioms, most built on a char-count map. (General hash-table mechanics live in [hashing.md](hashing.md).)

### Frequency counting

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

### Sliding window + hashmap

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

### Rolling hash (Rabin–Karp)

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

### Canonical key → hashmap grouping

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

### Two-pointer string tricks

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

## Quick reference

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
