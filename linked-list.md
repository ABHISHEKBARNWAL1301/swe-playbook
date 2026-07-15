# Linked Lists

Each node holds a value and a pointer to the next. No random access — every position needs a walk from `head`. O(1) insert/delete at a known node (no shifting), but index lookup is O(n).

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```

## Contents

- [Singly Linked List](#singly-linked-list)
- [Doubly Linked List](#doubly-linked-list)

---

## Singly Linked List

One `next` pointer per node. Every technique below is a variation on three core moves.

### Core operations

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

### Dummy Head (Sentinel)

Add a fake node before `head`. Eliminates "what if I delete the head?" / "what if the list is empty?" edge cases.

```python
dummy = ListNode(0, head)
prev = dummy
# ... mutate ...
return dummy.next
```

Use it for: remove nth from end, partition, merge, deletion.

### Fast / Slow Pointers

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

### Reverse

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

### Merge

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

For **k sorted lists**: divide-and-conquer pairwise merge, or a min-heap of the heads — both O(N log k). See [heaps.md](heaps.md).

### Reorder / Partition

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

### Intersection of Two Lists

Two pointers, each walks its own list then swaps to the other. They meet at the intersection (or at `None`) after at most `len(a) + len(b)` steps.

```python
def intersection(a, b):
    pa, pb = a, b
    while pa is not pb:
        pa = pa.next if pa else b
        pb = pb.next if pb else a
    return pa
```

### Deep Copy with Random Pointer

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

### Questions

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

---

## Doubly Linked List

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

**Where it's used:** the O(1) "move a node to the front / evict from the back" combo is exactly what an [LRU cache](design.md) (hashmap + DLL) needs. Deques, browser history, and Python's own `collections.OrderedDict` are built on the same idea.

### Questions

- [Design Linked List](https://leetcode.com/problems/design-linked-list/)
- [Flatten a Multilevel Doubly Linked List](https://leetcode.com/problems/flatten-a-multilevel-doubly-linked-list/)
- [Design Browser History](https://leetcode.com/problems/design-browser-history/)
- [LRU Cache](https://leetcode.com/problems/lru-cache/) (hashmap + DLL — full design in [design.md](design.md))
- [Convert Binary Search Tree to Sorted Doubly Linked List](https://leetcode.com/problems/convert-binary-search-tree-to-sorted-doubly-linked-list/)
- [All O`one Data Structure](https://leetcode.com/problems/all-oone-data-structure/) (DLL of frequency buckets)
- [Design Circular Deque](https://leetcode.com/problems/design-circular-deque/)
