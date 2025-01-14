# AVL Tree

Last Updated: 2025-01-14

## Overview
An AVL tree is a self-balancing binary search tree where the heights of the two child subtrees of any node differ by at most one.

## Properties
- Self-balancing
- Height difference (balance factor) between left and right subtrees is at most 1
- All BST properties are maintained
- Height is O(log n)

## Node Structure
```python
class AVLNode:
    def __init__(self, key):
        self.key = key
        self.left = None
        self.right = None
        self.height = 1  # Height of the node
```

## Operations

### Height and Balance Factor
```python
def get_height(node):
    if not node:
        return 0
    return node.height

def get_balance(node):
    if not node:
        return 0
    return get_height(node.left) - get_height(node.right)

def update_height(node):
    if not node:
        return
    node.height = max(get_height(node.left), get_height(node.right)) + 1
```

### Rotations
```python
def right_rotate(y):
    x = y.left
    T2 = x.right

    x.right = y
    y.left = T2

    update_height(y)
    update_height(x)

    return x

def left_rotate(x):
    y = x.right
    T2 = y.left

    y.left = x
    x.right = T2

    update_height(x)
    update_height(y)

    return y
```

### Insertion
```python
def insert(root, key):
    # 1. Perform standard BST insertion
    if not root:
        return AVLNode(key)
    
    if key < root.key:
        root.left = insert(root.left, key)
    elif key > root.key:
        root.right = insert(root.right, key)
    else:
        return root  # Duplicate keys not allowed

    # 2. Update height
    update_height(root)

    # 3. Get balance factor
    balance = get_balance(root)

    # 4. Balance if needed
    # Left Left Case
    if balance > 1 and key < root.left.key:
        return right_rotate(root)

    # Right Right Case
    if balance < -1 and key > root.right.key:
        return left_rotate(root)

    # Left Right Case
    if balance > 1 and key > root.left.key:
        root.left = left_rotate(root.left)
        return right_rotate(root)

    # Right Left Case
    if balance < -1 and key < root.right.key:
        root.right = right_rotate(root.right)
        return left_rotate(root)

    return root
```

### Deletion
```python
def get_min_value_node(node):
    current = node
    while current.left:
        current = current.left
    return current

def delete(root, key):
    # 1. Perform standard BST delete
    if not root:
        return root

    if key < root.key:
        root.left = delete(root.left, key)
    elif key > root.key:
        root.right = delete(root.right, key)
    else:
        # Node with only one child or no child
        if not root.left:
            return root.right
        elif not root.right:
            return root.left

        # Node with two children
        temp = get_min_value_node(root.right)
        root.key = temp.key
        root.right = delete(root.right, temp.key)

    # If tree had only one node
    if not root:
        return root

    # 2. Update height
    update_height(root)

    # 3. Get balance factor
    balance = get_balance(root)

    # 4. Balance if needed
    # Left Left Case
    if balance > 1 and get_balance(root.left) >= 0:
        return right_rotate(root)

    # Left Right Case
    if balance > 1 and get_balance(root.left) < 0:
        root.left = left_rotate(root.left)
        return right_rotate(root)

    # Right Right Case
    if balance < -1 and get_balance(root.right) <= 0:
        return left_rotate(root)

    # Right Left Case
    if balance < -1 and get_balance(root.right) > 0:
        root.right = right_rotate(root.right)
        return left_rotate(root)

    return root
```

## Time Complexity
- Search: O(log n)
- Insert: O(log n)
- Delete: O(log n)
- Space: O(n)

## Use Cases
1. Database Indexes
   - Maintaining sorted data
   - Range queries

2. In-memory dictionaries
   - When balanced tree performance is critical
   - Need for ordered traversal

3. File Systems
   - Directory structures
   - File indexing

## Advantages
1. Self-balancing
2. Guaranteed O(log n) operations
3. Simple implementation compared to Red-Black trees
4. Good for frequent lookups

## Disadvantages
1. Extra space for height information
2. More rotations than Red-Black trees
3. Complex deletion operation
4. Not cache-friendly due to pointer-based structure

## Common Operations

### Traversal
```python
def inorder(root):
    if not root:
        return
    inorder(root.left)
    print(root.key, end=' ')
    inorder(root.right)

def preorder(root):
    if not root:
        return
    print(root.key, end=' ')
    preorder(root.left)
    preorder(root.right)
```

### Search
```python
def search(root, key):
    if not root or root.key == key:
        return root
    
    if key < root.key:
        return search(root.left, key)
    return search(root.right, key)
```

## Best Practices

1. Implementation
   - Use recursive implementation for clarity
   - Cache height values
   - Handle duplicates according to requirements

2. Usage
   - Consider Red-Black trees for frequent updates
   - Use when order statistics are needed
   - Implement iterator for traversal

3. Optimization
   - Use iterative implementation for better performance
   - Consider bulk loading for initial construction
   - Use path compression for repeated operations

## Common Pitfalls

1. Implementation Issues
   - Incorrect balance factor calculation
   - Missing height updates
   - Wrong rotation choice

2. Usage Issues
   - Using AVL when Red-Black would be better
   - Not considering memory overhead
   - Ignoring cache performance

3. Maintenance Issues
   - Complex debugging
   - Difficult to visualize
   - Hard to maintain balance invariant
