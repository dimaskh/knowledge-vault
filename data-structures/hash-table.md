# Hash Table Data Structure

Last Updated: 2025-01-14

## Definition
A hash table (hash map) is a data structure that implements an associative array abstract data type, a structure that can map keys to values using a hash function.

## Characteristics
- Key-value pairs
- Uses hash function to compute index
- Handles collisions through:
  - Chaining (linked lists)
  - Open addressing (linear/quadratic probing)

## Time Complexity
- Average Case:
  - Insert: O(1)
  - Delete: O(1)
  - Search: O(1)
- Worst Case (with collisions):
  - All operations: O(n)

## Implementation Examples

### Python
```python
# Using built-in dict
hash_table = {}
hash_table['key1'] = 'value1'
value = hash_table['key1']

# Custom implementation
class HashTable:
    def __init__(self, size=100):
        self.size = size
        self.table = [[] for _ in range(self.size)]
    
    def _hash(self, key):
        return hash(key) % self.size
    
    def insert(self, key, value):
        index = self._hash(key)
        for item in self.table[index]:
            if item[0] == key:
                item[1] = value
                return
        self.table[index].append([key, value])
    
    def get(self, key):
        index = self._hash(key)
        for item in self.table[index]:
            if item[0] == key:
                return item[1]
        raise KeyError(key)
    
    def remove(self, key):
        index = self._hash(key)
        for i, item in enumerate(self.table[index]):
            if item[0] == key:
                del self.table[index][i]
                return
        raise KeyError(key)

# Usage
ht = HashTable()
ht.insert('key1', 'value1')
value = ht.get('key1')
```

### JavaScript
```javascript
// Using built-in Map
const hashMap = new Map();
hashMap.set('key1', 'value1');
const value = hashMap.get('key1');

// Custom implementation
class HashTable {
    constructor(size = 100) {
        this.size = size;
        this.table = Array(size).fill().map(() => []);
    }
    
    _hash(key) {
        let total = 0;
        for (let i = 0; i < key.length; i++) {
            total += key.charCodeAt(i);
        }
        return total % this.size;
    }
    
    set(key, value) {
        const index = this._hash(key);
        const bucket = this.table[index];
        
        for (let i = 0; i < bucket.length; i++) {
            if (bucket[i][0] === key) {
                bucket[i][1] = value;
                return;
            }
        }
        bucket.push([key, value]);
    }
    
    get(key) {
        const index = this._hash(key);
        const bucket = this.table[index];
        
        for (let i = 0; i < bucket.length; i++) {
            if (bucket[i][0] === key) {
                return bucket[i][1];
            }
        }
        return undefined;
    }
    
    remove(key) {
        const index = this._hash(key);
        const bucket = this.table[index];
        
        for (let i = 0; i < bucket.length; i++) {
            if (bucket[i][0] === key) {
                bucket.splice(i, 1);
                return;
            }
        }
    }
}

// Usage
const ht = new HashTable();
ht.set('key1', 'value1');
const value = ht.get('key1');
```

## Common Use Cases
1. Database indexing
2. Caching
3. Symbol tables in compilers
4. Unique value filtering
5. Counting frequencies (sets)

## Advantages
- Fast access O(1) average case
- Flexible keys (strings, numbers, etc)
- Dynamic size (in most implementations)
- Good for large datasets

## Disadvantages
- Collisions can degrade performance
- No ordering of keys
- More space complexity
- Need for a good hash function
