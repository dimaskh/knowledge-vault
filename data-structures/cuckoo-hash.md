# Cuckoo Hash Table

Last Updated: 2025-01-14

## Overview
Cuckoo hashing is a technique for resolving hash collisions in hash tables. It uses multiple hash functions and allows moving existing items to their alternate locations.

## Properties
1. Worst-case constant lookup time
2. Uses multiple hash functions
3. Load factor typically under 50%
4. May require rehashing
5. Guaranteed no cycles in successful insertions

## Basic Implementation

### Simple Cuckoo Hash Table
```python
class CuckooHash:
    def __init__(self, initial_size=16):
        self.size = initial_size
        self.table1 = [None] * self.size
        self.table2 = [None] * self.size
        self.max_recursion = 100
    
    def _hash1(self, key):
        return hash(str(key) + "1") % self.size
    
    def _hash2(self, key):
        return hash(str(key) + "2") % self.size
    
    def insert(self, key, value, recursion_level=0):
        if recursion_level >= self.max_recursion:
            # Rehash with larger tables
            self._rehash()
            self.insert(key, value)
            return
        
        h1 = self._hash1(key)
        if self.table1[h1] is None:
            self.table1[h1] = (key, value)
            return
        
        # Swap with existing item
        old_key, old_value = self.table1[h1]
        self.table1[h1] = (key, value)
        
        h2 = self._hash2(old_key)
        if self.table2[h2] is None:
            self.table2[h2] = (old_key, old_value)
            return
        
        # Recursively move items
        next_key, next_value = self.table2[h2]
        self.table2[h2] = (old_key, old_value)
        self.insert(next_key, next_value, recursion_level + 1)
    
    def get(self, key):
        h1 = self._hash1(key)
        if self.table1[h1] and self.table1[h1][0] == key:
            return self.table1[h1][1]
        
        h2 = self._hash2(key)
        if self.table2[h2] and self.table2[h2][0] == key:
            return self.table2[h2][1]
        
        return None
    
    def _rehash(self):
        old_table1 = self.table1
        old_table2 = self.table2
        self.size *= 2
        self.table1 = [None] * self.size
        self.table2 = [None] * self.size
        
        for table in [old_table1, old_table2]:
            for item in table:
                if item:
                    self.insert(item[0], item[1])
```

## Advanced Implementation

### Multi-Hash Cuckoo Table
```python
class MultiCuckooHash:
    def __init__(self, initial_size=16, num_tables=4):
        self.size = initial_size
        self.num_tables = num_tables
        self.tables = [[None] * self.size for _ in range(num_tables)]
        self.max_recursion = 100
    
    def _hash(self, key, table_id):
        return hash(str(key) + str(table_id)) % self.size
    
    def insert(self, key, value, recursion_level=0, start_table=0):
        if recursion_level >= self.max_recursion:
            self._rehash()
            self.insert(key, value)
            return
        
        current_key, current_value = key, value
        current_table = start_table
        
        while True:
            index = self._hash(current_key, current_table)
            
            if self.tables[current_table][index] is None:
                self.tables[current_table][index] = (current_key, current_value)
                return
            
            # Swap with existing item
            current_key, current_value, self.tables[current_table][index] = \
                self.tables[current_table][index][0], \
                self.tables[current_table][index][1], \
                (current_key, current_value)
            
            current_table = (current_table + 1) % self.num_tables
            
            if current_table == start_table:
                # Back to start, need to recurse
                self.insert(current_key, current_value, 
                          recursion_level + 1, 
                          (start_table + 1) % self.num_tables)
                return
```

