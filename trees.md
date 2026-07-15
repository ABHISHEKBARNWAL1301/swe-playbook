# Trees & BST

A tree is a connected acyclic graph with `n` nodes and `n-1` edges. A **binary tree** has at most 2 children per node. A **BST** maintains `left.val < node.val < right.val`.

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

## Contents

- [Traversals](#traversals) — BFS, DFS, operations (height, diameter)
- [BST](#bst) — search, insert, delete, validate
- [Advanced Concepts](#advanced-concepts) — LCA, construct, serialize, tree DP

---

## Traversals

```
        1
       / \
      2   3
     / \   \
    4   5   6

Pre-order  (root, L, R):  1 2 4 5 3 6
In-order   (L, root, R):  4 2 5 1 3 6     ← BST gives sorted output
Post-order (L, R, root):  4 5 2 6 3 1
Level-order (BFS)      :  1 2 3 4 5 6
```

### BFS (level-order)

A queue processes the tree level by level. Snapshot `len(q)` at the start of each level to group nodes.

```python
from collections import deque

def level_order(root):
    if not root: return []
    q, out = deque([root]), []
    while q:
        level = []
        for _ in range(len(q)):
            n = q.popleft()
            level.append(n.val)
            if n.left:  q.append(n.left)
            if n.right: q.append(n.right)
        out.append(level)
    return out
```

### DFS (pre / in / post order)

```python
def inorder(node, out):                          # recursive — swap the order for pre/post
    if not node: return
    inorder(node.left, out)
    out.append(node.val)
    inorder(node.right, out)
```

```python
def inorder_iter(root):                          # iterative inorder — explicit stack
    stack, out, node = [], [], root
    while stack or node:
        while node:
            stack.append(node)
            node = node.left
        node = stack.pop()
        out.append(node.val)
        node = node.right
    return out

def preorder_iter(root):                         # iterative preorder
    if not root: return []
    stack, out = [root], []
    while stack:
        node = stack.pop()
        out.append(node.val)
        if node.right: stack.append(node.right)  # right first → left pops first
        if node.left:  stack.append(node.left)
    return out
```

- **Postorder (iterative):** run preorder pushing left before right, then reverse the output.
- **Morris traversal — O(1) space:** thread each leaf's null-right pointer to its in-order successor. Rarely required.

### Operations (height, diameter, path sum)

Two DFS styles cover almost every tree computation:

**Return-style** — each node returns info upward (height, sum, "is balanced").

```python
def max_depth(root):
    if not root: return 0
    return 1 + max(max_depth(root.left), max_depth(root.right))
```

**Diameter** — longest path through a node = `leftHeight + rightHeight`; track a global max while computing heights.

```python
def diameter(root):
    best = [0]
    def height(node):
        if not node: return 0
        l, r = height(node.left), height(node.right)
        best[0] = max(best[0], l + r)
        return 1 + max(l, r)
    height(root)
    return best[0]
```

**Accumulator-style** — pass state *down* the path (running sum, depth, path so far).

```python
def has_path_sum(root, target):
    if not root: return False
    if not root.left and not root.right:
        return root.val == target
    rest = target - root.val
    return has_path_sum(root.left, rest) or has_path_sum(root.right, rest)
```

### Questions

- [Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)
- [Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/)
- [Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/)
- [Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)
- [Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)
- [Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/)
- [Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)
- [Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)
- [Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/)
- [Same Tree](https://leetcode.com/problems/same-tree/)
- [Symmetric Tree](https://leetcode.com/problems/symmetric-tree/)
- [Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/)
- [Path Sum](https://leetcode.com/problems/path-sum/)
- [Path Sum II](https://leetcode.com/problems/path-sum-ii/)
- [Path Sum III](https://leetcode.com/problems/path-sum-iii/) (prefix-sum hashmap)

---

## BST

In-order traversal of a BST is strictly increasing — that ordering is what makes search/insert/delete O(h).

### Search

```python
def bst_search(root, target):
    while root and root.val != target:
        root = root.left if target < root.val else root.right
    return root
```

### Insert

```python
def bst_insert(root, val):
    if not root: return TreeNode(val)
    if val < root.val: root.left  = bst_insert(root.left, val)
    else:              root.right = bst_insert(root.right, val)
    return root
```

### Delete

Three cases for the node to remove:

1. **No children** → return `None`.
2. **One child** → return that child.
3. **Two children** → replace with in-order successor (smallest in right subtree), then delete the successor.

```python
def bst_delete(root, key):
    if not root: return None
    if key < root.val:
        root.left = bst_delete(root.left, key)
    elif key > root.val:
        root.right = bst_delete(root.right, key)
    else:
        if not root.left:  return root.right
        if not root.right: return root.left
        succ = root.right
        while succ.left: succ = succ.left
        root.val = succ.val
        root.right = bst_delete(root.right, succ.val)
    return root
```

### Validate

Pass min/max bounds down (or check that an in-order traversal is strictly increasing).

```python
def is_valid_bst(root, lo=float('-inf'), hi=float('inf')):
    if not root: return True
    if not (lo < root.val < hi): return False
    return (is_valid_bst(root.left, lo, root.val) and
            is_valid_bst(root.right, root.val, hi))
```

### Questions

- [Search in a Binary Search Tree](https://leetcode.com/problems/search-in-a-binary-search-tree/)
- [Insert into a Binary Search Tree](https://leetcode.com/problems/insert-into-a-binary-search-tree/)
- [Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst/)
- [Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/)
- [Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/) (in-order)
- [Convert Sorted Array to BST](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)
- [Inorder Successor in BST](https://leetcode.com/problems/inorder-successor-in-bst/)
- [Recover Binary Search Tree](https://leetcode.com/problems/recover-binary-search-tree/)

---

## Advanced Concepts

### Lowest Common Ancestor (LCA)

**Binary tree (general):** if `p` or `q` is `root`, return `root`; else recurse both sides. If both come back non-null, `root` is the LCA.

```python
def lca(root, p, q):
    if not root or root is p or root is q: return root
    L = lca(root.left, p, q)
    R = lca(root.right, p, q)
    if L and R: return root
    return L or R
```

**BST (faster):** use the ordering — both values smaller → go left; both larger → go right; else `root` is the split point.

```python
def lca_bst(root, p, q):
    while root:
        if p.val < root.val and q.val < root.val:   root = root.left
        elif p.val > root.val and q.val > root.val: root = root.right
        else: return root
```

### Construct Tree from Traversals

Root = `preorder[0]`. Its index in `inorder` splits the remaining values into the left and right subtrees.

```python
def build(preorder, inorder):
    idx = {v: i for i, v in enumerate(inorder)}
    self_pre = [0]                               # pointer into preorder
    def go(lo, hi):
        if lo > hi: return None
        val = preorder[self_pre[0]]; self_pre[0] += 1
        node = TreeNode(val)
        m = idx[val]
        node.left  = go(lo, m - 1)
        node.right = go(m + 1, hi)
        return node
    return go(0, len(inorder) - 1)
```

### Serialize / Deserialize

Pre-order with a sentinel (`#`) for null children works for any binary tree.

```python
def serialize(root):
    out = []
    def go(node):
        if not node: out.append('#'); return
        out.append(str(node.val))
        go(node.left); go(node.right)
    go(root)
    return ','.join(out)

def deserialize(s):
    it = iter(s.split(','))
    def go():
        v = next(it)
        if v == '#': return None
        node = TreeNode(int(v))
        node.left  = go()
        node.right = go()
        return node
    return go()
```

### Tree DP

Each node returns a tuple of "states" to its parent. See [dp.md](dp.md) (Tree DP) for the full pattern.

```python
def rob(root):                                   # House Robber III
    def dp(n):
        if not n: return (0, 0)                   # (rob this node, skip it)
        lr, ls = dp(n.left)
        rr, rs = dp(n.right)
        return (n.val + ls + rs, max(lr, ls) + max(rr, rs))
    return max(dp(root))
```

### Questions

- [LCA of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)
- [LCA of a BST](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)
- [LCA II](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-ii/) (nodes may not exist)
- [LCA III](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-iii/) (parent pointers)
- [Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)
- [Construct Binary Tree from Inorder and Postorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)
- [Construct Binary Tree from Preorder and Postorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-postorder-traversal/)
- [Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/)
- [Serialize and Deserialize BST](https://leetcode.com/problems/serialize-and-deserialize-bst/)
- [House Robber III](https://leetcode.com/problems/house-robber-iii/)
- [Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/)
- [Binary Tree Cameras](https://leetcode.com/problems/binary-tree-cameras/)
- [Sum of Distances in Tree](https://leetcode.com/problems/sum-of-distances-in-tree/) (re-rooting)
