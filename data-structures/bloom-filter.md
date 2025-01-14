# Bloom Filter

Last Updated: 2025-01-14

## Overview
A Bloom filter is a space-efficient probabilistic data structure used to test whether an element is a member of a set. False positives are possible, but false negatives are not.

## Properties
1. Space efficient
2. Constant time lookups
3. No false negatives
4. Cannot remove elements
5. Tunable false positive rate

## Basic Implementation

### Simple Bloom Filter
```python
import math
from bitarray import bitarray
import mmh3  # MurmurHash3 for efficient hashing

class BloomFilter:
    def __init__(self, size, num_hash_functions):
        self.size = size
        self.num_hash_functions = num_hash_functions
        self.bit_array = bitarray(size)
        self.bit_array.setall(0)
    
    def add(self, item):
        for seed in range(self.num_hash_functions):
            index = mmh3.hash(str(item), seed) % self.size
            self.bit_array[index] = 1
    
    def contains(self, item):
        for seed in range(self.num_hash_functions):
            index = mmh3.hash(str(item), seed) % self.size
            if not self.bit_array[index]:
                return False
        return True
    
    @classmethod
    def get_size(cls, n, p):
        """Calculate size of bit array, n is number of items, p is false positive probability"""
        m = -(n * math.log(p)) / (math.log(2) ** 2)
        return int(m)
    
    @classmethod
    def get_hash_count(cls, m, n):
        """Calculate optimal number of hash functions, m is size, n is number of items"""
        k = (m / n) * math.log(2)
        return int(k)
```

## Advanced Implementation

### Counting Bloom Filter
```python
class CountingBloomFilter:
    def __init__(self, size, num_hash_functions):
        self.size = size
        self.num_hash_functions = num_hash_functions
        self.counters = [0] * size
    
    def add(self, item):
        for seed in range(self.num_hash_functions):
            index = mmh3.hash(str(item), seed) % self.size
            self.counters[index] += 1
    
    def remove(self, item):
        """Remove item (may affect other items)"""
        for seed in range(self.num_hash_functions):
            index = mmh3.hash(str(item), seed) % self.size
            if self.counters[index] > 0:
                self.counters[index] -= 1
    
    def contains(self, item):
        for seed in range(self.num_hash_functions):
            index = mmh3.hash(str(item), seed) % self.size
            if self.counters[index] == 0:
                return False
        return True
```

### Scalable Bloom Filter
```python
class ScalableBloomFilter:
    def __init__(self, initial_size, error_rate, growth_factor=2):
        self.initial_size = initial_size
        self.error_rate = error_rate
        self.growth_factor = growth_factor
        self.filters = []
        self.add_filter()
    
    def add_filter(self):
        size = self.initial_size * (self.growth_factor ** len(self.filters))
        error = self.error_rate / (len(self.filters) + 1)
        num_hash = BloomFilter.get_hash_count(size, int(size * math.log(1/error)))
        self.filters.append(BloomFilter(size, num_hash))
    
    def add(self, item):
        if not self.contains(item):
            if self.filters[-1].bit_array.count() / self.filters[-1].size > 0.5:
                self.add_filter()
            self.filters[-1].add(item)
    
    def contains(self, item):
        return any(f.contains(item) for f in self.filters)
```

## Partitioned Bloom Filter
```python
class PartitionedBloomFilter:
    def __init__(self, size, num_hash_functions):
        self.size = size
        self.num_hash_functions = num_hash_functions
        self.partition_size = size // num_hash_functions
        self.bit_arrays = [bitarray(self.partition_size) for _ in range(num_hash_functions)]
        for ba in self.bit_arrays:
            ba.setall(0)
    
    def add(self, item):
        for i in range(self.num_hash_functions):
            index = mmh3.hash(str(item), i) % self.partition_size
            self.bit_arrays[i][index] = 1
    
    def contains(self, item):
        for i in range(self.num_hash_functions):
            index = mmh3.hash(str(item), i) % self.partition_size
            if not self.bit_arrays[i][index]:
                return False
        return True
```

## Time Complexity
- Add: O(k) where k is number of hash functions
- Query: O(k)
- Space: O(m) where m is size of bit array

## Use Cases

1. Database Systems
   - Query optimization
   - Cache filtering
   - Avoiding disk lookups

2. Web Crawlers
   - URL deduplication
   - Link checking
   - Content filtering

3. Network Applications
   - Packet routing
   - Content delivery
   - Cache management

4. Security
   - Malware detection
   - Password checking
   - Spam filtering

## Advanced Applications

### Cache Filter
```python
class BloomCache:
    def __init__(self, size, num_hash_functions, cache_size):
        self.bloom = BloomFilter(size, num_hash_functions)
        self.cache = {}
        self.cache_size = cache_size
    
    def get(self, key):
        if not self.bloom.contains(key):
            return None  # Definitely not in cache
        return self.cache.get(key)  # May be in cache
    
    def put(self, key, value):
        self.bloom.add(key)
        if len(self.cache) >= self.cache_size:
            # Evict random item
            self.cache.pop(next(iter(self.cache)))
        self.cache[key] = value
```

### Distributed Bloom Filter
```python
class DistributedBloomFilter:
    def __init__(self, num_nodes, size_per_node, num_hash_functions):
        self.num_nodes = num_nodes
        self.size_per_node = size_per_node
        self.num_hash_functions = num_hash_functions
        self.filters = [BloomFilter(size_per_node, num_hash_functions) 
                       for _ in range(num_nodes)]
    
    def add(self, item):
        node = hash(str(item)) % self.num_nodes
        self.filters[node].add(item)
    
    def contains(self, item):
        node = hash(str(item)) % self.num_nodes
        return self.filters[node].contains(item)
```

## Best Practices

1. Sizing
   - Calculate optimal size based on expected items
   - Consider false positive rate requirements
   - Account for growth

2. Hash Functions
   - Use fast, high-quality hash functions
   - Choose appropriate number of functions
   - Consider distribution quality

3. Implementation
   - Use bit-level operations
   - Consider memory alignment
   - Implement thread safety if needed

## Common Pitfalls

1. Design Issues
   - Wrong size estimation
   - Poor hash function choice
   - Not considering growth

2. Implementation Issues
   - Thread safety problems
   - Memory management
   - Integer overflow

3. Usage Issues
   - Removing elements (not supported)
   - Ignoring false positives
   - Not monitoring fill ratio

## Performance Optimization

### Bit Array Implementation
```python
class OptimizedBitArray:
    def __init__(self, size):
        self.size = size
        self.data = bytearray((size + 7) // 8)
    
    def set_bit(self, index):
        byte_index = index // 8
        bit_index = index % 8
        self.data[byte_index] |= (1 << bit_index)
    
    def get_bit(self, index):
        byte_index = index // 8
        bit_index = index % 8
        return bool(self.data[byte_index] & (1 << bit_index))
```

### Parallel Implementation
```python
from concurrent.futures import ThreadPoolExecutor

class ParallelBloomFilter:
    def __init__(self, size, num_hash_functions, num_threads=4):
        self.bloom = BloomFilter(size, num_hash_functions)
        self.num_threads = num_threads
        self.executor = ThreadPoolExecutor(max_workers=num_threads)
    
    def add_batch(self, items):
        chunk_size = len(items) // self.num_threads
        futures = []
        
        for i in range(0, len(items), chunk_size):
            chunk = items[i:i + chunk_size]
            futures.append(
                self.executor.submit(self._add_chunk, chunk)
            )
        
        for future in futures:
            future.result()
    
    def _add_chunk(self, items):
        for item in items:
            self.bloom.add(item)
```
