# Skip List

Last Updated: 2025-01-14

## Overview
A Skip List is a probabilistic data structure that allows for faster search within an ordered sequence of elements. It uses a hierarchy of linked lists that connect increasingly sparse subsequences of the elements.

## Properties
1. Probabilistic balanced structure
2. O(log n) average case operations
3. Multiple layers of linked lists
4. Each level skips exponentially more elements
5. Simple implementation compared to balanced trees

## Basic Implementation

### Node Structure
```python
class Node:
    def __init__(self, key, value, level):
        self.key = key
        self.value = value
        # Array of forward pointers
        self.forward = [None] * (level + 1)
        self.level = level

class SkipList:
    def __init__(self, max_level=16, p=0.5):
        self.max_level = max_level
        self.p = p
        self.level = 0
        # Header node with maximum level
        self.header = Node(float('-inf'), None, max_level)
    
    def _random_level(self):
        """Generate random level for node"""
        level = 0
        while random.random() < self.p and level < self.max_level:
            level += 1
        return level
```

### Search Operation
```python
def search(self, key):
    current = self.header
    
    # Start from highest level and move down
    for i in range(self.level, -1, -1):
        while (current.forward[i] and 
               current.forward[i].key < key):
            current = current.forward[i]
    
    # Reached level 0 and next node
    current = current.forward[0]
    
    if current and current.key == key:
        return current.value
    return None
```

### Insert Operation
```python
def insert(self, key, value):
    # Track nodes to update at each level
    update = [None] * (self.max_level + 1)
    current = self.header
    
    # Find all nodes that need updating
    for i in range(self.level, -1, -1):
        while (current.forward[i] and 
               current.forward[i].key < key):
            current = current.forward[i]
        update[i] = current
    
    # Move to next node
    current = current.forward[0]
    
    # If key exists, update value
    if current and current.key == key:
        current.value = value
        return
    
    # Generate random level for new node
    new_level = self._random_level()
    
    # If new level is higher, update header links
    if new_level > self.level:
        for i in range(self.level + 1, new_level + 1):
            update[i] = self.header
        self.level = new_level
    
    # Create new node
    new_node = Node(key, value, new_level)
    
    # Insert node by updating references
    for i in range(new_level + 1):
        new_node.forward[i] = update[i].forward[i]
        update[i].forward[i] = new_node
```

### Delete Operation
```python
def delete(self, key):
    update = [None] * (self.max_level + 1)
    current = self.header
    
    # Find all nodes that need updating
    for i in range(self.level, -1, -1):
        while (current.forward[i] and 
               current.forward[i].key < key):
            current = current.forward[i]
        update[i] = current
    
    current = current.forward[0]
    
    # If key exists, remove it
    if current and current.key == key:
        # Start from lowest level and remove references
        for i in range(self.level + 1):
            if update[i].forward[i] != current:
                break
            update[i].forward[i] = current.forward[i]
        
        # Update level of list
        while self.level > 0 and not self.header.forward[self.level]:
            self.level -= 1
        return True
    
    return False
```

## Advanced Features

### Range Query
```python
def range_query(self, start_key, end_key):
    result = []
    current = self.header
    
    # Find start key
    for i in range(self.level, -1, -1):
        while (current.forward[i] and 
               current.forward[i].key < start_key):
            current = current.forward[i]
    
    current = current.forward[0]
    
    # Collect all keys in range
    while current and current.key <= end_key:
        result.append((current.key, current.value))
        current = current.forward[0]
    
    return result
```

