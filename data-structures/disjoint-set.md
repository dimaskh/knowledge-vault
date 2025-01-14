# Disjoint Set (Union-Find)

Last Updated: 2025-01-14

## Overview
A Disjoint Set (or Union-Find) data structure tracks a set of elements partitioned into non-overlapping subsets. It provides near-constant-time operations to merge sets and determine if elements are in the same set.

## Properties
1. Elements are partitioned into disjoint sets
2. Each set has a representative element
3. Supports efficient union operations
4. Uses path compression and union by rank
5. Near-constant time operations

## Basic Implementation

### Simple Union-Find
```python
class DisjointSet:
    def __init__(self, size):
        self.parent = list(range(size))
        self.rank = [0] * size
    
    def find(self, x):
        # Find set representative with path compression
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        # Unite two sets using union by rank
        px, py = self.find(x), self.find(y)
        
        if px == py:
            return
        
        if self.rank[px] < self.rank[py]:
            self.parent[px] = py
        elif self.rank[px] > self.rank[py]:
            self.parent[py] = px
        else:
            self.parent[py] = px
            self.rank[px] += 1
    
    def connected(self, x, y):
        return self.find(x) == self.find(y)
```

## Advanced Implementation

### Size-Based Union
```python
class SizeBasedDisjointSet:
    def __init__(self, size):
        self.parent = list(range(size))
        self.size = [1] * size
        self.count = size  # Number of disjoint sets
    
    def find(self, x):
        # Find with path compression
        root = x
        while root != self.parent[root]:
            root = self.parent[root]
        
        # Path compression
        while x != root:
            next_parent = self.parent[x]
            self.parent[x] = root
            x = next_parent
        
        return root
    
    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        
        if px == py:
            return False
        
        # Union by size
        if self.size[px] < self.size[py]:
            px, py = py, px
        
        self.parent[py] = px
        self.size[px] += self.size[py]
        self.count -= 1
        return True
    
    def get_size(self, x):
        return self.size[self.find(x)]
```

### Weighted Quick Union with Path Compression
```python
class WeightedQuickUnion:
    def __init__(self, size):
        self.parent = list(range(size))
        self.size = [1] * size
        self.max = list(range(size))  # Track maximum element in each set
    
    def find(self, x):
        while x != self.parent[x]:
            # Path compression by halving
            self.parent[x] = self.parent[self.parent[x]]
            x = self.parent[x]
        return x
    
    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        
        if px == py:
            return
        
        # Union by weight
        if self.size[px] < self.size[py]:
            self.parent[px] = py
            self.size[py] += self.size[px]
            self.max[py] = max(self.max[py], self.max[px])
        else:
            self.parent[py] = px
            self.size[px] += self.size[py]
            self.max[px] = max(self.max[px], self.max[py])
```

## Advanced Applications

### Connected Components
```python
class ConnectedComponents:
    def __init__(self, n, edges):
        self.ds = DisjointSet(n)
        
        for u, v in edges:
            self.ds.union(u, v)
        
        # Find all components
        self.components = {}
        for i in range(n):
            root = self.ds.find(i)
            if root not in self.components:
                self.components[root] = []
            self.components[root].append(i)
    
    def get_components(self):
        return list(self.components.values())
    
    def component_count(self):
        return len(self.components)
```

### Dynamic Graph Connectivity
```python
class DynamicConnectivity:
    def __init__(self, n):
        self.ds = DisjointSet(n)
        self.edges = set()
    
    def add_edge(self, u, v):
        self.edges.add((min(u, v), max(u, v)))
        self.ds.union(u, v)
    
    def remove_edge(self, u, v):
        self.edges.remove((min(u, v), max(u, v)))
        # Rebuild connectivity
        self.ds = DisjointSet(self.ds.parent.size)
        for edge_u, edge_v in self.edges:
            self.ds.union(edge_u, edge_v)
    
    def connected(self, u, v):
        return self.ds.connected(u, v)
```

## Time Complexity
- Find: O(α(n)) amortized
- Union: O(α(n)) amortized
- Connected: O(α(n))
where α(n) is the inverse Ackermann function

## Use Cases

