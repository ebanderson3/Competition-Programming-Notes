# Tips
- Always check **1-indexed vs 0-indexed** nodes.
- For **undirected graphs**, insert both directions in adjacency lists.
- For **weighted graphs**, store `(neighbor, weight)` pairs.
- Use adjacency list for **sparse** graphs, adjacency matrix for **dense** ones.
# Edge list input
```python
n, m = map(int, input().split())
edges = [tuple(map(int, input().split())) for _ in range(m)]
```
# Adjacency list input
```python
n = int(input())
adj = [[] for _ in range(n+1)]
for i in range(1, n+1):
    data = list(map(int, input().split()))
    k, neighbors = data[0], data[1:]
    adj[i] = neighbors
```

```cpp
// undirected & unweighted
vector<vector<int>> adj(N+1, vector<int>);
// N+1 because most graph problem number nodes from 1..N, so 0 goes unused

for (int i = 0; i < N; i++) {
	int a, b;
	cin >> a >> b;
	
	adj[a].push_back(b);
	adj[b].push_back(a);
}

// directed & weighted
vector<vector<pair<int, int>>> adj(N+1, vector<pair<int, int>>);
for (int i = 0; i < N; i++) {
	int a, b, w;
	cin >> a >> b >> w;
	
	adj[a].push_back({b, w});
}
```
# Adjacency matrix
```python
n = int(input())
mat = [list(map(int, input().split())) for _ in range(n)]
```
<div class="page-break" style="page-break-before: always;"></div>

# Edge list $\leftrightarrow$ adjacency list
```python
def edge_to_adjlist(edges, n, directed=False):
    """Convert edge list -> adjacency list"""
    adj = [[] for _ in range(n+1)]  # 1-indexed
    for u, v in edges:
        adj[u].append(v)
        if not directed:
            adj[v].append(u)
    return adj

def adjlist_to_edge(adj, directed=False):
    """Convert adjacency list -> edge list"""
    edges = []
    n = len(adj) - 1  # assume 1-indexed
    for u in range(1, n+1):
        for v in adj[u]:
            if directed or u < v:  # avoid duplicates for undirected
                edges.append((u, v))
    return edges
```

# Edge list $\leftrightarrow$ adjacency matrix
```python
def edge_to_adjmat(edges, n, directed=False):
    """Convert edge list -> adjacency matrix"""
    mat = [[0]* (n+1) for _ in range(n+1)]  # 1-indexed
    for u, v in edges:
        mat[u][v] = 1
        if not directed:
            mat[v][u] = 1
    return mat
    
def adjmat_to_edge(mat, directed=False):
    """Convert adjacency matrix -> edge list"""
    n = len(mat) - 1
    edges = []
    for u in range(1, n+1):
        for v in range(1, n+1):
            if mat[u][v]:
                if directed or u < v:
                    edges.append((u, v))
    return edges
```

# Adjacency list $\leftrightarrow$ adjacency matrix
```python
def adjlist_to_adjmat(adj, directed=False):
    """Convert adjacency list -> adjacency matrix"""
    n = len(adj)
    mat = [[0] * n for _ in range(n)]
    for u in range(1, n):
        for v in adj[u]:
            mat[u][v] = 1
            if not directed:
                mat[v][u] = 1
    return mat


def adjmat_to_adjlist(mat, directed=False):
    """Convert adjacency matrix -> adjacency list"""
    n = len(mat)
    adj = [[] for _ in range(n)]
    for u in range(1, n):
        for v in range(1, n):
            if mat[u][v]:
                adj[u].append(v)
    return adj
```

Convert from adjacency list to adjacency matrix in C++:
```cpp
// implies you already have an adjacency list as defined above
vector<vector<int>> adj_matrix(N+1, vector<int>(N+1, 0));

for (int i = 0; i <= N; i++) {
	for (auto j : adj[i]) adj_matrix[i][j] = 1; // or the weight of the edge
}
```