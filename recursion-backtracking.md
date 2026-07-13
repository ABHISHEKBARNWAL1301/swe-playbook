# Recursion, Backtracking & Divide and Conquer

Recursion = define `f(input)` in terms of `f(smaller_input)` + base case.  
Backtracking = recursion + "undo choice and try another."    
Divide & conquer = recursion + "split, solve halves, combine."

## Contents

- [Recursion](#recursion)
- [Backtracking — Choose / Explore / Unchoose](#backtracking--choose--explore--unchoose)
- [Divide & Conquer](#divide--conquer)
    - [Merge Sort](#merge-sort)
    - [Quick Sort](#quick-sort)
    - [Quickselect](#quickselect)

---
## Recursion

```
                   ┌─ Base case → return
recursive call ────┤
                   └─ Decompose → recurse → combine
```

Every recursive function needs:

1. **Base case** — when to stop.
2. **Recursive case** — call self with smaller input.
3. **Combine** — use the recursive result.

### Example: factorial

```python
def fact(n):
    if n <= 1: return 1
    return n * fact(n - 1)
```

### Example: reverse a string

```python
def rev(s):
    if len(s) <= 1: return s
    return rev(s[1:]) + s[0]
```

---

## Backtracking — Choose / Explore / Unchoose
Build a solution incrementally in a shared `path`. At each step: **choose** a candidate, **explore** deeper, then **unchoose** (undo) so the next candidate starts from a clean state.

```python
def recn(state):
    if is_solution(state):
        record(state)
        return
    for choice in choices(state):
        apply(choice, state)
        recn(state)
        undo(choice, state)
```

```python
# Combination Sum

# Identical, except exploring passes `i` (not `i + 1`) so the same element can be picked again, and # the base case checks the running total.

def combination_sum(candidates, target):
    res, path = [], []

    def backtrack(start, remaining):
        if remaining == 0:
            res.append(path[:])
            return
        if remaining < 0:                # prune: overshot the target
            return
        for i in range(start, len(candidates)):
            path.append(candidates[i])                   # choose
            backtrack(i, remaining - candidates[i])      # explore (i → reuse allowed)
            path.pop()                                   # unchoose

    backtrack(0, target)
    return res
```

```python
# Permutations

# Order matters, so every step may pick any element not already in `path`.

def permute(nums):
    res, path = [], []
    used = [False] * len(nums)

    def backtrack():
        if len(path) == len(nums):       # record only full-length permutations
            res.append(path[:])
            return
        for i in range(len(nums)):
            if used[i]:
                continue
            used[i] = True               # choose
            path.append(nums[i])
            backtrack()                  # explore
            path.pop()                   # unchoose
            used[i] = False

    backtrack()
    return res

```
```python
# Pruning duplicates

# Sort the input first, then skip a candidate equal to its left neighbor **within the same recursion level**:

    for i in range(start, len(nums)):
        if i > start and nums[i] == nums[i - 1]:   # skip duplicate at this level
            continue
        ...
```

For permutations, the "same level" check becomes: skip `nums[i]` if `nums[i] == nums[i-1] and not used[i-1]` (its identical twin hasn't been placed yet on this path).

### Common problems

- [Subsets](https://leetcode.com/problems/subsets/)
- [Subsets II](https://leetcode.com/problems/subsets-ii/)
- [Permutations](https://leetcode.com/problems/permutations/)
- [Permutations II](https://leetcode.com/problems/permutations-ii/)
- [Combinations](https://leetcode.com/problems/combinations/)
- [Combination Sum](https://leetcode.com/problems/combination-sum/)
- [Combination Sum II](https://leetcode.com/problems/combination-sum-ii/)
- [Letter Combinations of a Phone Number](https://leetcode.com/problems/letter-combinations-of-a-phone-number/)
- [Generate Parentheses](https://leetcode.com/problems/generate-parentheses/)
- [Palindrome Partitioning](https://leetcode.com/problems/palindrome-partitioning/)
- [N-Queens](https://leetcode.com/problems/n-queens/)
- [N-Queens II](https://leetcode.com/problems/n-queens-ii/)
- [Sudoku Solver](https://leetcode.com/problems/sudoku-solver/)
- [Remove Invalid Parentheses](https://leetcode.com/problems/remove-invalid-parentheses/)
- [Restore IP Addresses](https://leetcode.com/problems/restore-ip-addresses/)

---

## Divide & Conquer

**Divide** into independent subproblems → **conquer** each recursively → **combine** the results. The cost follows `T(n) = a·T(n/b) + f(n)` (see the Master theorem below).

### Merge Sort

`O(n log n)` time, stable, `O(n)` space. Split in half, sort each half, merge.

```python
def merge_sort(nums):
    if len(nums) <= 1:
        return nums
    mid = len(nums) // 2
    left  = merge_sort(nums[:mid])
    right = merge_sort(nums[mid:])
    return merge(left, right)

def merge(a, b):
    out, i, j = [], 0, 0
    while i < len(a) and j < len(b):
        if a[i] <= b[j]:
            out.append(a[i]); i += 1
        else:
            out.append(b[j]); j += 1
    out.extend(a[i:]); out.extend(b[j:])
    return out
```

Prefer it when stability matters, for linked lists, or external sorts.

### Quick Sort

`O(n log n)` average, `O(n²)` worst, in-place. Pick a pivot, partition around it, recurse on both sides.

```python
def quick_sort(nums, lo=0, hi=None):
    if hi is None:
        hi = len(nums) - 1
    if lo >= hi:
        return
    p = partition(nums, lo, hi)
    quick_sort(nums, lo, p - 1)
    quick_sort(nums, p + 1, hi)

def partition(nums, lo, hi):          # Lomuto: pivot = last element
    pivot = nums[hi]
    i = lo
    for j in range(lo, hi):
        if nums[j] <= pivot:
            nums[i], nums[j] = nums[j], nums[i]
            i += 1
    nums[i], nums[hi] = nums[hi], nums[i]
    return i
```

Avoid the `O(n²)` worst case by randomizing the pivot or using median-of-three.

### Quickselect

`O(n)` average. Find the k-th smallest without fully sorting — partition, then recurse into **one** side only.

```python
def quickselect(nums, k):             # k is 0-indexed
    lo, hi = 0, len(nums) - 1
    while lo <= hi:
        p = partition(nums, lo, hi)
        if p == k:
            return nums[p]
        if p < k:
            lo = p + 1
        else:
            hi = p - 1
```

> **Count inversions** is a merge-sort variant: while merging, each element taken from the right half forms an inversion with every element still left in the left half.

### Common problems

- [Sort an Array](https://leetcode.com/problems/sort-an-array/)
- [Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/) (quickselect or heap)
- [Count of Smaller Numbers After Self](https://leetcode.com/problems/count-of-smaller-numbers-after-self/) (merge-sort)
- [Reverse Pairs](https://leetcode.com/problems/reverse-pairs/)
- [Maximum Subarray](https://leetcode.com/problems/maximum-subarray/) (D&C version)
- [Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/) (pairwise merge, or heap — see [heaps.md](heaps.md))
- [Sort List](https://leetcode.com/problems/sort-list/) (merge sort on a linked list)
- [Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/) (binary-search partition)
- [Different Ways to Add Parentheses](https://leetcode.com/problems/different-ways-to-add-parentheses/) (split at each operator)
- [Longest Substring with At Least K Repeating Characters](https://leetcode.com/problems/longest-substring-with-at-least-k-repeating-characters/) (split on rare chars)
- [The Skyline Problem](https://leetcode.com/problems/the-skyline-problem/) (merge half-skylines)
- [Count of Range Sum](https://leetcode.com/problems/count-of-range-sum/) (merge-sort on prefix sums)
