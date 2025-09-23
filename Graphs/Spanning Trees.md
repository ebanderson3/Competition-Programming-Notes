```
WHICH SPANNING TREE ALGORITHM TO USE?

1. Input Format?
   ├─ Edge list → Kruskal's (natural fit)
   ├─ Adjacency list → Prim's (natural fit)
   └─ Points in plane → Euclidean MST

2. Graph Density?
   ├─ Sparse (E ≈ V) → Kruskal's
   ├─ Dense (E ≈ V²) → Prim's with Fibonacci heap
   └─ Medium → Either works

3. Special Requirements?
   ├─ Need second-best MST → Second-best algorithm
   ├─ Minimize maximum edge → Bottleneck MST (= regular MST)
   ├─ Degree constraints → Degree-constrained MST
   ├─ Multiple components → Minimum Spanning Forest
   ├─ Parallel processing → Borůvka's
   └─ Dynamic updates → Dynamic MST structure

4. Problem Variations?
   ├─ Maximum spanning tree → Negate weights or sort descending
   ├─ Product of weights → Use log(weights) as edge weights
   ├─ K-th best MST → Extension of second-best
   └─ Unique MST check → Check if any edge swap gives same weight

5. Optimization Level?
   ├─ Competition (speed coding) → Kruskal's (easiest to code)
   ├─ Large graphs → Prim's with optimized heap
   └─ Theoretical best → Depends on graph properties

COMMON PITFALLS:
- Forgetting to check if graph is connected
- Not handling duplicate edges
- Integer overflow with large weights
- Using O(V²) Prim's on sparse graphs
```
# Kruskal's Algorithm (Edge-based MST)
```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py:
            return False
        if self.rank[px] < self.rank[py]:
            self.parent[px] = py
        elif self.rank[px] > self.rank[py]:
            self.parent[py] = px
        else:
            self.parent[py] = px
            self.rank[px] += 1
        return True

def kruskal(edges, n):
    """
    Kruskal's algorithm for Minimum Spanning Tree
    Time: O(E log E) for sorting + O(E α(V)) for union-find
    Space: O(V)
    edges: [(weight, u, v), ...]
    Returns: (mst_weight, mst_edges)
    """
    edges.sort()  # Sort by weight
    uf = UnionFind(n)
    mst_weight = 0
    mst_edges = []
    
    for weight, u, v in edges:
        if uf.union(u, v):
            mst_weight += weight
            mst_edges.append((u, v, weight))
            if len(mst_edges) == n - 1:
                break
    
    return mst_weight, mst_edges

def kruskal_maximum(edges, n):
    """
    Kruskal's for Maximum Spanning Tree
    Time: O(E log E)
    Space: O(V)
    """
    edges.sort(reverse=True)  # Sort by weight descending
    uf = UnionFind(n)
    mst_weight = 0
    mst_edges = []
    
    for weight, u, v in edges:
        if uf.union(u, v):
            mst_weight += weight
            mst_edges.append((u, v, weight))
            if len(mst_edges) == n - 1:
                break
    
    return mst_weight, mst_edges
```
<div class="page-break" style="page-break-before: always;"></div>

# Prim's Algorithm (Vertex-based MST)
```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py:
            return False
        if self.rank[px] < self.rank[py]:
            self.parent[px] = py
        elif self.rank[px] > self.rank[py]:
            self.parent[py] = px
        else:
            self.parent[py] = px
            self.rank[px] += 1
        return True

def kruskal(edges, n):
    """
    Kruskal's algorithm for Minimum Spanning Tree
    Time: O(E log E) for sorting + O(E α(V)) for union-find
    Space: O(V)
    edges: [(weight, u, v), ...]
    Returns: (mst_weight, mst_edges)
    """
    edges.sort()  # Sort by weight
    uf = UnionFind(n)
    mst_weight = 0
    mst_edges = []
    
    for weight, u, v in edges:
        if uf.union(u, v):
            mst_weight += weight
            mst_edges.append((u, v, weight))
            if len(mst_edges) == n - 1:
                break
    
    return mst_weight, mst_edges

def kruskal_maximum(edges, n):
    """
    Kruskal's for Maximum Spanning Tree
    Time: O(E log E)
    Space: O(V)
    """
    edges.sort(reverse=True)  # Sort by weight descending
    uf = UnionFind(n)
    mst_weight = 0
    mst_edges = []
    
    for weight, u, v in edges:
        if uf.union(u, v):
            mst_weight += weight
            mst_edges.append((u, v, weight))
            if len(mst_edges) == n - 1:
                break
    
    return mst_weight, mst_edges
```
<div class="page-break" style="page-break-before: always;"></div>

