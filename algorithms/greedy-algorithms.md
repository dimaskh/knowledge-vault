# Greedy Algorithms

Last Updated: 2025-01-14

## Overview
Greedy algorithms make locally optimal choices at each step, hoping to find a global optimum. They don't always yield the best solution but are often simple and efficient.

## Dijkstra's Algorithm
(Already covered in graph-algorithms.md)

## Huffman Coding

### Description
- Used for lossless data compression
- Builds optimal prefix code
- Creates binary tree based on character frequencies

### Implementation
```python
import heapq
from collections import defaultdict

class HuffmanNode:
    def __init__(self, char, freq):
        self.char = char
        self.freq = freq
        self.left = None
        self.right = None
    
    def __lt__(self, other):
        return self.freq < other.freq

def build_huffman_tree(text):
    # Calculate frequencies
    freq = defaultdict(int)
    for char in text:
        freq[char] += 1
    
    # Create priority queue
    pq = [HuffmanNode(char, freq) for char, freq in freq.items()]
    heapq.heapify(pq)
    
    # Build tree
    while len(pq) > 1:
        left = heapq.heappop(pq)
        right = heapq.heappop(pq)
        
        internal = HuffmanNode(None, left.freq + right.freq)
        internal.left = left
        internal.right = right
        
        heapq.heappush(pq, internal)
    
    return pq[0]

def build_codes(root, code="", codes=None):
    if codes is None:
        codes = {}
    
    if root:
        if root.char is not None:
            codes[root.char] = code
        build_codes(root.left, code + "0", codes)
        build_codes(root.right, code + "1", codes)
    
    return codes
```

### Time Complexity
- Building tree: O(n log n)
- Encoding: O(n)
- Decoding: O(n)

## Kruskal's Algorithm

### Description
- Finds Minimum Spanning Tree
- Processes edges in ascending weight order
- Uses Union-Find data structure

### Implementation
```python
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
        if xroot == yroot:
            return
        if self.rank[xroot] < self.rank[yroot]:
            self.parent[xroot] = yroot
        elif self.rank[xroot] > self.rank[yroot]:
            self.parent[yroot] = xroot
        else:
            self.parent[yroot] = xroot
            self.rank[xroot] += 1

def kruskal_mst(graph):
    edges = [(w, u, v) for u in graph for v, w in graph[u].items()]
    edges.sort()  # Sort by weight
    vertices = list(graph.keys())
    uf = UnionFind(vertices)
    mst = []
    
    for weight, u, v in edges:
        if uf.find(u) != uf.find(v):
            uf.union(u, v)
            mst.append((u, v, weight))
    
    return mst
```

### Time Complexity
- O(E log E) or O(E log V)

## Ford-Fulkerson Algorithm

### Description
- Finds maximum flow in flow network
- Uses augmenting paths
- Iteratively increases flow

### Implementation
```python
def bfs(graph, source, sink, parent):
    visited = set()
    queue = [source]
    visited.add(source)
    
    while queue:
        u = queue.pop(0)
        for v, capacity in graph[u].items():
            if v not in visited and capacity > 0:
                queue.append(v)
                visited.add(v)
                parent[v] = u
                if v == sink:
                    return True
    return False

def ford_fulkerson(graph, source, sink):
    parent = {}
    max_flow = 0
    
    # Create residual graph
    residual = {u: {v: cap for v, cap in edges.items()}
                for u, edges in graph.items()}
    
    while bfs(residual, source, sink, parent):
        path_flow = float("Inf")
        s = sink
        
        while s != source:
            path_flow = min(path_flow, residual[parent[s]][s])
            s = parent[s]
        
        max_flow += path_flow
        
        v = sink
        while v != source:
            u = parent[v]
            residual[u][v] -= path_flow
            residual[v][u] += path_flow
            v = parent[v]
    
    return max_flow
```

### Time Complexity
- O(VE²) for Ford-Fulkerson
- O(VE) for Edmonds-Karp (BFS variant)

## Prim's Algorithm

### Description
- Finds Minimum Spanning Tree
- Grows tree one vertex at a time
- Uses priority queue for efficiency

### Implementation
```python
def prims_mst(graph):
    start_vertex = list(graph.keys())[0]
    mst = []
    visited = {start_vertex}
    edges = [
        (cost, start_vertex, to)
        for to, cost in graph[start_vertex].items()
    ]
    heapq.heapify(edges)
    
    while edges:
        cost, frm, to = heapq.heappop(edges)
        if to not in visited:
            visited.add(to)
            mst.append((frm, to, cost))
            
            for next_vertex, next_cost in graph[to].items():
                if next_vertex not in visited:
                    heapq.heappush(edges, (next_cost, to, next_vertex))
    
    return mst
```

### Time Complexity
- O((V + E) log V) with binary heap
- O(E + V log V) with Fibonacci heap

## Best Practices

### 1. Algorithm Selection
- Consider problem constraints
- Check if greedy approach is optimal
- Verify solution correctness

### 2. Implementation
- Use appropriate data structures
- Handle edge cases
- Consider memory constraints

### 3. Optimization
- Use efficient data structures
- Implement early termination
- Consider problem-specific optimizations

## Common Applications

1. Huffman Coding
   - Data compression
   - File encoding
   - Network protocols

2. Minimum Spanning Trees
   - Network design
   - Clustering
   - Circuit design

3. Maximum Flow
   - Network routing
   - Bipartite matching
   - Resource allocation

4. Shortest Paths
   - GPS navigation
   - Network routing
   - Game pathfinding

## Common Pitfalls

1. Assuming Greedy is Optimal
   - Verify solution correctness
   - Consider counter-examples
   - Test edge cases

2. Implementation Issues
   - Integer overflow
   - Infinite loops
   - Missing edge cases

3. Performance Issues
   - Poor data structure choice
   - Unnecessary computations
   - Memory leaks
