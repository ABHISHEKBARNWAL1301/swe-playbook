# Stacks & Queues

Stack = **LIFO** (use a `list`). Queue = **FIFO** (use `collections.deque`). This file has two parts: the **basic implementations / use cases**, and the **monotonic** variant that shows up for next-greater (stack) and sliding-window extremes (deque).

## Contents

**Stack**
- [Basic Implementation](#stack-lifo)
- [Monotonic Stack — Next Greater / Smaller](#monotonic-stack--next-greater--smaller)

**Queue**
- [Basic Implementation](#queue-fifo)
- [Monotonic Deque — Sliding Window Max/Min](#monotonic-deque--sliding-window-maxmin)

---

### Stack (LIFO)

Push / pop / peek in O(1) — in Python just use a `list`.

```python
st = []
st.append(x)      # push
st.pop()          # pop top   — both O(1)
st[-1]            # peek
while st: ...     # empty check
```

### Matching Pairs

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

#### Common problems

- [Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)
- [Minimum Remove to Make Valid Parentheses](https://leetcode.com/problems/minimum-remove-to-make-valid-parentheses/)
- [Longest Valid Parentheses](https://leetcode.com/problems/longest-valid-parentheses/)
- [Score of Parentheses](https://leetcode.com/problems/score-of-parentheses/)

### Expression Evaluation

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

#### Common problems

- [Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/)
- [Basic Calculator](https://leetcode.com/problems/basic-calculator/)
- [Basic Calculator II](https://leetcode.com/problems/basic-calculator-ii/)
- [Decode String](https://leetcode.com/problems/decode-string/)

### Stack as Recursion Substitute

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

#### Common problems

- [Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/) (iterative)
- [Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/)
- [Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/) (two stacks)
- [Flatten Nested List Iterator](https://leetcode.com/problems/flatten-nested-list-iterator/)

### Min Stack — O(1) min

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

#### Common problems

- [Min Stack](https://leetcode.com/problems/min-stack/)
- [Max Stack](https://leetcode.com/problems/max-stack/)


### Monotonic Stack — Next Greater / Smaller

A stack whose values stay in monotonic order. When a new element breaks the order, **pop everything that violates it** — those popped elements just "found" their answer.

**Use it when** you hear "for each element, find the next / previous element that is greater / smaller."

```
push x:
  while stack.top violates monotonicity vs x:
      record answer for stack.pop()
  stack.push(x)
```

#### Example: Next Greater Element

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

#### Example: Largest Rectangle in Histogram

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

#### Common problems

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

### Queue (FIFO)

Enqueue at the back, dequeue at the front — use `collections.deque` (O(1) at both ends; `list.pop(0)` is O(n)).

```python
from collections import deque
q = deque()
q.append(x)          # enqueue        O(1)
q.popleft()          # dequeue         O(1)
q.appendleft(x)      # push front      O(1)  (deque-only)
q.pop()              # pop back        O(1)  (deque-only)
```

### BFS

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

> BFS problem sets live where they're used: grids / graphs in [graph.md](graph.md), level-order in [trees.md](trees.md).

### Circular Queue / Ring Buffer

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

#### Common problems

- [Design Circular Queue](https://leetcode.com/problems/design-circular-queue/)
- [Design Circular Deque](https://leetcode.com/problems/design-circular-deque/)

### Queue from Two Stacks (and vice versa)

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

#### Common problems

- [Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/)
- [Implement Stack using Queues](https://leetcode.com/problems/implement-stack-using-queues/)

---


### Monotonic Deque — Sliding Window Max/Min

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

#### Common problems

- [Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/)
- [Sliding Window Median](https://leetcode.com/problems/sliding-window-median/) (two heaps; deque doesn't fit)
- [Constrained Subsequence Sum](https://leetcode.com/problems/constrained-subsequence-sum/) (DP + monotonic deque)
- [Shortest Subarray with Sum at Least K](https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/)
- [Jump Game VI](https://leetcode.com/problems/jump-game-vi/)
