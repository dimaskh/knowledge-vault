# Graph Representations

Last Updated: 2025-01-14

## Overview

Graphs can be represented in several ways, with the two most common being adjacency lists and adjacency matrices. Each has its own advantages and use cases.

## Adjacency List

### Definition

An adjacency list represents a graph as a collection of lists or arrays, where each vertex maintains a list of its adjacent vertices.

### Implementation

#### Python
```python
class AdjacencyList:
    def __init__(self):
        self.graph = {}
    
    def add_vertex(self, vertex):
        """Add a vertex to the graph"""
        if vertex not in self.graph:
            self.graph[vertex] = []
    
    def add_edge(self, from_vertex, to_vertex, directed=False, weight=None):
        """Add an edge between vertices"""
        if from_vertex not in self.graph:
            self.add_vertex(from_vertex)
        if to_vertex not in self.graph:
            self.add_vertex(to_vertex)
        
        # Add edge with optional weight
        if weight is not None:
            self.graph[from_vertex].append((to_vertex, weight))
        else:
            self.graph[from_vertex].append(to_vertex)
        
        # If undirected, add reverse edge
        if not directed:
            if weight is not None:
                self.graph[to_vertex].append((from_vertex, weight))
            else:
                self.graph[to_vertex].append(from_vertex)
    
    def get_neighbors(self, vertex):
        """Get all neighbors of a vertex"""
        return self.graph.get(vertex, [])
    
    def remove_edge(self, from_vertex, to_vertex, directed=False):
        """Remove an edge between vertices"""
        if from_vertex in self.graph and to_vertex in self.graph:
            self.graph[from_vertex] = [v for v in self.graph[from_vertex] 
                                     if v != to_vertex]
            if not directed:
                self.graph[to_vertex] = [v for v in self.graph[to_vertex] 
                                       if v != from_vertex]

# Usage
graph = AdjacencyList()
graph.add_edge('A', 'B')
graph.add_edge('B', 'C')
print(graph.get_neighbors('A'))  # Output: ['B']
```

#### JavaScript
```javascript
class AdjacencyList {
    constructor() {
        this.graph = new Map();
    }
    
    addVertex(vertex) {
        if (!this.graph.has(vertex)) {
            this.graph.set(vertex, []);
        }
    }
    
    addEdge(fromVertex, toVertex, directed = false, weight = null) {
        if (!this.graph.has(fromVertex)) {
            this.addVertex(fromVertex);
        }
        if (!this.graph.has(toVertex)) {
            this.addVertex(toVertex);
        }
        
        // Add edge with optional weight
        if (weight !== null) {
            this.graph.get(fromVertex).push({vertex: toVertex, weight});
        } else {
            this.graph.get(fromVertex).push(toVertex);
        }
        
        // If undirected, add reverse edge
        if (!directed) {
            if (weight !== null) {
                this.graph.get(toVertex).push({vertex: fromVertex, weight});
            } else {
                this.graph.get(toVertex).push(fromVertex);
            }
        }
    }
    
    getNeighbors(vertex) {
        return this.graph.get(vertex) || [];
    }
    
    removeEdge(fromVertex, toVertex, directed = false) {
        if (this.graph.has(fromVertex) && this.graph.has(toVertex)) {
            this.graph.set(fromVertex, 
                this.graph.get(fromVertex).filter(v => v !== toVertex));
            if (!directed) {
                this.graph.set(toVertex, 
                    this.graph.get(toVertex).filter(v => v !== fromVertex));
            }
        }
    }
}

// Usage
const graph = new AdjacencyList();
graph.addEdge('A', 'B');
graph.addEdge('B', 'C');
console.log(graph.getNeighbors('A')); // Output: ['B']
```

### Time Complexity
- Space: O(V + E)
- Add Vertex: O(1)
- Add Edge: O(1)
- Remove Edge: O(E)
- Query Edge: O(degree(v))
- Get All Edges: O(E)

### Advantages
- Space efficient for sparse graphs
- Faster to iterate over edges
- More efficient for most graph algorithms
- Dynamic size

### Disadvantages
- Slower edge existence checking
- More complex implementation
- No constant-time access to specific edges

## Adjacency Matrix

### Definition

An adjacency matrix represents a graph using a 2D array of size V×V, where V is the number of vertices. Each cell [i][j] represents an edge from vertex i to vertex j.

### Implementation

#### Python
```python
class AdjacencyMatrix:
    def __init__(self, vertices):
        self.V = vertices
        self.graph = [[0] * vertices for _ in range(vertices)]
        self.vertex_map = {}  # Maps vertex names to indices
        self.vertex_count = 0
    
    def add_vertex(self, vertex):
        """Add a vertex to the graph"""
        if vertex not in self.vertex_map and self.vertex_count < self.V:
            self.vertex_map[vertex] = self.vertex_count
            self.vertex_count += 1
    
    def add_edge(self, from_vertex, to_vertex, directed=False, weight=1):
        """Add an edge between vertices"""
        if from_vertex in self.vertex_map and to_vertex in self.vertex_map:
            i = self.vertex_map[from_vertex]
            j = self.vertex_map[to_vertex]
            self.graph[i][j] = weight
            if not directed:
                self.graph[j][i] = weight
    
    def get_neighbors(self, vertex):
        """Get all neighbors of a vertex"""
        if vertex in self.vertex_map:
            i = self.vertex_map[vertex]
            neighbors = []
            for j in range(self.V):
                if self.graph[i][j] != 0:
                    for v, index in self.vertex_map.items():
                        if index == j:
                            neighbors.append(v)
            return neighbors
        return []
    
    def remove_edge(self, from_vertex, to_vertex, directed=False):
        """Remove an edge between vertices"""
        if from_vertex in self.vertex_map and to_vertex in self.vertex_map:
            i = self.vertex_map[from_vertex]
            j = self.vertex_map[to_vertex]
            self.graph[i][j] = 0
            if not directed:
                self.graph[j][i] = 0

# Usage
graph = AdjacencyMatrix(3)
graph.add_vertex('A')
graph.add_vertex('B')
graph.add_vertex('C')
graph.add_edge('A', 'B')
print(graph.get_neighbors('A'))  # Output: ['B']
```

