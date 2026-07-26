## GRAPH 

A graph is a nonlinear data structure consisting of nodes and edges.

Quick Links

- [Representation](#representation)
- [Traversal](#traversal)
    - [BFS](#bfs)
    - [DFS](#dfs)
- [Topological Sorting](#topological-sorting)
    - [Kahn's Algorithm (BFS + in-degree)](#kahns-algorithm-bfs--in-degree)
    - [DFS-based Topological Sort](#dfs-based-topological-sort)
- [Disjoint Set Union (DSU) / Union-Find](#disjoint-set-union-dsu--union-find)
- [Shortest Path Algorithms](#shortest-path-algorithms)
    - [BFS (unweighted)](#bfs-unweighted-shortest-path)
    - [Dijkstra (non-negative weights)](#dijkstra-non-negative-weights)
    - [Bellman-Ford (handles negative edges, detects negative cycles)](#bellman-ford-handles-negative-edges-detects-negative-cycles)
    - [Floyd-Warshall (all-pairs)](#floyd-warshall-all-pairs)
    - [DAG (topological order + relax)](#dag-topological-order--relax)
- [Cycle Detection](#cycle-detection)
- [Minimum Spanning Tree (MST)](#minimum-spanning-tree-mst)
    - [Kruskal (sort edges + Union-Find)](#kruskal-sort-edges--union-find)
    - [Prim (heap-based)](#prim-heap-based)
- [Strongly Connected Components](#strongly-connected-components)
    - [Kosaraju's Algorithm](#kosarajus-algorithm)
    - [Bridges & Articulation Points](#bridges--articulation-points)
    - [Tarjan's Algorithm (SCC via low-link)](#tarjans-algorithm-scc-via-low-link)
- [Must-Do / FAANG Interview Questions](#must-do--faang-interview-questions)
- [Quick reference](#quick-reference)



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


### TOPOLOGICAL SORTING

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




### CYCLE DETECTION

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

