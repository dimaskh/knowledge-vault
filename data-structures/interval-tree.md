# Interval Tree

Last Updated: 2025-01-14

## Overview
An Interval Tree is a self-balancing binary search tree designed to hold intervals efficiently. It's particularly useful for finding overlapping intervals and range queries.

## Properties
1. Based on Red-Black Tree or AVL Tree
2. Each node stores an interval
3. Maintains max endpoint in subtree
4. O(log n) operations
5. Efficient overlap queries

## Basic Implementation

### Node Structure
```python
class Interval:
    def __init__(self, low, high):
        self.low = low
        self.high = high
    
    def overlaps(self, other):
        return (self.low <= other.high and 
                other.low <= self.high)
    
    def __str__(self):
        return f"[{self.low}, {self.high}]"

class IntervalNode:
    def __init__(self, interval):
        self.interval = interval
        self.max = interval.high
        self.left = None
        self.right = None
        self.height = 1  # For AVL balancing

class IntervalTree:
    def __init__(self):
        self.root = None
    
    def _height(self, node):
        if not node:
            return 0
        return node.height
    
    def _update_height(self, node):
        if not node:
            return
        node.height = max(self._height(node.left), 
                         self._height(node.right)) + 1
    
    def _update_max(self, node):
        if not node:
            return
        node.max = max(
            node.interval.high,
            node.left.max if node.left else float('-inf'),
            node.right.max if node.right else float('-inf')
        )
```

### Basic Operations
```python
def insert(self, interval):
    """Insert a new interval into the tree"""
    self.root = self._insert(self.root, interval)

def _insert(self, node, interval):
    # Standard BST insert
    if not node:
        return IntervalNode(interval)
    
    if interval.low < node.interval.low:
        node.left = self._insert(node.left, interval)
    else:
        node.right = self._insert(node.right, interval)
    
    # Update height and max
    self._update_height(node)
    self._update_max(node)
    
    # Balance tree
    return self._balance(node)

def search_overlap(self, interval):
    """Find an interval that overlaps with given interval"""
    return self._search_overlap(self.root, interval)

def _search_overlap(self, node, interval):
    if not node:
        return None
    
    if node.interval.overlaps(interval):
        return node.interval
    
    if (node.left and 
        node.left.max >= interval.low):
        return self._search_overlap(node.left, interval)
    
    return self._search_overlap(node.right, interval)
```

### AVL Balancing
```python
def _balance_factor(self, node):
    if not node:
        return 0
    return self._height(node.left) - self._height(node.right)

def _right_rotate(self, y):
    x = y.left
    T2 = x.right
    
    x.right = y
    y.left = T2
    
    self._update_height(y)
    self._update_height(x)
    self._update_max(y)
    self._update_max(x)
    
    return x

def _left_rotate(self, x):
    y = x.right
    T2 = y.left
    
    y.left = x
    x.right = T2
    
    self._update_height(x)
    self._update_height(y)
    self._update_max(x)
    self._update_max(y)
    
    return y

def _balance(self, node):
    if not node:
        return None
    
    balance = self._balance_factor(node)
    
    # Left Heavy
    if balance > 1:
        if self._balance_factor(node.left) < 0:
            node.left = self._left_rotate(node.left)
        return self._right_rotate(node)
    
    # Right Heavy
    if balance < -1:
        if self._balance_factor(node.right) > 0:
            node.right = self._right_rotate(node.right)
        return self._left_rotate(node)
    
    return node
```

## Advanced Features

### Range Query
```python
def query_range(self, low, high):
    """Find all intervals that overlap with [low, high]"""
    query_interval = Interval(low, high)
    result = []
    self._query_range(self.root, query_interval, result)
    return result

def _query_range(self, node, interval, result):
    if not node:
        return
    
    # If current node overlaps with query interval
    if node.interval.overlaps(interval):
        result.append(node.interval)
    
    # Search left subtree if it may contain overlapping intervals
    if (node.left and 
        node.left.max >= interval.low):
        self._query_range(node.left, interval, result)
    
    # Search right subtree if it may contain overlapping intervals
    if (node.right and 
        node.interval.low <= interval.high):
        self._query_range(node.right, interval, result)
```

### Deletion
```python
def delete(self, interval):
    """Delete an interval from the tree"""
    self.root = self._delete(self.root, interval)

def _delete(self, node, interval):
    if not node:
        return None
    
    if interval.low < node.interval.low:
        node.left = self._delete(node.left, interval)
    elif interval.low > node.interval.low:
        node.right = self._delete(node.right, interval)
    else:
        # Found the node to delete
        if not node.left:
            return node.right
        elif not node.right:
            return node.left
        
        # Node with two children
        successor = self._min_value_node(node.right)
        node.interval = successor.interval
        node.right = self._delete(node.right, successor.interval)
    
    self._update_height(node)
    self._update_max(node)
    return self._balance(node)

def _min_value_node(self, node):
    current = node
    while current.left:
        current = current.left
    return current
```

## Advanced Implementation

