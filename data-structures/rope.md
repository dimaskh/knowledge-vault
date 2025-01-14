# Rope (String Data Structure)

Last Updated: 2025-01-14

## Overview
A Rope is a tree-based data structure for efficiently storing and manipulating very long strings. It provides efficient operations for concatenation, substring extraction, and random access while avoiding copying large strings.

## Properties
1. Tree-based string representation
2. Efficient concatenation
3. Logarithmic access time
4. Memory efficient for large strings
5. Supports persistent versions

## Basic Implementation

### Node Structure
```python
class RopeNode:
    def __init__(self, text=""):
        self.left = None
        self.right = None
        self.weight = len(text)  # Number of characters in left subtree
        self.text = text  # Only leaf nodes store text
    
    @property
    def length(self):
        if self.text:
            return len(self.text)
        return (self.left.length if self.left else 0) + \
               (self.right.length if self.right else 0)

class Rope:
    def __init__(self, text=""):
        if len(text) <= 8:  # Small strings stored directly
            self.root = RopeNode(text)
        else:
            mid = len(text) // 2
            self.root = RopeNode()
            self.root.left = Rope(text[:mid]).root
            self.root.right = Rope(text[mid:]).root
            self.root.weight = self.root.left.length
```

### Basic Operations
```python
def index(self, i):
    """Get character at index i"""
    if i < 0 or i >= self.root.length:
        raise IndexError("Index out of range")
    
    return self._index(self.root, i)

def _index(self, node, i):
    if node.text:
        return node.text[i]
    
    if i < node.weight:
        return self._index(node.left, i)
    return self._index(node.right, i - node.weight)

def concat(self, other):
    """Concatenate two ropes"""
    if not self.root:
        return other
    if not other.root:
        return self
    
    new_rope = Rope()
    new_rope.root = RopeNode()
    new_rope.root.left = self.root
    new_rope.root.right = other.root
    new_rope.root.weight = self.root.length
    return new_rope
```

## Advanced Implementation

### Split Operation
```python
def split(self, i):
    """Split rope into two at index i"""
    if i < 0 or i > self.root.length:
        raise IndexError("Index out of range")
    
    left_rope = Rope()
    right_rope = Rope()
    
    if i == 0:
        right_rope.root = self.root
        return left_rope, right_rope
    
    if i == self.root.length:
        left_rope.root = self.root
        return left_rope, right_rope
    
    left_rope.root, right_rope.root = self._split(self.root, i)
    return left_rope, right_rope

def _split(self, node, i):
    if node.text:
        left = RopeNode(node.text[:i])
        right = RopeNode(node.text[i:])
        return left, right
    
    if i < node.weight:
        left, right = self._split(node.left, i)
        new_node = RopeNode()
        new_node.left = right
        new_node.right = node.right
        new_node.weight = right.length
        return left, new_node
    else:
        left, right = self._split(node.right, i - node.weight)
        new_node = RopeNode()
        new_node.left = node.left
        new_node.right = left
        new_node.weight = node.weight
        return new_node, right
```

### Insert and Delete
```python
def insert(self, i, text):
    """Insert text at index i"""
    if i < 0 or i > self.root.length:
        raise IndexError("Index out of range")
    
    if not text:
        return
    
    left, right = self.split(i)
    middle = Rope(text)
    return left.concat(middle).concat(right)

def delete(self, i, j):
    """Delete text from index i to j"""
    if i < 0 or j > self.root.length or i > j:
        raise IndexError("Invalid range")
    
    if i == j:
        return self
    
    left, temp = self.split(i)
    _, right = temp.split(j - i)
    return left.concat(right)
```

## Advanced Features

### Persistent Rope
```python
class PersistentRopeNode:
    def __init__(self, text=""):
        self.left = None
        self.right = None
        self.weight = len(text)
        self.text = text
        self.version = 0
    
    def clone(self):
        node = PersistentRopeNode()
        node.left = self.left
        node.right = self.right
        node.weight = self.weight
        node.text = self.text
        return node

class PersistentRope:
    def __init__(self):
        self.versions = [RopeNode()]
        self.current_version = 0
    
    def insert(self, i, text):
        new_root = self._persistent_insert(
            self.versions[self.current_version], 
            i, 
            text
        )
        self.versions.append(new_root)
        self.current_version += 1
        return self.current_version
    
    def _persistent_insert(self, node, i, text):
        if not node:
            return RopeNode(text)
        
        new_node = node.clone()
        
        if i <= node.weight:
            new_node.left = self._persistent_insert(
                node.left, i, text
            )
            new_node.weight += len(text)
        else:
            new_node.right = self._persistent_insert(
                node.right, i - node.weight, text
            )
        
        return new_node
```

