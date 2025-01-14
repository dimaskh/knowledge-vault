# Sorting Algorithms

Last Updated: 2025-01-14

## Overview

Sorting algorithms arrange elements in a specific order, typically in ascending or descending order. Each algorithm has its own advantages and use cases.

## 1. Bubble Sort

### Description
- Repeatedly steps through the list
- Compares adjacent elements and swaps them if they are in wrong order
- Simple but inefficient for large lists

### Implementation

```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        # Flag to optimize if array is already sorted
        swapped = False
        
        for j in range(0, n - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
                swapped = True
        
        # If no swapping occurred, array is already sorted
        if not swapped:
            break
    
    return arr
```

### Complexity
- Time Complexity: O(n²)
- Space Complexity: O(1)
- Best Case: O(n) when array is already sorted

### Use Cases
- Educational purposes
- Small datasets
- When simplicity is preferred over efficiency

## 2. Selection Sort

### Description
- Divides input into sorted and unsorted regions
- Repeatedly finds minimum element from unsorted region
- Places it at the end of sorted region

### Implementation

```python
def selection_sort(arr):
    n = len(arr)
    for i in range(n):
        min_idx = i
        for j in range(i + 1, n):
            if arr[j] < arr[min_idx]:
                min_idx = j
        
        # Swap found minimum element with first element
        arr[i], arr[min_idx] = arr[min_idx], arr[i]
    
    return arr
```

### Complexity
- Time Complexity: O(n²)
- Space Complexity: O(1)
- Best Case: O(n²)

### Use Cases
- Small arrays
- When memory is limited
- When number of writes needs to be minimized

## 3. Insertion Sort

### Description
- Builds final sorted array one item at a time
- Takes each element and inserts it into its correct position
- Efficient for small data sets and nearly sorted arrays

### Implementation

```python
def insertion_sort(arr):
    for i in range(1, len(arr)):
        key = arr[i]
        j = i - 1
        while j >= 0 and arr[j] > key:
            arr[j + 1] = arr[j]
            j -= 1
        arr[j + 1] = key
    
    return arr
```

### Complexity
- Time Complexity: O(n²)
- Space Complexity: O(1)
- Best Case: O(n) when array is nearly sorted

### Use Cases
- Small datasets
- Nearly sorted arrays
- Online sorting (sorting as data arrives)

## 4. Heap Sort

### Description
- Uses a binary heap data structure
- First builds a max-heap
- Repeatedly extracts maximum element and maintains heap property

### Implementation

```python
def heapify(arr, n, i):
    largest = i
    left = 2 * i + 1
    right = 2 * i + 2
    
    if left < n and arr[left] > arr[largest]:
        largest = left
    
    if right < n and arr[right] > arr[largest]:
        largest = right
    
    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        heapify(arr, n, largest)

def heap_sort(arr):
    n = len(arr)
    
    # Build max heap
    for i in range(n // 2 - 1, -1, -1):
        heapify(arr, n, i)
    
    # Extract elements from heap
    for i in range(n - 1, 0, -1):
        arr[0], arr[i] = arr[i], arr[0]
        heapify(arr, i, 0)
    
    return arr
```

### Complexity
- Time Complexity: O(n log n)
- Space Complexity: O(1)
- Best Case: O(n log n)

### Use Cases
- When stable sort isn't required
- When consistent performance is needed
- When memory usage is a concern

## 5. Quick Sort

### Description
- Uses divide-and-conquer strategy
- Picks a 'pivot' element
- Partitions array around pivot
- Recursively sorts sub-arrays

### Implementation

```python
def quick_sort(arr):
    if len(arr) <= 1:
        return arr
    
    def partition(low, high):
        pivot = arr[high]
        i = low - 1
        
        for j in range(low, high):
            if arr[j] <= pivot:
                i += 1
                arr[i], arr[j] = arr[j], arr[i]
        
        arr[i + 1], arr[high] = arr[high], arr[i + 1]
        return i + 1
    
    def quick_sort_helper(low, high):
        if low < high:
            pi = partition(low, high)
            quick_sort_helper(low, pi - 1)
            quick_sort_helper(pi + 1, high)
    
    quick_sort_helper(0, len(arr) - 1)
    return arr
```

### Complexity
- Time Complexity: O(n log n) average, O(n²) worst case
- Space Complexity: O(log n)
- Best Case: O(n log n)

### Use Cases
- Large datasets
- When average case performance is important
- When in-place sorting is needed

## 6. Merge Sort

### Description
- Uses divide-and-conquer strategy
- Divides array into two halves
- Recursively sorts them
- Merges sorted halves

### Implementation

```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    
    return merge(left, right)

def merge(left, right):
    result = []
    i = j = 0
    
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    
    result.extend(left[i:])
    result.extend(right[j:])
    return result
```

### Complexity
- Time Complexity: O(n log n)
- Space Complexity: O(n)
- Best Case: O(n log n)

### Use Cases
- When stable sorting is needed
- When predictable performance is required
- External sorting (sorting large files)

## Comparison Table

Algorithm      | Time (Average) | Time (Worst) | Space | Stable | In-Place
--------------|----------------|--------------|-------|---------|----------
Bubble Sort   | O(n²)         | O(n²)        | O(1)  | Yes     | Yes
Selection Sort| O(n²)         | O(n²)        | O(1)  | No      | Yes
Insertion Sort| O(n²)         | O(n²)        | O(1)  | Yes     | Yes
Heap Sort     | O(n log n)    | O(n log n)   | O(1)  | No      | Yes
Quick Sort    | O(n log n)    | O(n²)        | O(log n)| No    | Yes
Merge Sort    | O(n log n)    | O(n log n)   | O(n)  | Yes     | No

## Best Practices

### 1. Choosing the Right Algorithm

- For small arrays (n < 50):
  - Use Insertion Sort
  - Simple implementation
  - Good cache performance

- For medium arrays:
  - Use Quick Sort
  - Good average performance
  - In-place sorting

- For large arrays:
  - Use Merge Sort if stability needed
  - Use Quick Sort if memory is a concern
  - Use Heap Sort if worst-case performance matters

### 2. Implementation Tips

- Use built-in sorting functions for production code
- Consider hybrid approaches (e.g., Timsort)
- Handle edge cases (empty arrays, single elements)
- Consider stability requirements

### 3. Optimization Strategies

- Choose good pivot for Quick Sort
- Use insertion sort for small subarrays
- Implement iterative versions when possible
- Consider cache efficiency

### 4. Common Pitfalls

- Not considering stability requirements
- Choosing wrong algorithm for data size
- Ignoring space complexity
- Not handling edge cases
