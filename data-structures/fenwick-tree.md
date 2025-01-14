# Fenwick Tree (Binary Indexed Tree)

Last Updated: 2025-01-14

## Overview
A Fenwick Tree or Binary Indexed Tree is a data structure that provides efficient methods for calculating and updating prefix sums in an array.

## Properties
1. Uses binary representation properties
2. Stores partial sums in an array
3. Each index responsible for specific range
4. Space-efficient compared to segment trees

## Basic Implementation

### Core Operations
```python
class FenwickTree:
    def __init__(self, n):
        self.size = n
        self.tree = [0] * (n + 1)  # 1-based indexing
    
    def update(self, index, delta):
        """Add delta to index i"""
        index += 1  # Convert to 1-based
        while index <= self.size:
            self.tree[index] += delta
            index += index & (-index)  # Get next index
    
    def prefix_sum(self, index):
        """Get sum from index 1 to index"""
        index += 1  # Convert to 1-based
        total = 0
        while index > 0:
            total += self.tree[index]
            index -= index & (-index)  # Get parent
        return total
    
    def range_sum(self, left, right):
        """Get sum from index left to right (inclusive)"""
        return self.prefix_sum(right) - self.prefix_sum(left - 1)
```

### Construction from Array
```python
def build_from_array(arr):
    n = len(arr)
    fenwick = FenwickTree(n)
    for i in range(n):
        fenwick.update(i, arr[i])
    return fenwick
```

## Advanced Operations

### Range Update Point Query
```python
class RangeUpdateFenwickTree:
    def __init__(self, n):
        self.size = n
        self.tree1 = [0] * (n + 1)  # Store b[i] * i
        self.tree2 = [0] * (n + 1)  # Store b[i]
    
    def _update(self, tree, index, val):
        while index <= self.size:
            tree[index] += val
            index += index & (-index)
    
    def _query(self, tree, index):
        total = 0
        while index > 0:
            total += tree[index]
            index -= index & (-index)
        return total
    
    def range_update(self, left, right, val):
        """Add val to range [left, right]"""
        self._update(self.tree1, left + 1, val * (left + 1))
        self._update(self.tree1, right + 2, -val * (right + 2))
        self._update(self.tree2, left + 1, val)
        self._update(self.tree2, right + 2, -val)
    
    def point_query(self, index):
        """Get value at index"""
        return self._query(self.tree1, index + 1) - self._query(self.tree2, index + 1) * index
```

### Range Update Range Query
```python
class RangeUpdateRangeQueryFenwickTree:
    def __init__(self, n):
        self.size = n
        self.tree = [FenwickTree(n) for _ in range(2)]
    
    def range_update(self, left, right, val):
        """Add val to range [left, right]"""
        self.tree[0].update(left, val)
        self.tree[0].update(right + 1, -val)
        self.tree[1].update(left, val * (left - 1))
        self.tree[1].update(right + 1, -val * right)
    
    def range_query(self, left, right):
        """Get sum of range [left, right]"""
        def prefix_sum(index):
            if index < 0:
                return 0
            return self.tree[0].prefix_sum(index) * index - self.tree[1].prefix_sum(index)
        
        return prefix_sum(right) - prefix_sum(left - 1)
```

## 2D Fenwick Tree

### Implementation
```python
class FenwickTree2D:
    def __init__(self, n, m):
        self.n = n
        self.m = m
        self.tree = [[0] * (m + 1) for _ in range(n + 1)]
    
    def update(self, x, y, delta):
        x += 1  # Convert to 1-based
        y += 1
        i = x
        while i <= self.n:
            j = y
            while j <= self.m:
                self.tree[i][j] += delta
                j += j & (-j)
            i += i & (-i)
    
    def prefix_sum(self, x, y):
        x += 1  # Convert to 1-based
        y += 1
        total = 0
        i = x
        while i > 0:
            j = y
            while j > 0:
                total += self.tree[i][j]
                j -= j & (-j)
            i -= i & (-i)
        return total
    
    def range_sum(self, x1, y1, x2, y2):
        return (self.prefix_sum(x2, y2) - 
                self.prefix_sum(x2, y1 - 1) - 
                self.prefix_sum(x1 - 1, y2) + 
                self.prefix_sum(x1 - 1, y1 - 1))
```

## Time Complexity
- Update: O(log n)
- Prefix Sum: O(log n)
- Range Sum: O(log n)
- Space: O(n)

For 2D:
- Update: O(log n * log m)
- Query: O(log n * log m)
- Space: O(n * m)

## Use Cases

1. Range Query Problems
   - Cumulative frequency
   - Range sum queries
   - Dynamic prefix sums

2. Computational Geometry
   - Rectangle area queries
   - Point updates in 2D
   - Range counting

3. Statistics
   - Moving averages
   - Running medians
   - Order statistics

## Advanced Applications

### Order Statistics
```python
class OrderStatisticTree:
    def __init__(self, max_val):
        self.tree = FenwickTree(max_val)
    
    def insert(self, val):
        self.tree.update(val, 1)
    
    def delete(self, val):
        self.tree.update(val, -1)
    
    def kth_smallest(self, k):
        left, right = 0, self.tree.size - 1
        while left < right:
            mid = (left + right) // 2
            rank = self.tree.prefix_sum(mid)
            if rank < k:
                left = mid + 1
            else:
                right = mid
        return left
```

### Range Mode Query
```python
class RangeModeQuery:
    def __init__(self, arr):
        self.arr = arr
        self.n = len(arr)
        self.block_size = int(math.sqrt(self.n))
        self.blocks = [[0] * (max(arr) + 1) for _ in range((self.n + self.block_size - 1) // self.block_size)]
        
        for i in range(self.n):
            self.blocks[i // self.block_size][arr[i]] += 1
    
    def query(self, left, right):
        start_block = (left + self.block_size - 1) // self.block_size
        end_block = right // self.block_size
        
        if start_block >= end_block:
            return self._naive_query(left, right)
        
        # Count frequencies in partial blocks
        freq = [0] * (max(self.arr) + 1)
        for i in range(left, start_block * self.block_size):
            freq[self.arr[i]] += 1
        
        for i in range(end_block * self.block_size, right + 1):
            freq[self.arr[i]] += 1
        
        # Add frequencies from complete blocks
        for i in range(start_block, end_block):
            for j in range(len(freq)):
                freq[j] += self.blocks[i][j]
        
        # Find mode
        mode = 0
        max_freq = 0
        for i in range(len(freq)):
            if freq[i] > max_freq:
                max_freq = freq[i]
                mode = i
        
        return mode
```

## Best Practices

1. Implementation
   - Use 1-based indexing internally
   - Handle edge cases properly
   - Choose appropriate tree variant

2. Optimization
   - Use bit manipulation
   - Cache frequent queries
   - Consider space-time trade-offs

3. Usage
   - Preprocess static data
   - Batch updates when possible
   - Consider alternative structures

## Common Pitfalls

1. Implementation Issues
   - Off-by-one errors
   - Incorrect index handling
   - Overflow in large sums

2. Performance Issues
   - Not using appropriate variant
   - Unnecessary updates
   - Poor cache utilization

3. Design Issues
   - Wrong problem modeling
   - Inefficient range handling
   - Missing optimization opportunities
