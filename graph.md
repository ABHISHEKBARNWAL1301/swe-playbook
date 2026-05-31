## GRAPH 

A [graph](https://www.geeksforgeeks.org/graph-and-its-representations/) is a nonlinear data structure consisting of nodes and edges.

Quick Links

- [Representation](#-representation)
- [Traversal](#-traversal)
- [Topological Sorting](#-topological-sorting)
- [Disjoint Set Union (DSU) / Union-Find](#-disjoint-set-union-dsu--union-find)
- [Cycle Detection in Undirected Graph](#-cycle-detection)



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

- [Rotting Oranges](https://leetcode.com/problems/rotting-oranges/)
    
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
  helper(0, graph, visited);               
```


### TOPOLOGICAL SORTING

Topological sorting for Directed Acyclic Graph (DAG) is a linear ordering of vertices such that for every directed edge (u, v), vertex u comes before v in the ordering.

Topological sorting can be performed using both:
 - Kahn's Algorithm (BFS + in-degree) — the approach used in your findOrder solution.
 - Depth-First Search (DFS) — a very elegant and widely used approach.

Both produce a valid topological order for a Directed Acyclic Graph (DAG).


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
### Questions

- [Course Schedule](https://leetcode.com/problems/course-schedule/)
- [Course Schedule II](https://leetcode.com/problems/course-schedule-ii/)

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
- [Find Eventual Safe States](https://leetcode.com/problems/find-eventual-safe-states/)


### Disjoint Set Union (DSU) / Union-Find

Disjoint Set Union (DSU), also known as Union-Find, is a data structure used to efficiently maintain a collection of disjoint (non-overlapping) sets.

It supports two fundamental operations:
- Find(x) → Returns the representative (root) of the set containing x.
- Union(x, y) → Merges the sets containing x and y.

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
![1779288856411](image/graph/1779288856411.png)
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
Cycle Detection in Undirected Graph

```python
# For every edge (u, v):
# Find representatives of u and v
# If they are the same → cycle exists, Otherwise, union them

def hasCycle(n, edges):
    dsu = DSU(n)

    for u, v in edges:
        if dsu.find(u) == dsu.find(v):
            return True
        dsu.union(u, v)

    return False
```




### CYCLE DETECTION ( Undirected Graph )

In an undirected graph, if during DFS or BFS we encounter a visited neighbor that is not the current node's parent, then a cycle exists.
  ```python
# DFS

def has_cycle(graph):
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
            if dfs_cycle(graph, v, visited, u):   # current node becomes parent
                return True
        elif v != parent: # Visited neighbor that is not the parent => cycle
            return True

    return False
  
# BFS

def has_cycle_bfs(graph):
    visited = set()

    for start in graph:
        if start not in visited:
            if bfs_cycle(graph, start, visited):
                return True

    return False


def bfs_cycle(graph, start, visited):
    queue = deque()
    queue.append((start, -1))   # (node, parent)
    visited.add(start)

    while queue:
        u, parent = queue.popleft()

        for v in graph[u]:
            if v not in visited:
                visited.add(v)
                queue.append((v, u))
            elif v != parent:
                return True

    return False

```

#### Directed Graph (Topological Sort & Cycle Detection)
 -  DFS → encountering a node currently in the recursion stack = Cycle
 - BFS → If not all nodes can be processed → Cycle exists

#### DSU (Disjoint Set Union)
Used to find cycles in undirected graphs in O(α(n)) time.


### Shortest Path

- 


