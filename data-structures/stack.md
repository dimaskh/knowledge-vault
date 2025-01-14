# Stack Data Structure

Last Updated: 2025-01-14

## Definition
A stack is a linear data structure that follows the Last-In-First-Out (LIFO) principle. Elements are added and removed from the same end.

## Characteristics
- LIFO ordering
- Elements can only be added/removed from the top
- Can be implemented using arrays or linked lists

## Time Complexity
- Push: O(1)
- Pop: O(1)
- Peek: O(1)
- Search: O(n)

## Implementation Examples

### Python
```python
class Stack:
    def __init__(self):
        self.items = []
    
    def push(self, item):
        self.items.append(item)
    
    def pop(self):
        if not self.is_empty():
            return self.items.pop()
    
    def peek(self):
        if not self.is_empty():
            return self.items[-1]
    
    def is_empty(self):
        return len(self.items) == 0

# Usage
stack = Stack()
stack.push(1)
stack.push(2)
top_element = stack.peek()  # Returns 2
removed = stack.pop()       # Returns 2
```

### JavaScript
```javascript
class Stack {
    constructor() {
        this.items = [];
    }
    
    push(element) {
        this.items.push(element);
    }
    
    pop() {
        if (!this.isEmpty()) {
            return this.items.pop();
        }
    }
    
    peek() {
        if (!this.isEmpty()) {
            return this.items[this.items.length - 1];
        }
    }
    
    isEmpty() {
        return this.items.length === 0;
    }
}

// Usage
const stack = new Stack();
stack.push(1);
stack.push(2);
const topElement = stack.peek(); // Returns 2
const removed = stack.pop();     // Returns 2
```

## Common Use Cases
1. Function call stack
2. Expression evaluation
3. Undo operations
4. Browser history
5. Syntax parsing

## Advantages
- Simple implementation
- Efficient operations
- Memory efficient

## Disadvantages
- Limited access (only top element)
- No random access
- Fixed size (if array-based)
