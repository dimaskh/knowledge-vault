# Heap Data Structure

Last Updated: 2025-01-14

## Definition

A heap is a specialized tree-based data structure that satisfies the heap property. It's a complete binary tree where each node follows a specific ordering with respect to its children.

## Types of Heaps

### 1. Max Heap

- Parent nodes are always greater than or equal to their children
- Root node contains the maximum element
- Useful for finding maximum elements quickly

### 2. Min Heap

- Parent nodes are always less than or equal to their children
- Root node contains the minimum element
- Useful for finding minimum elements quickly

## Properties

- Complete binary tree (filled from left to right)
- Height is always ⌊log n⌋
- Parent of node at index i is at ⌊(i-1)/2⌋
- Left child of node at index i is at 2i + 1
- Right child of node at index i is at 2i + 2

## Time Complexity

- Insert: O(log n)
- Delete max/min: O(log n)
- Get max/min: O(1)
- Heapify: O(n)
- Search: O(n)

## Implementation Examples

### Python

```python
class MinHeap:
    def __init__(self):
        self.heap = []

    def parent(self, i):
        return (i - 1) // 2

    def left_child(self, i):
        return 2 * i + 1

    def right_child(self, i):
        return 2 * i + 2

    def swap(self, i, j):
        self.heap[i], self.heap[j] = self.heap[j], self.heap[i]

    def insert(self, key):
        self.heap.append(key)
        self._sift_up(len(self.heap) - 1)

    def _sift_up(self, i):
        parent = self.parent(i)
        if i > 0 and self.heap[i] < self.heap[parent]:
            self.swap(i, parent)
            self._sift_up(parent)

    def extract_min(self):
        if len(self.heap) == 0:
            return None
        if len(self.heap) == 1:
            return self.heap.pop()

        min_val = self.heap[0]
        self.heap[0] = self.heap.pop()
        self._sift_down(0)

        return min_val

    def _sift_down(self, i):
        min_index = i
        left = self.left_child(i)
        right = self.right_child(i)

        if left < len(self.heap) and self.heap[left] < self.heap[min_index]:
            min_index = left

        if right < len(self.heap) and self.heap[right] < self.heap[min_index]:
            min_index = right

        if i != min_index:
            self.swap(i, min_index)
            self._sift_down(min_index)

# Usage
min_heap = MinHeap()
min_heap.insert(3)
min_heap.insert(1)
min_heap.insert(4)
print(min_heap.extract_min())  # Returns 1
```

### JavaScript

```javascript
class MinHeap {
  constructor() {
    this.heap = [];
  }

  parent(i) {
    return Math.floor((i - 1) / 2);
  }

  leftChild(i) {
    return 2 * i + 1;
  }

  rightChild(i) {
    return 2 * i + 2;
  }

  swap(i, j) {
    [this.heap[i], this.heap[j]] = [this.heap[j], this.heap[i]];
  }

  insert(key) {
    this.heap.push(key);
    this._siftUp(this.heap.length - 1);
  }

  _siftUp(i) {
    const parent = this.parent(i);
    if (i > 0 && this.heap[i] < this.heap[parent]) {
      this.swap(i, parent);
      this._siftUp(parent);
    }
  }

  extractMin() {
    if (this.heap.length === 0) return null;
    if (this.heap.length === 1) return this.heap.pop();

    const min = this.heap[0];
    this.heap[0] = this.heap.pop();
    this._siftDown(0);

    return min;
  }

  _siftDown(i) {
    let minIndex = i;
    const left = this.leftChild(i);
    const right = this.rightChild(i);

    if (left < this.heap.length && this.heap[left] < this.heap[minIndex]) {
      minIndex = left;
    }

    if (right < this.heap.length && this.heap[right] < this.heap[minIndex]) {
      minIndex = right;
    }

    if (i !== minIndex) {
      this.swap(i, minIndex);
      this._siftDown(minIndex);
    }
  }
}

// Usage
const minHeap = new MinHeap();
minHeap.insert(3);
minHeap.insert(1);
minHeap.insert(4);
console.log(minHeap.extractMin()); // Returns 1
```

## Common Operations

### Heapify

Converting an array into a heap:

```python
def heapify(arr):
    n = len(arr)
    for i in range(n // 2 - 1, -1, -1):
        sift_down(arr, n, i)
```

### Heap Sort

Using heap to sort array:

```python
def heap_sort(arr):
    # Build max heap
    n = len(arr)
    for i in range(n // 2 - 1, -1, -1):
        sift_down(arr, n, i)

    # Extract elements from heap
    for i in range(n - 1, 0, -1):
        arr[0], arr[i] = arr[i], arr[0]
        sift_down(arr, i, 0)
```

## Common Applications

1. Priority Queues

   - Task scheduling
   - Event-driven simulation
   - Dijkstra's algorithm

2. Heap Sort

   - Efficient in-place sorting
   - Guaranteed O(n log n) complexity

3. Graph Algorithms

   - Finding shortest paths
   - Minimum spanning trees
   - Network flow algorithms

4. Operating Systems
   - Process scheduling
   - Memory management
   - I/O request handling

## Advantages

- Efficient access to min/max element
- Guaranteed logarithmic time for core operations
- Memory efficient (can be implemented as array)
- Good for priority-based operations

## Disadvantages

- Not suitable for searching specific elements
- No O(1) access to arbitrary elements
- Complex implementation compared to basic arrays
- Not stable for equal elements

## Variations

1. Fibonacci Heap

   - Improved amortized time complexity
   - Used in advanced graph algorithms

2. Binomial Heap

   - Collection of binomial trees
   - Efficient merging operations

3. Leftist Heap

   - Self-adjusting binary tree
   - Efficient merging operations

4. Skew Heap
   - Self-adjusting version of leftist heap
   - Simpler implementation

## Best Practices

1. Choose the Right Type

   - Min heap for finding minimums
   - Max heap for finding maximums

2. Consider Space Requirements

   - Array-based implementation is memory efficient
   - Pointer-based for more complex operations

3. Balance Operations

   - Use heapify for bulk construction
   - Maintain heap property after modifications

4. Handle Edge Cases
   - Empty heap
   - Single element
   - Duplicate values
