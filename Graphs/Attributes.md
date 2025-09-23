```
WHICH ATTRIBUTE TO CALCULATE?

1. Basic Structure Analysis?
   ├─ Degree distribution → calculate_degrees()
   ├─ Sources/sinks → degree_statistics()
   └─ Self-loops/multi-edges → find_multi_edges()

2. Node Importance?
   ├─ Simple importance → degree_centrality()
   ├─ Path importance → betweenness_centrality()
   ├─ Distance importance → closeness_centrality()
   └─ Subgraph importance → k_core_decomposition()

3. Distance Metrics?
   ├─ All distances from node → BFS/distance_distribution()
   ├─ Graph diameter → graph_diameter_radius_center()
   └─ Eccentricity → eccentricity()

4. Clustering/Density?
   ├─ Triangle counting → count_triangles()
   ├─ Clustering coefficient → clustering_coefficient()
   └─ Dense subgraphs → k_core_decomposition()

5. Special Properties?
   ├─ Bipartiteness → is_bipartite()
   ├─ Edge classification → classify_edges_dfs()
   └─ Connectivity → node_connectivity()

OPTIMIZATION TIPS:
- Cache degree calculations if used multiple times
- Use BFS for unweighted distance metrics
- Early termination when specific threshold reached
- Approximate algorithms for very large graphs
```
<div class="page-break" style="page-break-before: always;"></div>

# Basic Degree Calculations
```python
def calculate_degrees(graph, n, directed=False):
    """
    Calculate degree metrics for all nodes
    Time: O(V + E)
    Space: O(V)
    """
    if directed:
        in_degree = [0] * n
        out_degree = [0] * n
        
        for u in range(n):
            out_degree[u] = len(graph.get(u, []))
            for v in graph.get(u, []):
                in_degree[v] += 1
        
        return in_degree, out_degree
    else:
        degree = [0] * n
        for u in range(n):
            degree[u] = len(graph.get(u, []))
        return degree
```
<div class="page-break" style="page-break-before: always;"></div>

# Node Centrality Measures
```python
from collections import deque

def betweenness_centrality(graph, n):
    """
    Calculate betweenness centrality for all nodes
    Time: O(V * E) for unweighted graphs
    Space: O(V²)
    Measures how often a node appears on shortest paths
    """
    centrality = [0.0] * n
    
    for s in range(n):
        # Single-source shortest paths
        stack = []
        pred = [[] for _ in range(n)]
        sigma = [0] * n  # Number of shortest paths
        sigma[s] = 1
        dist = [-1] * n
        dist[s] = 0
        
        queue = deque([s])
        
        while queue:
            v = queue.popleft()
            stack.append(v)
            
            for w in graph.get(v, []):
                if dist[w] < 0:
                    dist[w] = dist[v] + 1
                    queue.append(w)
                
                if dist[w] == dist[v] + 1:
                    sigma[w] += sigma[v]
                    pred[w].append(v)
        
        # Accumulation
        delta = [0.0] * n
        while stack:
            w = stack.pop()
            for v in pred[w]:
                delta[v] += (sigma[v] / sigma[w]) * (1 + delta[w])
            if w != s:
                centrality[w] += delta[w]
    
    # Normalize
    if n > 2:
        scale = 1.0 / ((n - 1) * (n - 2))
        centrality = [c * scale for c in centrality]
    
    return centrality

def closeness_centrality(graph, n):
    """
    Calculate closeness centrality (inverse of average distance)
    Time: O(V * (V + E))
    Space: O(V)
    """
    centrality = []
    
    for node in range(n):
        # BFS from node
        dist = [-1] * n
        dist[node] = 0
        queue = deque([node])
        
        while queue:
            u = queue.popleft()
            for v in graph.get(u, []):
                if dist[v] == -1:
                    dist[v] = dist[u] + 1
                    queue.append(v)
        
        # Calculate closeness
        reachable = [d for d in dist if d > 0]
        if reachable:
            avg_dist = sum(reachable) / len(reachable)
            centrality.append(1.0 / avg_dist)
        else:
            centrality.append(0.0)
    
    return centrality

def degree_centrality(graph, n):
    """
    Simple degree-based centrality
    Time: O(V + E)
    Space: O(V)
    """
    degree = calculate_degrees(graph, n, False)
    max_possible = n - 1
    return [d / max_possible if max_possible > 0 else 0 for d in degree]
```
<div class="page-break" style="page-break-before: always;"></div>