#### JavaScript
```javascript
class AdjacencyMatrix {
    constructor(vertices) {
        this.V = vertices;
        this.graph = Array(vertices).fill().map(() => Array(vertices).fill(0));
        this.vertexMap = new Map();
        this.vertexCount = 0;
    }
    
    addVertex(vertex) {
        if (!this.vertexMap.has(vertex) && this.vertexCount < this.V) {
            this.vertexMap.set(vertex, this.vertexCount++);
        }
    }
    
    addEdge(fromVertex, toVertex, directed = false, weight = 1) {
        if (this.vertexMap.has(fromVertex) && this.vertexMap.has(toVertex)) {
            const i = this.vertexMap.get(fromVertex);
            const j = this.vertexMap.get(toVertex);
            this.graph[i][j] = weight;
            if (!directed) {
                this.graph[j][i] = weight;
            }
        }
    }
    
    getNeighbors(vertex) {
        if (this.vertexMap.has(vertex)) {
            const i = this.vertexMap.get(vertex);
            const neighbors = [];
            for (let j = 0; j < this.V; j++) {
                if (this.graph[i][j] !== 0) {
                    for (const [v, index] of this.vertexMap.entries()) {
                        if (index === j) {
                            neighbors.push(v);
                        }
                    }
                }
            }
            return neighbors;
        }
        return [];
    }
    
    removeEdge(fromVertex, toVertex, directed = false) {
        if (this.vertexMap.has(fromVertex) && this.vertexMap.has(toVertex)) {
            const i = this.vertexMap.get(fromVertex);
            const j = this.vertexMap.get(toVertex);
            this.graph[i][j] = 0;
            if (!directed) {
                this.graph[j][i] = 0;
            }
        }
    }
}

// Usage
const graph = new AdjacencyMatrix(3);
graph.addVertex('A');
graph.addVertex('B');
graph.addVertex('C');
graph.addEdge('A', 'B');
console.log(graph.getNeighbors('A')); // Output: ['B']
```

### Time Complexity
- Space: O(V²)
- Add Vertex: O(V²) for matrix resize
- Add Edge: O(1)
- Remove Edge: O(1)
- Query Edge: O(1)
- Get All Edges: O(V²)

### Advantages
- Simple implementation
- Constant time edge lookup
- Easy to implement graph algorithms
- Better for dense graphs

### Disadvantages
- Wastes space for sparse graphs
- Fixed size (needs resize)
- Always uses O(V²) space
- Slower to add new vertices

## Comparison

### When to Use Adjacency List
1. Sparse graphs (E << V²)
2. Need to save space
3. Need to iterate through edges
4. Graph size is dynamic
5. Memory is a constraint

### When to Use Adjacency Matrix
1. Dense graphs (E ≈ V²)
2. Need fast edge weight lookups
3. Need to check if edges exist
4. Graph is static
5. Memory is not a constraint

## Common Operations

### 1. Graph Traversal
```python
# DFS with Adjacency List
def dfs_list(graph, start, visited=None):
    if visited is None:
        visited = set()
    visited.add(start)
    for neighbor in graph.get_neighbors(start):
        if neighbor not in visited:
            dfs_list(graph, neighbor, visited)

# DFS with Adjacency Matrix
def dfs_matrix(graph, start, visited=None):
    if visited is None:
        visited = set()
    visited.add(start)
    start_idx = graph.vertex_map[start]
    for i in range(graph.V):
        if graph.graph[start_idx][i] and i not in visited:
            for vertex, idx in graph.vertex_map.items():
                if idx == i and vertex not in visited:
                    dfs_matrix(graph, vertex, visited)
```

### 2. Finding Connected Components
```python
def find_components_list(graph):
    visited = set()
    components = []
    
    for vertex in graph.graph:
        if vertex not in visited:
            component = set()
            dfs_list(graph, vertex, component)
            components.append(component)
            visited.update(component)
    
    return components
```

## Best Practices

1. Choose Representation Based on:
   - Graph density
   - Required operations
   - Memory constraints
   - Performance requirements

2. Consider Hybrid Approaches:
   - Use matrix for dense subgraphs
   - Use lists for sparse regions
   - Combine for optimal performance

3. Memory Management:
   - Use sparse matrix for large, sparse graphs
   - Implement dynamic resizing when needed
   - Clean up unused vertices and edges

4. Performance Optimization:
   - Cache frequently accessed edges
   - Use appropriate data structures for vertex storage
   - Implement efficient iteration methods
