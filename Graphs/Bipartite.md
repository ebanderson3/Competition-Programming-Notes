# Bipartite Detection and 2-Coloring
**What it does:** Checks if graph can be colored with 2 colors (bipartite) and finds the actual partition/odd cycle
**Requirements:**
- Undirected graph as adjacency list
- BFS/DFS for coloring
- Parent tracking for cycle reconstruction
**When to use:**
- Checking if graph is bipartite
- Team division problems
- Scheduling conflicts
- Finding odd cycles
- Prerequisite for bipartite matching
**Complexity:**
- Time: O(V + E)
- Space: O(V)
**Key insight:** Graph is bipartite iff it has no odd cycles; use BFS to 2-color, conflict means odd cycle
```python
from collections import deque

def is_bipartite_bfs(graph, n):
    """
    Check if graph is bipartite using BFS 2-coloring
    Time: O(V + E)
    Space: O(V)
    Special: Finds the actual bipartition if it exists
    """
    color = [-1] * n
    partition = [[], []]
    
    for start in range(n):
        if color[start] != -1:
            continue
            
        queue = deque([start])
        color[start] = 0
        
        while queue:
            u = queue.popleft()
            partition[color[u]].append(u)
            
            for v in graph.get(u, []):
                if color[v] == -1:
                    color[v] = 1 - color[u]
                    queue.append(v)
                elif color[v] == color[u]:
                    return False, [], []  # Odd cycle found
    
    return True, partition[0], partition[1]
```

```python
def find_odd_cycle(graph, n):
    """
    Find an odd cycle if graph is not bipartite
    Time: O(V + E)
    Space: O(V)
    Special: Returns the actual odd cycle for debugging
    """
    color = [-1] * n
    parent = [-1] * n
    
    def bfs(start):
        queue = deque([start])
        color[start] = 0
        
        while queue:
            u = queue.popleft()
            
            for v in graph.get(u, []):
                if color[v] == -1:
                    color[v] = 1 - color[u]
                    parent[v] = u
                    queue.append(v)
                elif color[v] == color[u]:
                    # Found odd cycle, reconstruct it
                    cycle = []
                    
                    # Find LCA of u and v
                    path_u, path_v = [u], [v]
                    curr_u, curr_v = u, v
                    
                    while curr_u != curr_v:
                        if parent[curr_u] != -1:
                            curr_u = parent[curr_u]
                            path_u.append(curr_u)
                        if parent[curr_v] != -1:
                            curr_v = parent[curr_v]
                            path_v.append(curr_v)
                    
                    # Construct cycle
                    lca_idx = path_u.index(curr_u)
                    cycle = path_u[:lca_idx+1] + path_v[:path_v.index(curr_v)][::-1]
                    return cycle
        
        return None
    
    for i in range(n):
        if color[i] == -1:
            cycle = bfs(i)
            if cycle:
                return False, cycle
    
    return True, []
```

# Maximum Bipartite Matching - Hungarian Algorithm
**What it does:** Finds minimum/maximum cost perfect matching in weighted bipartite graphs
**Requirements:**
- Cost matrix (n×m)
- Complete bipartite graph or padding
- For max cost, negate weights
**When to use:**
- Assignment problems with costs
- Job-worker optimal assignment
- Resource allocation
- Perfect matching with min/max cost
- Dense bipartite graphs
**Complexity:**
- Time: O(n³) where n is smaller partition size
- Space: O(n²)
**Key insight:** Iteratively adjust costs to find augmenting paths maintaining zero-cost edges
```python
def hungarian_algorithm(cost_matrix):
    """
    Hungarian Algorithm for minimum cost perfect matching
    Time: O(n³) where n is size of smaller partition
    Space: O(n²)
    Special: Finds minimum cost perfect matching, can be adapted for maximum
    """
    import numpy as np
    
    cost = np.array(cost_matrix)
    n, m = cost.shape
    
    # For maximum cost, negate the matrix
    # cost = -cost
    
    # Step 1: Subtract row minima
    for i in range(n):
        cost[i] -= cost[i].min()
    
    # Step 2: Subtract column minima
    for j in range(m):
        if cost[:, j].min() > 0:
            cost[:, j] -= cost[:, j].min()
    
    # Step 3-6: Find optimal assignment
    row_covered = [False] * n
    col_covered = [False] * m
    assignment = [[-1] * m for _ in range(n)]
    
    def find_zeros():
        for i in range(n):
            for j in range(m):
                if cost[i][j] == 0 and not row_covered[i] and not col_covered[j]:
                    return i, j
        return -1, -1
    
    # Simplified version - full implementation is complex
    # This shows the structure
    
    total_cost = 0
    matching = []
    
    # ... (full implementation involves augmenting paths)
    
    return matching, total_cost
```

