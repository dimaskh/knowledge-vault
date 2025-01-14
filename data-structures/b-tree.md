# B-Tree and B+ Tree

Last Updated: 2025-01-14

## Overview
B-Trees are self-balancing tree data structures that maintain sorted data and allow searches, sequential access, insertions, and deletions in logarithmic time. B+ Trees are a variation that stores all data in leaf nodes.

## B-Tree Properties
1. All leaves are at the same level
2. A node can have a maximum of m children (order m)
3. Each node except root must have at least m/2 children
4. Root must have at least 2 children if not a leaf
5. A non-leaf node with k children must have k-1 keys

## Node Structure

### B-Tree Node
```python
class BTreeNode:
    def __init__(self, leaf=False):
        self.leaf = leaf
        self.keys = []
        self.children = []
```

### B+ Tree Node
```python
class BPlusNode:
    def __init__(self, leaf=False):
        self.leaf = leaf
        self.keys = []
        self.children = []
        self.next = None  # For leaf nodes
```

## B-Tree Operations

### Search
```python
def search(node, key):
    i = 0
    while i < len(node.keys) and key > node.keys[i]:
        i += 1
    
    if i < len(node.keys) and key == node.keys[i]:
        return (node, i)
    
    if node.leaf:
        return None
    
    return search(node.children[i], key)
```

### Insert
```python
def insert(tree, key):
    root = tree.root
    if len(root.keys) == (2 * tree.t) - 1:
        # Create new root
        new_root = BTreeNode()
        tree.root = new_root
        new_root.children.append(root)
        split_child(new_root, 0)
        insert_non_full(new_root, key)
    else:
        insert_non_full(root, key)

def insert_non_full(node, key):
    i = len(node.keys) - 1
    
    if node.leaf:
        # Insert key in leaf node
        while i >= 0 and key < node.keys[i]:
            i -= 1
        node.keys.insert(i + 1, key)
    else:
        # Recursively insert in appropriate child
        while i >= 0 and key < node.keys[i]:
            i -= 1
        i += 1
        
        if len(node.children[i].keys) == (2 * t) - 1:
            split_child(node, i)
            if key > node.keys[i]:
                i += 1
        
        insert_non_full(node.children[i], key)

def split_child(parent, i):
    t = parent.t
    y = parent.children[i]
    z = BTreeNode(leaf=y.leaf)
    
    parent.keys.insert(i, y.keys[t-1])
    parent.children.insert(i + 1, z)
    
    z.keys = y.keys[t:]
    y.keys = y.keys[:t-1]
    
    if not y.leaf:
        z.children = y.children[t:]
        y.children = y.children[:t]
```

## B+ Tree Operations

### Search
```python
def search(node, key):
    if node.leaf:
        for i, k in enumerate(node.keys):
            if k == key:
                return (node, i)
        return None
    
    i = 0
    while i < len(node.keys) and key > node.keys[i]:
        i += 1
    
    return search(node.children[i], key)
```

### Range Search
```python
def range_search(node, start_key, end_key):
    result = []
    
    # Find leaf node containing start_key
    current = node
    while not current.leaf:
        i = 0
        while i < len(current.keys) and start_key > current.keys[i]:
            i += 1
        current = current.children[i]
    
    # Collect all keys in range using leaf node links
    while current:
        for key in current.keys:
            if start_key <= key <= end_key:
                result.append(key)
            elif key > end_key:
                return result
        current = current.next
    
    return result
```

## Time Complexity

### B-Tree
- Search: O(log n)
- Insert: O(log n)
- Delete: O(log n)
- Space: O(n)

### B+ Tree
- Search: O(log n)
- Range Search: O(log n + k) where k is number of elements in range
- Insert: O(log n)
- Delete: O(log n)
- Space: O(n)

## Use Cases

1. Database Indexing
   - B+ Trees are widely used
   - Efficient range queries
   - Good for disk-based storage

2. File Systems
   - Directory structures
   - File allocation tables
   - Large file indexing

3. Search Engines
   - Inverted indexes
   - Document storage
   - Query optimization

## Comparison: B-Tree vs B+ Tree

### B-Tree
#### Advantages
1. Better for single key lookups
2. Less space for sparse indexes
3. Good for in-memory databases

#### Disadvantages
1. Range queries are slower
2. More complex rebalancing
3. Not optimal for sequential access

### B+ Tree
#### Advantages
1. Efficient range queries
2. Simpler structure
3. Better for disk-based storage

#### Disadvantages
1. More space required
2. Duplicate keys in internal nodes
3. More updates during modification

## Best Practices

1. Implementation
   - Choose appropriate node size
   - Implement efficient splitting
   - Handle duplicates properly

2. Usage
   - Use B+ Trees for databases
   - Consider cache efficiency
   - Optimize for disk access

3. Optimization
   - Bulk loading for initial construction
   - Buffer pool management
   - Compression techniques

## Common Pitfalls

1. Implementation Issues
   - Incorrect splitting
   - Memory management
   - Pointer inconsistency

2. Performance Issues
   - Wrong node size
   - Poor cache utilization
   - Unnecessary disk I/O

3. Design Issues
   - Wrong tree type selection
   - Ignoring system requirements
   - Poor concurrency handling

## Advanced Features

### Concurrent Access
```python
class BTreeWithLock:
    def __init__(self):
        self.root = BTreeNode()
        self.lock = threading.RLock()
    
    def insert(self, key):
        with self.lock:
            # Perform insert operation
            pass
```

### Bulk Loading
```python
def bulk_load(keys):
    # Sort keys
    sorted_keys = sorted(keys)
    
    # Create leaf nodes
    leaves = []
    current_node = BPlusNode(leaf=True)
    
    for key in sorted_keys:
        if len(current_node.keys) == MAX_KEYS:
            leaves.append(current_node)
            current_node = BPlusNode(leaf=True)
        current_node.keys.append(key)
    
    if current_node.keys:
        leaves.append(current_node)
    
    # Link leaves
    for i in range(len(leaves)-1):
        leaves[i].next = leaves[i+1]
    
    # Build internal nodes
    return build_internal_nodes(leaves)
```

### Compression
```python
def compress_node(node):
    # Prefix compression for keys
    if not node.keys:
        return
    
    prefix = os.path.commonprefix(node.keys)
    if prefix:
        node.prefix = prefix
        node.keys = [k[len(prefix):] for k in node.keys]
```