### Balanced Rope
```python
class BalancedRope(Rope):
    def _rebalance(self, node):
        if not node:
            return None
        
        # Collect nodes in-order
        nodes = []
        self._collect_nodes(node, nodes)
        
        # Rebuild balanced tree
        return self._build_balanced(nodes, 0, len(nodes))
    
    def _collect_nodes(self, node, nodes):
        if not node:
            return
        
        if node.text:
            nodes.append(node)
        else:
            self._collect_nodes(node.left, nodes)
            self._collect_nodes(node.right, nodes)
    
    def _build_balanced(self, nodes, start, end):
        if start >= end:
            return None
        
        mid = (start + end) // 2
        node = nodes[mid]
        
        node.left = self._build_balanced(nodes, start, mid)
        node.right = self._build_balanced(nodes, mid + 1, end)
        
        node.weight = (node.left.length if node.left else 0)
        return node
```

## Time Complexity
- Index: O(log n)
- Concat: O(1)
- Split: O(log n)
- Insert/Delete: O(log n)
- Space: O(n)

## Use Cases

1. Text Editors
   - Large document handling
   - Undo/redo functionality
   - Collaborative editing

2. String Processing
   - Large string manipulation
   - Pattern matching
   - Text analysis

3. Version Control
   - Text file versioning
   - Diff operations
   - Merge operations

## Best Practices

1. Implementation
   - Choose appropriate chunk size
   - Balance tree regularly
   - Handle edge cases

2. Performance
   - Optimize for common operations
   - Use lazy evaluation
   - Cache frequently used nodes

3. Memory
   - Reuse nodes when possible
   - Implement garbage collection
   - Consider memory pooling

## Common Pitfalls

1. Implementation Issues
   - Unbalanced trees
   - Incorrect weight updates
   - Memory leaks

2. Performance Issues
   - Too small/large chunks
   - Excessive rebalancing
   - Poor locality

3. Design Issues
   - Wrong choice of operations
   - Missing optimizations
   - Complex maintenance

## Advanced Applications

### Collaborative Text Editor
```python
class CollaborativeRope:
    def __init__(self):
        self.rope = Rope()
        self.operations = []
        self.site_id = uuid.uuid4()
    
    def local_insert(self, pos, text):
        timestamp = time.time()
        op = {
            'type': 'insert',
            'pos': pos,
            'text': text,
            'site': self.site_id,
            'timestamp': timestamp
        }
        self.apply_operation(op)
        return op
    
    def remote_operation(self, op):
        # Transform operation based on concurrent ops
        transformed_op = self.transform(op)
        self.apply_operation(transformed_op)
    
    def transform(self, op):
        for past_op in self.operations:
            if past_op['timestamp'] < op['timestamp']:
                op = self.operational_transform(op, past_op)
        return op
    
    def apply_operation(self, op):
        if op['type'] == 'insert':
            self.rope = self.rope.insert(op['pos'], op['text'])
        elif op['type'] == 'delete':
            self.rope = self.rope.delete(
                op['pos'], 
                op['pos'] + len(op['text'])
            )
        self.operations.append(op)
```

### Memory-Efficient Implementation
```python
class CompactRope:
    def __init__(self):
        self.chunks = []
        self.tree = []
    
    def _new_node(self, left, right, weight):
        self.tree.append((left, right, weight))
        return len(self.tree) - 1
    
    def _new_leaf(self, text):
        self.chunks.append(text)
        return -(len(self.chunks))
    
    def insert(self, i, text):
        if len(text) < 8:
            # Store small strings directly
            node_id = self._new_leaf(text)
        else:
            # Split large strings
            mid = len(text) // 2
            left = self.insert(0, text[:mid])
            right = self.insert(0, text[mid:])
            node_id = self._new_node(left, right, len(text[:mid]))
        return node_id
```