# Edge Classification and Properties
```python
def classify_edges_dfs(graph, n):
    """
    Classify edges as tree, back, forward, or cross edges
    Time: O(V + E)
    Space: O(V + E)
    For directed graphs
    """
    color = [0] * n  # 0: white, 1: gray, 2: black
    discovery = [0] * n
    finish = [0] * n
    time = [0]
    parent = [-1] * n
    
    edge_types = {
        'tree': [],
        'back': [],
        'forward': [],
        'cross': []
    }
    
    def dfs(u):
        color[u] = 1
        time[0] += 1
        discovery[u] = time[0]
        
        for v in graph.get(u, []):
            if color[v] == 0:  # White - tree edge
                parent[v] = u
                edge_types['tree'].append((u, v))
                dfs(v)
            elif color[v] == 1:  # Gray - back edge
                edge_types['back'].append((u, v))
            else:  # Black
                if discovery[u] < discovery[v]:  # Forward edge
                    edge_types['forward'].append((u, v))
                else:  # Cross edge
                    edge_types['cross'].append((u, v))
        
        color[u] = 2
        time[0] += 1
        finish[u] = time[0]
    
    for i in range(n):
        if color[i] == 0:
            dfs(i)
    
    return edge_types, discovery, finish

def find_multi_edges(graph, n):
    """
    Find parallel edges and self-loops
    Time: O(E)
    Space: O(E)
    """
    edge_count = {}
    self_loops = []
    multi_edges = []
    
    for u in range(n):
        for v in graph.get(u, []):
            if u == v:
                self_loops.append(u)
            else:
                edge = tuple(sorted([u, v]))
                edge_count[edge] = edge_count.get(edge, 0) + 1
    
    for edge, count in edge_count.items():
        if count > 1:
            multi_edges.append((*edge, count))
    
    return self_loops, multi_edges
```
<div class="page-break" style="page-break-before: always;"></div>

# Node Distance and Eccentricity
```python
def eccentricity(graph, n):
    """
    Calculate eccentricity (max distance) for each node
    Time: O(V * (V + E))
    Space: O(V)
    """
    ecc = []
    
    for node in range(n):
        # BFS to find max distance
        dist = [-1] * n
        dist[node] = 0
        queue = deque([node])
        max_dist = 0
        
        while queue:
            u = queue.popleft()
            for v in graph.get(u, []):
                if dist[v] == -1:
                    dist[v] = dist[u] + 1
                    max_dist = max(max_dist, dist[v])
                    queue.append(v)
        
        # Check if graph is connected from this node
        if -1 in dist:
            ecc.append(float('inf'))  # Not all nodes reachable
        else:
            ecc.append(max_dist)
    
    return ecc

def graph_diameter_radius_center(graph, n):
    """
    Calculate diameter, radius, and center of graph
    Time: O(V * (V + E))
    Space: O(V)
    """
    ecc = eccentricity(graph, n)
    
    # Filter out infinity values for connected component
    finite_ecc = [e for e in ecc if e != float('inf')]
    
    if not finite_ecc:
        return None, None, []
    
    diameter = max(finite_ecc)
    radius = min(finite_ecc)
    center = [i for i in range(n) if ecc[i] == radius]
    
    return diameter, radius, center

def distance_distribution(graph, n, source):
    """
    Get distribution of distances from source
    Time: O(V + E)
    Space: O(V)
    """
    dist = [-1] * n
    dist[source] = 0
    queue = deque([source])
    
    while queue:
        u = queue.popleft()
        for v in graph.get(u, []):
            if dist[v] == -1:
                dist[v] = dist[u] + 1
                queue.append(v)
    
    # Create distribution
    distribution = {}
    for d in dist:
        if d >= 0:
            distribution[d] = distribution.get(d, 0) + 1
    
    return dist, distribution
```
<div class="page-break" style="page-break-before: always;"></div>