# Minimum Bottleneck Spanning Tree
```python
def bottleneck_spanning_tree(edges, n):
    """
    Find spanning tree minimizing maximum edge weight
    Time: O(E log E)
    Space: O(V)
    Note: Any MST is also a bottleneck spanning tree
    """
    edges.sort()
    uf = UnionFind(n)
    max_edge_weight = 0
    mst_edges = []
    
    for weight, u, v in edges:
        if uf.union(u, v):
            max_edge_weight = max(max_edge_weight, weight)
            mst_edges.append((u, v, weight))
            if len(mst_edges) == n - 1:
                break
    
    return max_edge_weight, mst_edges

def bottleneck_path_query(graph, n, queries):
    """
    Answer bottleneck path queries after building MST
    Time: O(E log E) + O(Q × V) where Q is number of queries
    """
    # Build MST first
    edges = []
    for u in range(n):
        for v, w in graph.get(u, []):
            if u < v:
                edges.append((w, u, v))
    
    _, mst_edges = bottleneck_spanning_tree(edges, n)
    
    # Build MST graph
    mst_graph = {i: [] for i in range(n)}
    for u, v, w in mst_edges:
        mst_graph[u].append((v, w))
        mst_graph[v].append((u, w))
    
    # Answer queries
    results = []
    for start, end in queries:
        # Find maximum edge on path in MST
        visited = [False] * n
        
        def dfs(u, target, max_weight):
            if u == target:
                return max_weight
            
            visited[u] = True
            for v, w in mst_graph[u]:
                if not visited[v]:
                    result = dfs(v, target, max(max_weight, w))
                    if result != -1:
                        return result
            return -1
        
        results.append(dfs(start, end, 0))
    
    return results
```
<div class="page-break" style="page-break-before: always;"></div>

# Minimum Spanning Forest (Disconnected Graphs)
```python
def minimum_spanning_forest(edges, n):
    """
    Find MST for each connected component
    Time: O(E log E)
    Space: O(V)
    Returns: (total_weight, forest_edges, num_components)
    """
    edges.sort()
    uf = UnionFind(n)
    total_weight = 0
    forest_edges = []
    
    for weight, u, v in edges:
        if uf.union(u, v):
            total_weight += weight
            forest_edges.append((u, v, weight))
    
    # Group edges by component
    component_edges = {}
    for u, v, w in forest_edges:
        comp = uf.find(u)
        if comp not in component_edges:
            component_edges[comp] = []
        component_edges[comp].append((u, v, w))
    
    return total_weight, forest_edges, uf.components
```
<div class="page-break" style="page-break-before: always;"></div>

# Dynamic MST (Add/Remove Edges)
```python
class DynamicMST:
    """
    Maintain MST with edge additions/deletions
    Simplified version - full dynamic MST is complex
    """
    def __init__(self, n):
        self.n = n
        self.edges = set()
        self.mst_weight = 0
        self.mst_edges = []
    
    def add_edge(self, u, v, weight):
        """
        Add edge and update MST
        Time: O(E log E) - recomputes MST
        """
        self.edges.add((weight, u, v))
        self._recompute_mst()
    
    def remove_edge(self, u, v, weight):
        """
        Remove edge and update MST
        Time: O(E log E)
        """
        self.edges.discard((weight, u, v))
        self._recompute_mst()
    
    def _recompute_mst(self):
        """Recompute MST from scratch"""
        if not self.edges:
            self.mst_weight = 0
            self.mst_edges = []
            return
        
        sorted_edges = sorted(self.edges)
        uf = UnionFind(self.n)
        self.mst_weight = 0
        self.mst_edges = []
        
        for weight, u, v in sorted_edges:
            if uf.union(u, v):
                self.mst_weight += weight
                self.mst_edges.append((u, v, weight))
    
    def get_mst(self):
        return self.mst_weight, self.mst_edges
```