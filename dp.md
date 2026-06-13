# Dynamic Programming

Dynamic programming is a method for solving complex problems by breaking them down into simpler subproblems. It is applicable when the problem has **overlapping subproblems** and **optimal substructure** properties. The main idea is to store the results of subproblems to avoid redundant work, which can significantly reduce the time complexity of algorithms.

## Top-down (Memoization)

In the top-down approach, we start with the original problem and recursively break it down into smaller subproblems. We use a cache (often a dictionary) to store the results of these subproblems. If we encounter the same subproblem again, we can simply return the cached result instead of recomputing it.

## Bottom-up (Tabulation)

In the bottom-up approach, we start with the smallest subproblems and iteratively build up the solution to the original problem. We typically use a table (like an array) to store the results of subproblems. This approach often involves filling out a table based on the relationships between subproblems, and it can be more space-efficient than the top-down approach.

---

## DP vs Greedy: When to Use Which?

Before diving into patterns, let's understand the fundamental difference between DP and Greedy.

**Problem:** You're a robber with two streets to rob. Each house has some money. You can rob houses from both streets, but you cannot rob two adjacent houses from the same street (alarms will trigger). What's the maximum money you can steal?

```
Street A: [2, 7, 9, 3, 1]
Street B: [3, 2, 6, 8, 2]
```

**Greedy Robber:** "I'll always rob the house with most money available!"

- Rob B[0] = 3 (greedy)
- Rob A[1] = 7
- Rob B[2] = 6
- Rob A[3] = 3
- Rob B[4] = 2
- **Total: 21**

**DP Robber:** "Let me consider all valid combinations!"

- Option 1: A[0]=2, B[1]=2, A[2]=9, B[3]=8, A[4]=1 → Total = **22**
- Option 2: B[0]=3, A[1]=7, B[2]=6, A[3]=3, B[4]=2 → Total = 21
- **Best: 22**

**Why Greedy Failed:** By greedily picking B[0] first, it blocked access to the optimal combination that includes both 9 and 8 from different streets.

- **When Greedy Works:** Problems with the "greedy choice property" where local optimal leads to global optimal (e.g., Activity Selection, Huffman Coding).
- **When DP is Needed:** Problems requiring exploring multiple choices where greedy fails (e.g., most optimization problems, counting problems).

---

## Pattern 1: Linear DP (1D)

**Why this pattern matters:** Foundation of DP. Teaches the core idea — break problem into subproblems, build the answer incrementally.

### Example: House Robber

`nums[i]` = money in house `i`. Cannot rob two adjacent houses. Return max money.

```
nums = [2,7,9,3,1]  →  12   (rob 2 + 9 + 1)
```

### Recurrence

At each house `i`, pick or skip:

```
f(i) = max(nums[i] + f(i+2),  f(i+1))
f(i) = 0  for i >= n
```

`f(2)` and `f(3)` recur in different branches → overlapping subproblems → memoize.

### Recursion — O(2^n)

```python
def f(i):
    if i >= n: return 0
    return max(nums[i] + f(i+2), f(i+1))
```

### Memoization — O(n) time, O(n) space

```python
memo = {}
def f(i):
    if i >= n: return 0
    if i in memo: return memo[i]
    memo[i] = max(nums[i] + f(i+2), f(i+1))
    return memo[i]
```

### Tabulation — O(n) time, O(n) space

`dp[i]` = max money from index `i` onward. Fill right to left.

```python
dp = [0] * (n + 2)
for i in range(n - 1, -1, -1):
    dp[i] = max(nums[i] + dp[i+2], dp[i+1])
return dp[0]
```

### Space-optimized — O(n) time, O(1) space

Only `dp[i+1]` and `dp[i+2]` are needed.

```python
next1 = next2 = 0
for i in range(n - 1, -1, -1):
    curr = max(nums[i] + next2, next1)
    next2, next1 = next1, curr
return next1
```

### Pattern

Standard **Pick vs Not Pick** DP. State `f(i)`, choices = take/skip, recurrence `f(i) = max(nums[i] + f(i+2), f(i+1))`.

