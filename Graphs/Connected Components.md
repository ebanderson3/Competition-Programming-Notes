All of these are basically in $O(V + E)$ time.
# Find connected components in an undirected graph (DFS)
**What it does:** Identifies all connected components in an undirected graph and assigns component IDs to nodes
**Requirements:**
- Undirected graph as adjacency list
- Number of nodes n
**When to use:**
- Graph partitioning
- Finding isolated subgraphs
- Network analysis
- Counting disconnected parts
- Preprocessing for other algorithms
**Complexity:**
- Time: O(V + E)
- Space: O(V)
**Key insight:** Run DFS from each unvisited node; each DFS traversal finds one complete component
```python
def connected_components_dfs(graph, n):
    """
    Find connected components using DFS
    graph: adjacency list {node: [neighbors]}
    n: number of nodes (0 to n-1)
    Returns: (num_components, component_id for each node)
    """
    visited = [False] * n
    component = [-1] * n
    comp_id = 0
    
    def dfs(node, comp_id):
        visited[node] = True
        component[node] = comp_id
        
        for neighbor in graph.get(node, []):
            if not visited[neighbor]:
                dfs(neighbor, comp_id)
    
    # Find all components
    for i in range(n):
        if not visited[i]:
            dfs(i, comp_id)
            comp_id += 1
    
    return comp_id, component

def get_component_nodes(graph, n):
    """
    Get list of nodes in each component
    Returns: List of lists, each containing nodes of a component
    """
    num_comp, component = connected_components_dfs(graph, n)
    components = [[] for _ in range(num_comp)]
    
    for node in range(n):
        components[component[node]].append(node)
    
    return components
```

# Strongly Connected Components - Tarjan's Algorithm
**What it does:** Finds all strongly connected components (SCCs) in a directed graph where every node can reach every other node in the SCC
**Requirements:**
- Directed graph as adjacency list
- Stack for tracking current path
- Low-link values for each node
**When to use:**
- Condensing directed graphs
- Finding cycles in directed graphs
- Dependency analysis
- Dead code detection
- Social network analysis (mutual connections)
**Complexity:**
- Time: O(V + E) - single DFS pass
- Space: O(V)
**Key insight:** Uses DFS with stack and low-link values; when low-link equals index, found SCC root
```python
def scc_tarjan(graph, n):
    """
    Find strongly connected components using Tarjan's algorithm
    Time: O(V + E) - Single DFS pass
    Returns: List of SCCs (each SCC is a list of nodes)
    """
    index_counter = [0]
    stack = []
    lowlink = [0] * n
    index = [0] * n
    on_stack = [False] * n
    index_initialized = [False] * n
    sccs = []
    
    def strongconnect(v):
        index[v] = index_counter[0]
        lowlink[v] = index_counter[0]
        index_counter[0] += 1
        index_initialized[v] = True
        stack.append(v)
        on_stack[v] = True
        
        for w in graph.get(v, []):
            if not index_initialized[w]:
                strongconnect(w)
                lowlink[v] = min(lowlink[v], lowlink[w])
            elif on_stack[w]:
                lowlink[v] = min(lowlink[v], index[w])
        
        # If v is a root node, pop the stack and create SCC
        if lowlink[v] == index[v]:
            scc = []
            while True:
                w = stack.pop()
                on_stack[w] = False
                scc.append(w)
                if w == v:
                    break
            sccs.append(scc)
    
    for v in range(n):
        if not index_initialized[v]:
            strongconnect(v)
    
    return sccs
```

# Bridge Finding
**What it does:** Finds all bridges (cut edges) in an undirected graph - edges whose removal disconnects the graph
**Requirements:**
- Undirected graph as adjacency list
- Discovery time and low values
- Parent tracking in DFS tree
**When to use:**
- Network vulnerability analysis
- Finding critical connections
- Circuit design weak points
- Transportation bottlenecks
- Graph connectivity problems
**Complexity:**
- Time: O(V + E)
- Space: O(V)
**Key insight:** Edge u-v is a bridge if low\[v] > disc\[u] (v cannot reach back to u or earlier without using edge u-v)
```python
def find_bridges(graph, n):
    """
    Find bridges (cut edges) in undirected graph
    Removing a bridge disconnects the graph
    """
    visited = [False] * n
    disc = [0] * n  # Discovery times
    low = [0] * n   # Low values
    parent = [-1] * n
    bridges = []
    time = [0]
    
    def dfs(u):
        visited[u] = True
        disc[u] = low[u] = time[0]
        time[0] += 1
        
        for v in graph.get(u, []):
            if not visited[v]:
                parent[v] = u
                dfs(v)
                
                low[u] = min(low[u], low[v])
                
                # Check if edge u-v is a bridge
                if low[v] > disc[u]:
                    bridges.append((u, v))
            
            elif v != parent[u]:  # Back edge
                low[u] = min(low[u], disc[v])
    
    for i in range(n):
        if not visited[i]:
            dfs(i)
    
    return bridges
```

# Articulation Point Finding (Cut Vertices)
**What it does:** Finds all articulation points - vertices whose removal increases the number of connected components
**Requirements:**
- Undirected graph as adjacency list
- Discovery time and low values
- Parent and children tracking
**When to use:**
- Finding critical nodes in networks
- Vulnerability analysis
- Network reliability
- Finding bottleneck vertices
- Graph robustness testing
**Complexity:**
- Time: O(V + E)
- Space: O(V)
**Key insight:** Node u is articulation point if: (1) root with >1 child, or (2) non-root where child v cannot reach ancestors of u (low\[v] >= disc\[u])
```python
def find_articulation_points(graph, n):
    """
    Find articulation points (cut vertices) in undirected graph
    Removing an articulation point increases number of components
    """
    visited = [False] * n
    disc = [0] * n
    low = [0] * n
    parent = [-1] * n
    ap = [False] * n  # Articulation points
    time = [0]
    
    def dfs(u):
        children = 0
        visited[u] = True
        disc[u] = low[u] = time[0]
        time[0] += 1
        
        for v in graph.get(u, []):
            if not visited[v]:
                children += 1
                parent[v] = u
                dfs(v)
                
                low[u] = min(low[u], low[v])
                
                # u is articulation point if:
                # (1) u is root and has more than 1 child
                if parent[u] == -1 and children > 1:
                    ap[u] = True
                
                # (2) u is not root and low[v] >= disc[u]
                if parent[u] != -1 and low[v] >= disc[u]:
                    ap[u] = True
            
            elif v != parent[u]:
                low[u] = min(low[u], disc[v])
    
    for i in range(n):
        if not visited[i]:
            dfs(i)
    
    return [i for i in range(n) if ap[i]]
```