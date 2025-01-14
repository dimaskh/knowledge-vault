# Caching Algorithms

Last Updated: 2025-01-14

## Overview
Caching algorithms determine which items to keep in cache and which to remove when the cache is full.

## MFU (Most Frequently Used) Cache

### Description
- Keeps track of how often each item is accessed
- Removes least frequently used items when cache is full
- Good for frequently accessed items

### Implementation
```python
from collections import defaultdict

class MFUCache:
    def __init__(self, capacity):
        self.capacity = capacity
        self.cache = {}
        self.freq = defaultdict(int)
        self.size = 0
    
    def get(self, key):
        if key not in self.cache:
            return -1
        
        self.freq[key] += 1
        return self.cache[key]
    
    def put(self, key, value):
        if self.capacity <= 0:
            return
        
        if key in self.cache:
            self.cache[key] = value
            self.freq[key] += 1
            return
        
        if self.size >= self.capacity:
            # Find most frequently used item
            max_freq = max(self.freq.values())
            for k in self.cache:
                if self.freq[k] == max_freq:
                    del self.cache[k]
                    del self.freq[k]
                    break
            self.size -= 1
        
        self.cache[key] = value
        self.freq[key] = 1
        self.size += 1
```

### Time Complexity
- Get: O(1)
- Put: O(n) due to finding MFU item

## LRU (Least Recently Used) Cache

### Description
- Removes least recently used items
- Maintains items in order of use
- Efficient for temporal locality

### Implementation
```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity):
        self.capacity = capacity
        self.cache = OrderedDict()
    
    def get(self, key):
        if key not in self.cache:
            return -1
        
        # Move to end (most recently used)
        self.cache.move_to_end(key)
        return self.cache[key]
    
    def put(self, key, value):
        if key in self.cache:
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.capacity:
            # Remove least recently used item
            self.cache.popitem(last=False)
```

### Time Complexity
- Get: O(1)
- Put: O(1)

## LFU (Least Frequently Used) Cache

### Description
- Tracks frequency of item usage
- Removes least frequently used items
- Good for frequency-based access patterns

### Implementation
```python
class Node:
    def __init__(self, key, value, freq=1):
        self.key = key
        self.value = value
        self.freq = freq
        self.prev = None
        self.next = None

class DLList:
    def __init__(self):
        self.head = Node(0, 0)  # sentinel
        self.tail = Node(0, 0)  # sentinel
        self.head.next = self.tail
        self.tail.prev = self.head
        self.size = 0
    
    def add(self, node):
        node.next = self.head.next
        node.prev = self.head
        self.head.next.prev = node
        self.head.next = node
        self.size += 1
    
    def remove(self, node):
        node.prev.next = node.next
        node.next.prev = node.prev
        self.size -= 1

class LFUCache:
    def __init__(self, capacity):
        self.capacity = capacity
        self.size = 0
        self.min_freq = 0
        self.cache = {}
        self.freq = defaultdict(DLList)
    
    def get(self, key):
        if key not in self.cache:
            return -1
        
        node = self.cache[key]
        self._update(node)
        return node.value
    
    def put(self, key, value):
        if self.capacity <= 0:
            return
        
        if key in self.cache:
            node = self.cache[key]
            node.value = value
            self._update(node)
            return
        
        if self.size >= self.capacity:
            # Remove LFU item
            old_list = self.freq[self.min_freq]
            del self.cache[old_list.tail.prev.key]
            old_list.remove(old_list.tail.prev)
            self.size -= 1
        
        # Add new item
        self.min_freq = 1
        node = Node(key, value)
        self.cache[key] = node
        self.freq[1].add(node)
        self.size += 1
    
    def _update(self, node):
        freq = node.freq
        self.freq[freq].remove(node)
        if freq == self.min_freq and self.freq[freq].size == 0:
            self.min_freq += 1
        
        node.freq += 1
        self.freq[node.freq].add(node)
```

### Time Complexity
- Get: O(1)
- Put: O(1)

## Best Practices

### 1. Choosing Cache Type
- LRU for temporal locality
- LFU for frequency-based patterns
- MFU for inverse frequency patterns

### 2. Implementation Considerations
- Thread safety
- Eviction policies
- Cache size

### 3. Performance Optimization
- Use efficient data structures
- Implement proper cleanup
- Consider memory constraints

## Common Applications

1. Web Browsers
   - Page caching
   - Resource caching
   - DNS caching

2. Operating Systems
   - Page cache
   - Inode cache
   - Buffer cache

3. Databases
   - Query cache
   - Result cache
   - Buffer pool

## Cache Design Patterns

### 1. Write-Through
```python
class WriteThroughCache:
    def __init__(self, storage):
        self.cache = {}
        self.storage = storage
    
    def write(self, key, value):
        # Write to both cache and storage
        self.cache[key] = value
        self.storage.write(key, value)
    
    def read(self, key):
        if key not in self.cache:
            self.cache[key] = self.storage.read(key)
        return self.cache[key]
```

### 2. Write-Back
```python
class WriteBackCache:
    def __init__(self, storage):
        self.cache = {}
        self.storage = storage
        self.dirty = set()
    
    def write(self, key, value):
        self.cache[key] = value
        self.dirty.add(key)
    
    def flush(self):
        for key in self.dirty:
            self.storage.write(key, self.cache[key])
        self.dirty.clear()
```

## Common Pitfalls

1. Cache Size
   - Too small: High miss rate
   - Too large: Memory waste
   - Not monitoring usage

2. Eviction Policy
   - Wrong policy for workload
   - Inefficient implementation
   - Not handling edge cases

3. Concurrency Issues
   - Race conditions
   - Deadlocks
   - Inconsistent state

4. Memory Leaks
   - Not cleaning references
   - Circular references
   - Improper cleanup
