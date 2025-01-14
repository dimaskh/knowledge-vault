# Graph Data Structure

Last Updated: 2025-01-14

## Definition

A graph is a non-linear data structure consisting of vertices (nodes) and edges that connect these vertices. It's used to represent relationships between objects.

## Basic Components

- Vertices (V): Points or nodes in the graph
- Edges (E): Lines connecting vertices
- Weight: Cost or value associated with an edge
- Path: Sequence of vertices connected by edges
- Cycle: Path that starts and ends at the same vertex

## Types of Graphs

### 1. Undirected Graph

- Edges have no direction
- If vertex A is connected to B, then B is connected to A
- Example: Social network friendships

```python
# Undirected graph representation using adjacency list
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}
```

### 2. Directed Graph (Digraph)

- Edges have direction (one-way connections)
- If A points to B, B might not point to A
- Example: Web pages and links

```python
# Directed graph representation
digraph = {
    'A': ['B', 'C'],
    'B': ['D'],
    'C': ['D'],
    'D': []  # D has no outgoing edges
}
```

## Graph Representations

### 1. Adjacency Matrix

```python
class Graph:
    def __init__(self, vertices):
        self.V = vertices
        self.graph = [[0] * vertices for _ in range(vertices)]
    
    def add_edge(self, v1, v2, weight=1):
        self.graph[v1][v2] = weight
        # For undirected graph, add:
        # self.graph[v2][v1] = weight

# Usage
graph = Graph(4)  # 4 vertices
graph.add_edge(0, 1)  # Add edge between vertex 0 and 1
```

### 2. Adjacency List

```python
class Graph:
    def __init__(self):
        self.graph = {}
    
    def add_vertex(self, vertex):
        if vertex not in self.graph:
            self.graph[vertex] = []
    
    def add_edge(self, v1, v2):
        if v1 in self.graph:
            self.graph[v1].append(v2)
        # For undirected graph, add:
        # if v2 in self.graph:
        #     self.graph[v2].append(v1)

# Usage
graph = Graph()
graph.add_vertex('A')
graph.add_vertex('B')
graph.add_edge('A', 'B')
```

## Spanning Trees

### Definition
A spanning tree is a subset of a graph that:
- Includes all vertices
- Contains minimum edges to connect all vertices
- Has no cycles

### Types

1. Minimum Spanning Tree (MST)
   - Spanning tree with minimum total edge weight
   - Common algorithms:
     - Kruskal's Algorithm
     - Prim's Algorithm

```python
# Kruskal's Algorithm implementation
class UnionFind:
    def __init__(self, vertices):
        self.parent = {v: v for v in vertices}
        self.rank = {v: 0 for v in vertices}
    
    def find(self, item):
        if self.parent[item] != item:
            self.parent[item] = self.find(self.parent[item])
        return self.parent[item]
    
    def union(self, x, y):
        xroot, yroot = self.find(x), self.find(y)
        if self.rank[xroot] < self.rank[yroot]:
            self.parent[xroot] = yroot
        elif self.rank[xroot] > self.rank[yroot]:
            self.parent[yroot] = xroot
        else:
            self.parent[yroot] = xroot
            self.rank[xroot] += 1

def kruskal_mst(graph, vertices):
    result = []
    edges = [(w, u, v) for u in graph for v, w in graph[u].items()]
    edges.sort()
    union_find = UnionFind(vertices)
    
    for weight, u, v in edges:
        if union_find.find(u) != union_find.find(v):
            union_find.union(u, v)
            result.append((u, v, weight))
    
    return result
```

2. Maximum Spanning Tree
   - Spanning tree with maximum total edge weight
   - Similar algorithms to MST but with reversed weight comparison

## Graph Traversal

### 1. Depth-First Search (DFS)

```python
def dfs(graph, start, visited=None):
    if visited is None:
        visited = set()
    
    visited.add(start)
    print(start, end=' ')
    
    for neighbor in graph[start]:
        if neighbor not in visited:
            dfs(graph, neighbor, visited)
```

### 2. Breadth-First Search (BFS)

```python
from collections import deque

def bfs(graph, start):
    visited = set()
    queue = deque([start])
    visited.add(start)
    
    while queue:
        vertex = queue.popleft()
        print(vertex, end=' ')
        
        for neighbor in graph[vertex]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
```

## Time Complexity

### Adjacency Matrix
- Space: O(V²)
- Add Vertex: O(V²)
- Add Edge: O(1)
- Remove Vertex: O(V²)
- Remove Edge: O(1)
- Query: O(1)

### Adjacency List
- Space: O(V + E)
- Add Vertex: O(1)
- Add Edge: O(1)
- Remove Vertex: O(V + E)
- Remove Edge: O(E)
- Query: O(V)

## Common Applications

1. Social Networks
   - Friend connections
   - Network analysis
   - Influence mapping

2. Transportation
   - Road networks
   - Flight paths
   - Railway systems

3. Computer Networks
   - Network topology
   - Routing algorithms
   - Internet connectivity

4. Biology
   - Protein interaction networks
   - Gene regulatory networks
   - Neural networks

## Best Practices

1. Choosing Representation
   - Use Adjacency Matrix for:
     - Dense graphs
     - Quick edge weight lookups
     - Small number of vertices
   
   - Use Adjacency List for:
     - Sparse graphs
     - Large number of vertices
     - Memory constraints

2. Algorithm Selection
   - BFS for shortest path in unweighted graphs
   - Dijkstra's for weighted graphs
   - DFS for topological sorting

3. Performance Optimization
   - Use appropriate data structures
   - Consider graph density
   - Handle disconnected components

4. Memory Management
   - Clear visited sets after traversal
   - Use efficient storage methods
   - Consider compression for large graphs

## Common Pitfalls

1. Not handling disconnected components
2. Forgetting about cycles in traversal
3. Incorrect direction handling in directed graphs
4. Not considering edge cases (empty graph, single vertex)
5. Memory overflow in large dense graphs
