# Array Data Structure

Last Updated: 2025-01-14

## Definition
An array is a linear data structure that stores elements in contiguous memory locations. It's the simplest and most widely used data structure.

## Characteristics
- Fixed size (in most languages)
- Constant-time access to elements
- Elements stored in continuous memory
- Index-based access

## Time Complexity
- Access: O(1)
- Search: O(n)
- Insertion: O(n)
- Deletion: O(n)

## Implementation Examples

### Python
```python
# Basic array (list in Python)
numbers = [1, 2, 3, 4, 5]

# Common operations
numbers.append(6)        # Add element
numbers.pop()           # Remove last element
numbers.insert(0, 0)    # Insert at index
element = numbers[0]    # Access element
length = len(numbers)   # Array length
```

### JavaScript
```javascript
// Basic array
let numbers = [1, 2, 3, 4, 5];

// Common operations
numbers.push(6);        # Add element
numbers.pop();          # Remove last element
numbers.unshift(0);     # Insert at beginning
let element = numbers[0]; # Access element
let length = numbers.length; # Array length
```

### Java
```java
// Basic array
int[] numbers = new int[5];
// or
Integer[] numbers = {1, 2, 3, 4, 5};

// Common operations
// Note: Java arrays are fixed size
numbers[0] = 1;         // Set element
int element = numbers[0]; // Access element
int length = numbers.length; // Array length

// For dynamic size, use ArrayList
ArrayList<Integer> list = new ArrayList<>();
list.add(6);           // Add element
list.remove(0);        // Remove element
```

## Common Use Cases
1. Storing sequential data
2. Temporary storage
3. Buffer for other data structures
4. Lookup tables
5. Return values from functions

## Advantages
- Simple and easy to use
- Random access
- Fast access if index known

## Disadvantages
- Fixed size (in some languages)
- Insertion and deletion are expensive
- Waste of memory if elements removed
