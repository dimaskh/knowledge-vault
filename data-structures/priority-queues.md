# Advanced Priority Queues

Last Updated: 2025-01-14

## Overview
This document covers three advanced priority queue implementations: Fibonacci Heap, Binomial Heap, and Pairing Heap. Each has unique properties and use cases, offering different performance trade-offs.

# Fibonacci Heap

## Properties
1. Amortized O(1) for insert and decrease-key
2. O(log n) for delete-min
3. Lazy consolidation strategy
4. Supports efficient merging
5. Complex but theoretically optimal

## Implementation

### Basic Structure
```python
class FibNode:
    def __init__(self, key, value):
        self.key = key
        self.value = value
        self.degree = 0
        self.marked = False
        self.parent = None
        self.child = None
        self.left = self
        self.right = self

class FibonacciHeap:
    def __init__(self):
        self.min = None
        self.size = 0
    
    def _add_to_root_list(self, node):
        if not self.min:
            self.min = node
        else:
            node.right = self.min.right
            node.left = self.min
            self.min.right.left = node
            self.min.right = node
            if node.key < self.min.key:
                self.min = node
    
    def insert(self, key, value):
        node = FibNode(key, value)
        self._add_to_root_list(node)
        self.size += 1
        return node
    
    def extract_min(self):
        if not self.min:
            return None
        
        # Add all children to root list
        if self.min.child:
            curr = self.min.child
            while True:
                next_node = curr.right
                curr.parent = None
                self._add_to_root_list(curr)
                curr = next_node
                if curr == self.min.child:
                    break
        
        # Remove min from root list
        self.min.left.right = self.min.right
        self.min.right.left = self.min.left
        
        if self.min == self.min.right:
            self.min = None
        else:
            self.min = self.min.right
            self._consolidate()
        
        self.size -= 1
        return self.min
```

### Advanced Operations
```python
def _consolidate(self):
    """Consolidate the root list by combining trees of same degree"""
    A = [None] * int(math.log2(self.size) * 2)
    nodes = []
    curr = self.min
    
    # Collect all roots
    while curr not in nodes:
        nodes.append(curr)
        curr = curr.right
    
    for node in nodes:
        degree = node.degree
        while A[degree]:
            other = A[degree]
            
            if node.key > other.key:
                node, other = other, node
            
            self._link(other, node)
            A[degree] = None
            degree += 1
        
        A[degree] = node
    
    # Reconstruct root list
    self.min = None
    for i in range(len(A)):
        if A[i]:
            self._add_to_root_list(A[i])

def decrease_key(self, node, new_key):
    if new_key > node.key:
        raise ValueError("New key is greater than current key")
    
    node.key = new_key
    parent = node.parent
    
    if parent and node.key < parent.key:
        self._cut(node, parent)
        self._cascading_cut(parent)
    
    if node.key < self.min.key:
        self.min = node

def _cut(self, node, parent):
    """Cut node from its parent and add to root list"""
    parent.degree -= 1
    if parent.child == node:
        parent.child = node.right
    if parent.degree == 0:
        parent.child = None
    
    node.left.right = node.right
    node.right.left = node.left
    
    self._add_to_root_list(node)
    node.parent = None
    node.marked = False

def _cascading_cut(self, node):
    """Perform cascading cut operation"""
    parent = node.parent
    if parent:
        if not node.marked:
            node.marked = True
        else:
            self._cut(node, parent)
            self._cascading_cut(parent)
```

# Binomial Heap

## Properties
1. Collection of binomial trees
2. O(log n) for most operations
3. Simpler implementation than Fibonacci heap
4. Efficient merging operation
5. Deterministic performance

## Implementation

### Basic Structure
```python
class BinomialNode:
    def __init__(self, key, value):
        self.key = key
        self.value = value
        self.degree = 0
        self.parent = None
        self.child = None
        self.sibling = None

class BinomialHeap:
    def __init__(self):
        self.head = None
    
    def _merge_roots(self, h1, h2):
        if not h1:
            return h2
        if not h2:
            return h1
        
        if h1.degree <= h2.degree:
            result = h1
            result.sibling = self._merge_roots(h1.sibling, h2)
        else:
            result = h2
            result.sibling = self._merge_roots(h1, h2.sibling)
        
        return result
    
    def _link(self, y, z):
        """Make node y a child of node z"""
        y.parent = z
        y.sibling = z.child
        z.child = y
        z.degree += 1
```

### Core Operations
```python
def insert(self, key, value):
    node = BinomialNode(key, value)
    new_heap = BinomialHeap()
    new_heap.head = node
    self.union(new_heap)

def union(self, other):
    self.head = self._merge(self.head, other.head)

def _merge(self, h1, h2):
    """Merge two binomial heaps"""
    current = self._merge_roots(h1, h2)
    if not current:
        return None
    
    prev = None
    x = current
    next_x = x.sibling
    
    while next_x:
        if (x.degree != next_x.degree or 
            (next_x.sibling and 
             next_x.sibling.degree == x.degree)):
            prev = x
            x = next_x
        elif x.key <= next_x.key:
            x.sibling = next_x.sibling
            self._link(next_x, x)
        else:
            if not prev:
                current = next_x
            else:
                prev.sibling = next_x
            self._link(x, next_x)
            x = next_x
        next_x = x.sibling
    
    return current

def extract_min(self):
    if not self.head:
        return None
    
    min_node = self.head
    min_prev = None
    current = self.head.sibling
    prev = self.head
    
    # Find minimum
    while current:
        if current.key < min_node.key:
            min_node = current
            min_prev = prev
        prev = current
        current = current.sibling
    
    # Remove minimum node
    if min_prev:
        min_prev.sibling = min_node.sibling
    else:
        self.head = min_node.sibling
    
    # Create heap from children
    if min_node.child:
        child_heap = BinomialHeap()
        current = min_node.child
        while current:
            next_node = current.sibling
            current.sibling = child_heap.head
            current.parent = None
            child_heap.head = current
            current = next_node
        
        self.union(child_heap)
    
    return min_node
```

