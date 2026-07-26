# Binary Search

Halve the search space each step. Works when the space is sorted **or** has a monotonic predicate.

```
T T T T F F F F F          (predicate flips exactly once)
        ▲
   find the boundary
```

## Patterns covered

- Classic binary search (exact match)
- Lower bound / upper bound
- Search on the answer (parametric search)
- Binary search in a rotated array
- Binary search on a 2D matrix
- Find peak / find min in rotated

---

## Pattern 1: Classic — Exact Match

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

---

## Pattern 2: Lower Bound / Upper Bound

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

### Why `lo < hi` and `hi = len(nums)`?

- Half-open `[lo, hi)` avoids the `lo <= hi` infinite-loop trap with `hi = mid`.
- Result can be `n` if `target` is greater than everything.

### Common problems

- [Search Insert Position](https://leetcode.com/problems/search-insert-position/)
- [First Bad Version](https://leetcode.com/problems/first-bad-version/)
- [Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)
- [Number of Occurrences](https://leetcode.com/problems/number-of-occurrences-of-an-element-in-an-array/)
- [Guess Number Higher or Lower](https://leetcode.com/problems/guess-number-higher-or-lower/)

---

## Pattern 3: Search on the Answer (Parametric)

The array isn't sorted, but the **answer space** is. Binary-search the answer; use a `check(x)` predicate that's monotonic in `x`.

### Template

```python
def min_feasible(lo, hi, check):
    while lo < hi:
        mid = (lo + hi) // 2
        if check(mid): hi = mid                  # mid works → maybe smaller works
        else:          lo = mid + 1
    return lo
```

### Example: Koko Eating Bananas

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

### Common problems

- [Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/)
- [Capacity To Ship Packages Within D Days](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/)
- [Split Array Largest Sum](https://leetcode.com/problems/split-array-largest-sum/)
- [Minimum Number of Days to Make m Bouquets](https://leetcode.com/problems/minimum-number-of-days-to-make-m-bouquets/)
- [Find the Smallest Divisor Given a Threshold](https://leetcode.com/problems/find-the-smallest-divisor-given-a-threshold/)
- [Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/) (BS on partition)
- [Aggressive Cows / Magnetic Force](https://leetcode.com/problems/magnetic-force-between-two-balls/)

---

## Pattern 4: Rotated Sorted Array

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

### Find min in rotated sorted array

```python
def find_min(nums):
    lo, hi = 0, len(nums) - 1
    while lo < hi:
        mid = (lo + hi) // 2
        if nums[mid] > nums[hi]: lo = mid + 1
        else: hi = mid
    return nums[lo]
```

### Common problems

- [Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)
- [Search in Rotated Sorted Array II](https://leetcode.com/problems/search-in-rotated-sorted-array-ii/) (duplicates)
- [Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)
- [Find Minimum in Rotated Sorted Array II](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array-ii/)

---

## Pattern 5: 2D Matrix

### Rows + cols sorted, and `M[i][m-1] < M[i+1][0]` — full sorted

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

### Only rows sorted, only cols sorted (no row-row ordering)

Start at top-right (or bottom-left). O(n + m). See [arrays.md](arrays.md).

### Common problems

- [Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/)
- [Search a 2D Matrix II](https://leetcode.com/problems/search-a-2d-matrix-ii/)
- [Find K-th Smallest Pair Distance](https://leetcode.com/problems/find-k-th-smallest-pair-distance/) (search on answer)

---

## Pattern 6: Peak / Valley

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

---

## Common pitfalls

| Pitfall                              | Fix                                              |
|--------------------------------------|--------------------------------------------------|
| Infinite loop with `hi = mid` and `lo <= hi` | Use `lo < hi` + `hi = len(nums)` (half-open) |
| Off-by-one (`mid - 1` vs `mid`)      | Match `hi` init to interval style (closed vs open) |
| Overflow (C++/Java)                  | `mid = lo + (hi - lo) // 2`                       |
| Missing duplicates                   | When `nums[lo] == nums[mid] == nums[hi]`, `lo++; hi--` (rotated II) |
| Predicate not monotonic              | Search-on-answer needs `check(x)` to be monotonic |

---

## Quick reference

| Pattern              | Search space          | Predicate           |
|----------------------|-----------------------|---------------------|
| Exact match          | Sorted array          | `==`                |
| Lower/upper bound    | Sorted array          | `>=` / `>`          |
| Search on answer     | Answer range          | `check(x)` monotonic |
| Rotated array        | Sorted-then-rotated   | Which half is sorted |
| 2D matrix            | `n*m` flat            | Same as 1D          |
| Peak                 | Unsorted              | Slope direction     |
