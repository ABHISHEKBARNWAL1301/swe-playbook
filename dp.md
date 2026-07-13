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

## Patterns
- [Pattern 1: Linear DP (1D)](#pattern-1-linear-dp-1d)
- [Pattern 2: Longest Increasing Subsequence (LIS)](#pattern-2-longest-increasing-subsequence-lis)
- [Pattern 3: Knapsack (0/1, Unbounded, Bounded)](#pattern-3-knapsack-01-unbounded-bounded)
- [Pattern 4: String DP](#pattern-4-string-dp)
- [Pattern 5: State Machine DP](#pattern-5-state-machine-dp)
- [Pattern 6: Interval DP](#pattern-6-interval-dp)
- [Pattern 7: Game Theory DP (Minimax)](#pattern-7-game-theory-dp-minimax)
- [Pattern 8: Grid DP](#pattern-8-grid-dp)
- [Pattern 9: Tree DP](#pattern-9-tree-dp)
- [Bitmask & Probability DP](#bitmask--probability-dp)
- [Must-Do / FAANG DP Questions](#must-do--faang-dp-questions)

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
- [House Robber](https://leetcode.com/problems/house-robber/) — pick vs skip; can't take two adjacent houses.
- [Word Break](https://leetcode.com/problems/word-break/) — `dp[i]` = can `s[:i]` be segmented into dictionary words.

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

- [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/) — the template; O(n log n) via patience sorting.
- [Russian Doll Envelopes](https://leetcode.com/problems/russian-doll-envelopes/) — sort by width, run LIS on height (2D LIS).


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

- [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/) — 0/1 subset-sum: can a subset hit total/2?

**Unbounded Knapsack** (items can be used multiple times):

- [Coin Change](https://leetcode.com/problems/coin-change/) — min coins to make an amount; each coin reusable (forward loop).



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

State `f(i, j)` = LCS length of the prefixes `s1[:i]` and `s2[:j]` (first `i` chars of `s1`, first `j` chars of `s2`):

```
f(i, j) = 1 + f(i-1, j-1)              if s1[i-1] == s2[j-1]
        = max(f(i-1, j), f(i, j-1))    otherwise
f(i, j) = 0  if i == 0 or j == 0
```

Match → consume both. Mismatch → drop one character from either side, take the best. The answer is `f(n, m)`.

### Recursion — O(2^(n+m))

```python
def f(i, j):
    if i == 0 or j == 0: return 0
    if s1[i-1] == s2[j-1]:
        return 1 + f(i-1, j-1)
    return max(f(i-1, j), f(i, j-1))
```

### Memoization — O(n·m) time and space

```python
memo = {}
def f(i, j):
    if i == 0 or j == 0: return 0
    if (i, j) in memo: return memo[(i, j)]
    if s1[i-1] == s2[j-1]:
        memo[(i, j)] = 1 + f(i-1, j-1)
    else:
        memo[(i, j)] = max(f(i-1, j), f(i, j-1))
    return memo[(i, j)]
```

### Tabulation — O(n·m) time, O(n·m) space

`dp[i][j]` = LCS of the prefixes `s1[:i]` and `s2[:j]`. Fill from `i = 1, j = 1` upward; row/col `0` are the empty-prefix base cases.

```python
    n, m = len(text1), len(text2)
    dp = [[0]*(m+1) for _ in range(n+1)]
    
    for i in range(1, n+1):
        for j in range(1, m+1):
            if text1[i-1] == text2[j-1]:
                dp[i][j] = 1 + dp[i-1][j-1]
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
        
    return dp[n][m]
```

### Space-optimized — O(n·m) time, O(m) space

Each row depends only on the row below (`dp[i+1]`) plus one already-computed cell to the right in the current row. Keep two rows.

```python
    n, m = len(text1), len(text2)
    prev = [0]*(m+1)
    
    for i in range(1, n+1):
        curr = [0]*(m+1)
        for j in range(1, m+1):
            if text1[i-1] == text2[j-1]:
                curr[j] = 1 + prev[j-1]
            else:
                curr[j] = max(prev[j], curr[j-1])
        prev = curr

    return prev[m]
```

### Reconstructing the LCS

Walk the `dp` table backward from `(n, m)`: on match, take the char and step to `(i-1, j-1)`; otherwise move toward the larger of `dp[i-1][j]` / `dp[i][j-1]`. Reverse at the end since we collect from the back.

```python
i, j, out = n, m, []
while i > 0 and j > 0:
    if s1[i-1] == s2[j-1]:
        out.append(s1[i-1]); i -= 1; j -= 1
    elif dp[i-1][j] >= dp[i][j-1]:
        i -= 1
    else:
        j -= 1
return "".join(reversed(out))
```

### Pattern

State `f(i, j)` on two index pointers, branch on `s1[i] == s2[j]`. Most string DP problems (Edit Distance, SCS, Delete Operation, Palindromic Subsequence as `LCS(s, reversed(s))`) are LCS with the recurrence's recombination step swapped.

**Practice problems:**

- [Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/) — the 2D `(i, j)` template every other string-DP tweaks.
- [Edit Distance](https://leetcode.com/problems/edit-distance/) — LCS-shaped with insert / delete / replace transitions.




## Pattern 5: State Machine DP

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

- [Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) — two states: holding / not holding.
- [Best Time to Buy and Sell Stock IV](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/) — add a "transactions left" (k) dimension to the state.




## Pattern 6: Interval DP

**The idea:** the subproblem is a contiguous range `[i, j]`. To solve it you choose a **partition point** `k` inside the range, solve the two pieces independently, and combine them. Because every range depends only on **shorter** ranges, you solve small intervals first and build up.

```
solve(i, j):
    if base case: return ...
    best = optimum over all k in [i, j) of:
              solve(i, k) + solve(k+1, j) + cost_of_combining
    return best
```

Two things change from problem to problem: **what the partition `k` means** (a split position, the last element removed, the root of a tree...) and **the combine cost**. Everything else is boilerplate.

> **Evaluation order (tabulation):** fill by increasing interval length, so all shorter intervals are ready when you need them.

---

### Worked example: Matrix Chain Multiplication

**Problem:** You have `n` matrices to multiply in order. Matrix `i` has shape `dims[i] x dims[i+1]`. Matrix multiplication is associative — the *result* is the same regardless of how you parenthesise — but the *cost* differs. Find the cheapest parenthesisation.

Multiplying A(10×30), B(30×5), C(5×60):
- `(AB)C` → 10·30·5 + 10·5·60 = **4500**
- `A(BC)` → 30·5·60 + 10·30·60 = **27000**

Here `k` = where the chain splits. `solve(i, j)` = min cost to multiply matrices `i..j`. Splitting at `k` produces one matrix of shape `dims[i] x dims[k+1]` and another of `dims[k+1] x dims[j+1]`; combining them costs `dims[i] * dims[k+1] * dims[j+1]`.

### Recursion

```python
def f(i, j):
    if i == j:                       # single matrix, nothing to multiply
        return 0
    best = float('inf')
    for k in range(i, j):            # split between k and k+1
        cost = f(i, k) + f(k+1, j) + dims[i] * dims[k+1] * dims[j+1]
        best = min(best, cost)
    return best
```

### Memoization — O(n³) time, O(n²) space

```python
memo = {}
def f(i, j):
    if i == j:
        return 0
    if (i, j) in memo:
        return memo[(i, j)]
    best = float('inf')
    for k in range(i, j):
        cost = f(i, k) + f(k+1, j) + dims[i] * dims[k+1] * dims[j+1]
        best = min(best, cost)
    memo[(i, j)] = best
    return best
```

### Tabulation — O(n³) time, O(n²) space

```python
dp = [[0] * n for _ in range(n)]
for length in range(2, n + 1):           # interval length
    for i in range(n - length + 1):
        j = i + length - 1
        dp[i][j] = float('inf')
        for k in range(i, j):
            cost = dp[i][k] + dp[k+1][j] + dims[i] * dims[k+1] * dims[j+1]
            dp[i][j] = min(dp[i][j], cost)
return dp[0][n - 1]
```

---

### Variant: the "last operation" trick (Burst Balloons)

Sometimes splitting *forward* breaks down because each choice reshapes the problem — bursting a balloon changes its neighbours. The fix: let `k` be the **last** thing done in `[i, j]`. At that moment `i` and `j` are guaranteed to be its neighbours (everything between is gone), so the two sides become independent.

```python
# pad with 1s; dp[i][j] = max coins from the open interval (i, j)
nums = [1] + nums + [1]
n = len(nums)
dp = [[0] * n for _ in range(n)]
for length in range(2, n):
    for i in range(n - length):
        j = i + length
        for k in range(i + 1, j):        # k = last balloon burst in (i, j)
            dp[i][j] = max(dp[i][j],
                           dp[i][k] + nums[i] * nums[k] * nums[j] + dp[k][j])
return dp[0][n - 1]
```

---

### Variant: Palindrome Partitioning II (min cuts)

**Problem:** Partition string `s` so every part is a palindrome, using the **fewest cuts**.

Here the "interval" thinking is on prefixes: `cuts[i]` = min cuts for `s[:i]`. Precompute `isPal[a][b]` (itself an interval DP), then for each end `i` try every start `j` of the last palindrome piece.

```python
n = len(s)
isPal = [[False] * n for _ in range(n)]
for i in range(n - 1, -1, -1):
    for j in range(i, n):
        if s[i] == s[j] and (j - i < 2 or isPal[i+1][j-1]):
            isPal[i][j] = True

cuts = [0] * (n + 1)
for i in range(1, n + 1):
    cuts[i] = float('inf')
    for j in range(i):
        if isPal[j][i-1]:                 # s[j:i] is a palindrome
            cuts[i] = min(cuts[i], (0 if j == 0 else cuts[j] + 1))
return cuts[n]
```

---

### Variant: Egg Drop

**Problem:** `k` eggs, `n` floors. Find the highest safe floor with the **minimum number of trials in the worst case**. Dropping an egg from a floor either breaks it (search below with one fewer egg) or not (search above with same eggs).

`dp[e][f]` = min worst-case trials with `e` eggs and `f` floors. For each floor `x` you drop from, the worst case is `1 + max(broken, survived)`; minimise over `x`. (The naive split is O(k·n²); a binary-search-on-`x` or the "moves" reformulation makes it faster.)

```python
def f(eggs, floors):
    if floors == 0 or eggs == 1:
        return floors
    best = float('inf')
    for x in range(1, floors + 1):
        broke   = f(eggs - 1, x - 1)      # egg breaks → below
        survive = f(eggs, floors - x)     # egg survives → above
        best = min(best, 1 + max(broke, survive))
    return best
```

---

### Recognition checklist

- Subproblem is a **contiguous range** and depends only on strictly smaller ranges
- You choose a **partition / split / last-element** inside the range and combine the pieces
- Problem involves **merging, removing, cutting, or building** something over a range

### Complexity

Standard interval DP is **O(n³) time, O(n²) space** — two loops for the interval `(i, j)`, one for the split `k`.

### Practice problems

- [Burst Balloons](https://leetcode.com/problems/burst-balloons/) — think about which balloon is burst *last* in a range.
- [Palindrome Partitioning II](https://leetcode.com/problems/palindrome-partitioning-ii/) — min cuts; precompute palindrome table, then interval DP.

---

## Pattern 7: Game Theory DP (Minimax)

**The idea:** two players alternate turns and **both play optimally**. The trick is to track the score from the **current player's** perspective at every state. Whatever the opponent gains on their turn is your loss, so you flip the sign as you recurse.

`dp[i][j]` = best **score advantage** (my total − opponent's total) the player to move can guarantee on the interval `[i, j]`.

### Worked example: Predict the Winner

**Problem:** Players take turns taking a number from **either end** of `nums`. Both play to maximise their own total. Can player 1 win (or tie)?

If I take `nums[i]`, the opponent then faces `[i+1, j]` and plays optimally — their guaranteed advantage there becomes *my* deficit. So I subtract the subproblem's result:

```
dp[i][j] = max(nums[i] - dp[i+1][j],   # take left end
               nums[j] - dp[i][j-1])   # take right end
```

### Recursion

```python
def f(i, j):
    if i == j:
        return nums[i]
    return max(nums[i] - f(i + 1, j),
               nums[j] - f(i, j - 1))

return f(0, n - 1) >= 0                  # >= 0 means player 1 wins / ties
```

### Memoization — O(n²) time, O(n²) space

```python
memo = {}
def f(i, j):
    if i == j:
        return nums[i]
    if (i, j) in memo:
        return memo[(i, j)]
    memo[(i, j)] = max(nums[i] - f(i + 1, j),
                       nums[j] - f(i, j - 1))
    return memo[(i, j)]

return f(0, n - 1) >= 0
```

### Tabulation — O(n²) time, O(n²) space

```python
dp = [[0] * n for _ in range(n)]
for i in range(n):
    dp[i][i] = nums[i]
for length in range(2, n + 1):
    for i in range(n - length + 1):
        j = i + length - 1
        dp[i][j] = max(nums[i] - dp[i+1][j],
                       nums[j] - dp[i][j-1])
return dp[0][n - 1] >= 0
```

### Practice problems

- [Predict the Winner](https://leetcode.com/problems/predict-the-winner/) — `dp[i][j]` = best score difference the current player can force.
- [Stone Game](https://leetcode.com/problems/stone-game/) — same score-difference minimax, picking from either end.

## Pattern 8: Grid DP

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
class Solution:
    def minPathSum(self, grid: List[List[int]]) -> int:
        def dfs(i, j):
            if  0 <= i < n and 0 <= j < m:
                if i == n-1 and j == m-1:
                    return grid[i][j]
                
                if (i, j) in memo:
                    return memo[(i, j)]
            
                memo[(i, j)] = grid[i][j] + min(dfs(i+1, j), dfs(i, j+1))
                return memo[(i, j)]
            
            return float(inf)

        memo = {}
        n, m = len(grid), len(grid[0])
        return dfs(0, 0)
```

### Tabulation — O(n·m) time, O(n·m) space

`dp[r][c]` = min path sum from `(r, c)` onward. Fill bottom-right to top-left.

```python
    n, m = len(grid), len(grid[0])
    dp = [[float(inf)] * (m+1) for _ in range(n+1)]
    dp[n-1][m-1] = grid[n - 1][m - 1]

    for r in range(n - 1, -1, -1):
        for c in range(m - 1, -1, -1):
            if r == n - 1 and c == m - 1:
                continue

            dp[r][c] = grid[r][c] + min(dp[r + 1][c], dp[r][c + 1])

    return dp[0][0]
```

### Space-optimized — O(n·m) time, O(m) space

Each row only needs the row below it. Keep a single 1D array and update in-place right-to-left.

```python
dp = [float('inf')] * (m + 1)
dp[m - 1] = 0  # sentinel: stepping "out of bounds right" = inf, handled by init

for r in range(n - 1, -1, -1):
    new_dp = [float('inf')] * (m + 1)
    for c in range(m - 1, -1, -1):
        down  = dp[c]           # row below, same column
        right = new_dp[c + 1]   # same row, column to the right
        new_dp[c] = grid[r][c] + min(down, right)
    dp = new_dp

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

- [Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/) — the template: `dp[r][c] = grid + min(up, left)`.
- [Maximal Square](https://leetcode.com/problems/maximal-square/) — `dp[r][c] = 1 + min` of three neighbors (square side length).


## Pattern 9: Tree DP

**Why this pattern matters:** The tree structure enforces a natural subproblem order — children before parents (post-order). Every node becomes a DP state, and you propagate answers bottom-up. The key skill is deciding **what to return from each node** so the parent has exactly what it needs.

---

### Core idea

```
dfs(node) -> (some state tuple)
    if node is None: return base case
    left  = dfs(node.left)
    right = dfs(node.right)
    # combine left, right, node.val → answer for this subtree
    return result
```

The global answer is often updated **inside** dfs, not just at the root, because the optimal solution might be rooted at any node.

---

### Sub-pattern A: Single value return (path/diameter problems)

The function returns one number — the best value achievable going **downward** from this node. The answer that **passes through** this node is computed locally and updates a global variable.

**Template:**
```python
ans = float('-inf')

def dfs(node):
    if not node:
        return 0
    left  = max(dfs(node.left),  0)  # discard negative branches
    right = max(dfs(node.right), 0)
    ans = max(ans, left + right + node.val)  # path through this node
    return max(left, right) + node.val       # best single arm upward

dfs(root)
```

**Problems:**
- [Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/)
- [Longest ZigZag Path in a Binary Tree](https://leetcode.com/problems/longest-zigzag-path-in-a-binary-tree/)
- [Difference Between Maximum and Minimum Price Sum](https://leetcode.com/problems/difference-between-maximum-and-minimum-price-sum/)

---

### Sub-pattern B: State tuple return (choose/reject at each node)

Each node has discrete states (e.g., rob or skip, camera or no camera). Return a value per state; parent picks the best combination.

**Template (House Robber III):**
```python
def dfs(node):
    if not node:
        return (0, 0)  # (robbed, skipped)
    l_rob, l_skip = dfs(node.left)
    r_rob, r_skip = dfs(node.right)

    robbed  = node.val + l_skip + r_skip   # rob this → children must be skipped
    skipped = max(l_rob, l_skip) + max(r_rob, r_skip)  # skip this → children free
    return (robbed, skipped)

l_rob, l_skip = dfs(root)
return max(l_rob, l_skip)
```

**Problems:**
- [House Robber III](https://leetcode.com/problems/house-robber-iii/)

- [Binary Tree Cameras](https://leetcode.com/problems/binary-tree-cameras/)

- [Maximum Sum BST in Binary Tree](https://leetcode.com/problems/maximum-sum-bst-in-binary-tree/)


---

### Sub-pattern C: Rerooting (answer changes with root choice)

Two-pass technique. First DFS computes subtree answers (bottom-up). Second DFS propagates answers **down** so every node knows the answer if it were the root. Used when the problem asks for something like "sum of distances from every node to all others."

**Template:**
```python
# Pass 1: compute subtree sizes / subtree-sums rooted at node 0
def dfs1(u, parent):
    for v in graph[u]:
        if v != parent:
            dfs1(v, u)
            subtree_size[u] += subtree_size[v]
            dp[u] += dp[v] + subtree_size[v]  # e.g. sum of distances

# Pass 2: reroot — push parent's contribution down to child
def dfs2(u, parent):
    for v in graph[u]:
        if v != parent:
            # moving root from u to v: nodes outside v's subtree get 1 farther
            dp[v] = dp[u] - subtree_size[v] + (n - subtree_size[v])
            dfs2(v, u)
```

**Problems:**
- [Sum of Distances in Tree](https://leetcode.com/problems/sum-of-distances-in-tree/)

---

### Sub-pattern D: DP on tree paths / knapsack on tree

Merge children's DP tables at each node. Often O(n²) but tight — each pair of nodes is merged exactly once.

**Problems:**
- [Distribute Coins in Binary Tree](https://leetcode.com/problems/distribute-coins-in-binary-tree/)
- [Maximum Product of Splitted Binary Tree](https://leetcode.com/problems/maximum-product-of-splitted-binary-tree/)


---

### What to return checklist

| What the parent needs | Return from dfs |
|---|---|
| Best value going down | single int (height / gain) |
| Multiple exclusive states | tuple of ints |
| Subtree aggregate (sum, size, min, max) | tuple with all aggregates |
| Whether subtree satisfies a property | bool + relevant value |

---

### Common pitfalls

- **Forgetting to clamp negatives** — if a subtree has negative contribution, return 0 (discard it), not the raw value
- **Updating global answer at wrong place** — update it inside dfs at each node, not just at the root
- **Wrong base case for None** — match the return type: if you return a tuple, None should return a tuple of neutral values



## Bitmask & Probability DP

### Bitmask DP

Use when n ≤ 20 and you need to track which elements have been used. A bitmask integer represents the subset.

```python
dp = [INF] * (1 << n)
dp[0] = 0
for mask in range(1 << n):
    for i in range(n):
        if mask & (1 << i):
            prev = mask ^ (1 << i)
            dp[mask] = min(dp[mask], dp[prev] + cost(prev, i))
```

Bit tricks: `mask & (1<<i)` check, `mask | (1<<i)` add, `mask ^ (1<<i)` remove, `(1<<n)-1` full set.

- [Partition to K Equal Sum Subsets](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/) — mask = which elements are used so far.
- [Shortest Path Visiting All Nodes](https://leetcode.com/problems/shortest-path-visiting-all-nodes/) — TSP-style state `(mask, node)`.

---

### Probability DP

`dp[state]` = probability of reaching that state. Transition multiplies by probability of each choice. Watch for floating point errors on large inputs.

```python
dp[start] = 1.0
for state in all_states:
    for each outcome with probability p:
        dp[next_state] += dp[state] * p
```

- [Knight Probability in Chessboard](https://leetcode.com/problems/knight-probability-in-chessboard/) — `dp[step][r][c]` = prob of still being on the board.
- [New 21 Game](https://leetcode.com/problems/new-21-game/) — probability DP with a sliding-window sum.

---

## Must-Do / FAANG DP Questions

Problems **not** already used as examples above — a flat practice bank. Figuring out which pattern each needs is part of the drill.

- [Fibonacci Number](https://leetcode.com/problems/fibonacci-number/)
- [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)
- [Min Cost Climbing Stairs](https://leetcode.com/problems/min-cost-climbing-stairs/)
- [Decode Ways](https://leetcode.com/problems/decode-ways/)
- [House Robber II](https://leetcode.com/problems/house-robber-ii/)
- [Number of Longest Increasing Subsequence](https://leetcode.com/problems/number-of-longest-increasing-subsequence/)
- [Longest String Chain](https://leetcode.com/problems/longest-string-chain/)
- [Maximum Length of Pair Chain](https://leetcode.com/problems/maximum-length-of-pair-chain/)
- [Find the Longest Valid Obstacle Course at Each Position](https://leetcode.com/problems/find-the-longest-valid-obstacle-course-at-each-position/)
- [Arithmetic Slices II - Subsequence](https://leetcode.com/problems/arithmetic-slices-ii-subsequence/)
- [Target Sum](https://leetcode.com/problems/target-sum/)
- [Last Stone Weight II](https://leetcode.com/problems/last-stone-weight-ii/)
- [Ones and Zeroes](https://leetcode.com/problems/ones-and-zeroes/)
- [Partition Array Into Two Arrays to Minimize Sum Difference](https://leetcode.com/problems/partition-array-into-two-arrays-to-minimize-sum-difference/)
- [Coin Change 2](https://leetcode.com/problems/coin-change-2/)
- [Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/)
- [Perfect Squares](https://leetcode.com/problems/perfect-squares/)
- [Minimum Cost For Tickets](https://leetcode.com/problems/minimum-cost-for-tickets/)
- [Delete Operation for Two Strings](https://leetcode.com/problems/delete-operation-for-two-strings/)
- [Minimum ASCII Delete Sum for Two Strings](https://leetcode.com/problems/minimum-ascii-delete-sum-for-two-strings/)
- [Shortest Common Supersequence](https://leetcode.com/problems/shortest-common-supersequence/)
- [Longest Palindromic Subsequence](https://leetcode.com/problems/longest-palindromic-subsequence/)
- [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)
- [Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/)
- [Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching/)
- [Wildcard Matching](https://leetcode.com/problems/wildcard-matching/)
- [Distinct Subsequences](https://leetcode.com/problems/distinct-subsequences/)
- [Distinct Subsequences II](https://leetcode.com/problems/distinct-subsequences-ii/)
- [Number of Unique Good Subsequences](https://leetcode.com/problems/number-of-unique-good-subsequences/)
- [Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)
- [Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/)
- [Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)
- [Best Time to Buy and Sell Stock with Transaction Fee](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)
- [Maximum Profit from Trading Stocks](https://leetcode.com/problems/maximum-profit-from-trading-stocks/)
- [Cherry Pickup](https://leetcode.com/problems/cherry-pickup/)
- [Maximum Alternating Subsequence Sum](https://leetcode.com/problems/maximum-alternating-subsequence-sum/)
- [Minimum Swaps to Make Sequences Increasing](https://leetcode.com/problems/minimum-swaps-to-make-sequences-increasing/)
- [Flip String to Monotone Increasing](https://leetcode.com/problems/flip-string-to-monotone-increasing/)
- [Solving Questions With Brainpower](https://leetcode.com/problems/solving-questions-with-brainpower/)
- [Knight Dialer](https://leetcode.com/problems/knight-dialer/)
- [Student Attendance Record II](https://leetcode.com/problems/student-attendance-record-ii/)
- [Paint House](https://leetcode.com/problems/paint-house/)
- [Paint House II](https://leetcode.com/problems/paint-house-ii/)
- [Paint House III](https://leetcode.com/problems/paint-house-iii/)
- [Paint Fence](https://leetcode.com/problems/paint-fence/)
- [Decode Ways II](https://leetcode.com/problems/decode-ways-ii/)
- [Minimum Cost to Merge Stones](https://leetcode.com/problems/minimum-cost-to-merge-stones/)
- [Minimum Cost Tree From Leaf Values](https://leetcode.com/problems/minimum-cost-tree-from-leaf-values/)
- [Strange Printer](https://leetcode.com/problems/strange-printer/)
- [Minimum Score Triangulation of Polygon](https://leetcode.com/problems/minimum-score-triangulation-of-polygon/)
- [Remove Boxes](https://leetcode.com/problems/remove-boxes/)
- [Super Egg Drop](https://leetcode.com/problems/super-egg-drop/)
- [Unique Binary Search Trees](https://leetcode.com/problems/unique-binary-search-trees/)
- [Different Ways to Add Parentheses](https://leetcode.com/problems/different-ways-to-add-parentheses/)
- [Stone Game II](https://leetcode.com/problems/stone-game-ii/)
- [Stone Game III](https://leetcode.com/problems/stone-game-iii/)
- [Stone Game IV](https://leetcode.com/problems/stone-game-iv/)
- [Can I Win](https://leetcode.com/problems/can-i-win/)
- [Guess Number Higher or Lower II](https://leetcode.com/problems/guess-number-higher-or-lower-ii/)
- [Zuma Game](https://leetcode.com/problems/zuma-game/)
- [Unique Paths](https://leetcode.com/problems/unique-paths/)
- [Unique Paths II](https://leetcode.com/problems/unique-paths-ii/)
- [Maximal Rectangle](https://leetcode.com/problems/maximal-rectangle/)
- [Minimum Falling Path Sum](https://leetcode.com/problems/minimum-falling-path-sum/)
- [Count Square Submatrices with All Ones](https://leetcode.com/problems/count-square-submatrices-with-all-ones/)
- [Triangle](https://leetcode.com/problems/triangle/)
- [Find the Shortest Superstring](https://leetcode.com/problems/find-the-shortest-superstring/)
- [Smallest Sufficient Team](https://leetcode.com/problems/smallest-sufficient-team/)
- [Number of Ways to Wear Different Hats to Each Other](https://leetcode.com/problems/number-of-ways-to-wear-different-hats-to-each-other/)
- [Minimum Number of Work Sessions to Finish the Tasks](https://leetcode.com/problems/minimum-number-of-work-sessions-to-finish-the-tasks/)
- [Soup Servings](https://leetcode.com/problems/soup-servings/)
- [Toss Strange Coins](https://leetcode.com/problems/toss-strange-coins/)
- [Probability of a Two Boxes Having The Same Number of Distinct Balls](https://leetcode.com/problems/probability-of-a-two-boxes-having-the-same-number-of-distinct-balls/)
- [Maximum Profit in Job Scheduling](https://leetcode.com/problems/maximum-profit-in-job-scheduling/)
- [Constrained Subsequence Sum](https://leetcode.com/problems/constrained-subsequence-sum/)
- [Number of Dice Rolls With Target Sum](https://leetcode.com/problems/number-of-dice-rolls-with-target-sum/)
- [Tallest Billboard](https://leetcode.com/problems/tallest-billboard/)
- [Painting the Walls](https://leetcode.com/problems/painting-the-walls/)
- [Count Ways to Build Good Strings](https://leetcode.com/problems/count-ways-to-build-good-strings/)
- [Minimum Number of Removals to Make Mountain Array](https://leetcode.com/problems/minimum-number-of-removals-to-make-mountain-array/)
- [Longest Arithmetic Subsequence of Given Difference](https://leetcode.com/problems/longest-arithmetic-subsequence-of-given-difference/)