# Pairing Heap

## Properties
1. Simple implementation
2. Good practical performance
3. O(1) amortized for merge
4. O(log n) amortized for delete-min
5. Supports decrease-key

## Implementation

### Basic Structure
```python
class PairingNode:
    def __init__(self, key, value):
        self.key = key
        self.value = value
        self.child = None
        self.sibling = None
        self.prev = None

class PairingHeap:
    def __init__(self):
        self.root = None
    
    def _link(self, first, second):
        """Link two nodes, making the larger key a child of the smaller"""
        if first.key <= second.key:
            second.sibling = first.child
            if first.child:
                first.child.prev = second
            second.prev = first
            first.child = second
            return first
        else:
            first.sibling = second.child
            if second.child:
                second.child.prev = first
            first.prev = second
            second.child = first
            return second
```

### Core Operations
```python
def insert(self, key, value):
    node = PairingNode(key, value)
    self.root = self._merge(self.root, node)
    return node

def _merge(self, first, second):
    if not first:
        return second
    if not second:
        return first
    return self._link(first, second)

def extract_min(self):
    if not self.root:
        return None
    
    old_root = self.root
    self.root = self._merge_pairs(self.root.child)
    return old_root

def _merge_pairs(self, first):
    """Merge pairs of nodes from left to right"""
    if not first:
        return None
    if not first.sibling:
        return first
    
    second = first.sibling
    next_pair = second.sibling
    
    # Merge pair
    merged = self._link(first, second)
    merged.prev = None
    
    # Recursively merge rest
    merged_rest = self._merge_pairs(next_pair)
    
    # Merge results
    return self._merge(merged, merged_rest)

def decrease_key(self, node, new_key):
    if new_key > node.key:
        raise ValueError("New key is greater than current key")
    
    node.key = new_key
    if node == self.root:
        return
    
    # Cut node from its location
    if node.prev.child == node:
        node.prev.child = node.sibling
    else:
        node.prev.sibling = node.sibling
    
    if node.sibling:
        node.sibling.prev = node.prev
    
    node.sibling = None
    node.prev = None
    
    # Merge with root
    self.root = self._merge(self.root, node)
```

## Time Complexity Comparison

| Operation     | Fibonacci Heap | Binomial Heap | Pairing Heap |
|--------------|----------------|---------------|--------------|
| Insert       | O(1)           | O(log n)      | O(1)         |
| Extract-Min  | O(log n)       | O(log n)      | O(log n)*    |
| Decrease-Key | O(1)           | O(log n)      | O(log n)*    |
| Merge        | O(1)           | O(log n)      | O(1)         |

*Amortized time complexity

## Use Cases

1. Graph Algorithms
   - Dijkstra's shortest path
   - Prim's minimum spanning tree
   - Network flow algorithms

2. Event Processing
   - Event scheduling
   - Task prioritization
   - Real-time systems

3. Operating Systems
   - Process scheduling
   - Memory management
   - I/O request handling

## Best Practices

1. Implementation
   - Choose based on operation frequency
   - Consider memory overhead
   - Handle edge cases

2. Performance
   - Monitor amortized costs
   - Profile real-world usage
   - Optimize critical operations

3. Maintenance
   - Keep structure balanced
   - Handle memory carefully
   - Document invariants

## Common Pitfalls

1. Implementation Issues
   - Incorrect linking
   - Memory leaks
   - Broken heap properties

2. Performance Issues
   - Wrong heap choice
   - Unnecessary operations
   - Poor memory locality

3. Usage Issues
   - Incorrect decrease-key
   - Memory fragmentation
   - Race conditions

## Advanced Applications

### Multi-threaded Priority Queue
```python
from threading import Lock

class ThreadSafeFibonacciHeap:
    def __init__(self):
        self.heap = FibonacciHeap()
        self.lock = Lock()
    
    def insert(self, key, value):
        with self.lock:
            return self.heap.insert(key, value)
    
    def extract_min(self):
        with self.lock:
            return self.heap.extract_min()
    
    def decrease_key(self, node, new_key):
        with self.lock:
            return self.heap.decrease_key(node, new_key)
```

### Persistent Priority Queue
```python
class PersistentPairingHeap:
    def __init__(self):
        self.versions = [PairingHeap()]
        self.current = 0
    
    def insert(self, key, value):
        new_heap = copy.deepcopy(self.versions[self.current])
        new_heap.insert(key, value)
        self.versions.append(new_heap)
        self.current += 1
        return self.current
    
    def extract_min(self):
        if not self.versions[self.current].root:
            return None
        
        new_heap = copy.deepcopy(self.versions[self.current])
        min_node = new_heap.extract_min()
        self.versions.append(new_heap)
        self.current += 1
        return min_node, self.current
```
