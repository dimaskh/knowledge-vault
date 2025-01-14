# Tree Traversal Algorithms

Last Updated: 2025-01-14

## Tree Node Structure
```python
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None
```

## Pre-Order Traversal

### Description
- Visit root first, then left subtree, then right subtree
- Pattern: Root → Left → Right
- Useful for creating copy of tree or prefix expression

### Implementation
```python
def preorder_recursive(root):
    if root:
        print(root.value, end=' ')  # Process root
        preorder_recursive(root.left)
        preorder_recursive(root.right)

def preorder_iterative(root):
    if not root:
        return
    
    stack = [root]
    while stack:
        node = stack.pop()
        print(node.value, end=' ')  # Process node
        
        if node.right:
            stack.append(node.right)
        if node.left:
            stack.append(node.left)
```

### Time Complexity
- Time: O(n)
- Space: O(h) where h is height of tree

## In-Order Traversal

### Description
- Visit left subtree, then root, then right subtree
- Pattern: Left → Root → Right
- Gives nodes in sorted order for BST

### Implementation
```python
def inorder_recursive(root):
    if root:
        inorder_recursive(root.left)
        print(root.value, end=' ')  # Process root
        inorder_recursive(root.right)

def inorder_iterative(root):
    stack = []
    current = root
    
    while current or stack:
        while current:
            stack.append(current)
            current = current.left
        
        current = stack.pop()
        print(current.value, end=' ')  # Process node
        current = current.right
```

### Time Complexity
- Time: O(n)
- Space: O(h)

## Post-Order Traversal

### Description
- Visit left subtree, then right subtree, then root
- Pattern: Left → Right → Root
- Useful for deletion or resource cleanup

### Implementation
```python
def postorder_recursive(root):
    if root:
        postorder_recursive(root.left)
        postorder_recursive(root.right)
        print(root.value, end=' ')  # Process root

def postorder_iterative(root):
    if not root:
        return
    
    stack1 = [root]
    stack2 = []
    
    while stack1:
        node = stack1.pop()
        stack2.append(node)
        
        if node.left:
            stack1.append(node.left)
        if node.right:
            stack1.append(node.right)
    
    while stack2:
        node = stack2.pop()
        print(node.value, end=' ')  # Process node
```

### Time Complexity
- Time: O(n)
- Space: O(h)

## Level-Order (Breadth First) Traversal

### Description
- Visit nodes level by level
- Uses queue data structure
- Good for level-wise processing

### Implementation
```python
from collections import deque

def levelorder(root):
    if not root:
        return
    
    queue = deque([root])
    
    while queue:
        level_size = len(queue)
        
        for _ in range(level_size):
            node = queue.popleft()
            print(node.value, end=' ')  # Process node
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        print()  # New line for each level
```

### Time Complexity
- Time: O(n)
- Space: O(w) where w is maximum width of tree

## Depth First Search in Trees

### Description
- Similar to DFS in graphs but simpler due to tree structure
- Can be implemented using any of the above traversals
- Often used for searching specific values

### Implementation
```python
def dfs_tree(root, target):
    if not root:
        return False
    
    if root.value == target:
        return True
    
    return dfs_tree(root.left, target) or dfs_tree(root.right, target)
```

### Time Complexity
- Time: O(n)
- Space: O(h)

## Comparison of Traversals

### Use Cases

1. Pre-order
   - Creating a copy of the tree
   - Serializing or storing tree structure
   - Getting prefix expression

2. In-order
   - Getting sorted elements from BST
   - Checking if tree is BST
   - Tree comparison

3. Post-order
   - Deleting nodes (bottom-up)
   - Computing space used by subtrees
   - Mathematical expression evaluation

4. Level-order
   - Level-wise processing
   - Finding minimum height
   - Connecting nodes at same level

### Best Practices

1. Choosing Traversal Method
   - Use recursive for simple cases
   - Use iterative for better space complexity
   - Consider level-order for width-based operations

2. Implementation Tips
   - Handle null nodes properly
   - Consider using helper functions
   - Use appropriate data structures (stack/queue)

3. Common Pitfalls
   - Stack overflow in recursive implementation
   - Not handling empty trees
   - Incorrect order of operations
