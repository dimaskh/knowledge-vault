# Backtracking Algorithms

Last Updated: 2025-01-14

## Overview
Backtracking is an algorithmic technique that considers searching every possible combination in order to solve a computational problem.

## Finding Hamiltonian Paths

### Description
- Finds a path that visits each vertex exactly once
- NP-complete problem
- Uses systematic backtracking

### Implementation
```python
def hamiltonian_path(graph, N):
    path = [-1] * N
    path[0] = 0  # Start from vertex 0
    
    def is_valid(v, pos, path):
        # Check if vertex can be added at position
        if v not in graph[path[pos-1]]:
            return False
        
        # Check if vertex is already in path
        if v in path:
            return False
        
        return True
    
    def hamiltonian_util(path, pos):
        if pos == N:
            return True
        
        for v in range(1, N):
            if is_valid(v, pos, path):
                path[pos] = v
                
                if hamiltonian_util(path, pos + 1):
                    return True
                    
                path[pos] = -1
        
        return False
    
    if not hamiltonian_util(path, 1):
        return None
    return path
```

### Time Complexity
- O(N!) in worst case

## N-Queens Problem

### Description
- Place N queens on NxN chessboard
- No two queens should threaten each other
- Classic backtracking problem

### Implementation
```python
def solve_n_queens(n):
    def is_safe(board, row, col):
        # Check row on left side
        for j in range(col):
            if board[row][j] == 1:
                return False
        
        # Check upper diagonal
        for i, j in zip(range(row, -1, -1), range(col, -1, -1)):
            if board[i][j] == 1:
                return False
        
        # Check lower diagonal
        for i, j in zip(range(row, n, 1), range(col, -1, -1)):
            if board[i][j] == 1:
                return False
        
        return True
    
    def solve_util(board, col):
        if col >= n:
            return True
        
        for i in range(n):
            if is_safe(board, i, col):
                board[i][col] = 1
                
                if solve_util(board, col + 1):
                    return True
                    
                board[i][col] = 0
        
        return False
    
    board = [[0 for x in range(n)] for y in range(n)]
    
    if not solve_util(board, 0):
        return None
    return board
```

### Time Complexity
- O(N!) in worst case

## Maze Solving Problem

### Description
- Find path from start to end in maze
- Can move in four directions
- Must avoid obstacles

### Implementation
```python
def solve_maze(maze):
    def is_valid(x, y):
        return 0 <= x < len(maze) and 0 <= y < len(maze[0]) and maze[x][y] == 1
    
    def solve_util(x, y, solution):
        if x == len(maze)-1 and y == len(maze[0])-1:
            solution[x][y] = 1
            return True
        
        if is_valid(x, y):
            solution[x][y] = 1
            
            # Move right
            if solve_util(x, y+1, solution):
                return True
            
            # Move down
            if solve_util(x+1, y, solution):
                return True
            
            # If no path works, backtrack
            solution[x][y] = 0
            return False
        
        return False
    
    solution = [[0 for y in range(len(maze[0]))] for x in range(len(maze))]
    
    if not solve_util(0, 0, solution):
        return None
    return solution
```

### Time Complexity
- O(2^(N²)) in worst case

## Knight's Tour Problem

### Description
- Visit all squares on chessboard
- Using valid knight moves
- Each square visited exactly once

### Implementation
```python
def knights_tour(n):
    def is_valid(x, y, board):
        return 0 <= x < n and 0 <= y < n and board[x][y] == -1
    
    def solve_kt(board, curr_x, curr_y, moves, move_x, move_y, pos):
        if pos == n**2:
            return True
        
        for i in range(8):
            next_x = curr_x + move_x[i]
            next_y = curr_y + move_y[i]
            
            if is_valid(next_x, next_y, board):
                board[next_x][next_y] = pos
                
                if solve_kt(board, next_x, next_y, moves, move_x, move_y, pos+1):
                    return True
                    
                board[next_x][next_y] = -1
        
        return False
    
    board = [[-1 for x in range(n)] for y in range(n)]
    move_x = [2, 1, -1, -2, -2, -1, 1, 2]
    move_y = [1, 2, 2, 1, -1, -2, -2, -1]
    
    board[0][0] = 0
    if not solve_kt(board, 0, 0, n**2, move_x, move_y, 1):
        return None
    return board
```

### Time Complexity
- O(8^(N²)) in worst case

## Rabin-Karp Algorithm

### Description
- String matching algorithm
- Uses hashing to find pattern in text
- Efficient for multiple pattern search

### Implementation
```python
def rabin_karp(text, pattern):
    def calculate_hash(string, length):
        hash_value = 0
        for i in range(length):
            hash_value += ord(string[i]) * (prime**(length-1-i))
        return hash_value
    
    n, m = len(text), len(pattern)
    prime = 101
    pattern_hash = calculate_hash(pattern, m)
    text_hash = calculate_hash(text[:m], m)
    
    for i in range(n - m + 1):
        if pattern_hash == text_hash:
            if text[i:i+m] == pattern:
                return i
        
        if i < n - m:
            text_hash = (text_hash - ord(text[i]) * (prime**(m-1))) * prime
            text_hash += ord(text[i+m])
    
    return -1
```

### Time Complexity
- Average case: O(n+m)
- Worst case: O(nm)

## Best Practices

### 1. Problem Analysis
- Identify base cases
- Define constraints clearly
- Plan backtracking conditions

### 2. Implementation
- Use efficient pruning
- Implement state validation
- Consider memory usage

### 3. Optimization
- Use branch and bound
- Implement forward checking
- Consider problem-specific heuristics

## Common Applications

1. Constraint Satisfaction
   - Sudoku
   - Crosswords
   - Map coloring

2. Path Finding
   - Maze solving
   - Robot navigation
   - Circuit design

3. Optimization Problems
   - Traveling Salesman
   - Subset Sum
   - Graph coloring

## Common Pitfalls

1. Infinite Recursion
   - Missing base cases
   - Incorrect state updates
   - Poor constraint checking

2. Performance Issues
   - Inefficient pruning
   - Unnecessary backtracking
   - Poor state representation

3. Implementation Problems
   - Stack overflow
   - Memory leaks
   - Incorrect state restoration
