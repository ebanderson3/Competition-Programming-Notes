Passes through each edge once.

```
WHICH EULERIAN ALGORITHM TO USE?

1. Just need to check if Eulerian exists?
   └─ Use Detection Algorithm (O(V+E))

2. Need to find the actual Eulerian path/circuit?
   ├─ Graph is undirected?
   │   └─ Use Hierholzer's Undirected (O(E))
   └─ Graph is directed?
       └─ Use Hierholzer's Directed (O(E))

3. Graph is not Eulerian but need to make it?
   ├─ Need minimum edges to add?
   │   └─ Use degree analysis (add edges between odd vertices)
   └─ Need minimum cost to traverse all edges?
       └─ Use Chinese Postman (O(V³))

4. Special applications?
   ├─ De Bruijn sequences?
   │   └─ Use De Bruijn generator
   └─ Educational/step-by-step?
       └─ Use Fleury's (but warn about O(E²))

Common Pitfalls:
- Forgetting to check connectivity
- Not handling isolated vertices
- Confusing Eulerian (edges) with Hamiltonian (vertices)
- Using Fleury's in competition (too slow!)
```
# Eulerian Path/Circuit Detection (Undirected)
**What it does:** Determines if an undirected graph has an Eulerian path (visits every edge once) or circuit (path that returns to start)
**Requirements:**
- Undirected graph as adjacency list
- Connected graph (for edges that exist)
- Degree count for each vertex
**When to use:**
- Route planning visiting all roads once
- Drawing figures without lifting pen
- DNA fragment assembly
- Network traversal problems
- Checking if graph can be drawn in one stroke
**Complexity:**
- Time: O(V + E)
- Space: O(V)
**Key insight:** Circuit exists if all vertices have even degree; path exists if exactly 2 vertices have odd degree
```python
def is_eulerian_undirected(graph, n):
    """
    Check if undirected graph has Eulerian path/circuit
    Time: O(V + E)
    Space: O(V)
    Returns: (has_circuit, has_path, start_node)
    """
    # Count degree of each vertex
    degree = [0] * n
    for u in range(n):
        degree[u] = len(graph.get(u, []))
    
    # Count vertices with odd degree
    odd_degree_vertices = []
    for i in range(n):
        if degree[i] % 2 == 1:
            odd_degree_vertices.append(i)
    
    # Check connectivity (only consider vertices with edges)
    def is_connected():
        visited = [False] * n
        start = -1
        for i in range(n):
            if degree[i] > 0:
                start = i
                break
        
        if start == -1:  # No edges
            return True
        
        # DFS from a vertex with edges
        def dfs(v):
            visited[v] = True
            for u in graph.get(v, []):
                if not visited[u]:
                    dfs(u)
        
        dfs(start)
        
        # Check if all vertices with edges are visited
        for i in range(n):
            if degree[i] > 0 and not visited[i]:
                return False
        return True
    
    if not is_connected():
        return False, False, -1
    
    # Eulerian circuit: all vertices have even degree
    if len(odd_degree_vertices) == 0:
        start = next((i for i in range(n) if degree[i] > 0), 0)
        return True, True, start
    
    # Eulerian path: exactly 2 vertices have odd degree
    elif len(odd_degree_vertices) == 2:
        return False, True, odd_degree_vertices[0]
    
    return False, False, -1
```

# Eulerian Path/Circuit Detection (Directed)
**What it does:** Determines if a directed graph has an Eulerian path or circuit considering edge directions
**Requirements:**
- Directed graph as adjacency list
- In-degree and out-degree arrays
- Weakly connected graph
**When to use:**
- One-way street routing
- Process flow optimization
- Directed network traversal
- De Bruijn sequences
- Word chain puzzles
**Complexity:**
- Time: O(V + E)
- Space: O(V)
**Key insight:** Circuit needs in-degree = out-degree for all vertices; path needs exactly one vertex with out-degree = in-degree + 1 and one with in-degree = out-degree + 1
```python
def is_eulerian_directed(graph, n):
    """
    Check if directed graph has Eulerian path/circuit
    Time: O(V + E)
    Space: O(V)
    Returns: (has_circuit, has_path, start_node)
    """
    in_degree = [0] * n
    out_degree = [0] * n
    
    # Calculate in and out degrees
    for u in range(n):
        out_degree[u] = len(graph.get(u, []))
        for v in graph.get(u, []):
            in_degree[v] += 1
    
    # Check if graph is strongly connected (for circuit)
    # or weakly connected (for path)
    def is_weakly_connected():
        # Create undirected version
        undirected = {i: set() for i in range(n)}
        for u in range(n):
            for v in graph.get(u, []):
                undirected[u].add(v)
                undirected[v].add(u)
        
        visited = [False] * n
        start = -1
        for i in range(n):
            if in_degree[i] + out_degree[i] > 0:
                start = i
                break
        
        if start == -1:
            return True
        
        def dfs(v):
            visited[v] = True
            for u in undirected[v]:
                if not visited[u]:
                    dfs(u)
        
        dfs(start)
        
        for i in range(n):
            if (in_degree[i] + out_degree[i] > 0) and not visited[i]:
                return False
        return True
    
    if not is_weakly_connected():
        return False, False, -1
    
    # Count vertices with degree imbalance
    start_vertices = []  # out > in by 1
    end_vertices = []    # in > out by 1
    
    for i in range(n):
        if out_degree[i] - in_degree[i] == 1:
            start_vertices.append(i)
        elif in_degree[i] - out_degree[i] == 1:
            end_vertices.append(i)
        elif in_degree[i] != out_degree[i]:
            return False, False, -1  # Invalid configuration
    
    # Eulerian circuit: all vertices have equal in and out degree
    if len(start_vertices) == 0 and len(end_vertices) == 0:
        start = next((i for i in range(n) if out_degree[i] > 0), 0)
        return True, True, start
    
    # Eulerian path: one start vertex and one end vertex
    elif len(start_vertices) == 1 and len(end_vertices) == 1:
        return False, True, start_vertices[0]
    
    return False, False, -1
```