# Clustering and Triangle Counting
```python
def count_triangles(graph, n):
    """
    Count triangles in undirected graph
    Time: O(V * d²) where d is max degree
    Space: O(1)
    """
    triangles = 0
    
    for u in range(n):
        neighbors_u = set(graph.get(u, []))
        for v in graph.get(u, []):
            if v > u:  # Avoid counting same triangle multiple times
                for w in graph.get(v, []):
                    if w > v and w in neighbors_u:
                        triangles += 1
    
    return triangles

def clustering_coefficient(graph, n):
    """
    Calculate local and global clustering coefficients
    Time: O(V * d²)
    Space: O(V)
    Measures how connected a node's neighbors are
    """
    local_clustering = []
    
    for v in range(n):
        neighbors = list(graph.get(v, []))
        k = len(neighbors)
        
        if k < 2:
            local_clustering.append(0.0)
            continue
        
        # Count edges between neighbors
        edges = 0
        neighbor_set = set(neighbors)
        
        for i, u in enumerate(neighbors):
            for w in graph.get(u, []):
                if w in neighbor_set and w in neighbors[i+1:]:
                    edges += 1
        
        # Local clustering coefficient
        max_edges = k * (k - 1) / 2
        local_clustering.append(edges / max_edges if max_edges > 0 else 0)
    
    # Global clustering coefficient
    global_clustering = sum(local_clustering) / n if n > 0 else 0
    
    return local_clustering, global_clustering

def node_triangle_count(graph, n):
    """
    Count triangles each node participates in
    Time: O(V * d²)
    Space: O(V)
    """
    triangle_count = [0] * n
    
    for u in range(n):
        neighbors_u = set(graph.get(u, []))
        for v in graph.get(u, []):
            for w in graph.get(v, []):
                if w != u and w in neighbors_u:
                    triangle_count[u] += 1
    
    # Each triangle is counted 6 times (2 per vertex)
    return [count // 2 for count in triangle_count]
```

# K-Core Decomposition
```python
def k_core_decomposition(graph, n):
    """
    Find k-core decomposition (maximal subgraph with min degree k)
    Time: O(V + E)
    Space: O(V)
    """
    from collections import defaultdict
    
    # Calculate initial degrees
    degree = [len(graph.get(v, [])) for v in range(n)]
    core = [0] * n
    
    # Create buckets for each degree
    degree_buckets = defaultdict(set)
    for v in range(n):
        degree_buckets[degree[v]].add(v)
    
    # Process vertices in order of degree
    curr_core = 0
    remaining = set(range(n))
    
    while remaining:
        # Find minimum degree
        min_deg = min(degree[v] for v in remaining)
        curr_core = max(curr_core, min_deg)
        
        # Process all vertices with this degree
        if min_deg in degree_buckets:
            while degree_buckets[min_deg]:
                v = degree_buckets[min_deg].pop()
                if v in remaining:
                    core[v] = curr_core
                    remaining.remove(v)
                    
                    # Update neighbors' degrees
                    for u in graph.get(v, []):
                        if u in remaining:
                            old_deg = degree[u]
                            degree[u] -= 1
                            
                            if old_deg in degree_buckets:
                                degree_buckets[old_deg].discard(u)
                            if degree[u] >= 0:
                                degree_buckets[degree[u]].add(u)
    
    return core

def get_k_core(graph, n, k):
    """
    Extract k-core subgraph
    Time: O(V + E)
    Space: O(V)
    """
    core_values = k_core_decomposition(graph, n)
    k_core_nodes = [v for v in range(n) if core_values[v] >= k]
    
    k_core_graph = {}
    for v in k_core_nodes:
        k_core_graph[v] = [u for u in graph.get(v, []) if core_values[u] >= k]
    
    return k_core_nodes, k_core_graph
```
<div class="page-break" style="page-break-before: always;"></div>

