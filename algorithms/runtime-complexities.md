# Common Runtime Complexities

Last Updated: 2025-01-14

## Overview
Runtime complexities describe how algorithm performance scales with input size. Here are the most common complexities from fastest to slowest.

## Types of Runtime Complexities

### 1. Constant Time - O(1)
- Performance is independent of input size
- Executes in the same time regardless of input

Examples:
```python
def get_first(arr):
    return arr[0]  # Always one operation

def check_even(num):
    return num % 2 == 0  # Single calculation
```

Common Use Cases:
- Array index access
- Hash table lookup
- Stack push/pop
- Basic arithmetic operations

### 2. Logarithmic Time - O(log n)
- Performance increases logarithmically with input size
- Common in divide-and-conquer algorithms

Examples:
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
    return -1
```

Common Use Cases:
- Binary search
- Balanced tree operations
- Number of digits in a number
- Divide and conquer algorithms

### 3. Linear Time - O(n)
- Performance scales linearly with input size
- Must process each input element at least once

Examples:
```python
def find_max(arr):
    max_val = float('-inf')
    for num in arr:  # Processes each element once
        max_val = max(max_val, num)
    return max_val
```

Common Use Cases:
- Linear search
- Array traversal
- String processing
- Simple loops

### 4. Linearithmic Time - O(n log n)
- Combination of linear and logarithmic growth
- Common in efficient sorting algorithms

Examples:
```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    
    return merge(left, right)
```

Common Use Cases:
- Merge sort
- Quick sort (average case)
- Heap sort
- Certain divide and conquer algorithms

### 5. Quadratic Time - O(n²)
- Performance grows quadratically with input size
- Often involves nested iterations

Examples:
```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        for j in range(n - 1):  # Nested loops
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
```

Common Use Cases:
- Simple sorting algorithms
- 2D array processing
- Finding all pairs
- Nested loops

### 6. Exponential Time - O(2ⁿ)
- Performance doubles with each additional input
- Often indicates brute force solutions

Examples:
```python
def fibonacci_recursive(n):
    if n <= 1:
        return n
    return fibonacci_recursive(n-1) + fibonacci_recursive(n-2)
```

Common Use Cases:
- Recursive fibonacci
- Power set generation
- Traveling salesman (brute force)
- Subset sum problems

### 7. Factorial Time - O(n!)
- Performance grows factorial with input size
- Usually indicates brute force permutations

Examples:
```python
def generate_permutations(arr):
    if len(arr) <= 1:
        return [arr]
    
    perms = []
    for i in range(len(arr)):
        curr = arr[i]
        remaining = arr[:i] + arr[i+1:]
        
        for p in generate_permutations(remaining):
            perms.append([curr] + p)
    
    return perms
```

Common Use Cases:
- Generating permutations
- Traveling salesman (exact)
- Brute force solutions to NP-complete problems

## Comparison Chart

Input Size (n) | O(1) | O(log n) | O(n) | O(n log n) | O(n²) | O(2ⁿ) | O(n!)
--------------|------|-----------|-------|------------|-------|--------|-------
1            | 1    | 0         | 1     | 0          | 1     | 2      | 1
10           | 1    | 3.32      | 10    | 33.2       | 100   | 1024   | 3.6M
100          | 1    | 6.64      | 100   | 664        | 10K   | 1.27e30| ∞
1000         | 1    | 9.97      | 1K    | 9.97K      | 1M    | 1.07e301| ∞

## Space vs Time Complexity
- Often trade-off between space and time
- Example: Memoization improves time but uses more space
- Consider both when designing algorithms

## Optimization Tips

1. Look for unnecessary loops
   - Can you combine loops?
   - Can you exit early?

2. Use appropriate data structures
   - Hash tables for O(1) lookup
   - Binary search trees for O(log n) operations

3. Consider space-time tradeoffs
   - Caching vs recomputation
   - Memory vs CPU usage

4. Identify bottlenecks
   - Focus on the dominant term
   - Ignore lower-order terms

## Common Patterns

1. Divide and Conquer → Often O(n log n)
2. Single loop → Usually O(n)
3. Nested loops → Usually O(n²)
4. Binary search → O(log n)
5. Recursive with branching → Often exponential
