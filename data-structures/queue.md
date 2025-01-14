# Queue Data Structure

Last Updated: 2025-01-14

## Definition
A queue is a linear data structure that follows the First-In-First-Out (FIFO) principle. Elements are added at the rear and removed from the front.

## Types
- Simple Queue: Basic FIFO queue
- Circular Queue: Last position connected to first position
- Priority Queue: Elements have priority values
- Deque (Double-ended queue): Insert/delete from both ends

## Time Complexity
- Enqueue: O(1)
- Dequeue: O(1)
- Peek: O(1)
- Search: O(n)

## Implementation Examples

### Python
```python
from collections import deque

# Using built-in deque
queue = deque()
queue.append(1)      # Enqueue
queue.append(2)
first = queue.popleft()  # Dequeue

# Custom implementation
class Queue:
    def __init__(self):
        self.items = []
        
    def enqueue(self, item):
        self.items.append(item)
        
    def dequeue(self):
        if not self.is_empty():
            return self.items.pop(0)
            
    def peek(self):
        if not self.is_empty():
            return self.items[0]
            
    def is_empty(self):
        return len(self.items) == 0

# Usage
queue = Queue()
queue.enqueue(1)
queue.enqueue(2)
first = queue.dequeue()  # Returns 1
```

### JavaScript
```javascript
class Queue {
    constructor() {
        this.items = [];
    }
    
    enqueue(element) {
        this.items.push(element);
    }
    
    dequeue() {
        if (!this.isEmpty()) {
            return this.items.shift();
        }
    }
    
    peek() {
        if (!this.isEmpty()) {
            return this.items[0];
        }
    }
    
    isEmpty() {
        return this.items.length === 0;
    }
}

// Usage
const queue = new Queue();
queue.enqueue(1);
queue.enqueue(2);
const first = queue.dequeue(); // Returns 1
```

## Common Use Cases
1. Task scheduling
2. Print queue
3. Breadth-first search
4. Request handling in web servers
5. Buffer for data streams

## Advantages
- Fair processing (FIFO)
- Predictable data flow
- Good for managing sequential data
- Simple implementation

## Disadvantages
- Fixed size in array implementation
- No random access
- Head-of-line blocking
- Memory deallocation issues in some implementations
