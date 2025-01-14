# Segment Tree

Last Updated: 2025-01-14

## Overview
A Segment Tree is a binary tree data structure used for storing information about intervals, or segments. It allows querying over an interval and modifying elements while still achieving good complexity.

## Properties
1. Complete binary tree
2. Each node represents an interval
3. Leaf nodes represent individual elements
4. Parent nodes represent union of children's intervals

## Implementation

### Basic Structure
```python
class SegmentTree:
    def __init__(self, arr):
        self.n = len(arr)
        # Height of segment tree
        x = (self.n-1).bit_length()
        # Maximum size of segment tree
        max_size = 2 * (2 ** x) - 1
        # Initialize tree with dummy values
        self.tree = [0] * max_size
        # Build the tree
        self._build_tree(arr, 0, self.n-1, 0)
    
    def _build_tree(self, arr, start, end, node):
        # If leaf node, store value
        if start == end:
            self.tree[node] = arr[start]
            return arr[start]
        
        # Recursively build left and right subtrees
        mid = (start + end) // 2
        left = self._build_tree(arr, start, mid, 2*node + 1)
        right = self._build_tree(arr, mid+1, end, 2*node + 2)
        
        # Internal node will have sum of both children
        self.tree[node] = left + right
        return self.tree[node]
```

### Range Query
```python
def query(self, start, end):
    return self._query_helper(0, self.n-1, start, end, 0)

def _query_helper(self, tree_start, tree_end, query_start, query_end, node):
    # If segment is outside range, return 0
    if query_start > tree_end or query_end < tree_start:
        return 0
    
    # If segment is completely inside range
    if query_start <= tree_start and query_end >= tree_end:
        return self.tree[node]
    
    # Partial overlap case
    mid = (tree_start + tree_end) // 2
    left = self._query_helper(tree_start, mid, 
                            query_start, query_end, 2*node + 1)
    right = self._query_helper(mid+1, tree_end, 
                             query_start, query_end, 2*node + 2)
    return left + right
```

### Update Operation
```python
def update(self, index, value):
    self._update_helper(0, self.n-1, index, value, 0)

def _update_helper(self, tree_start, tree_end, index, value, node):
    # Base case: leaf node
    if tree_start == tree_end:
        self.tree[node] = value
        return
    
    mid = (tree_start + tree_end) // 2
    
    if index <= mid:
        self._update_helper(tree_start, mid, index, value, 2*node + 1)
    else:
        self._update_helper(mid+1, tree_end, index, value, 2*node + 2)
    
    # Update internal nodes
    self.tree[node] = self.tree[2*node + 1] + self.tree[2*node + 2]
```

## Variations

### Lazy Propagation
```python
class LazySegmentTree:
    def __init__(self, arr):
        self.n = len(arr)
        x = (self.n-1).bit_length()
        max_size = 2 * (2 ** x) - 1
        self.tree = [0] * max_size
        self.lazy = [0] * max_size
        self._build_tree(arr, 0, self.n-1, 0)
    
    def range_update(self, start, end, value):
        self._update_range_helper(0, self.n-1, start, end, value, 0)
    
    def _update_range_helper(self, tree_start, tree_end, 
                           update_start, update_end, value, node):
        # If lazy value exists, update current node
        if self.lazy[node] != 0:
            self.tree[node] += (tree_end - tree_start + 1) * self.lazy[node]
            
            # If not leaf, propagate lazy value
            if tree_start != tree_end:
                self.lazy[2*node + 1] += self.lazy[node]
                self.lazy[2*node + 2] += self.lazy[node]
            
            self.lazy[node] = 0
        
        # No overlap
        if update_start > tree_end or update_end < tree_start:
            return
        
        # Complete overlap
        if update_start <= tree_start and update_end >= tree_end:
            self.tree[node] += (tree_end - tree_start + 1) * value
            
            if tree_start != tree_end:
                self.lazy[2*node + 1] += value
                self.lazy[2*node + 2] += value
            return
        
        # Partial overlap
        mid = (tree_start + tree_end) // 2
        self._update_range_helper(tree_start, mid, 
                                update_start, update_end, value, 2*node + 1)
        self._update_range_helper(mid+1, tree_end, 
                                update_start, update_end, value, 2*node + 2)
        
        self.tree[node] = self.tree[2*node + 1] + self.tree[2*node + 2]
```

## Time Complexity
- Build: O(n)
- Query: O(log n)
- Update: O(log n)
- Space: O(n)

With Lazy Propagation:
- Range Update: O(log n)
- Query with propagation: O(log n)

## Use Cases

1. Range Query Problems
   - Sum of range
   - Minimum/Maximum in range
   - Count of elements in range

2. Computational Geometry
   - Line segment intersection
   - Rectangle overlap queries
   - Range searching

3. Database Applications
   - Range queries
   - Aggregation operations
   - Temporal data management

## Advanced Applications

### Range Minimum Query (RMQ)
```python
class RMQSegmentTree:
    def __init__(self, arr):
        self.n = len(arr)
        x = (self.n-1).bit_length()
        max_size = 2 * (2 ** x) - 1
        self.tree = [float('inf')] * max_size
        self._build_tree(arr, 0, self.n-1, 0)
    
    def _build_tree(self, arr, start, end, node):
        if start == end:
            self.tree[node] = arr[start]
            return arr[start]
        
        mid = (start + end) // 2
        left = self._build_tree(arr, start, mid, 2*node + 1)
        right = self._build_tree(arr, mid+1, end, 2*node + 2)
        self.tree[node] = min(left, right)
        return self.tree[node]
```

### Persistent Segment Tree
```python
class Node:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class PersistentSegmentTree:
    def __init__(self, arr):
        self.roots = []
        self.n = len(arr)
        self.roots.append(self._build_tree(arr, 0, self.n-1))
    
    def _build_tree(self, arr, start, end):
        if start == end:
            return Node(arr[start])
        
        mid = (start + end) // 2
        left = self._build_tree(arr, start, mid)
        right = self._build_tree(arr, mid+1, end)
        node = Node(left.val + right.val, left, right)
        return node
```

## Best Practices

1. Implementation
   - Use 0-based indexing
   - Handle edge cases properly
   - Implement lazy propagation when needed

2. Optimization
   - Use appropriate node structure
   - Minimize memory usage
   - Consider using arrays instead of objects

3. Usage
   - Choose correct variation for problem
   - Consider trade-offs with other structures
   - Handle updates efficiently

## Common Pitfalls

1. Implementation Issues
   - Index calculation errors
   - Incorrect propagation
   - Memory overflow

2. Performance Issues
   - Not using lazy propagation when needed
   - Inefficient memory usage
   - Poor cache utilization

3. Design Issues
   - Wrong choice of base operation
   - Incorrect interval representation
   - Not handling edge cases
