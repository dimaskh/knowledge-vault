# Recursion and Searching Algorithms

Last Updated: 2025-01-14

## Recursion

### Tail Recursion

#### Description
- Recursive call is the last operation in the function
- Can be optimized by compiler to use constant space
- More efficient than non-tail recursion

#### Implementation
```python
# Non-tail recursive factorial
def factorial(n):
    if n == 0:
        return 1
    return n * factorial(n - 1)  # Must multiply after recursive call

# Tail recursive factorial
def factorial_tail(n, accumulator=1):
    if n == 0:
        return accumulator
    return factorial_tail(n - 1, n * accumulator)  # Last operation is recursive call
```

### Non-Tail Recursion

#### Description
- Recursive call is not the last operation
- Requires maintaining call stack
- More intuitive but less efficient

#### Implementation
```python
# Non-tail recursive Fibonacci
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)  # Must add after recursive calls

# Converting to tail recursion
def fibonacci_tail(n, a=0, b=1):
    if n == 0:
        return a
    return fibonacci_tail(n - 1, b, a + b)
```

## Searching Algorithms

### Binary Search

#### Description
- Works on sorted arrays
- Divides search interval in half
- Very efficient for large sorted datasets

#### Implementation
```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    
    while left <= right:
        mid = (left + right) // 2
        
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return -1  # Element not found

# Recursive implementation
def binary_search_recursive(arr, target, left, right):
    if left > right:
        return -1
    
    mid = (left + right) // 2
    
    if arr[mid] == target:
        return mid
    elif arr[mid] < target:
        return binary_search_recursive(arr, target, mid + 1, right)
    else:
        return binary_search_recursive(arr, target, left, mid - 1)
```

#### Time Complexity
- Time: O(log n)
- Space: O(1) iterative, O(log n) recursive

### Linear Search

#### Description
- Simple search algorithm
- Checks each element sequentially
- Works on unsorted arrays

#### Implementation
```python
def linear_search(arr, target):
    for i in range(len(arr)):
        if arr[i] == target:
            return i
    return -1

# With sentinel
def linear_search_sentinel(arr, target):
    last = arr[-1]
    arr[-1] = target  # Set sentinel
    i = 0
    
    while arr[i] != target:
        i += 1
    
    arr[-1] = last  # Restore original value
    if i < len(arr) - 1 or arr[-1] == target:
        return i
    return -1
```

#### Time Complexity
- Time: O(n)
- Space: O(1)

## Best Practices

### Recursion

1. When to Use Tail Recursion
   - When stack space is a concern
   - For functions that can be naturally tail-recursive
   - When compiler supports tail-call optimization

2. When to Use Non-Tail Recursion
   - When code clarity is more important
   - For naturally recursive problems
   - When stack space isn't a concern

3. Converting to Tail Recursion
   - Use accumulator parameters
   - Move computations to parameter passing
   - Consider iteration as alternative

### Searching

1. When to Use Binary Search
   - Sorted data
   - Large datasets
   - Frequent searches

2. When to Use Linear Search
   - Unsorted data
   - Small datasets
   - One-time searches
   - When data structure doesn't support random access

## Common Pitfalls

### Recursion
1. Stack overflow
2. Redundant calculations
3. Not having proper base case
4. Incorrect accumulator initialization

### Binary Search
1. Infinite loops due to incorrect mid calculation
2. Not handling array bounds correctly
3. Using on unsorted array
4. Integer overflow in mid calculation

### Linear Search
1. Not handling edge cases
2. Inefficient for large datasets
3. Not considering better alternatives
4. Missing optimization opportunities

## Optimization Techniques

### Recursion
1. Memoization for overlapping subproblems
2. Tail recursion optimization
3. Converting to iteration when appropriate

### Binary Search
1. Using unsigned integers
2. Mid calculation: left + (right - left) // 2
3. Binary search variants (lower/upper bound)

### Linear Search
1. Using sentinel values
2. Early termination conditions
3. Block search for cache efficiency
