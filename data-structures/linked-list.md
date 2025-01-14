# Linked List Data Structure

Last Updated: 2025-01-14

## Definition
A linked list is a linear data structure where elements are stored in nodes, and each node points to the next node in the sequence. The last node points to null.

## Types
- Singly Linked List: Each node points to the next node
- Doubly Linked List: Each node points to both next and previous nodes
- Circular Linked List: Last node points back to the first node

## Time Complexity
- Access: O(n)
- Search: O(n)
- Insertion: O(1) if position known
- Deletion: O(1) if position known

## Implementation Examples

### Python
```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None

class LinkedList:
    def __init__(self):
        self.head = None
    
    def append(self, data):
        new_node = Node(data)
        if not self.head:
            self.head = new_node
            return
        current = self.head
        while current.next:
            current = current.next
        current.next = new_node
    
    def delete(self, data):
        if not self.head:
            return
        if self.head.data == data:
            self.head = self.head.next
            return
        current = self.head
        while current.next:
            if current.next.data == data:
                current.next = current.next.next
                return
            current = current.next

# Usage
linked_list = LinkedList()
linked_list.append(1)
linked_list.append(2)
linked_list.delete(1)
```

### JavaScript
```javascript
class Node {
    constructor(data) {
        this.data = data;
        this.next = null;
    }
}

class LinkedList {
    constructor() {
        this.head = null;
    }
    
    append(data) {
        const newNode = new Node(data);
        if (!this.head) {
            this.head = newNode;
            return;
        }
        let current = this.head;
        while (current.next) {
            current = current.next;
        }
        current.next = newNode;
    }
    
    delete(data) {
        if (!this.head) return;
        if (this.head.data === data) {
            this.head = this.head.next;
            return;
        }
        let current = this.head;
        while (current.next) {
            if (current.next.data === data) {
                current.next = current.next.next;
                return;
            }
            current = current.next;
        }
    }
}

// Usage
const list = new LinkedList();
list.append(1);
list.append(2);
list.delete(1);
```

## Common Use Cases
1. Implementation of other data structures (stacks, queues)
2. Undo functionality in software
3. Memory allocation
4. Music playlist
5. Browser history

## Advantages
- Dynamic size
- Efficient insertion/deletion
- No memory waste
- Flexible memory allocation

## Disadvantages
- No random access
- Extra memory for pointers
- Not cache friendly
- Sequential access only