### Indexed Skip List
```python
class IndexedNode(Node):
    def __init__(self, key, value, level):
        super().__init__(key, value, level)
        self.width = [1] * (level + 1)

class IndexedSkipList(SkipList):
    def __init__(self, max_level=16, p=0.5):
        super().__init__(max_level, p)
        self.header = IndexedNode(float('-inf'), None, max_level)
    
    def get_rank(self, key):
        rank = 0
        current = self.header
        
        for i in range(self.level, -1, -1):
            while (current.forward[i] and 
                   current.forward[i].key < key):
                rank += current.width[i]
                current = current.forward[i]
        
        current = current.forward[0]
        
        if current and current.key == key:
            return rank + 1
        return 0
    
    def get_by_rank(self, rank):
        if rank <= 0:
            return None
        
        current = self.header
        position = 0
        
        for i in range(self.level, -1, -1):
            while (current.forward[i] and 
                   position + current.width[i] <= rank):
                position += current.width[i]
                current = current.forward[i]
        
        if position == rank:
            return current.value
        return None
```

## Time Complexity
- Search: O(log n) average case
- Insert: O(log n) average case
- Delete: O(log n) average case
- Space: O(n)

## Use Cases

1. In-Memory Databases
   - Ordered data storage
   - Range queries
   - Fast lookups

2. File Systems
   - Index structures
   - Directory organization
   - Fast file lookup

3. Caching Systems
   - LRU cache implementation
   - Priority queues
   - Sorted cache entries

## Best Practices

1. Implementation
   - Choose appropriate max level
   - Set probability factor carefully
   - Handle edge cases

2. Optimization
   - Use array-based implementation
   - Implement memory pooling
   - Consider cache efficiency

3. Usage
   - Monitor distribution
   - Adjust parameters dynamically
   - Consider alternatives

## Common Pitfalls

1. Implementation Issues
   - Poor random level generation
   - Memory management
   - Reference handling

2. Performance Issues
   - Too many/few levels
   - Unbalanced structure
   - Memory fragmentation

3. Usage Issues
   - Wrong probability factor
   - Inappropriate max level
   - Not considering alternatives

## Advanced Implementations

### Lock-Free Skip List
```python
from threading import atomic

class AtomicNode(Node):
    def __init__(self, key, value, level):
        super().__init__(key, value, level)
        self.marked = atomic.AtomicBoolean(False)
        self.forward = [atomic.AtomicReference(None) 
                       for _ in range(level + 1)]

class LockFreeSkipList:
    def __init__(self, max_level=16):
        self.header = AtomicNode(float('-inf'), None, max_level)
        self.tail = AtomicNode(float('inf'), None, max_level)
        for i in range(max_level + 1):
            self.header.forward[i].set(self.tail)
    
    def find(self, key, preds, succs):
        retry = True
        while retry:
            retry = False
            pred = self.header
            
            for level in range(self.max_level, -1, -1):
                curr = pred.forward[level].get()
                while True:
                    succ = curr.forward[level].get()
                    while curr.marked.get():
                        if not pred.forward[level].compare_and_set(
                            curr, succ, False, False):
                            retry = True
                            break
                        curr = succ
                        succ = curr.forward[level].get()
                    
                    if curr.key < key:
                        pred = curr
                        curr = succ
                    else:
                        break
                
                if retry:
                    break
                
                preds[level] = pred
                succs[level] = curr
        
        return preds, succs
```

### Memory-Efficient Skip List
```python
class CompactSkipList:
    def __init__(self, max_level=16):
        self.max_level = max_level
        # Single array to store all nodes
        self.nodes = []
        # Index mapping for levels
        self.level_index = [[] for _ in range(max_level + 1)]
        self.header_index = -1
    
    def _create_node(self, key, value, level):
        node_index = len(self.nodes)
        self.nodes.append((key, value))
        
        for i in range(level + 1):
            self.level_index[i].append(node_index)
        
        return node_index
    
    def insert(self, key, value):
        level = self._random_level()
        node_index = self._create_node(key, value, level)
        
        for i in range(level + 1):
            # Update level indices
            insert_pos = bisect.bisect_left(
                [self.nodes[j][0] for j in self.level_index[i]], 
                key
            )
            self.level_index[i].insert(insert_pos, node_index)
```
