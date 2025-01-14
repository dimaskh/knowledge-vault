# Graph Algorithms

Last Updated: 2025-01-14

## Breadth First Search (BFS)

### Description
- Explores a graph level by level
- Uses a queue to track vertices to visit
- Visits all vertices at current distance before moving to next level

### Implementation
```python
from collections import deque

def bfs(graph, start):
    visited = set()
    queue = deque([start])
    visited.add(start)
    
    while queue:
        vertex = queue.popleft()
        print(vertex, end=' ')  # Process vertex
        
        for neighbor in graph[vertex]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
```

### Time Complexity
- Time: O(V + E) where V is vertices and E is edges
- Space: O(V) for queue and visited set

### Use Cases
- Finding shortest path in unweighted graph
- Level-order traversal
- Social networking (finding connections)

## Depth First Search (DFS)

### Description
- Explores a graph by going as deep as possible
- Uses recursion or stack
- Backtracks when no more unvisited adjacent vertices

### Implementation
```python
def dfs(graph, start, visited=None):
    if visited is None:
        visited = set()
    
    visited.add(start)
    print(start, end=' ')  # Process vertex
    
    for neighbor in graph[start]:
        if neighbor not in visited:
            dfs(graph, neighbor, visited)
```

### Time Complexity
- Time: O(V + E)
- Space: O(V) for recursion stack

### Use Cases
- Topological sorting
- Cycle detection
- Path finding

## Bellman-Ford's Algorithm

### Description
- Finds shortest paths from source to all vertices
- Handles negative edge weights
- Can detect negative cycles

### Implementation
```python
def bellman_ford(graph, source):
    # Initialize distances
    distances = {vertex: float('infinity') for vertex in graph}
    distances[source] = 0
    
    # Relax edges V-1 times
    for _ in range(len(graph) - 1):
        for u in graph:
            for v, weight in graph[u].items():
                if distances[u] + weight < distances[v]:
                    distances[v] = distances[u] + weight
    
    # Check for negative cycles
    for u in graph:
        for v, weight in graph[u].items():
            if distances[u] + weight < distances[v]:
                raise ValueError("Graph contains negative cycle")
    
    return distances
```

### Time Complexity
- Time: O(VE)
- Space: O(V)

### Use Cases
- Networks with negative weights
- Forex arbitrage detection
- Distance vector routing

## Dijkstra's Algorithm

### Description
- Finds shortest paths from source to all vertices
- Uses priority queue for efficient selection
- Only works with non-negative edge weights

### Implementation
```python
import heapq

def dijkstra(graph, source):
    distances = {vertex: float('infinity') for vertex in graph}
    distances[source] = 0
    pq = [(0, source)]
    visited = set()
    
    while pq:
        current_distance, current_vertex = heapq.heappop(pq)
        
        if current_vertex in visited:
            continue
            
        visited.add(current_vertex)
        
        for neighbor, weight in graph[current_vertex].items():
            distance = current_distance + weight
            
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                heapq.heappush(pq, (distance, neighbor))
    
    return distances
```

### Time Complexity
- Time: O((V + E) log V) with binary heap
- Space: O(V)

### Use Cases
- GPS navigation
- Network routing
- Social networks (shortest path)

## A* Algorithm

### Description
- Informed search algorithm
- Uses heuristic to guide search
- Combines actual cost and estimated cost

### Implementation
```python
def heuristic(node, goal):
    # Example: Manhattan distance for grid
    return abs(node[0] - goal[0]) + abs(node[1] - goal[1])

def a_star(graph, start, goal):
    frontier = [(0, start)]
    came_from = {start: None}
    cost_so_far = {start: 0}
    
    while frontier:
        current = heapq.heappop(frontier)[1]
        
        if current == goal:
            break
            
        for next_node, cost in graph[current].items():
            new_cost = cost_so_far[current] + cost
            
            if next_node not in cost_so_far or new_cost < cost_so_far[next_node]:
                cost_so_far[next_node] = new_cost
                priority = new_cost + heuristic(next_node, goal)
                heapq.heappush(frontier, (priority, next_node))
                came_from[next_node] = current
    
    return came_from, cost_so_far
```

### Time Complexity
- Time: O((V + E) log V) but often better in practice
- Space: O(V)

### Use Cases
- Pathfinding in games
- Robot navigation
- Route planning
