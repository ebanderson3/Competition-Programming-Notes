# Cycle Detection in Undirected Graph (Using DFS)
**What it does:** Detects if an undirected graph contains any cycle using depth-first search
**Requirements:**
- Undirected graph as adjacency list
- Track parent during DFS traversal
**When to use:**
- Checking if graph is a tree (connected + no cycles)
- Detecting redundant connections
- Network loop detection
- Graph validation problems
**Complexity:**
- Time: O(V + E)
- Space: O(V) for visited array + recursion stack
**Key insight:** If we visit a node that's already visited and it's not our parent, we found a cycle
```python
def has_cycle_undirected_dfs(graph, n):
    """
    Detects cycle in undirected graph using DFS
    graph: adjacency list representation {node: [neighbors]}
    n: number of nodes (0 to n-1)
    Returns: True if cycle exists, False otherwise
    """
    visited = [False] * n
    
    def dfs(node, parent):
        visited[node] = True
        
        for neighbor in graph.get(node, []):
            if not visited[neighbor]:
                if dfs(neighbor, node):
                    return True
            elif parent != neighbor:
                # Found a back edge (cycle)
                return True
        return False
    
    # Check all components (for disconnected graphs)
    for i in range(n):
        if not visited[i]:
            if dfs(i, -1):
                return True
    return False
```

# Cycle Detection in Undirected Graph (Using Union-Find)
**What it does:** Detects cycles in undirected graph by checking if edge endpoints are already connected
**Requirements:**
- Edge list representation
- Union-Find data structure
**When to use:**
- Online cycle detection (edges added incrementally)
- When edges are given as list
- Finding redundant connections
- Kruskal's MST algorithm
**Complexity:**
- Time: O(E × α(V)) where α is inverse Ackermann function ≈ O(E)
- Space: O(V) for Union-Find structure
**Key insight:** If two nodes are already in same component when adding edge, it creates a cycle
```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # Path compression
        return self.parent[x]
    
    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py:
            return False  # Already in same set (cycle detected)
        
        # Union by rank
        if self.rank[px] < self.rank[py]:
            self.parent[px] = py
        elif self.rank[px] > self.rank[py]:
            self.parent[py] = px
        else:
            self.parent[py] = px
            self.rank[px] += 1
        return True

def has_cycle_undirected_uf(edges, n):
    """
    Detects cycle in undirected graph using Union-Find
    edges: list of edges [(u, v), ...]
    n: number of nodes (0 to n-1)
    Returns: True if cycle exists, False otherwise
    """
    uf = UnionFind(n)
    
    for u, v in edges:
        if not uf.union(u, v):
            return True  # Cycle detected
    return False
```

# Find All Cycles in Directed Graph
**What it does:** Finds and returns all cycles in a directed graph using DFS coloring
**Requirements:**
- Directed graph as adjacency list
- Three-color DFS (white=0, gray=1, black=2)
- Path tracking during DFS
**When to use:**
- Dependency cycle detection
- Deadlock detection
- Finding all circular references
- Analyzing feedback loops
- Course prerequisite validation
**Complexity:**
- Time: O(V + E) for detection, O(V × (V + E)) to find all cycles
- Space: O(V) for colors and path
**Key insight:** Use three colors: white (unvisited), gray (in current path), black (finished); cycle exists when reaching gray node
```python
def find_all_cycles_directed(graph, n):
    """
    Finds all cycles in a directed graph
    Returns: List of cycles (each cycle is a list of nodes)
    """
    color = [0] * n
    path = []
    cycles = []
    
    def dfs(node):
        if color[node] == 2:  # Already processed
            return
        if color[node] == 1:  # Found cycle
            # Extract cycle from path
            cycle_start = path.index(node)
            cycles.append(path[cycle_start:] + [node])
            return
        
        color[node] = 1
        path.append(node)
        
        for neighbor in graph.get(node, []):
            dfs(neighbor)
        
        path.pop()
        color[node] = 2
    
    for i in range(n):
        if color[i] == 0:
            dfs(i)
    
    return cycles
```

# Examples
```python
# Example 1: Undirected graph
graph_undirected = {
    0: [1, 2],
    1: [0, 2],
    2: [0, 1, 3],
    3: [2]
}
print(has_cycle_undirected_dfs(graph_undirected, 4))  # True

# Example 2: Directed graph
graph_directed = {
    0: [1],
    1: [2],
    2: [3],
    3: [1]  # Creates cycle: 1->2->3->1
}
print(has_cycle_directed_dfs(graph_directed, 4))  # True

# Example 3: Using Union-Find for undirected
edges = [(0, 1), (1, 2), (2, 0)]  # Triangle cycle
print(has_cycle_undirected_uf(edges, 3))  # True
```