### Augmented Interval Tree
```python
class AugmentedInterval(Interval):
    def __init__(self, low, high, data=None):
        super().__init__(low, high)
        self.data = data  # Additional data associated with interval

class AugmentedIntervalNode(IntervalNode):
    def __init__(self, interval):
        super().__init__(interval)
        self.count = 1  # Number of intervals in subtree
        self.sum_length = interval.high - interval.low

class AugmentedIntervalTree(IntervalTree):
    def _update_augmented_data(self, node):
        if not node:
            return
        
        node.count = 1
        node.sum_length = (node.interval.high - 
                          node.interval.low)
        
        if node.left:
            node.count += node.left.count
            node.sum_length += node.left.sum_length
        
        if node.right:
            node.count += node.right.count
            node.sum_length += node.right.sum_length
    
    def _insert(self, node, interval):
        node = super()._insert(node, interval)
        self._update_augmented_data(node)
        return node
    
    def get_total_coverage(self):
        """Get total length covered by all intervals"""
        return self._get_total_coverage(self.root)
    
    def _get_total_coverage(self, node):
        if not node:
            return 0
        
        # Merge overlapping intervals
        intervals = []
        self._inorder_traversal(node, intervals)
        
        # Sort by start time
        intervals.sort(key=lambda x: x.low)
        
        total = 0
        current = intervals[0]
        
        for i in range(1, len(intervals)):
            if intervals[i].low <= current.high:
                current.high = max(current.high, 
                                 intervals[i].high)
            else:
                total += (current.high - current.low)
                current = intervals[i]
        
        total += (current.high - current.low)
        return total
```

### Persistent Interval Tree
```python
class PersistentIntervalNode(IntervalNode):
    def __init__(self, interval):
        super().__init__(interval)
        self.version = 0

class PersistentIntervalTree:
    def __init__(self):
        self.roots = [None]
        self.current_version = 0
    
    def _copy_node(self, node):
        if not node:
            return None
        new_node = PersistentIntervalNode(node.interval)
        new_node.left = node.left
        new_node.right = node.right
        new_node.height = node.height
        new_node.max = node.max
        new_node.version = self.current_version + 1
        return new_node
    
    def insert(self, interval):
        new_root = self._persistent_insert(
            self.roots[self.current_version], 
            interval
        )
        self.current_version += 1
        self.roots.append(new_root)
        return self.current_version
    
    def _persistent_insert(self, node, interval):
        if not node:
            new_node = PersistentIntervalNode(interval)
            new_node.version = self.current_version + 1
            return new_node
        
        new_node = self._copy_node(node)
        
        if interval.low < node.interval.low:
            new_node.left = self._persistent_insert(
                node.left, 
                interval
            )
        else:
            new_node.right = self._persistent_insert(
                node.right, 
                interval
            )
        
        self._update_height(new_node)
        self._update_max(new_node)
        return self._balance(new_node)
```

## Time Complexity
- Insert: O(log n)
- Delete: O(log n)
- Search Overlap: O(log n)
- Range Query: O(k + log n) where k is number of overlapping intervals
- Space: O(n)

## Use Cases

1. Calendar Applications
   - Meeting scheduling
   - Resource allocation
   - Conflict detection

2. Database Systems
   - Range queries
   - Temporal databases
   - Version control

3. Network Management
   - IP range allocation
   - Bandwidth reservation
   - Resource scheduling

## Best Practices

1. Implementation
   - Keep tree balanced
   - Update max values correctly
   - Handle edge cases

2. Performance
   - Choose appropriate interval representation
   - Optimize overlap checks
   - Consider bulk operations

3. Memory
   - Use appropriate data types
   - Implement cleanup
   - Consider persistence needs

## Common Pitfalls

1. Implementation Issues
   - Incorrect max updates
   - Balance violations
   - Overlap calculation errors

2. Performance Issues
   - Unbalanced trees
   - Inefficient range queries
   - Poor memory locality

3. Design Issues
   - Wrong interval representation
   - Missing edge cases
   - Incorrect overlap logic

## Advanced Applications

### Scheduling System
```python
class SchedulingSystem:
    def __init__(self):
        self.tree = IntervalTree()
        self.resources = {}
    
    def schedule_meeting(self, start, end, resource):
        interval = Interval(start, end)
        
        # Check for conflicts
        if self.tree.search_overlap(interval):
            return False
        
        self.tree.insert(interval)
        self.resources[interval] = resource
        return True
    
    def get_available_slots(self, start, end, duration):
        busy_intervals = self.tree.query_range(start, end)
        busy_intervals.sort(key=lambda x: x.low)
        
        available = []
        current = start
        
        for interval in busy_intervals:
            if interval.low - current >= duration:
                available.append(
                    Interval(current, interval.low)
                )
            current = interval.high
        
        if end - current >= duration:
            available.append(Interval(current, end))
        
        return available
```

### Time Series Data Storage
```python
class TimeSeriesStore:
    def __init__(self):
        self.tree = AugmentedIntervalTree()
        self.data_points = {}
    
    def insert_data(self, start, end, data):
        interval = AugmentedInterval(start, end, data)
        self.tree.insert(interval)
        self.data_points[interval] = []
    
    def add_point(self, time, value):
        intervals = self.tree.query_range(time, time)
        for interval in intervals:
            self.data_points[interval].append((time, value))
    
    def query_timespan(self, start, end):
        result = {}
        intervals = self.tree.query_range(start, end)
        
        for interval in intervals:
            points = self.data_points[interval]
            filtered_points = [
                p for p in points 
                if start <= p[0] <= end
            ]
            result[interval] = filtered_points
        
        return result
```