# Bipartiteness and Coloring Check
```python
def is_bipartite(graph, n):
    """
    Check if graph is bipartite and return coloring
    Time: O(V + E)
    Space: O(V)
    """
    color = [-1] * n
    
    def bfs(start):
        queue = deque([start])
        color[start] = 0
        
        while queue:
            u = queue.popleft()
            for v in graph.get(u, []):
                if color[v] == -1:
                    color[v] = 1 - color[u]
                    queue.append(v)
                elif color[v] == color[u]:
                    return False
        return True
    
    for i in range(n):
        if color[i] == -1:
            if not bfs(i):
                return False, []
    
    # Partition nodes by color
    partition = [[], []]
    for i in range(n):
        if color[i] >= 0:
            partition[color[i]].append(i)
    
    return True, partition

def chromatic_polynomial_small(graph, n):
    """
    Calculate chromatic polynomial for small graphs
    Time: O(2^n * n²) - exponential!
    Space: O(2^n)
    Only use for n <= 15
    """
    if n > 15:
        return None  # Too large
    
    # Use inclusion-exclusion on edge subsets
    edges = []
    for u in range(n):
        for v in graph.get(u, []):
            if u < v:
                edges.append((u, v))
    
    m = len(edges)
    poly_coeffs = [0] * (n + 1)
    
    # Iterate through all edge subsets
    for mask in range(1 << m):
        # Count components in subgraph
        uf = list(range(n))
        
        def find(x):
            if uf[x] != x:
                uf[x] = find(uf[x])
            return uf[x]
        
        edge_count = 0
        for i in range(m):
            if mask & (1 << i):
                u, v = edges[i]
                pu, pv = find(u), find(v)
                if pu != pv:
                    uf[pu] = pv
                edge_count += 1
        
        components = len(set(find(i) for i in range(n)))
        sign = 1 if edge_count % 2 == 0 else -1
        poly_coeffs[components] += sign
    
    return poly_coeffs
```
<div class="page-break" style="page-break-before: always;"></div>

# Path and Connectivity Metrics
```python
def node_connectivity(graph, n, s, t):
    """
    Find node connectivity between s and t (Menger's theorem)
    Time: O(V * E²) using max flow
    Space: O(V + E)
    """
    # Create auxiliary graph with node splitting
    # Each node v becomes v_in and v_out with edge capacity 1
    from collections import defaultdict
    import heapq
    
    # Build flow network
    flow_graph = defaultdict(lambda: defaultdict(int))
    
    for u in range(n):
        if u != s and u != t:
            # Split node: u_in = u, u_out = u + n
            flow_graph[u][u + n] = 1
        
        for v in graph.get(u, []):
            if u == s:
                flow_graph[s][v] = 1
            elif v == t:
                flow_graph[u + n][t] = 1
            else:
                flow_graph[u + n][v] = 1
    
    # Run max flow (simplified Ford-Fulkerson)
    def bfs(source, sink):
        parent = {source: None}
        visited = {source}
        queue = deque([source])
        
        while queue:
            u = queue.popleft()
            
            for v in flow_graph[u]:
                if v not in visited and flow_graph[u][v] > 0:
                    visited.add(v)
                    parent[v] = u
                    if v == sink:
                        return parent
                    queue.append(v)
        return None
    
    max_flow = 0
    while True:
        parent = bfs(s, t)
        if not parent:
            break
        
        # Find min capacity
        path_flow = float('inf')
        v = t
        while parent[v] is not None:
            u = parent[v]
            path_flow = min(path_flow, flow_graph[u][v])
            v = u
        
        # Update flow
        v = t
        while parent[v] is not None:
            u = parent[v]
            flow_graph[u][v] -= path_flow
            flow_graph[v][u] += path_flow
            v = u
        
        max_flow += path_flow
    
    return max_flow

def all_pairs_connectivity(graph, n):
    """
    Check connectivity between all pairs
    Time: O(V³)
    Space: O(V²)
    """
    # Use Floyd-Warshall approach
    connected = [[False] * n for _ in range(n)]
    
    # Initialize with direct edges
    for u in range(n):
        connected[u][u] = True
        for v in graph.get(u, []):
            connected[u][v] = True
    
    # Transitive closure
    for k in range(n):
        for i in range(n):
            for j in range(n):
                connected[i][j] = connected[i][j] or (connected[i][k] and connected[k][j])
    
    return connected
```