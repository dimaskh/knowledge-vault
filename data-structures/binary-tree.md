# Binary Trees

Last Updated: 2025-01-14

## Types of Binary Trees

### 1. Basic Binary Tree
- Each node has at most 2 children
- Children are typically called left and right
- No specific ordering of nodes

### 2. Binary Search Tree (BST)
- Left subtree contains only nodes with values less than parent
- Right subtree contains only nodes with values greater than parent
- No duplicate values
- Enables efficient searching

### 3. Full Binary Tree
- Every node has either 0 or 2 children
- No nodes have only one child

### 4. Complete Binary Tree
- All levels except possibly the last are completely filled
- Last level has nodes as left as possible
- Common in heap implementations

### 5. Perfect Binary Tree
- All internal nodes have 2 children
- All leaves are at same level

### 6. Balanced Binary Tree
- Height difference between left and right subtrees ≤ 1
- Examples: AVL Tree, Red-Black Tree

### 7. Unbalanced Binary Tree
- Significant height difference between subtrees
- Can degrade to linked list performance

## Time Complexity

### Balanced BST
- Search: O(log n)
- Insert: O(log n)
- Delete: O(log n)

### Unbalanced BST (Worst Case)
- Search: O(n)
- Insert: O(n)
- Delete: O(n)

## Implementation Examples

### Python
```python
class BinaryTreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

class BinarySearchTree:
    def __init__(self):
        self.root = None
    
    def insert(self, value):
        if not self.root:
            self.root = BinaryTreeNode(value)
        else:
            self._insert_recursive(self.root, value)
    
    def _insert_recursive(self, node, value):
        if value < node.value:
            if node.left is None:
                node.left = BinaryTreeNode(value)
            else:
                self._insert_recursive(node.left, value)
        else:
            if node.right is None:
                node.right = BinaryTreeNode(value)
            else:
                self._insert_recursive(node.right, value)
    
    def search(self, value):
        return self._search_recursive(self.root, value)
    
    def _search_recursive(self, node, value):
        if node is None or node.value == value:
            return node
        if value < node.value:
            return self._search_recursive(node.left, value)
        return self._search_recursive(node.right, value)

# Usage
bst = BinarySearchTree()
bst.insert(5)
bst.insert(3)
bst.insert(7)
node = bst.search(3)  # Returns node with value 3
```

### JavaScript
```javascript
class BinaryTreeNode {
    constructor(value) {
        this.value = value;
        this.left = null;
        this.right = null;
    }
}

class BinarySearchTree {
    constructor() {
        this.root = null;
    }
    
    insert(value) {
        if (!this.root) {
            this.root = new BinaryTreeNode(value);
        } else {
            this._insertRecursive(this.root, value);
        }
    }
    
    _insertRecursive(node, value) {
        if (value < node.value) {
            if (node.left === null) {
                node.left = new BinaryTreeNode(value);
            } else {
                this._insertRecursive(node.left, value);
            }
        } else {
            if (node.right === null) {
                node.right = new BinaryTreeNode(value);
            } else {
                this._insertRecursive(node.right, value);
            }
        }
    }
    
    search(value) {
        return this._searchRecursive(this.root, value);
    }
    
    _searchRecursive(node, value) {
        if (node === null || node.value === value) {
            return node;
        }
        if (value < node.value) {
            return this._searchRecursive(node.left, value);
        }
        return this._searchRecursive(node.right, value);
    }
}

// Usage
const bst = new BinarySearchTree();
bst.insert(5);
bst.insert(3);
bst.insert(7);
const node = bst.search(3); // Returns node with value 3
```

## Properties and Formulas

### Full Binary Tree
- If n is the number of nodes:
  - Number of leaf nodes = (n + 1) / 2
  - Number of internal nodes = n / 2
  - Height ≤ (n - 1) / 2

### Complete Binary Tree
- Maximum number of nodes at level i = 2^i
- Maximum number of nodes in tree of height h = 2^(h+1) - 1
- Height of a complete binary tree with n nodes = ⌊log₂(n)⌋

### Perfect Binary Tree
- All levels are full
- Number of nodes = 2^(h+1) - 1
- Number of leaf nodes = 2^h
- Height = log₂(n+1) - 1

## Common Use Cases
1. Binary Search Trees
   - Efficient searching and sorting
   - Database indexing
   - File system organization

2. Complete Binary Trees
   - Heap implementation
   - Priority queues
   - Tournament brackets

3. Balanced Trees
   - Database management systems
   - File systems
   - Network routing tables

## Advantages
- Efficient searching (BST)
- Ordered traversal
- Flexible structure
- Natural recursion

## Disadvantages
- Complex implementation
- Balancing overhead
- Memory usage
- Potential degradation to O(n) if unbalanced