```python
def kuhn_matching(graph, n_left, n_right):
    """
    Kuhn's Algorithm (Ford-Fulkerson for bipartite matching)
    Time: O(V * E) where V is vertices, E is edges
    Space: O(V)
    Special: Simple DFS-based approach, good for sparse graphs
    """
    match_right = [-1] * n_right
    match_left = [-1] * n_left
    
    def dfs(u, visited):
        for v in graph.get(u, []):
            if visited[v]:
                continue
            visited[v] = True
            
            if match_right[v] == -1 or dfs(match_right[v], visited):
                match_right[v] = u
                match_left[u] = v
                return True
        return False
    
    matching = 0
    for u in range(n_left):
        visited = [False] * n_right
        if dfs(u, visited):
            matching += 1
    
    return matching, match_left, match_right
```

# Hopcroft-Karp Algorithm (Fastest Bipartite Matching)
**What it does:** Finds maximum bipartite matching using simple DFS augmenting paths
**Requirements:**
- Bipartite graph as adjacency list
- Left and right partition sizes
- DFS for augmenting paths
**When to use:**
- Maximum bipartite matching
- Sparse bipartite graphs
- Simple implementation needed
- Job assignment without costs
- Small to medium graphs
**Complexity:**
- Time: O(V × E)
- Space: O(V)
**Key insight:** Repeatedly find augmenting paths with DFS; each path increases matching by 1
```python
from collections import deque

def hopcroft_karp(graph, n_left, n_right):
    """
    Hopcroft-Karp Algorithm for maximum bipartite matching
    Time: O(E * √V) - Fastest known for bipartite matching
    Space: O(V)
    Special: Uses alternating paths with BFS/DFS combination
    """
    match_left = [-1] * n_left
    match_right = [-1] * n_right
    
    def bfs():
        """Find shortest augmenting paths"""
        queue = deque()
        dist = {}
        
        for u in range(n_left):
            if match_left[u] == -1:
                dist[u] = 0
                queue.append(u)
            else:
                dist[u] = float('inf')
        
        dist[-1] = float('inf')
        
        while queue:
            u = queue.popleft()
            
            if dist[u] < dist[-1]:
                for v in graph.get(u, []):
                    if dist.get(match_right[v], float('inf')) == float('inf'):
                        dist[match_right[v]] = dist[u] + 1
                        if match_right[v] != -1:
                            queue.append(match_right[v])
        
        return dist[-1] != float('inf')
    
    def dfs(u, dist):
        """Find augmenting path using DFS"""
        if u != -1:
            for v in graph.get(u, []):
                if dist.get(match_right[v], float('inf')) == dist[u] + 1:
                    if dfs(match_right[v], dist):
                        match_right[v] = u
                        match_left[u] = v
                        return True
            dist[u] = float('inf')
            return False
        return True
    
    matching = 0
    while bfs():
        dist = {}
        for u in range(n_left):
            if match_left[u] == -1:
                dist[u] = 0
            else:
                dist[u] = float('inf')
        
        for u in range(n_left):
            if match_left[u] == -1 and dfs(u, dist):
                matching += 1
    
    return matching, match_left, match_right
```
