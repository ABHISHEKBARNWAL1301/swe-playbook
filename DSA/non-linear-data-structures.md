# Non-Linear Data Structures

Elements connect through pointers/references rather than sequential position — each node can branch (tree, trie), point anywhere (graph), or just chain to one `next` (linked list, grouped here since it's the pointer-based building block the rest are made of). A **heap** sits between the two worlds: array-backed like a linear structure, but logically a tree.

## Contents

- [Linked Lists](#linked-lists)
  - [Singly Linked List](#singly-linked-list)
  - [Doubly Linked List](#doubly-linked-list)
- [Trees & BST](#trees--bst)
  - [Traversals](#traversals)
  - [BST](#bst)
  - [Advanced Concepts](#advanced-concepts)
- [Trie](#trie)
- [Heaps](#heaps)
  - [Heaps in Python](#heaps-in-python)
  - [Core operations (implemented from scratch)](#core-operations-implemented-from-scratch)
  - [Pattern 1: Top-K](#pattern-1-top-k)
  - [Pattern 2: Two Heaps](#pattern-2-two-heaps)
  - [Pitfalls](#pitfalls)
- [Graphs](#graphs)
  - [Representation](#representation)
  - [Traversal](#traversal)
  - [Topological Sorting](#topological-sorting)
  - [Disjoint Set Union (DSU) / Union-Find](#disjoint-set-union-dsu--union-find)
  - [Shortest Path Algorithms](#shortest-path-algorithms)
  - [Cycle Detection](#cycle-detection)
  - [Minimum Spanning Tree (MST)](#minimum-spanning-tree-mst)
  - [Strongly Connected Components](#strongly-connected-components)
  - [Bridges & Articulation Points](#bridges--articulation-points)
  - [Tarjan's Algorithm (SCC via low-link)](#tarjans-algorithm-scc-via-low-link)

---

## Linked Lists

Each node holds a value and a pointer to the next. No random access — every position needs a walk from `head`. O(1) insert/delete at a known node (no shifting), but index lookup is O(n).

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```

### Singly Linked List

One `next` pointer per node. Every technique below is a variation on three core moves.

#### Core operations

```python
# traverse
node = head
while node:
    node = node.next

# insert `new` after `prev`
new.next  = prev.next
prev.next = new

# delete the node after `prev`
prev.next = prev.next.next
```

#### Dummy Head (Sentinel)

Add a fake node before `head`. Eliminates "what if I delete the head?" / "what if the list is empty?" edge cases.

```python
dummy = ListNode(0, head)
prev = dummy
# ... mutate ...
return dummy.next
```

Use it for: remove nth from end, partition, merge, deletion.

#### Fast / Slow Pointers

`slow` moves 1 step, `fast` moves 2.

| Need              | Trick                                             |
|-------------------|---------------------------------------------------|
| Middle of list    | When `fast` hits end, `slow` is at middle         |
| Detect cycle      | If they meet, there's a cycle (Floyd's)           |
| Find cycle entry  | After meeting, reset one to head; move both by 1  |
| nth from end      | Move `fast` n steps first, then advance both      |

```
                ┌───────────────┐
                ▼               │
 head → a → b → c → d → e → f → g (cycle back to c)

slow and fast meet inside the cycle → cycle exists.
Reset slow to head; move both 1 step → they meet at cycle entry.
```

```python
def detect_cycle(head):                          # Floyd's — returns cycle entry
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            slow = head
            while slow is not fast:
                slow = slow.next
                fast = fast.next
            return slow
    return None

def middle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow

def remove_nth(head, n):
    dummy = ListNode(0, head)
    slow = fast = dummy
    for _ in range(n): fast = fast.next
    while fast.next:
        slow = slow.next
        fast = fast.next
    slow.next = slow.next.next
    return dummy.next
```

#### Reverse

Walk the list, flipping each node's `next` to point at the previous one.

```python
def reverse(head):                               # iterative
    prev, cur = None, head
    while cur:
        nxt = cur.next
        cur.next = prev
        prev, cur = cur, nxt
    return prev
```

```
prev = None, cur = 1 → 2 → 3 → 4
after one step:  1 → None  ,  cur = 2 → 3 → 4
after two:       2 → 1 → None ,  cur = 3 → 4
final:           4 → 3 → 2 → 1 → None
```

```python
def reverse_rec(head):                           # recursive
    if not head or not head.next: return head
    new_head = reverse_rec(head.next)
    head.next.next = head
    head.next = None
    return new_head

def reverse_between(head, l, r):                 # reverse sublist [l, r]
    dummy = ListNode(0, head)
    prev = dummy
    for _ in range(l - 1): prev = prev.next
    cur = prev.next
    for _ in range(r - l):                        # move next-pointer in front
        nxt = cur.next
        cur.next = nxt.next
        nxt.next = prev.next
        prev.next = nxt
    return dummy.next

def reverse_k_group(head, k):                    # reverse in groups of k
    node = head
    for _ in range(k):                            # check k nodes ahead
        if not node: return head
        node = node.next
    prev, cur = None, head
    for _ in range(k):
        nxt = cur.next; cur.next = prev
        prev, cur = cur, nxt
    head.next = reverse_k_group(cur, k)
    return prev
```

#### Merge

```python
def merge_two(a, b):                             # two sorted lists
    dummy = ListNode(0)
    tail = dummy
    while a and b:
        if a.val <= b.val:
            tail.next = a; a = a.next
        else:
            tail.next = b; b = b.next
        tail = tail.next
    tail.next = a or b
    return dummy.next
```

For **k sorted lists**: divide-and-conquer pairwise merge, or a min-heap of the heads — both O(N log k). See [Heaps](#heaps).

#### Reorder / Partition

**Reorder List** (`L0 → Ln → L1 → Ln-1 → …`): find middle → reverse second half → interleave.

```python
def reorder(head):
    if not head or not head.next: return
    slow = fast = head                            # 1. middle
    while fast.next and fast.next.next:
        slow = slow.next; fast = fast.next.next
    prev, cur = None, slow.next                    # 2. reverse second half
    slow.next = None
    while cur:
        nxt = cur.next; cur.next = prev
        prev, cur = cur, nxt
    a, b = head, prev                              # 3. interleave
    while b:
        a_next, b_next = a.next, b.next
        a.next = b; b.next = a_next
        a, b = a_next, b_next
```

**Partition** around `x`: build two dummy lists (less / greater-eq), then stitch.

```python
def partition(head, x):
    less, greater = ListNode(0), ListNode(0)
    lt, gt = less, greater
    while head:
        if head.val < x:
            lt.next = head; lt = lt.next
        else:
            gt.next = head; gt = gt.next
        head = head.next
    gt.next = None                                # terminate the tail
    lt.next = greater.next                        # stitch less → greater
    return less.next
```

#### Intersection of Two Lists

Two pointers, each walks its own list then swaps to the other. They meet at the intersection (or at `None`) after at most `len(a) + len(b)` steps.

```python
def intersection(a, b):
    pa, pb = a, b
    while pa is not pb:
        pa = pa.next if pa else b
        pb = pb.next if pb else a
    return pa
```

#### Deep Copy with Random Pointer

Node has `next` **and** `random`. Interleave clones into the original list so each clone's `random` is reachable as `orig.random.next` — O(1) extra space.

```python
def copy_random(head):
    if not head: return None
    cur = head                                   # 1. A → A' → B → B' → ...
    while cur:
        cur.next = Node(cur.val, cur.next)
        cur = cur.next.next
    cur = head                                   # 2. set clone.random
    while cur:
        if cur.random:
            cur.next.random = cur.random.next
        cur = cur.next.next
    cur, clone_head = head, head.next            # 3. unweave
    while cur:
        clone = cur.next
        cur.next = clone.next
        clone.next = clone.next.next if clone.next else None
        cur = cur.next
    return clone_head
```

#### Questions

- [Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)
- [Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/)
- [Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/)
- [Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/)
- [Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/)
- [Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)
- [Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/)
- [Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/)
- [Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)
- [Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)
- [Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)
- [Sort List](https://leetcode.com/problems/sort-list/)
- [Reorder List](https://leetcode.com/problems/reorder-list/)
- [Partition List](https://leetcode.com/problems/partition-list/)
- [Odd Even Linked List](https://leetcode.com/problems/odd-even-linked-list/)
- [Rotate List](https://leetcode.com/problems/rotate-list/)
- [Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/)
- [Remove Linked List Elements](https://leetcode.com/problems/remove-linked-list-elements/)
- [Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/)

### Doubly Linked List

Each node carries **two** pointers, `prev` and `next`. That buys two things a singly list can't do:

- **O(1) delete given only the node** — a singly list needs the predecessor; here `node.prev` is right there.
- **Bidirectional traversal** — walk forward or backward.

The cost is an extra pointer per node. Pair it with **sentinel head/tail** nodes and every insert/delete becomes branch-free (no null checks at the ends).

```python
class DNode:
    def __init__(self, val=0):
        self.val = val
        self.prev = self.next = None

class DoublyLinkedList:
    def __init__(self):
        self.head = DNode()                       # sentinels — never removed
        self.tail = DNode()
        self.head.next = self.tail
        self.tail.prev = self.head

    def _link(self, node, after):                 # insert `node` right after `after`
        nxt = after.next
        after.next = node; node.prev = after
        node.next = nxt;   nxt.prev = node

    def push_front(self, val):                    # O(1)
        self._link(DNode(val), self.head)

    def push_back(self, val):                     # O(1)
        self._link(DNode(val), self.tail.prev)

    def remove(self, node):                       # O(1) unlink, given the node
        node.prev.next = node.next
        node.next.prev = node.prev
```

**Where it's used:** the O(1) "move a node to the front / evict from the back" combo is exactly what an [LRU cache](../SystemDesign/problems.md#lru-cache) (hashmap + DLL) needs. Deques, browser history, and Python's own `collections.OrderedDict` are built on the same idea.

#### Questions

- [Design Linked List](https://leetcode.com/problems/design-linked-list/)
- [Flatten a Multilevel Doubly Linked List](https://leetcode.com/problems/flatten-a-multilevel-doubly-linked-list/)
- [Design Browser History](https://leetcode.com/problems/design-browser-history/)
- [LRU Cache](https://leetcode.com/problems/lru-cache/) (hashmap + DLL — full design in [SystemDesign/problems.md](../SystemDesign/problems.md#lru-cache))
- [Convert Binary Search Tree to Sorted Doubly Linked List](https://leetcode.com/problems/convert-binary-search-tree-to-sorted-doubly-linked-list/)
- [All O`one Data Structure](https://leetcode.com/problems/all-oone-data-structure/) (DLL of frequency buckets)
- [Design Circular Deque](https://leetcode.com/problems/design-circular-deque/)

---

## Trees & BST

A tree is a connected acyclic graph with `n` nodes and `n-1` edges. A **binary tree** has at most 2 children per node. A **BST** maintains `left.val < node.val < right.val`.

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

### Traversals

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

#### BFS (level-order)

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

#### DFS (pre / in / post order)

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

#### Operations (height, diameter, path sum)

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

#### Questions

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

### BST

In-order traversal of a BST is strictly increasing — that ordering is what makes search/insert/delete O(h).

#### Search

```python
def bst_search(root, target):
    while root and root.val != target:
        root = root.left if target < root.val else root.right
    return root
```

#### Insert

```python
def bst_insert(root, val):
    if not root: return TreeNode(val)
    if val < root.val: root.left  = bst_insert(root.left, val)
    else:              root.right = bst_insert(root.right, val)
    return root
```

#### Delete

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

#### Validate

Pass min/max bounds down (or check that an in-order traversal is strictly increasing).

```python
def is_valid_bst(root, lo=float('-inf'), hi=float('inf')):
    if not root: return True
    if not (lo < root.val < hi): return False
    return (is_valid_bst(root.left, lo, root.val) and
            is_valid_bst(root.right, root.val, hi))
```

#### Questions

- [Search in a Binary Search Tree](https://leetcode.com/problems/search-in-a-binary-search-tree/)
- [Insert into a Binary Search Tree](https://leetcode.com/problems/insert-into-a-binary-search-tree/)
- [Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst/)
- [Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/)
- [Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/) (in-order)
- [Convert Sorted Array to BST](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)
- [Inorder Successor in BST](https://leetcode.com/problems/inorder-successor-in-bst/)
- [Recover Binary Search Tree](https://leetcode.com/problems/recover-binary-search-tree/)

### Advanced Concepts

#### Lowest Common Ancestor (LCA)

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

#### Construct Tree from Traversals

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

#### Serialize / Deserialize

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

#### Tree DP

Each node returns a tuple of "states" to its parent. See [algorithms.md](algorithms.md#pattern-9-tree-dp) (Tree DP) for the full pattern.

```python
def rob(root):                                   # House Robber III
    def dp(n):
        if not n: return (0, 0)                   # (rob this node, skip it)
        lr, ls = dp(n.left)
        rr, rs = dp(n.right)
        return (n.val + ls + rs, max(lr, ls) + max(rr, rs))
    return max(dp(root))
```

#### Questions

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

---

## Trie

A tree where each node represents a character. Paths from root spell out stored strings. Used for prefix lookup, autocomplete, dictionary, longest common prefix.

### Properties

- Each node has up to `k` children (k = alphabet size, 26 for lowercase).
- A node is marked `end` if some inserted word terminates there.
- Insert / search / delete / prefix-search: `O(L)` where `L` = word length.
- Space: `O(N * L * k)` worst case (N = number of words).

### Node

```python
class TrieNode:
    def __init__(self):
        self.children = {}   # char -> TrieNode
        self.end = False     # True if a word ends here
```

### Trie with CRUD

```python
class Trie:
    def __init__(self):
        self.root = TrieNode()

    # CREATE / UPDATE — insert a word
    def insert(self, word: str) -> None:
        node = self.root
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
        node.end = True

    # READ — exact word lookup
    def search(self, word: str) -> bool:
        node = self.root
        for ch in word:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return node.end

    # READ — prefix lookup
    def starts_with(self, prefix: str) -> bool:
        node = self.root
        for ch in prefix:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return True

    # DELETE — remove a word, prune dead branches
    def delete(self, word: str) -> bool:

        def helper(node: TrieNode, word: str, index: int) -> bool:
            if index == len(word):
                if not node.end:
                    return False        # word not present
                node.end = False
                return len(node.children) == 0   # prune if leaf
            ch = word[index]
            child = node.children.get(ch)
            if child is None:
                return False
            should_prune = helper(child, word, index + 1)
            if should_prune:
                del node.children[ch]
                return not node.end and len(node.children) == 0
            return False

        return helper(self.root, word, 0)
```

### Usage

```python
t = Trie()
t.insert("apple")
t.insert("app")

t.search("apple")      # True
t.search("appl")       # False
t.starts_with("appl")  # True

t.delete("apple")
t.search("apple")      # False
t.search("app")        # True   (still present)
```

### Common problems

- [Implement Trie (Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree/)
- [Design Add and Search Words Data Structure](https://leetcode.com/problems/design-add-and-search-words-data-structure/) — wildcard `.`
- [Word Search II](https://leetcode.com/problems/word-search-ii/) — trie + DFS on board
- [Replace Words](https://leetcode.com/problems/replace-words/)
- [Longest Word in Dictionary](https://leetcode.com/problems/longest-word-in-dictionary/)
- [Maximum XOR of Two Numbers in an Array](https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/) — bit trie

---

## Heaps

A **heap** is a **complete binary tree** (every level full except possibly the last, which fills left to right) that satisfies the **heap property**:

- **Min-heap:** every parent ≤ its children → smallest element at the root
- **Max-heap:** every parent ≥ its children → largest element at the root

```
      Min-heap                         Max-heap
         5                                30
       /   \                            /    \
      11    12                        15      18
     / \   / \                       / \     / \
    20 25 30 18                     10 12   9   8

   root = minimum                  root = maximum
```

#### Stored as an array, not with pointers

A complete binary tree maps perfectly onto an array — index math replaces child/parent pointers.

```
        5(0)
       /    \                  index:  0   1   2   3   4   5   6
    11(1)   12(2)             value:  [ 5, 11, 12, 20, 25, 30, 18 ]
    /  \    /  \
 20(3) 25(4) 30(5) 18(6)

  left(i)   = 2*i + 1
  right(i)  = 2*i + 2
  parent(i) = (i - 1) // 2
```

#### Why an array (and why heaps over BST for priority queues)

- **Height is `log n`** — it's a complete tree, so operations are `O(log n)`.
- **No pointer memory** — no `left`/`right`/`parent` references to store.
- **Cache friendly** — contiguous array → better locality of reference than a pointer-based BST.
- **Build in `O(n)`** — `heapify` builds a heap in linear time vs `O(n log n)` to build a balanced BST.

That combination (fast build, no pointers, cache locality) is why **priority queues use heaps, not BSTs**.

---

### Heaps in Python

Python's `heapq` module is a **min-heap** over a plain list.

```python
import heapq

pq = []
heapq.heappush(pq, 5)        # push        O(log n)
heapq.heappush(pq, 1)
heapq.heappush(pq, 3)
pq[0]                        # peek min    O(1)   → 1
heapq.heappop(pq)            # pop min     O(log n) → 1

arr = [5, 1, 4, 2, 30, 25]
heapq.heapify(arr)           # build heap in-place, O(n)
```

**Max-heap:** `heapq` has no max-heap, so **negate the values** (or wrap as `(-key, item)`).

```python
heapq.heappush(pq, -x)       # push
top = -heapq.heappop(pq)     # pop max
```

For reference, in C++ it's the opposite default:

```cpp
priority_queue<int> pq;                                // max-heap
priority_queue<int, vector<int>, greater<int>> pq;     // min-heap
```

---

### Core operations (implemented from scratch)

Understanding `heapq` means understanding these. All examples are **min-heaps**.

#### 1. Heapify (sift-down) — `O(log n)`

Fix a single violation by pushing the value at `i` down to its correct spot. Assumes the subtrees below `i` are already valid heaps.

```python
def heapify(arr, n, i):                  # min-heapify subtree rooted at i
    smallest = i
    l, r = 2*i + 1, 2*i + 2
    if l < n and arr[l] < arr[smallest]:
        smallest = l
    if r < n and arr[r] < arr[smallest]:
        smallest = r
    if smallest != i:
        arr[i], arr[smallest] = arr[smallest], arr[i]
        heapify(arr, n, smallest)        # recurse into the affected subtree
```

#### 2. Build heap — `O(n)`

Heapify every internal node, starting from the **last non-leaf** (`n//2 - 1`) up to the root. Leaves are already valid heaps, so they're skipped. The work telescopes to `O(n)`, not `O(n log n)`.

```python
def build_heap(arr):
    n = len(arr)
    for i in range(n // 2 - 1, -1, -1):  # last non-leaf → root
        heapify(arr, n, i)
```

#### 3. Insert (sift-up) — `O(log n)`

Append at the end, then bubble up while smaller than the parent.

```python
def push(arr, x):
    arr.append(x)
    i = len(arr) - 1
    while i > 0 and arr[(i - 1) // 2] > arr[i]:
        p = (i - 1) // 2
        arr[p], arr[i] = arr[i], arr[p]
        i = p
```

#### 4. Extract-min — `O(log n)`

Swap root with the last element, pop it off, then sift the new root down.

```python
def pop_min(arr):
    arr[0], arr[-1] = arr[-1], arr[0]
    mn = arr.pop()
    heapify(arr, len(arr), 0)
    return mn
```

#### 5. Delete at index — `O(log n)`

Set the key to `-inf`, sift it up to the root, then extract-min.

```python
def delete(arr, i):
    arr[i] = float('-inf')
    while i > 0 and arr[(i - 1) // 2] > arr[i]:
        p = (i - 1) // 2
        arr[p], arr[i] = arr[i], arr[p]
        i = p
    pop_min(arr)
```

#### 6. Heap sort — `O(n log n)`

Build a **max-heap**, then repeatedly move the root (largest) to the end and shrink the heap. Sorts ascending in place.

```python
def max_heapify(arr, n, i):
    largest = i
    l, r = 2*i + 1, 2*i + 2
    if l < n and arr[l] > arr[largest]: largest = l
    if r < n and arr[r] > arr[largest]: largest = r
    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        max_heapify(arr, n, largest)

def heap_sort(arr):
    n = len(arr)
    for i in range(n // 2 - 1, -1, -1):  # build max-heap
        max_heapify(arr, n, i)
    for end in range(n - 1, 0, -1):
        arr[0], arr[end] = arr[end], arr[0]   # largest to the back
        max_heapify(arr, end, 0)              # restore on the shrunk heap
```

#### Complexity summary

| Operation      | Time       |
|----------------|------------|
| peek min/max   | `O(1)`     |
| push / insert  | `O(log n)` |
| pop / extract  | `O(log n)` |
| delete         | `O(log n)` |
| build heap     | `O(n)`     |
| heap sort      | `O(n log n)` |

---

### Pattern 1: Top-K

**When:** "k largest / smallest / most frequent / closest" — anything asking for the top `k` out of `n`.

**Key trick:** to keep the **k largest**, use a **min-heap of size k**. The smallest of your top-k sits at the root; whenever the heap exceeds `k`, pop it. Runs in `O(n log k)` — cheaper than sorting (`O(n log n)`) when `k` is small.

> For the **k smallest**, flip it: use a **max-heap of size k** (negate values).

#### Example: Top K Frequent Elements

Return the `k` most frequent values in `nums`. Three ways, from brute to optimal:

```python
from collections import Counter
import heapq

# 1) Sort by frequency — O(n log n)
def topKFrequent(nums, k):
    freq = Counter(nums)
    return [x for x, _ in freq.most_common(k)]

# 2) Max-heap of ALL items, pop k times — O(n log n)
def topKFrequent(nums, k):
    freq = Counter(nums)
    heap = [(-cnt, x) for x, cnt in freq.items()]
    heapq.heapify(heap)
    return [heapq.heappop(heap)[1] for _ in range(k)]

# 3) Min-heap of size k — O(n log k)   ← best when k << n
def topKFrequent(nums, k):
    freq = Counter(nums)
    heap = []                              # holds (count, value)
    for x, cnt in freq.items():
        heapq.heappush(heap, (cnt, x))
        if len(heap) > k:                  # drop the least frequent so far
            heapq.heappop(heap)
    return [x for _, x in heap]
```

Approach 3 is the pattern: **push everything, but never let the heap grow past k.** What survives is your answer.

#### Example: Kth largest element

Same idea — a min-heap of size `k`, and the root *is* the k-th largest.

```python
def kth_largest(nums, k):
    heap = nums[:k]
    heapq.heapify(heap)
    for x in nums[k:]:
        if x > heap[0]:
            heapq.heapreplace(heap, x)     # pop + push in one sift
    return heap[0]
```

#### K-way merge (a Top-K variant)

Same machinery when merging `k` sorted streams: seed the heap with the head of each stream, pop the min, push its successor.

```python
def merge_k_lists(lists):
    heap = []
    for i, node in enumerate(lists):
        if node:
            heapq.heappush(heap, (node.val, i, node))   # i breaks val ties
    dummy = tail = ListNode(0)
    while heap:
        val, i, node = heapq.heappop(heap)
        tail.next = node; tail = node
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))
    return dummy.next
```

#### Problems

- [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)
- [Top K Frequent Words](https://leetcode.com/problems/top-k-frequent-words/)
- [Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/)
- [Kth Smallest Element in a Sorted Matrix](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/)
- [K Closest Points to Origin](https://leetcode.com/problems/k-closest-points-to-origin/)
- [Find K Pairs with Smallest Sums](https://leetcode.com/problems/find-k-pairs-with-smallest-sums/)
- [Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)
- [Smallest Range Covering Elements from K Lists](https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists/)
- [Ugly Number II](https://leetcode.com/problems/ugly-number-ii/)
- [Reorganize String](https://leetcode.com/problems/reorganize-string/)
- [Task Scheduler](https://leetcode.com/problems/task-scheduler/)
- [Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)
- [Single-Threaded CPU](https://leetcode.com/problems/single-threaded-cpu/)
- [Maximum Performance of a Team](https://leetcode.com/problems/maximum-performance-of-a-team/)

---

### Pattern 2: Two Heaps

**When:** you need the **middle** of a stream, or to balance two groups by size/priority. Split the data into a smaller half and a larger half, each in its own heap.

**Setup:** a **max-heap `lo`** for the smaller half + a **min-heap `hi`** for the larger half. The two roots sit right at the boundary — so the median is always one pop away.

```
       hi  (min-heap, larger half)
        │   root = smallest of the big numbers
     median
        │   root = largest of the small numbers
       lo  (max-heap, smaller half)
```

Keep them balanced: `len(lo) == len(hi)` or `len(lo) == len(hi) + 1`.

#### Example: Find Median from a Data Stream

```python
class MedianFinder:
    def __init__(self):
        self.lo = []                       # max-heap (store negatives)
        self.hi = []                       # min-heap

    def addNum(self, x):
        heapq.heappush(self.lo, -x)                        # add to lo
        heapq.heappush(self.hi, -heapq.heappop(self.lo))   # move lo's max to hi
        if len(self.hi) > len(self.lo):                    # rebalance
            heapq.heappush(self.lo, -heapq.heappop(self.hi))

    def findMedian(self):
        if len(self.lo) > len(self.hi):
            return -self.lo[0]                             # odd count
        return (-self.lo[0] + self.hi[0]) / 2              # even count
```

Every `addNum` funnels the value through `lo → hi`, then rebalances so `lo` never falls behind — `O(log n)` per insert, `O(1)` median.

#### Problems

- [Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/)
- [Sliding Window Median](https://leetcode.com/problems/sliding-window-median/)

---

### Pitfalls

| Pitfall                                        | Fix                                                   |
|------------------------------------------------|-------------------------------------------------------|
| Max-heap with `heapq`                          | Negate the value, or wrap as `(-key, item)`           |
| `heappush` on a list that isn't heap-ordered   | `heapify(list)` first                                 |
| Comparing un-orderable objects on a tie        | Add an index/counter as a tiebreaker: `(key, i, obj)` |
| Two-heaps drifting out of balance              | Always route through one heap, then rebalance sizes   |

---

## Graphs

A graph is a nonlinear data structure consisting of nodes and edges. Every algorithm below builds on two primitives — BFS and DFS.

### Representation

```python

from collections import defaultdict, deque
# Unweighted graph
graph = defaultdict(list)
graph['A'].append('B')
graph['A'].append('C')

# Weighted graph
weighted_graph = defaultdict(list)
weighted_graph['A'].append(('B', 2))
weighted_graph['A'].append(('C', 4))
```

The `graph['A'].append('B')` calls above build this — each key maps to a list of its neighbours (an **adjacency list**):

```
Unweighted                 Weighted                 Adjacency list (dict)
                                                     graph = {
      A                        A                       'A': ['B', 'C'],
     / \                      / \                       'B': [],
    B   C                  2 /   \ 4                     'C': [],
                            B     C                    }
   directed A→B, A→C      edge labels = weights
```

To make it **undirected**, add the edge both ways — `graph['B'].append('A')` as well. A fuller example and its picture:

```python
graph = defaultdict(list)
for u, v in [('A','B'), ('A','C'), ('B','D'), ('C','D')]:
    graph[u].append(v)
    graph[v].append(u)          # drop this line for a directed graph
```

```
        A                graph = {
       / \                 'A': ['B', 'C'],
      B   C                'B': ['A', 'D'],
       \ /                 'C': ['A', 'D'],
        D                  'D': ['B', 'C'],
                         }
   undirected: every edge stored on both endpoints
```

### Traversal 

#### BFS

```python
def bfs_unweighted(graph, start, visited):
    queue = deque([start])
    visited.add(start)
    
    while queue:
        node = queue.popleft()
        print(node, end=" ")  # Process the node
        
        # Visit all unvisited neighbors
        for neighbor in graph[node]:
            if neighbor not in visited:
                queue.append(neighbor)
                visited.add(neighbor)

def bfs(graph):
    visited = set()
    for i in graph:
        if i not in visited:
            bfs_unweighted(graph, i, visited)
```    
#### Questions

- [Rotting Oranges](https://leetcode.com/problems/rotting-oranges/) — multi-source BFS: seed the queue with every rotten orange, then expand level by level.
- [Word Ladder](https://leetcode.com/problems/word-ladder/) — BFS over an implicit graph where words one letter apart are neighbors.

#### DFS

```python
def helper(u, graph, visited):
    visited.add(u)
    print(u)
    for v in graph[u]:
        if v not in visited:
          helper(v,graph, visited)

def dfs(graph):
    visited = set()
    for u in graph:
        if u not in visited:
            helper(u, graph, visited)
```

#### Questions

- [Number of Islands](https://leetcode.com/problems/number-of-islands/) — flood-fill each unvisited land cell; the number of flood-fills you start is the answer.
- [Clone Graph](https://leetcode.com/problems/clone-graph/) — DFS while mapping original→copy in a hashmap so each node is cloned once.

#### Bipartite Check (BFS/DFS 2-coloring)

Just a traversal that colors each node the opposite of its parent. A graph is bipartite iff it's 2-colorable iff it has no odd cycles — if you ever reach a neighbor already colored the same as the current node, it isn't bipartite.

```python
def is_bipartite(graph, n):
    color = [-1] * n
    for s in range(n):
        if color[s] != -1: continue
        color[s] = 0
        q = deque([s])
        while q:
            u = q.popleft()
            for v in graph[u]:
                if color[v] == -1:
                    color[v] = 1 - color[u]     # opposite color
                    q.append(v)
                elif color[v] == color[u]:      # same color = odd cycle
                    return False
    return True
```

- [Is Graph Bipartite?](https://leetcode.com/problems/is-graph-bipartite/) — 2-color via BFS/DFS; a same-colored neighbor means it isn't bipartite.

### Topological Sorting

Topological sorting for Directed Acyclic Graph (DAG) is a linear ordering of vertices such that for every directed edge (u, v), vertex u comes before v in the ordering.

Topological sorting can be performed using both:
 - Kahn's Algorithm (BFS + in-degree) — the approach used in your findOrder solution.
 - Depth-First Search (DFS) — a very elegant and widely used approach.

Both produce a valid topological order for a Directed Acyclic Graph (DAG).

#### Kahn's Algorithm (BFS + in-degree)

```python
# Kahn's BFS

from collections import defaultdict, deque

def topological_sort(n, edges):
    """
    n: number of nodes (0 to n-1)
    edges: list of directed edges [u, v] meaning u -> v

    Returns:
        A topological ordering if the graph is a DAG,
        otherwise returns [] if a cycle exists.
    """

    # Step 1: Build graph and compute in-degree
    graph = defaultdict(list)
    in_degree = [0] * n

    for u, v in edges:
        graph[u].append(v)
        in_degree[v] += 1

    # Step 2: Initialize queue with all nodes having in-degree 0
    queue = deque()
    for node in range(n):
        if in_degree[node] == 0:
            queue.append(node)

    # Step 3: Process nodes in BFS order
    topo_order = []

    while queue:
        u = queue.popleft()
        topo_order.append(u)

        for v in graph[u]:
            in_degree[v] -= 1
            if in_degree[v] == 0:
                queue.append(v)

    # Step 4: Check for cycle
    if len(topo_order) == n:
        return topo_order

    # Cycle detected
    return []
```

#### DFS-based Topological Sort

```python

# DFS-Based Topological Sort

def topo_sort_dfs(n, edges):
    graph = [[] for _ in range(n)]
    for u, v in edges:
        graph[u].append(v)

    state = [0] * n   # 0=unvisited, 1=visiting, 2=visited
    result = []

    def dfs(u):
        if state[u] == 1:
            return False      # cycle found
        if state[u] == 2:
            return True       # already processed

        state[u] = 1          # visiting

        for v in graph[u]:
            if not dfs(v):
                return False

        state[u] = 2          # fully processed
        result.append(u)
        return True

    for i in range(n):
        if state[i] == 0:
            if not dfs(i):
                return []     # cycle exists

    return result[::-1]
```
### Questions

- [Course Schedule II](https://leetcode.com/problems/course-schedule-ii/) — return any valid order; an empty result means a cycle exists.
- [Alien Dictionary](https://leetcode.com/problems/alien-dictionary/) — derive precedence edges from adjacent words, then topological sort.

### Disjoint Set Union (DSU) / Union-Find

Disjoint Set Union (DSU), also known as Union-Find, is a data structure used to efficiently maintain a collection of disjoint (non-overlapping) sets.

It supports two fundamental operations:
- Find(x) → Returns the representative (root) of the set containing x.
- Union(x, y) → Merges the sets containing x and y.

**Time complexity:** with both optimizations (**path compression** + **union by rank/size**), each `Find` and `Union` runs in **O(α(n))** amortized — where α is the inverse Ackermann function, effectively constant (α(n) ≤ 4 for any practical n). Without the optimizations it degrades to **O(n)** per operation in the worst case (a chain-like tree). Space is **O(n)**.

| Optimization | Find / Union |
|---|---|
| Naive (no optimization) | O(n) |
| Union by rank/size only | O(log n) |
| Path compression only | O(log n) amortized |
| Both (standard DSU) | O(α(n)) ≈ O(1) |

#### Intuition
Suppose we have the elements:

``` 
{0}, {1}, {2}, {3}, {4}
```

Initially, every element is in its own set.

After performing:
```
union(0, 1)
union(3, 4)
union(1, 4)
```
The sets become:

``` 
{0, 1, 3, 4}, {2}
``` 
Each set is represented by a single root node.

```python
class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # Path compression
        return self.parent[x]

    def union(self, x, y):
        root_x = self.find(x)
        root_y = self.find(y)

        if root_x == root_y:
            return False   # Already connected

        if self.rank[root_x] < self.rank[root_y]:
            self.parent[root_x] = root_y
        elif self.rank[root_x] > self.rank[root_y]:
            self.parent[root_y] = root_x
        else:
            self.parent[root_y] = root_x
            self.rank[root_x] += 1

        return True
```

#### Questions

- [Redundant Connection](https://leetcode.com/problems/redundant-connection/) — the edge whose `union` returns `False` is the one that closes a cycle.
- [Accounts Merge](https://leetcode.com/problems/accounts-merge/) — union accounts that share any email, then group members by their root.

### Shortest Path Algorithms

Find the minimum-cost path from a source to one/all other vertices. Every algorithm here is built on **edge relaxation** — "if going through `u` reaches `v` cheaper than the best known, update `v`." They differ only in the **order** they relax edges, and that order is dictated by the graph's shape (weights, signs, DAG or not).

| Algorithm | Handles | Source→ | Negative edges | Negative cycle | Time |
|---|---|---|---|---|---|
| BFS | unweighted | single | — | — | O(V + E) |
| Dijkstra | non-negative weights | single | ✗ | ✗ | O((V + E) log V) |
| Bellman-Ford | any weights | single | ✓ | detects | O(V · E) |
| Floyd-Warshall | any weights | all pairs | ✓ | detects (diagonal < 0) | O(V³) |
| DAG relax | any weights, DAG only | single | ✓ | impossible (acyclic) | O(V + E) |

#### BFS (unweighted shortest path)

**Use when** every edge has the same cost (grids, "minimum number of steps/moves"). The first time BFS reaches a node, it's via a shortest path, because BFS explores in order of distance.

```python
def bfs_dist(graph, src):
    dist = {src: 0}
    q = deque([src])
    while q:
        u = q.popleft()
        for v in graph[u]:
            if v not in dist:
                dist[v] = dist[u] + 1
                q.append(v)
    return dist
```

#### Dijkstra (non-negative weights)

**Use when** edges have varying **non-negative** weights and you want the single-source shortest paths. A min-heap of `(dist, node)` always pops the closest unprocessed node; once popped it's settled (final). This greedy step is exactly why negative edges break it — a later cheaper detour can't be reconsidered.

```python
import heapq

def dijkstra(graph, src):                       # graph: {u: [(v, w), ...]}
    dist = {src: 0}
    h = [(0, src)]
    while h:
        d, u = heapq.heappop(h)
        if d > dist[u]: continue                 # stale entry
        for v, w in graph[u]:
            nd = d + w
            if nd < dist.get(v, float('inf')):
                dist[v] = nd
                heapq.heappush(h, (nd, v))
    return dist
```

#### Bellman-Ford (handles negative edges, detects negative cycles)

**Use when** the graph has **negative-weight edges**, or you need to detect a negative cycle. Relax every edge `V - 1` times (any shortest path has at most `V - 1` edges). If a relaxation still succeeds on the V-th pass → a negative cycle exists.

```python
def bellman_ford(n, edges, src):                 # edges: [(u, v, w), ...]
    dist = [float('inf')] * n
    dist[src] = 0
    for _ in range(n - 1):
        for u, v, w in edges:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
    for u, v, w in edges:                        # negative cycle check
        if dist[u] + w < dist[v]:
            return None
    return dist
```

#### Floyd-Warshall (all-pairs)

**Use when** you need shortest paths between **every pair** of vertices, and the graph is small (`V ≤ ~400`, since it's `O(V³)`). `dist[i][j]` is improved by trying each vertex `k` as an intermediate. A negative value on the diagonal afterward means a negative cycle.

```python
def floyd_warshall(n, edges):
    dist = [[float('inf')] * n for _ in range(n)]
    for i in range(n): dist[i][i] = 0
    for u, v, w in edges: dist[u][v] = w
    for k in range(n):
        for i in range(n):
            for j in range(n):
                if dist[i][k] + dist[k][j] < dist[i][j]:
                    dist[i][j] = dist[i][k] + dist[k][j]
    return dist
```

#### DAG (topological order + relax)

On a DAG there are no cycles, so process vertices in **topological order** and relax each one's outgoing edges. Every node is finalized before its successors are touched — one linear pass, and it handles **negative weights** too (no cycles to break the assumption).

```python
def dag_shortest_path(n, edges, src):            # edges: [(u, v, w), ...]
    graph = [[] for _ in range(n)]
    indeg = [0] * n
    for u, v, w in edges:
        graph[u].append((v, w))
        indeg[v] += 1

    q = deque(u for u in range(n) if indeg[u] == 0)
    topo = []
    while q:                                     # Kahn's topological sort
        u = q.popleft()
        topo.append(u)
        for v, w in graph[u]:
            indeg[v] -= 1
            if indeg[v] == 0:
                q.append(v)

    dist = [float('inf')] * n
    dist[src] = 0
    for u in topo:                               # relax in topological order
        if dist[u] == float('inf'): continue
        for v, w in graph[u]:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
    return dist
```

#### Questions

- [Network Delay Time](https://leetcode.com/problems/network-delay-time/) — single source to all nodes; works with **Dijkstra, Bellman-Ford, or Floyd-Warshall** — a great problem to implement all three and compare.
- [Cheapest Flights Within K Stops](https://leetcode.com/problems/cheapest-flights-within-k-stops/) — Bellman-Ford with exactly k+1 relaxation rounds (the stop limit caps path length).

---

### Cycle Detection

The method depends on whether the graph is **undirected** or **directed** — the two need different rules.

| Graph | Techniques |
|---|---|
| Undirected | DFS/BFS with a **parent** check, or **DSU** (cycle if an edge's endpoints are already connected) |
| Directed | DFS with a **recursion-stack** check (back edge = cycle), or **Kahn's** (leftover nodes = cycle) |

---

#### Undirected — DFS / BFS (parent check)

Traverse the graph. If you reach an already-visited neighbor that **isn't the node you came from** (the parent), it's reachable by a second route → a cycle. The parent check is essential: without it the edge `u–v` looks like a cycle the instant you see `u` again from `v`.

```python
def has_cycle(graph):                     # DFS version
    visited = set()
    for node in graph:
        if node not in visited:
            if dfs_cycle(graph, node, visited, parent=-1):
                return True
    return False

def dfs_cycle(graph, u, visited, parent):
    visited.add(u)
    for v in graph[u]:
        if v not in visited:
            if dfs_cycle(graph, v, visited, u):   # u becomes the parent
                return True
        elif v != parent:                          # visited & not parent → cycle
            return True
    return False
```

```python
def bfs_cycle(graph, start, visited):     # BFS version
    queue = deque([(start, -1)])          # (node, parent)
    visited.add(start)
    while queue:
        u, parent = queue.popleft()
        for v in graph[u]:
            if v not in visited:
                visited.add(v)
                queue.append((v, u))
            elif v != parent:             # visited & not parent → cycle
                return True
    return False
```

#### Undirected — DSU (Union-Find)

For each edge `(u, v)`: if `u` and `v` are **already in the same set**, this edge closes a cycle. Otherwise union them. `O(α(n))` per edge.

```python
def has_cycle_dsu(n, edges):
    dsu = DSU(n)
    for u, v in edges:
        if dsu.find(u) == dsu.find(v):    # already connected → cycle
            return True
        dsu.union(u, v)
    return False
```

---

#### Directed graphs → it's just topological sort

Directed cycle detection is the **same algorithm** as [Topological Sorting](#topological-sorting) — no new code needed:

- **DFS-based** — the 3-state (unvisited / on-stack / done) DFS already returns a cycle flag when it revisits an on-stack node (a back edge). See [DFS-based Topological Sort](#dfs-based-topological-sort).
- **Kahn's BFS** — if the topo order can't place every node (`len(topo_order) != n`), the leftover nodes form a cycle. See [Kahn's Algorithm](#kahns-algorithm-bfs--in-degree).

---

#### Questions

- [Course Schedule](https://leetcode.com/problems/course-schedule/) — directed cycle detection = "can every node be topologically sorted?"
- [Detect Cycles in 2D Grid](https://leetcode.com/problems/detect-cycles-in-2d-grid/) — undirected cycle on a grid via a DFS parent check (or DSU).

### Minimum Spanning Tree (MST)

Connect all `V` nodes with `V - 1` edges, minimizing total weight. Undirected, weighted, connected graph.

#### Kruskal (sort edges + Union-Find)

```python
def kruskal(n, edges):                           # edges: [(w, u, v), ...]
    edges.sort()
    parent = list(range(n))
    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x
    total, used = 0, 0
    for w, u, v in edges:
        ru, rv = find(u), find(v)
        if ru != rv:
            parent[ru] = rv
            total += w; used += 1
            if used == n - 1: break
    return total
```

#### Prim (heap-based)

Like Dijkstra but tracks edge weights, not path sums.

```python
def prim(graph, n):                              # graph: {u: [(v, w), ...]}
    visited = {0}
    h = [(w, v) for v, w in graph[0]]
    heapq.heapify(h)
    total = 0
    while h and len(visited) < n:
        w, u = heapq.heappop(h)
        if u in visited: continue
        visited.add(u); total += w
        for v, w2 in graph[u]:
            if v not in visited:
                heapq.heappush(h, (w2, v))
    return total
```

#### Questions

- [Min Cost to Connect All Points](https://leetcode.com/problems/min-cost-to-connect-all-points/) — MST over a complete graph of Manhattan distances (Prim or Kruskal).

---

### Strongly Connected Components

A **strongly connected component (SCC)** is a maximal set of vertices in a **directed** graph where every vertex is reachable from every other vertex in the set. (Undirected graphs just have "connected components" — reachability is automatically mutual there.)

```
   1 ──▶ 0 ◀──▶ 3        SCCs: {0, 1, 2}  (0→2→1→0 cycle)
   ▲     │      │              {3}
   │     ▼      ▼              {4}
   2 ◀───┘      4        collapse each SCC to a node ⇒ a DAG (the "condensation")
```

#### Kosaraju's Algorithm

Two DFS passes, `O(V + E)`:

1. DFS the original graph, pushing each vertex onto a stack **when it finishes** (post-order).
2. **Reverse** every edge (transpose the graph).
3. Pop vertices off the stack; each DFS on the reversed graph collects exactly one SCC.

Why it works: the stack orders vertices by finish time, so the vertex on top belongs to a "source" SCC of the condensation. Reversing the edges makes that source a sink — so a DFS from it can't escape its own SCC, cleanly carving out one component at a time.

```python
def kosaraju(n, adj):                     # adj: adjacency list over 0..n-1
    visited = [False] * n
    order = []

    # Pass 1: record vertices by finish time
    def dfs1(u):
        visited[u] = True
        for v in adj[u]:
            if not visited[v]:
                dfs1(v)
        order.append(u)                   # pushed once fully explored

    for i in range(n):
        if not visited[i]:
            dfs1(i)

    # Pass 2: transpose the graph
    radj = [[] for _ in range(n)]
    for u in range(n):
        for v in adj[u]:
            radj[v].append(u)

    # Pass 3: DFS on the transpose in reverse finish order
    visited = [False] * n
    sccs = []

    def dfs2(u, comp):
        visited[u] = True
        comp.append(u)
        for v in radj[u]:
            if not visited[v]:
                dfs2(v, comp)

    for u in reversed(order):
        if not visited[u]:
            comp = []
            dfs2(u, comp)
            sccs.append(comp)

    return sccs                           # len(sccs) = number of SCCs
```

#### Questions

Pure SCC problems are uncommon on LeetCode; the Kosaraju/Tarjan machinery usually shows up as bridges, articulation points, or reachability.

- [Critical Connections in a Network](https://leetcode.com/problems/critical-connections-in-a-network/) — find all bridges with Tarjan's low-link DFS.

---

### Bridges & Articulation Points

Both are found by a single DFS that tracks two timestamps per vertex — this is the **low-link** idea that Tarjan's SCC (next) reuses.

- `disc[u]` — **discovery time**: the order in which DFS first reaches `u`.
- `low[u]`  — the **lowest `disc`** reachable from `u`'s subtree using tree edges plus **at most one back edge**.

A **bridge** is an edge whose removal disconnects the graph. An **articulation point** is a vertex whose removal disconnects it. Both come out of comparing a child's `low` against the parent's `disc`, in `O(V + E)`.

**Bridge:** the tree edge `u → v` is a bridge when `low[v] > disc[u]` — nothing in `v`'s subtree can reach `u` or anything above it except through this edge.

```python
def find_bridges(n, adj):
    disc = [-1] * n
    low  = [0] * n
    timer = [0]
    bridges = []

    def dfs(u, parent):
        disc[u] = low[u] = timer[0]; timer[0] += 1
        for v in adj[u]:
            if v == parent:
                continue
            if disc[v] == -1:                 # tree edge
                dfs(v, u)
                low[u] = min(low[u], low[v])
                if low[v] > disc[u]:          # v can't reach u or above
                    bridges.append((u, v))
            else:                             # back edge
                low[u] = min(low[u], disc[v])

    for i in range(n):
        if disc[i] == -1:
            dfs(i, -1)
    return bridges
```

**Articulation point:** a non-root `u` is an AP if some child `v` has `low[v] >= disc[u]` (that subtree has no back edge climbing above `u`). The **root** is an AP only if it has **more than one** DFS child.

```python
def articulation_points(n, adj):
    disc = [-1] * n
    low  = [0] * n
    timer = [0]
    ap = set()

    def dfs(u, parent):
        disc[u] = low[u] = timer[0]; timer[0] += 1
        child = 0
        for v in adj[u]:
            if v == parent:
                continue
            if disc[v] == -1:
                child += 1
                dfs(v, u)
                low[u] = min(low[u], low[v])
                if parent != -1 and low[v] >= disc[u]:
                    ap.add(u)
            else:
                low[u] = min(low[u], disc[v])
        if parent == -1 and child > 1:        # root with 2+ subtrees
            ap.add(u)

    for i in range(n):
        if disc[i] == -1:
            dfs(i, -1)
    return ap
```

- [Critical Connections in a Network](https://leetcode.com/problems/critical-connections-in-a-network/) — return all bridges

---

### Tarjan's Algorithm (SCC via low-link)

Tarjan finds SCCs in a **single DFS** using the same `disc` / `low` timestamps, plus a **stack of the current path** and an `on_stack` flag. Only edges to vertices *still on the stack* update `low` (they're part of the current, unfinished SCC). When a vertex satisfies `low[u] == disc[u]`, it's the **root** of an SCC — pop the stack down to it to emit that component.

```python
def tarjan_scc(n, adj):
    disc = [-1] * n
    low  = [0] * n
    on_stack = [False] * n
    timer = [0]
    stack = []
    sccs = []

    def dfs(u):
        disc[u] = low[u] = timer[0]; timer[0] += 1
        stack.append(u); on_stack[u] = True
        for v in adj[u]:
            if disc[v] == -1:                 # tree edge
                dfs(v)
                low[u] = min(low[u], low[v])
            elif on_stack[v]:                 # edge into the current SCC
                low[u] = min(low[u], disc[v])
        if low[u] == disc[u]:                 # u is an SCC root
            comp = []
            while True:
                w = stack.pop(); on_stack[w] = False
                comp.append(w)
                if w == u:
                    break
            sccs.append(comp)

    for i in range(n):
        if disc[i] == -1:
            dfs(i)
    return sccs
```

> Tarjan vs Kosaraju: same `O(V + E)`, but Tarjan does it in **one** DFS (no transpose) at the cost of the extra stack bookkeeping.

---

### Must-Do / FAANG Interview Questions

The graph problems that show up most in big-tech interviews. Figuring out which technique each needs is part of the practice — concepts and worked hints live in the sections above.

- [Number of Islands](https://leetcode.com/problems/number-of-islands/)
- [Clone Graph](https://leetcode.com/problems/clone-graph/)
- [Course Schedule](https://leetcode.com/problems/course-schedule/)
- [Course Schedule II](https://leetcode.com/problems/course-schedule-ii/)
- [Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/)
- [Rotting Oranges](https://leetcode.com/problems/rotting-oranges/)
- [Word Ladder](https://leetcode.com/problems/word-ladder/)
- [Accounts Merge](https://leetcode.com/problems/accounts-merge/)
- [Network Delay Time](https://leetcode.com/problems/network-delay-time/)
- [Cheapest Flights Within K Stops](https://leetcode.com/problems/cheapest-flights-within-k-stops/)
- [Flood Fill](https://leetcode.com/problems/flood-fill/)
- [Max Area of Island](https://leetcode.com/problems/max-area-of-island/)
- [Surrounded Regions](https://leetcode.com/problems/surrounded-regions/)
- [Shortest Bridge](https://leetcode.com/problems/shortest-bridge/)
- [01 Matrix](https://leetcode.com/problems/01-matrix/)
- [Shortest Path in Binary Matrix](https://leetcode.com/problems/shortest-path-in-binary-matrix/)
- [As Far from Land as Possible](https://leetcode.com/problems/as-far-from-land-as-possible/)
- [Open the Lock](https://leetcode.com/problems/open-the-lock/)
- [Keys and Rooms](https://leetcode.com/problems/keys-and-rooms/)
- [All Paths From Source to Target](https://leetcode.com/problems/all-paths-from-source-to-target/)
- [Number of Provinces](https://leetcode.com/problems/number-of-provinces/)
- [Number of Connected Components in an Undirected Graph](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/)
- [Redundant Connection](https://leetcode.com/problems/redundant-connection/)
- [Graph Valid Tree](https://leetcode.com/problems/graph-valid-tree/)
- [Number of Operations to Make Network Connected](https://leetcode.com/problems/number-of-operations-to-make-network-connected/)
- [Satisfiability of Equality Equations](https://leetcode.com/problems/satisfiability-of-equality-equations/)
- [Evaluate Division](https://leetcode.com/problems/evaluate-division/)
- [Smallest String With Swaps](https://leetcode.com/problems/smallest-string-with-swaps/)
- [Most Stones Removed with Same Row or Column](https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/)
- [Number of Islands II](https://leetcode.com/problems/number-of-islands-ii/)
- [Redundant Connection II](https://leetcode.com/problems/redundant-connection-ii/)
- [Course Schedule IV](https://leetcode.com/problems/course-schedule-iv/)
- [Alien Dictionary](https://leetcode.com/problems/alien-dictionary/)
- [Find Eventual Safe States](https://leetcode.com/problems/find-eventual-safe-states/)
- [Minimum Height Trees](https://leetcode.com/problems/minimum-height-trees/)
- [Parallel Courses](https://leetcode.com/problems/parallel-courses/)
- [Find All Possible Recipes from Given Supplies](https://leetcode.com/problems/find-all-possible-recipes-from-given-supplies/)
- [Sort Items by Groups Respecting Dependencies](https://leetcode.com/problems/sort-items-by-groups-respecting-dependencies/)
- [Build a Matrix With Conditions](https://leetcode.com/problems/build-a-matrix-with-conditions/)
- [Path with Minimum Effort](https://leetcode.com/problems/path-with-minimum-effort/)
- [Swim in Rising Water](https://leetcode.com/problems/swim-in-rising-water/)
- [Path with Maximum Probability](https://leetcode.com/problems/path-with-maximum-probability/)
- [The Maze II](https://leetcode.com/problems/the-maze-ii/)
- [Minimum Cost to Make at Least One Valid Path in a Grid](https://leetcode.com/problems/minimum-cost-to-make-at-least-one-valid-path-in-a-grid/)
- [Minimum Cost to Convert String I](https://leetcode.com/problems/minimum-cost-to-convert-string-i/)
- [Find Minimum Time to Reach Last Room I](https://leetcode.com/problems/find-minimum-time-to-reach-last-room-i/)
- [Minimum Cost Path with Edge Reversals](https://leetcode.com/problems/minimum-cost-path-with-edge-reversals/)
- [Minimum Weighted Subgraph With the Required Paths](https://leetcode.com/problems/minimum-weighted-subgraph-with-the-required-paths/)
- [Find the City With the Smallest Number of Neighbors at a Threshold Distance](https://leetcode.com/problems/find-the-city-with-the-smallest-number-of-neighbors-at-a-threshold-distance/)
- [Is Graph Bipartite?](https://leetcode.com/problems/is-graph-bipartite/)
- [Possible Bipartition](https://leetcode.com/problems/possible-bipartition/)
- [Min Cost to Connect All Points](https://leetcode.com/problems/min-cost-to-connect-all-points/)
- [Connecting Cities With Minimum Cost](https://leetcode.com/problems/connecting-cities-with-minimum-cost/)
- [Detect Cycles in 2D Grid](https://leetcode.com/problems/detect-cycles-in-2d-grid/)
- [Critical Connections in a Network](https://leetcode.com/problems/critical-connections-in-a-network/)

---

### Quick reference

| Need                                | Algorithm                    |
|-------------------------------------|------------------------------|
| Connected components                | DFS / BFS / DSU              |
| Shortest path, unweighted           | BFS                          |
| Shortest path, non-neg weights      | Dijkstra                     |
| Shortest path, neg weights          | Bellman-Ford                 |
| Shortest path, all pairs            | Floyd-Warshall               |
| Topological order                   | Kahn's BFS or DFS post-order |
| Cycle in undirected                 | DFS with parent, or DSU      |
| Cycle in directed                   | DFS rec-stack, or Kahn's     |
| MST                                 | Kruskal or Prim              |
| Bipartite check                     | 2-coloring via BFS/DFS       |
| Strongly connected components (dir) | Kosaraju (2 DFS) or Tarjan    |
| Bridges / articulation              | Tarjan (DFS low-link)        |