1. Graph Algorithms
   - Kruskal's MST algorithm
   - Cycle detection
   - Connected components

2. Network Management
   - Network connectivity
   - Routing tables
   - Peer groups

3. Image Processing
   - Connected component labeling
   - Region growing
   - Image segmentation

## Advanced Features

### Persistent Disjoint Set
```python
class PersistentDisjointSet:
    def __init__(self, size):
        self.parent = list(range(size))
        self.rank = [0] * size
        self.history = []
    
    def find(self, x):
        if self.parent[x] != x:
            return self.find(self.parent[x])
        return x
    
    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        
        if px == py:
            return
        
        # Save state for rollback
        self.history.append((px, py, self.rank[px], self.rank[py]))
        
        if self.rank[px] < self.rank[py]:
            self.parent[px] = py
        elif self.rank[px] > self.rank[py]:
            self.parent[py] = px
        else:
            self.parent[py] = px
            self.rank[px] += 1
    
    def rollback(self):
        if not self.history:
            return
        
        px, py, rank_x, rank_y = self.history.pop()
        self.parent[px] = px
        self.parent[py] = py
        self.rank[px] = rank_x
        self.rank[py] = rank_y
```

### Lock-Free Implementation
```python
from threading import atomic

class LockFreeDisjointSet:
    def __init__(self, size):
        self.parent = [atomic.AtomicReference(i) for i in range(size)]
        self.rank = [atomic.AtomicInteger(0) for _ in range(size)]
    
    def find(self, x):
        while True:
            p = self.parent[x].value
            if p == x:
                return x
            gp = self.parent[p].value
            self.parent[x].compare_and_set(p, gp)
            x = gp
    
    def union(self, x, y):
        while True:
            px = self.find(x)
            py = self.find(y)
            
            if px == py:
                return False
            
            rank_x = self.rank[px].value
            rank_y = self.rank[py].value
            
            if rank_x < rank_y or (rank_x == rank_y and px < py):
                if self.parent[px].compare_and_set(px, py):
                    if rank_x == rank_y:
                        self.rank[py].increment()
                    return True
            else:
                if self.parent[py].compare_and_set(py, px):
                    if rank_x == rank_y:
                        self.rank[px].increment()
                    return True
```

## Best Practices

1. Implementation
   - Use path compression
   - Implement union by rank/size
   - Consider persistence needs

2. Performance
   - Balance between find and union
   - Use iterative path compression
   - Optimize for specific use case

3. Memory
   - Use appropriate data types
   - Consider space-time trade-offs
   - Implement cleanup if needed

## Common Pitfalls

1. Implementation Issues
   - Missing path compression
   - Incorrect rank updates
   - Poor parent tracking

2. Performance Issues
   - Not using optimization techniques
   - Excessive finds
   - Poor memory locality

3. Design Issues
   - Wrong choice of variant
   - Missing functionality
   - Poor error handling

## Performance Optimization

### Cache-Friendly Implementation
```python
class CacheOptimizedDisjointSet:
    def __init__(self, size):
        # Pack parent and rank into single integer
        self.data = bytearray(size * 8)  # 8 bytes per element
        for i in range(size):
            self._set_parent(i, i)
            self._set_rank(i, 0)
    
    def _get_parent(self, x):
        return int.from_bytes(self.data[x*8:x*8+4], 'little')
    
    def _get_rank(self, x):
        return int.from_bytes(self.data[x*8+4:x*8+8], 'little')
    
    def _set_parent(self, x, parent):
        self.data[x*8:x*8+4] = parent.to_bytes(4, 'little')
    
    def _set_rank(self, x, rank):
        self.data[x*8+4:x*8+8] = rank.to_bytes(4, 'little')
```

### Batch Operations
```python
class BatchDisjointSet:
    def __init__(self, size):
        self.ds = DisjointSet(size)
        self.pending = []
    
    def batch_union(self, pairs):
        # Sort pairs to improve cache locality
        pairs.sort()
        
        for x, y in pairs:
            px = self.ds.find(x)
            py = self.ds.find(y)
            
            if px != py:
                self.pending.append((px, py))
        
        # Process pending unions
        for px, py in self.pending:
            self.ds.union(px, py)
        
        self.pending.clear()
```