**Practice problems:**
- [Fibonacci Number](https://leetcode.com/problems/fibonacci-number/)
- [House Robber](https://leetcode.com/problems/house-robber/)
- [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)
- [Min Cost Climbing Stairs](https://leetcode.com/problems/min-cost-climbing-stairs/)
- [Word Break](https://leetcode.com/problems/word-break/)
- [Decode Ways](https://leetcode.com/problems/decode-ways/)
- [House Robber II](https://leetcode.com/problems/house-robber-ii/)

## Pattern 2: Longest Increasing Subsequence (LIS)

**Why this pattern matters:** Appears in version control diffs, patience sorting, box stacking. The `O(n log n)` binary-search variant is a must-know.

### Example: Longest Increasing Subsequence

Given `nums`, return the length of the longest strictly increasing subsequence (not necessarily contiguous).

```
nums = [10, 9, 2, 5, 3, 7, 101, 18]  →  4   (2, 3, 7, 101)
```

### Recurrence (pick vs not pick)

State `f(i, prev)` = LIS length starting at index `i`, where `prev` is the index of the last picked element (`-1` if none):

```
f(i, prev) = max(
    1 + f(i+1, i)        if prev == -1 or nums[i] > nums[prev],   # pick
    f(i+1, prev)                                                  # skip
)
f(i, prev) = 0  for i == n
```

### Recursion — O(2^n)

```python
def f(i, prev):
    if i == n: return 0
    skip = f(i+1, prev)
    take = 0
    if prev == -1 or nums[i] > nums[prev]:
        take = 1 + f(i+1, i)
    return max(take, skip)
```

### Memoization — O(n²) time, O(n²) space

Shift `prev` by `+1` so it indexes into a 2D table.

```python
memo = {}
def f(i, prev):
    if i == n: return 0
    if (i, prev) in memo: return memo[(i, prev)]
    skip = f(i+1, prev)
    take = 0
    if prev == -1 or nums[i] > nums[prev]:
        take = 1 + f(i+1, i)
    memo[(i, prev)] = max(take, skip)
    return memo[(i, prev)]
```

### Tabulation — O(n²) time, O(n) space

`dp[i]` = LIS length **ending at** index `i`. Every element alone is length 1.

```python
dp = [1] * n
for i in range(n):
    for j in range(i):
        if nums[j] < nums[i]:
            dp[i] = max(dp[i], dp[j] + 1)
return max(dp)
```

### Binary search — O(n log n) time, O(n) space

Maintain `tails`, where `tails[k]` = smallest possible tail of an increasing subsequence of length `k+1`. For each `x`, find the leftmost index in `tails` with value `>= x` and overwrite it; append if no such index. Final length of `tails` is the answer.

> `tails` is **not** an actual LIS — just a tracker of best tails.

```python
def lower_bound(arr, target):
    # leftmost index i such that arr[i] >= target, else len(arr)
    lo, hi = 0, len(arr)
    while lo < hi:
        mid = (lo + hi) // 2
        if arr[mid] < target:
            lo = mid + 1
        else:
            hi = mid
    return lo

tails = []
for x in nums:
    i = lower_bound(tails, x)
    if i == len(tails):
        tails.append(x)
    else:
        tails[i] = x
return len(tails)
```

### Pattern

State `f(i, prev)`, choices = take/skip with order constraint `nums[i] > nums[prev]`. The `prev`-index trick generalizes to any "subsequence with a monotonic constraint" problem.

**Practice problems:**

- [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)
- [Number of Longest Increasing Subsequence](https://leetcode.com/problems/number-of-longest-increasing-subsequence/)
- [Longest String Chain](https://leetcode.com/problems/longest-string-chain/)
- [Russian Doll Envelopes](https://leetcode.com/problems/russian-doll-envelopes/)
- [Maximum Length of Pair Chain](https://leetcode.com/problems/maximum-length-of-pair-chain/)
- [Find the Longest Valid Obstacle Course at Each Position](https://leetcode.com/problems/find-the-longest-valid-obstacle-course-at-each-position/)

## Pattern 3: Knapsack (0/1, Unbounded, Bounded)

**Why this pattern matters:** Versatile pattern for resource allocation and subset selection. Two flavors: each item used **once** (0/1) vs. **unlimited** times (unbounded). The only structural difference is one index in the recurrence.

### Example: 0/1 Knapsack

`weights[i]`, `values[i]` for `n` items, capacity `W`. Pick a subset (each item at most once) maximizing total value with total weight `<= W`.

```
weights = [1, 3, 4, 5]
values  = [1, 4, 5, 7]
W = 7
        →  9   (items 1 + 2: weight 3+4, value 4+5)
```

### Recurrence — 0/1

State `f(i, w)` = max value using items `i..n-1` with remaining capacity `w`:

```
f(i, w) = max(
    f(i+1, w),                                       # skip
    values[i] + f(i+1, w - weights[i])  if w >= weights[i]    # take, move to i+1
)
f(n, w) = 0
```

Key bit: after taking item `i`, we move to `i+1` — it cannot be reused.

### Recursion — O(2^n)

```python
def f(i, w):
    if i == n: return 0
    skip = f(i+1, w)
    take = 0
    if w >= weights[i]:
        take = values[i] + f(i+1, w - weights[i])
    return max(take, skip)
```

### Memoization — O(n·W) time and space

```python
memo = {}
def f(i, w):
    if i == n: return 0
    if (i, w) in memo: return memo[(i, w)]
    skip = f(i+1, w)
    take = 0
    if w >= weights[i]:
        take = values[i] + f(i+1, w - weights[i])
    memo[(i, w)] = max(take, skip)
    return memo[(i, w)]
```

### Tabulation — O(n·W) time, O(n·W) space

`dp[i][w]` = max value using items `i..n-1` with capacity `w`. Fill bottom-up from `i = n` down.

```python
dp = [[0] * (W + 1) for _ in range(n + 1)]
for i in range(n - 1, -1, -1):
    for w in range(W + 1):
        skip = dp[i+1][w]
        take = 0
        if w >= weights[i]:
            take = values[i] + dp[i+1][w - weights[i]]
        dp[i][w] = max(take, skip)
return dp[0][W]
```

### Space-optimized — O(n·W) time, O(W) space

Each row depends only on the row below. Use a 1D `dp[w]` and iterate `w` from **high to low** so each item is used at most once.

```python
dp = [0] * (W + 1)
for i in range(n):
    for w in range(W, weights[i] - 1, -1):     # reverse — prevents reuse
        dp[w] = max(dp[w], values[i] + dp[w - weights[i]])
return dp[W]
```

### Extension: Unbounded Knapsack

Same problem, but each item can be picked **unlimited** times.

Only change in the recurrence: when we take item `i`, we stay at `i` instead of moving to `i+1`.

```
f(i, w) = max(
    f(i+1, w),                                       # skip
    values[i] + f(i, w - weights[i])    if w >= weights[i]    # take, stay at i
)
```

Space-optimized version: iterate `w` from **low to high** so the updated `dp[w - weights[i]]` (which already includes item `i`) is reused.

```python
dp = [0] * (W + 1)
for i in range(n):
    for w in range(weights[i], W + 1):         # forward — allows reuse
        dp[w] = max(dp[w], values[i] + dp[w - weights[i]])
return dp[W]
```

### Pattern

| Variant   | Recurrence on take | 1D loop direction |
|-----------|--------------------|--------------------|
| 0/1       | `f(i+1, w - wt)`   | `w` high → low     |
| Unbounded | `f(i,   w - wt)`   | `w` low  → high    |

Same skeleton. Loop direction is the whole trick.

**0/1 Knapsack** (each item used once):

- [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/)
- [Target Sum](https://leetcode.com/problems/target-sum/)
- [Last Stone Weight II](https://leetcode.com/problems/last-stone-weight-ii/)
- [Ones and Zeroes](https://leetcode.com/problems/ones-and-zeroes/)
- [Partition Array Into Two Arrays to Minimize Sum Difference](https://leetcode.com/problems/partition-array-into-two-arrays-to-minimize-sum-difference/)

**Unbounded Knapsack** (items can be used multiple times):

- [Coin Change](https://leetcode.com/problems/coin-change/)
- [Coin Change 2](https://leetcode.com/problems/coin-change-2/)
- [Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/)
- [Perfect Squares](https://leetcode.com/problems/perfect-squares/)
- [Minimum Cost For Tickets](https://leetcode.com/problems/minimum-cost-for-tickets/)



## Pattern 4: String DP

**Why this pattern matters:** Two-string problems (diff, edit distance, alignment) all reduce to a 2D DP on indices `(i, j)`. LCS is the template — most others are 1–2 line tweaks of it.

### Example: Longest Common Subsequence

Given strings `s1`, `s2`, return the length of the longest subsequence present in both (not necessarily contiguous).

```
s1 = "abcde"
s2 = "ace"
                →  3   ("ace")
```

### Recurrence

State `f(i, j)` = LCS length of `s1[i:]` and `s2[j:]`:

```
f(i, j) = 1 + f(i+1, j+1)              if s1[i] == s2[j]
        = max(f(i+1, j), f(i, j+1))    otherwise
f(i, j) = 0  if i == n or j == m
```

Match → consume both. Mismatch → drop one character from either side, take the best.

### Recursion — O(2^(n+m))

```python
def f(i, j):
    if i == n or j == m: return 0
    if s1[i] == s2[j]:
        return 1 + f(i+1, j+1)
    return max(f(i+1, j), f(i, j+1))
```

### Memoization — O(n·m) time and space

```python
memo = {}
def f(i, j):
    if i == n or j == m: return 0
    if (i, j) in memo: return memo[(i, j)]
    if s1[i] == s2[j]:
        memo[(i, j)] = 1 + f(i+1, j+1)
    else:
        memo[(i, j)] = max(f(i+1, j), f(i, j+1))
    return memo[(i, j)]
```

### Tabulation — O(n·m) time, O(n·m) space

`dp[i][j]` = LCS of `s1[i:]` and `s2[j:]`. Fill bottom-up from `i = n`, `j = m`.

```python
dp = [[0] * (m + 1) for _ in range(n + 1)]
for i in range(n - 1, -1, -1):
    for j in range(m - 1, -1, -1):
        if s1[i] == s2[j]:
            dp[i][j] = 1 + dp[i+1][j+1]
        else:
            dp[i][j] = max(dp[i+1][j], dp[i][j+1])
return dp[0][0]
```

### Space-optimized — O(n·m) time, O(m) space

Each row depends only on the row below (`dp[i+1]`) plus one already-computed cell to the right in the current row. Keep two rows.

```python
nxt = [0] * (m + 1)
for i in range(n - 1, -1, -1):
    cur = [0] * (m + 1)
    for j in range(m - 1, -1, -1):
        if s1[i] == s2[j]:
            cur[j] = 1 + nxt[j+1]
        else:
            cur[j] = max(nxt[j], cur[j+1])
    nxt = cur
return nxt[0]
```

### Reconstructing the LCS

Walk the `dp` table from `(0, 0)`: on match, take the char and step to `(i+1, j+1)`; otherwise move toward the larger of `dp[i+1][j]` / `dp[i][j+1]`.

```python
i, j, out = 0, 0, []
while i < n and j < m:
    if s1[i] == s2[j]:
        out.append(s1[i]); i += 1; j += 1
    elif dp[i+1][j] >= dp[i][j+1]:
        i += 1
    else:
        j += 1
return "".join(out)
```

### Pattern

State `f(i, j)` on two index pointers, branch on `s1[i] == s2[j]`. Most string DP problems (Edit Distance, SCS, Delete Operation, Palindromic Subsequence as `LCS(s, reversed(s))`) are LCS with the recurrence's recombination step swapped.

**Practice problems:**

- [Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/)
- [Edit Distance](https://leetcode.com/problems/edit-distance/)
- [Delete Operation for Two Strings](https://leetcode.com/problems/delete-operation-for-two-strings/)
- [Minimum ASCII Delete Sum for Two Strings](https://leetcode.com/problems/minimum-ascii-delete-sum-for-two-strings/)
- [Shortest Common Supersequence](https://leetcode.com/problems/shortest-common-supersequence/)
- [Longest Palindromic Subsequence](https://leetcode.com/problems/longest-palindromic-subsequence/)
- [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)
- [Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/)
- [Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching/)
- [Wildcard Matching](https://leetcode.com/problems/wildcard-matching/)



## Pattern 5: Grid DP

**Why this pattern matters:** 2D state space `(r, c)` with moves in fixed directions. Common in pathfinding, counting paths, min/max cost grids. Once you have the recurrence, the table fill is mechanical.

### Example: Minimum Path Sum

Grid of non-negative ints. Start at top-left, end at bottom-right. Each step goes **right** or **down**. Return the minimum path sum.

```
grid = [[1, 3, 1],
        [1, 5, 1],
        [4, 2, 1]]
                    →  7   (1 → 3 → 1 → 1 → 1)
```

### Recurrence

State `f(r, c)` = min path sum from `(r, c)` to `(n-1, m-1)`:

```
f(r, c) = grid[r][c] + min(f(r+1, c), f(r, c+1))
f(n-1, m-1) = grid[n-1][m-1]
f(r, c) = +∞   if r == n or c == m   (out of bounds)
```

### Recursion — O(2^(n+m))

```python
def f(r, c):
    if r == n or c == m: return float('inf')
    if r == n - 1 and c == m - 1: return grid[r][c]
    return grid[r][c] + min(f(r+1, c), f(r, c+1))
```

### Memoization — O(n·m) time and space

```python
memo = {}
def f(r, c):
    if r == n or c == m: return float('inf')
    if r == n - 1 and c == m - 1: return grid[r][c]
    if (r, c) in memo: return memo[(r, c)]
    memo[(r, c)] = grid[r][c] + min(f(r+1, c), f(r, c+1))
    return memo[(r, c)]
```

### Tabulation — O(n·m) time, O(n·m) space

`dp[r][c]` = min path sum from `(r, c)` onward. Fill bottom-right to top-left.

```python
dp = [[0] * m for _ in range(n)]
dp[n-1][m-1] = grid[n-1][m-1]
for c in range(m - 2, -1, -1):
    dp[n-1][c] = grid[n-1][c] + dp[n-1][c+1]
for r in range(n - 2, -1, -1):
    dp[r][m-1] = grid[r][m-1] + dp[r+1][m-1]
for r in range(n - 2, -1, -1):
    for c in range(m - 2, -1, -1):
        dp[r][c] = grid[r][c] + min(dp[r+1][c], dp[r][c+1])
return dp[0][0]
```

### Space-optimized — O(n·m) time, O(m) space

Row depends only on the row below + the cell to the right in the same row.

```python
dp = [float('inf')] * m
dp[m-1] = 0
for r in range(n - 1, -1, -1):
    for c in range(m - 1, -1, -1):
        right = dp[c+1] if c + 1 < m else float('inf')
        down  = dp[c]                              # old value = row below
        if r == n - 1 and c == m - 1:
            dp[c] = grid[r][c]
        else:
            dp[c] = grid[r][c] + min(right, down)
return dp[0]
```

### Variant: Unique Paths (counting)

Same grid skeleton, but count paths instead of summing costs. Recurrence becomes addition, base is `1`:

```
f(r, c) = f(r+1, c) + f(r, c+1)
f(n-1, m-1) = 1
```

```python
dp = [[1] * m for _ in range(n)]
for r in range(n - 2, -1, -1):
    for c in range(m - 2, -1, -1):
        dp[r][c] = dp[r+1][c] + dp[r][c+1]
return dp[0][0]
```

### Pattern

State `(r, c)` + fixed move set (right/down, or 4-dir, etc.). Recurrence aggregates over allowed moves (`min` / `max` / sum / count). 1D space-opt works when transitions only touch the next row + same row.

**Practice problems:**

- [Unique Paths](https://leetcode.com/problems/unique-paths/)
- [Unique Paths II](https://leetcode.com/problems/unique-paths-ii/)
- [Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/)
- [Maximal Square](https://leetcode.com/problems/maximal-square/)
- [Maximal Rectangle](https://leetcode.com/problems/maximal-rectangle/)
- [Minimum Falling Path Sum](https://leetcode.com/problems/minimum-falling-path-sum/)
- [Count Square Submatrices with All Ones](https://leetcode.com/problems/count-square-submatrices-with-all-ones/)
- [Triangle](https://leetcode.com/problems/triangle/)


## Pattern 6: State Machine DP

**Why this pattern matters:** Carry an extra state (holding / not holding, cooldown, k transactions left, etc.) alongside the index. Stock problems are the canonical family — once you nail the state diagram, the recurrence writes itself.

### Example: Best Time to Buy and Sell Stock II

`prices[i]` = price on day `i`. Unlimited transactions allowed, but cannot hold more than one share at a time. Return max profit.

```
prices = [7, 1, 5, 3, 6, 4]  →  7   (buy 1, sell 5) + (buy 3, sell 6)
```

### State diagram

Two states per day: `0` = not holding, `1` = holding.

```
        buy (-price)
   0 ─────────────────▶ 1
   ▲                    │
   │   sell (+price)    │
   └────────────────────┘

   self-loops on both: do nothing
```

### Recurrence

State `f(i, holding)` = max profit from day `i` onward:

```
f(i, 0) = max(
    f(i+1, 0),                   # rest
    -prices[i] + f(i+1, 1)       # buy
)
f(i, 1) = max(
    f(i+1, 1),                   # hold
    prices[i] + f(i+1, 0)        # sell
)
f(n, *) = 0
```

### Recursion — O(2^n)

```python
def f(i, holding):
    if i == n: return 0
    if holding:
        return max(f(i+1, 1), prices[i] + f(i+1, 0))
    return max(f(i+1, 0), -prices[i] + f(i+1, 1))
```

### Memoization — O(n) time, O(n) space

```python
memo = {}
def f(i, holding):
    if i == n: return 0
    if (i, holding) in memo: return memo[(i, holding)]
    if holding:
        memo[(i, holding)] = max(f(i+1, 1), prices[i] + f(i+1, 0))
    else:
        memo[(i, holding)] = max(f(i+1, 0), -prices[i] + f(i+1, 1))
    return memo[(i, holding)]
```

### Tabulation — O(n) time, O(n) space

`dp[i][s]` = max profit from day `i` onward in state `s`.

```python
dp = [[0, 0] for _ in range(n + 1)]
for i in range(n - 1, -1, -1):
    dp[i][0] = max(dp[i+1][0], -prices[i] + dp[i+1][1])
    dp[i][1] = max(dp[i+1][1],  prices[i] + dp[i+1][0])
return dp[0][0]
```

### Space-optimized — O(n) time, O(1) space

Each day only needs the next day's two values.

```python
not_hold, hold = 0, 0
for i in range(n - 1, -1, -1):
    new_not_hold = max(not_hold, -prices[i] + hold)
    new_hold     = max(hold,      prices[i] + not_hold)
    not_hold, hold = new_not_hold, new_hold
return not_hold
```

### Extension: extra states

The pattern scales by adding dimensions to the state:

| Variant            | Extra state              | Recurrence tweak |
|--------------------|--------------------------|------------------|
| At most `k` txns   | `k` transactions left    | decrement `k` on buy (or sell) |
| Cooldown 1 day     | after sell → skip 1 day  | sell transitions to `f(i+2, 0)` |
| Transaction fee    | constant `fee`           | subtract `fee` on sell |

### Pattern

State = `(index, current_state)`. Transitions are the state-machine edges; recurrence picks the max over all outgoing edges from `(i, s)`. Add a dimension for every independent quantity you must track (`k`, cooldown flag, fee already paid, etc.).

**Practice problems:**

- [Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)
- [Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)
- [Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/)
- [Best Time to Buy and Sell Stock IV](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/)
- [Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)
- [Best Time to Buy and Sell Stock with Transaction Fee](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)


## Pattern 7: Interval DP

**Why this pattern matters:** Tests ability to think about problems in ranges/intervals. Common in scheduling, matrix chain multiplication type problems, and game theory.

**Practice problems:**

- [Burst Balloons](https://leetcode.com/problems/burst-balloons/)
- [Minimum Score Triangulation of Polygon](https://leetcode.com/problems/minimum-score-triangulation-of-polygon/)
- [Minimum Cost Tree From Leaf Values](https://leetcode.com/problems/minimum-cost-tree-from-leaf-values/)
- [Unique Binary Search Trees](https://leetcode.com/problems/unique-binary-search-trees/)
- [Unique Binary Search Trees II](https://leetcode.com/problems/unique-binary-search-trees-ii/)
- [Minimum Cost to Merge Stones](https://leetcode.com/problems/minimum-cost-to-merge-stones/)
- [Guess Number Higher or Lower II](https://leetcode.com/problems/guess-number-higher-or-lower-ii/)


## Pattern 8: Tree DP

**Why this pattern matters:** Combines tree traversal with DP. Important for system design (like designing file systems) and optimization problems on hierarchical structures.

**Practice problems:**

- [House Robber III](https://leetcode.com/problems/house-robber-iii/)
- [Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/)
- [Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)
- [Binary Tree Cameras](https://leetcode.com/problems/binary-tree-cameras/)
- [Maximum Sum BST in Binary Tree](https://leetcode.com/problems/maximum-sum-bst-in-binary-tree/)
- [Difference Between Maximum and Minimum Price Sum](https://leetcode.com/problems/difference-between-maximum-and-minimum-price-sum/)

## Pattern 9: Digit DP

**Why this pattern matters:** Specialized pattern for counting numbers with certain properties. Appears in competitive programming and some advanced interviews.

**Practice problems:**

- [Number of Digit One](https://leetcode.com/problems/number-of-digit-one/)
- [Count Numbers with Unique Digits](https://leetcode.com/problems/count-numbers-with-unique-digits/)
- [Numbers At Most N Given Digit Set](https://leetcode.com/problems/numbers-at-most-n-given-digit-set/)
- [Numbers With Repeated Digits](https://leetcode.com/problems/numbers-with-repeated-digits/)
- [Count Special Integers](https://leetcode.com/problems/count-special-integers/)

## Pattern 10: Game Theory DP (Minimax)

**Why this pattern matters:** Models two-player games where both play optimally. Important for AI, game development, and adversarial scenarios.

**Practice problems:**

- [Predict the Winner](https://leetcode.com/problems/predict-the-winner/)
- [Stone Game](https://leetcode.com/problems/stone-game/)
- [Stone Game II](https://leetcode.com/problems/stone-game-ii/)
- [Stone Game III](https://leetcode.com/problems/stone-game-iii/)
- [Can I Win](https://leetcode.com/problems/can-i-win/)
- [Stone Game IV](https://leetcode.com/problems/stone-game-iv/)

## Pattern 11: Bitmask DP

**Why this pattern matters:** Powerful technique for problems with small sets (≤20 elements) where you need to track subsets. Essential for Traveling Salesman Problem variants and NP-hard problem approximations.

**Practice problems:**

- [Partition to K Equal Sum Subsets](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/)
- [Shortest Path Visiting All Nodes](https://leetcode.com/problems/shortest-path-visiting-all-nodes/)
- [Find the Shortest Superstring](https://leetcode.com/problems/find-the-shortest-superstring/)
- [Smallest Sufficient Team](https://leetcode.com/problems/smallest-sufficient-team/)
- [Number of Ways to Wear Different Hats to Each Other](https://leetcode.com/problems/number-of-ways-to-wear-different-hats-to-each-other/)
- [Minimum Number of Work Sessions to Finish the Tasks](https://leetcode.com/problems/minimum-number-of-work-sessions-to-finish-the-tasks/)

## Pattern 12: DP on Subsequences

**Why this pattern matters:** Critical for array partitioning and subset generation problems. Teaches how to handle exponential search spaces efficiently.

**Practice problems:**

- [Distinct Subsequences](https://leetcode.com/problems/distinct-subsequences/)
- [Distinct Subsequences II](https://leetcode.com/problems/distinct-subsequences-ii/)
- [Arithmetic Slices II - Subsequence](https://leetcode.com/problems/arithmetic-slices-ii-subsequence/)
- [Number of Unique Good Subsequences](https://leetcode.com/problems/number-of-unique-good-subsequences/)
- [Constrained Subsequence Sum](https://leetcode.com/problems/constrained-subsequence-sum/)

## Pattern 13: Probability DP

**Why this pattern matters:** Models uncertain outcomes. Important for risk analysis, game development, and any scenario involving randomness or probability.

**Practice problems:**

- [Knight Probability in Chessboard](https://leetcode.com/problems/knight-probability-in-chessboard/)
- [Soup Servings](https://leetcode.com/problems/soup-servings/)
- [New 21 Game](https://leetcode.com/problems/new-21-game/)
- [Toss Strange Coins](https://leetcode.com/problems/toss-strange-coins/)
- [Probability of a Two Boxes Having The Same Number of Distinct Balls](https://leetcode.com/problems/probability-of-a-two-boxes-having-the-same-number-of-distinct-balls/)



