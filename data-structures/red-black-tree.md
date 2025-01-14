# Red-Black Tree

Last Updated: 2025-01-14

## Overview
A Red-Black tree is a self-balancing binary search tree where each node has an extra bit for denoting the color of the node, either red or black.

## Properties
1. Every node is either red or black
2. Root is always black
3. All leaves (NIL) are black
4. If a node is red, both its children are black
5. Every path from root to leaves has same number of black nodes

## Node Structure
```python
class RBNode:
    def __init__(self, key):
        self.key = key
        self.left = None
        self.right = None
        self.parent = None
        self.color = 'RED'  # New nodes are always red
        self.is_nil = False
```

## Operations

### Color Operations
```python
def is_red(node):
    if not node:
        return False
    return node.color == 'RED'

def set_color(node, color):
    if node:
        node.color = color
```

### Rotations
```python
def left_rotate(tree, x):
    y = x.right
    x.right = y.left
    
    if y.left:
        y.left.parent = x
    
    y.parent = x.parent
    
    if not x.parent:
        tree.root = y
    elif x == x.parent.left:
        x.parent.left = y
    else:
        x.parent.right = y
    
    y.left = x
    x.parent = y

def right_rotate(tree, y):
    x = y.left
    y.left = x.right
    
    if x.right:
        x.right.parent = y
    
    x.parent = y.parent
    
    if not y.parent:
        tree.root = x
    elif y == y.parent.right:
        y.parent.right = x
    else:
        y.parent.left = x
    
    x.right = y
    y.parent = x
```

### Insertion
```python
def insert(tree, key):
    node = RBNode(key)
    
    # 1. Standard BST insert
    y = None
    x = tree.root
    
    while x:
        y = x
        if node.key < x.key:
            x = x.left
        else:
            x = x.right
    
    node.parent = y
    
    if not y:
        tree.root = node
    elif node.key < y.key:
        y.left = node
    else:
        y.right = node
    
    # 2. Fix Red-Black tree violations
    insert_fixup(tree, node)

def insert_fixup(tree, node):
    while node.parent and is_red(node.parent):
        if node.parent == node.parent.parent.left:
            y = node.parent.parent.right
            
            if is_red(y):
                # Case 1
                node.parent.color = 'BLACK'
                y.color = 'BLACK'
                node.parent.parent.color = 'RED'
                node = node.parent.parent
            else:
                if node == node.parent.right:
                    # Case 2
                    node = node.parent
                    left_rotate(tree, node)
                # Case 3
                node.parent.color = 'BLACK'
                node.parent.parent.color = 'RED'
                right_rotate(tree, node.parent.parent)
        else:
            # Same as above with 'right' and 'left' exchanged
            y = node.parent.parent.left
            
            if is_red(y):
                node.parent.color = 'BLACK'
                y.color = 'BLACK'
                node.parent.parent.color = 'RED'
                node = node.parent.parent
            else:
                if node == node.parent.left:
                    node = node.parent
                    right_rotate(tree, node)
                node.parent.color = 'BLACK'
                node.parent.parent.color = 'RED'
                left_rotate(tree, node.parent.parent)
    
    tree.root.color = 'BLACK'
```

### Deletion
```python
def transplant(tree, u, v):
    if not u.parent:
        tree.root = v
    elif u == u.parent.left:
        u.parent.left = v
    else:
        u.parent.right = v
    if v:
        v.parent = u.parent

def delete(tree, node):
    y = node
    y_original_color = y.color
    
    if not node.left:
        x = node.right
        transplant(tree, node, node.right)
    elif not node.right:
        x = node.left
        transplant(tree, node, node.left)
    else:
        y = minimum(node.right)
        y_original_color = y.color
        x = y.right
        
        if y.parent == node:
            if x:
                x.parent = y
        else:
            transplant(tree, y, y.right)
            y.right = node.right
            y.right.parent = y
        
        transplant(tree, node, y)
        y.left = node.left
        y.left.parent = y
        y.color = node.color
    
    if y_original_color == 'BLACK':
        delete_fixup(tree, x)

def delete_fixup(tree, x):
    while x != tree.root and x.color == 'BLACK':
        if x == x.parent.left:
            w = x.parent.right
            
            if w.color == 'RED':
                w.color = 'BLACK'
                x.parent.color = 'RED'
                left_rotate(tree, x.parent)
                w = x.parent.right
            
            if not is_red(w.left) and not is_red(w.right):
                w.color = 'RED'
                x = x.parent
            else:
                if not is_red(w.right):
                    w.left.color = 'BLACK'
                    w.color = 'RED'
                    right_rotate(tree, w)
                    w = x.parent.right
                
                w.color = x.parent.color
                x.parent.color = 'BLACK'
                w.right.color = 'BLACK'
                left_rotate(tree, x.parent)
                x = tree.root
        else:
            # Same as above with 'right' and 'left' exchanged
            pass
    
    x.color = 'BLACK'
```

## Time Complexity
- Search: O(log n)
- Insert: O(log n)
- Delete: O(log n)
- Space: O(n)

## Use Cases
1. Java TreeMap and TreeSet
2. Linux Kernel's Completely Fair Scheduler
3. Database Indexes
4. File System Implementation

## Advantages
1. Guaranteed O(log n) operations
2. More balanced than AVL trees
3. Fewer rotations during insertion/deletion
4. Good for frequent modifications

## Disadvantages
1. Complex implementation
2. Extra space for color information
3. Harder to understand and maintain
4. Not as strictly balanced as AVL trees

## Best Practices

1. Implementation
   - Use sentinel NIL nodes
   - Implement iterator pattern
   - Cache parent pointers
   - Handle duplicates properly

2. Usage
   - Use for frequent modifications
   - Consider simpler alternatives if possible
   - Implement proper cleanup

3. Optimization
   - Use sentinel nodes
   - Implement path compression
   - Consider bulk loading

## Common Pitfalls

1. Implementation Issues
   - Missing color updates
   - Incorrect rotation handling
   - Parent pointer inconsistency

2. Usage Issues
   - Using when simpler structure suffices
   - Not considering memory overhead
   - Ignoring cache performance

3. Maintenance Issues
   - Difficult debugging
   - Complex violation fixes
   - Hard to visualize structure