### Cuckoo Filter
```python
class CuckooFilter:
    def __init__(self, size, bucket_size=4):
        self.size = size
        self.bucket_size = bucket_size
        self.table = [[None] * bucket_size for _ in range(size)]
        self.max_kicks = 500
    
    def _fingerprint(self, item):
        """Generate fingerprint for item"""
        return hash(str(item)) % 256  # 8-bit fingerprint
    
    def _hash1(self, item):
        return hash(str(item) + "1") % self.size
    
    def _hash2(self, fingerprint, index):
        """Calculate alternate index based on fingerprint"""
        return (index ^ hash(str(fingerprint))) % self.size
    
    def insert(self, item):
        fingerprint = self._fingerprint(item)
        i1 = self._hash1(item)
        i2 = self._hash2(fingerprint, i1)
        
        # Try to insert in either bucket
        for index in [i1, i2]:
            for j in range(self.bucket_size):
                if self.table[index][j] is None:
                    self.table[index][j] = fingerprint
                    return True
        
        # Need to relocate existing items
        index = i1
        for _ in range(self.max_kicks):
            # Pick random slot in bucket
            slot = random.randrange(self.bucket_size)
            fingerprint, self.table[index][slot] = \
                self.table[index][slot], fingerprint
            
            index = self._hash2(fingerprint, index)
            for j in range(self.bucket_size):
                if self.table[index][j] is None:
                    self.table[index][j] = fingerprint
                    return True
        
        return False  # Insert failed
```

## Time Complexity
- Insert: O(1) amortized
- Lookup: O(1) worst case
- Delete: O(1)
- Space: O(n)

## Use Cases

1. High-Performance Dictionaries
   - Real-time applications
   - Network routing tables
   - Cache implementations

2. Set Membership
   - Bloom filter alternatives
   - Duplicate detection
   - Fast lookups

3. Security Applications
   - Password storage
   - Authentication
   - Access control

## Advanced Applications

### Load-Balanced Hash Table
```python
class LoadBalancedCuckoo:
    def __init__(self, size, threshold=0.5):
        self.cuckoo = CuckooHash(size)
        self.threshold = threshold
        self.count = 0
    
    def insert(self, key, value):
        self.count += 1
        if self.count / (2 * self.cuckoo.size) > self.threshold:
            # Preemptive rehash
            self.cuckoo._rehash()
        self.cuckoo.insert(key, value)
```

### Concurrent Cuckoo Hash
```python
from threading import Lock

class ConcurrentCuckoo:
    def __init__(self, size):
        self.cuckoo = CuckooHash(size)
        self.locks = [Lock() for _ in range(size)]
    
    def insert(self, key, value):
        h1 = self.cuckoo._hash1(key)
        h2 = self.cuckoo._hash2(key)
        
        # Acquire locks in order to prevent deadlock
        first, second = sorted([h1, h2])
        with self.locks[first], self.locks[second]:
            return self.cuckoo.insert(key, value)
```

## Best Practices

1. Implementation
   - Choose good hash functions
   - Handle rehashing properly
   - Set appropriate recursion limits

2. Performance
   - Monitor load factor
   - Use multiple tables
   - Implement early rehashing

3. Concurrency
   - Use fine-grained locking
   - Handle deadlocks
   - Consider lock-free approaches

## Common Pitfalls

1. Implementation Issues
   - Poor hash function choice
   - Infinite loops in insertion
   - Memory management

2. Performance Issues
   - High load factors
   - Frequent rehashing
   - Cache inefficiency

3. Concurrency Issues
   - Deadlocks
   - Race conditions
   - Lock contention

## Performance Optimization

### Cache-Friendly Implementation
```python
class CacheOptimizedCuckoo:
    def __init__(self, size):
        self.size = size
        # Store key-value pairs contiguously
        self.table1 = [(None, None)] * self.size
        self.table2 = [(None, None)] * self.size
    
    def insert(self, key, value):
        pair = (key, value)
        h1 = self._hash1(key)
        
        if self.table1[h1][0] is None:
            self.table1[h1] = pair
            return True
        
        # Swap and continue as before
        old_pair = self.table1[h1]
        self.table1[h1] = pair
        
        h2 = self._hash2(old_pair[0])
        if self.table2[h2][0] is None:
            self.table2[h2] = old_pair
            return True
        
        # Continue with displacement chain
```

### Batch Operations
```python
class BatchCuckoo:
    def __init__(self, size):
        self.cuckoo = CuckooHash(size)
        self.batch_size = 1000
        self.pending = []
    
    def insert_batch(self, items):
        """Insert multiple items efficiently"""
        self.pending.extend(items)
        
        if len(self.pending) >= self.batch_size:
            # Sort by hash to improve cache locality
            self.pending.sort(key=lambda x: self.cuckoo._hash1(x[0]))
            
            for key, value in self.pending:
                self.cuckoo.insert(key, value)
            
            self.pending = []
```
