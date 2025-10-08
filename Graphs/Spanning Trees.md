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
**What it does:** Finds minimum/maximum spanning tree by sorting edges and greedily adding them if they don't create cycles
**Requirements:**
- Edge list format: \[(weight, u, v), ...]
- Number of nodes n
- Union-Find data structure
**When to use:**
- Finding minimum/maximum spanning tree
- Sparse graphs (few edges)
- When edges are given directly
- Network design problems (minimum cost to connect all nodes)
**Complexity:**
- Time: O(E log E) for sorting + O(E α(V)) for union-find ≈ O(E log E)
- Space: O(V) for Union-Find
**Key insight:** Sort edges by weight, add smallest edges that don't form cycles

### Python
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
```

```python
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
```

```python
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
### C++
```cpp
sort(edges.begin(), edges.end(), [](auto a, auto b){return a.w < b.w;});

for (auto [a, b, w] : edges) {
	if (!same(a, b)) {
		unite(a, b);
		// Do whatever you need to do with this edge which is present in the MST
	}
}
```
For most graph problems which require finding an MST, a fancy union find isn’t required since input sizes are generally smaller. The following implementation of find() and unite() will suffice.
```cpp
int find(vector<int>& d, int a) {
	if(d[a] == -1) {
	     return a;
	}

	return d[a] = find(d, d[a]);
}

void join(vector<int>& d, int a, int b) {

	a = find(d, a);
	b = find(d, b);
	
	if(a == b) {
	     return;
	}

	d[a] = b;
}
```

# Prim's Algorithm (Vertex-based MST)
What it does: Builds MST by growing from a starting vertex, always adding minimum weight edge to unvisited vertex
Requirements:
    Adjacency list representation
    Priority queue (heap)
    Starting vertex
When to use:
    Dense graphs (many edges)
    When graph is given as adjacency list
    Need to start from specific vertex
    Real-time MST construction
Complexity:
    Time: O(E log V) with binary heap, O(E + V log V) with Fibonacci heap
    Space: O(V)
Key insight: Grow MST one vertex at a time, always choosing minimum edge to new vertex

### Python
```python
def prim(graph, n):
    """
    Prim's algorithm for Minimum Spanning Tree
    Time: O(E log V) using a priority queue
    Space: O(V + E)
    graph: adjacency list {u: [(weight, v), ...], ...}
    Returns: (mst_weight, mst_edges)
    """
    visited = [False] * n
    mst_weight = 0
    mst_edges = []
    
    # Min-heap of edges (weight, u, v)
    pq = [(0, 0, -1)]  # start from node 0, weight=0, parent=-1
    
    while pq and len(mst_edges) < n - 1:
        weight, u, parent = heapq.heappop(pq)
        if visited[u]:
            continue
        visited[u] = True
        if parent != -1:  # skip the initial dummy edge
            mst_weight += weight
            mst_edges.append((parent, u, weight))
        
        for w, v in graph[u]:
            if not visited[v]:
                heapq.heappush(pq, (w, v, u))
    
    return mst_weight, mst_edges
```
### C++
```cpp
vector<int> prim(const vector<vector<pii>>& g) {
	int n = g.size();
	vector<int> key(n, INF); // Key values used to pick minimum weight edge
	vector<bool> vis(n, false); // To track vertices included in MST
	vector<int> parent(n, -1); // MST
	priority_queue<pii, vector<pii>, greater<pii>> pq; // Min-heap
	key[0] = 0;
	pq.push({0, 0}); // {key, vertex}
	
	while (!pq.empty()) {
		int u = pq.top().se;
		pq.pop();
		if (vis[u]) continue;
		vis[u] = true;
		
		for (auto& edge : g[u]) {
			int v = edge.fi;
			int w = edge.se;
			
			if (!vis[v] && w < key[v]) {
				 key[v] = w;
				 parent[v] = u;
				 pq.push({key[v], v});
			}
		}
	}
	return parent; // Return MST as parent array
}

// Print MST edges 
for (int i = 1; i < n; i++) cout << mst[i] + 1 << " " << i + 1 << "\n";

// Get total weight
ll total_weight = 0;
for (int i = 1; i < n; i++) {
	for (auto& edge : g[mst[i]]) { 
		if (edge.fi == i) {
			 total_weight += edge.se; break; 
		}
	} 
}
```

# Minimum Bottleneck Spanning Tree
What it does: Finds spanning tree that minimizes the maximum edge weight in the tree
Requirements:
    Edge list format
    Number of nodes n
    Union-Find structure
When to use:
    Minimize maximum edge weight in path
    Network reliability (weakest link problems)
    Bottleneck path queries between nodes
    Note: Any MST is also a bottleneck spanning tree
Complexity:
    Time: O(E log E) for construction
    Space: O(V)
    Query time: O(V) per query with DFS
Key insight: Regular MST algorithms automatically produce bottleneck spanning trees
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
```

```python
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

# Minimum Spanning Forest (Disconnected Graphs)
What it does: Finds MST for each connected component in a disconnected graph
Requirements:
    Edge list format
    Number of nodes n
    Graph may be disconnected
When to use:
    Graph with multiple components
    Need separate MSTs for each component
    Counting connected components
    Network partitioning problems
Complexity:
    Time: O(E log E)
    Space: O(V)
Key insight: Run MST algorithm normally; it naturally handles disconnected components
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

# Dynamic MST (Add/Remove Edges)
**What it does:** Maintains MST while allowing edge additions and deletions
**Requirements:**
- Initial graph (can be empty)
- Efficient recomputation strategy
**When to use:**
- Evolving networks
- Online MST maintenance
- Interactive graph problems
- Note: Full dynamic MST is complex; shown version recomputes from scratch
**Complexity:**
- Time: O(E log E) per update (naive recomputation)
- Space: O(E + V)
- Better algorithms exist with O(log^4 n) amortized update time
**Key insight:** Simple version recomputes MST after each change; advanced versions use link-cut trees
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
