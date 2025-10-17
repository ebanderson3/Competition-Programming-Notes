# Lowest Common Ancestor (LCA) with Binary Lifting
**What it does:** Finds the lowest common ancestor of two nodes in a tree and calculates distances between nodes
**Requirements:**
- Tree as adjacency list
- Designated root node
- Static tree (no modifications after preprocessing)
**When to use:**
- Multiple LCA queries on same tree
- Finding path between two nodes
- Distance queries between nodes
- Path sum/max/min queries (decomposable via LCA)
- Tree path problems in general
**Complexity:**
- Preprocessing: O(n log n) time, O(n log n) space
- Per query: O(log n) time
- Space: O(n log n) for binary lifting table
**Key insight:** Jumps up the tree in powers of 2 for efficient ancestor queries

```python
import sys
sys.setrecursionlimit(10**7)

def build_lca(adj, root=1):
    n = len(adj) - 1
    LOG = (n).bit_length()
    depth = [0] * (n + 1)
    up = [[-1] * (LOG + 1) for _ in range(n + 1)]

    def dfs(u, p):
        up[u][0] = p
        
        for j in range(LOG):
            if up[u][j] != -1:
                up[u][j + 1] = up[up[u][j]][j]
        
        for v in adj[u]:
            if v != p:
                depth[v] = depth[u] + 1
                dfs(v, u)

    dfs(root, -1)
    return up, depth, LOG
```

```python
def lca(u, v, up, depth, LOG):
    if depth[u] < depth[v]:
        u, v = v, u
    
    diff = depth[u] - depth[v]
    
    for j in range(LOG+1):
        if diff & (1 << j):
            u = up[u][j]
    
    if u == v: return u
    
    for j in reversed(range(LOG+1)):
        if up[u][j] != up[v][j]:
            u = up[u][j]
            v = up[v][j]
    
    return up[u][0]
```

```python
def distance(u, v, up, depth, LOG):
    """Distance between u and v in edges"""
    w = lca(u, v, up, depth, LOG)
    
    return depth[u] + depth[v] - 2*depth[w]
```
### Additional path attributes
LCA can be modified to allow evaluating any associative function $f$ on the costs of path.

Example with `max`:
```python
import sys
sys.setrecursionlimit(10**7)

def build_lca_with_max(adj, root=1):
    """
    adj[u] = list of (v, w) pairs where w is edge weight
    """
    n = len(adj) - 1
    LOG = n.bit_length()
    
    depth = [0] * (n + 1)
    up = [[-1] * (LOG + 1) for _ in range(n + 1)]
    max_up = [[0] * (LOG + 1) for _ in range(n + 1)]  # maximum edge weight to ancestor

    def dfs(u, p):
        for v, w in adj[u]:
            if v == p:
                continue
            depth[v] = depth[u] + 1
            up[v][0] = u
            max_up[v][0] = w
            # compute higher ancestors
            for j in range(1, LOG + 1):
                if up[v][j - 1] != -1:
                    up[v][j] = up[up[v][j - 1]][j - 1]
                    max_up[v][j] = max(max_up[v][j - 1], max_up[up[v][j - 1]][j - 1])
            dfs(v, u)

    dfs(root, -1)
    return up, max_up, depth, LOG

```

```python
def max_edge_on_path(u, v, up, max_up, depth, LOG):
    """
    Returns maximum edge weight on path between u and v
    """
    if depth[u] < depth[v]:
        u, v = v, u
    
    max_edge = 0
    
    # 1️⃣ Lift u up to same depth as v
    diff = depth[u] - depth[v]
    for j in range(LOG + 1):
        if diff & (1 << j):
            max_edge = max(max_edge, max_up[u][j])
            u = up[u][j]
    
    if u == v:
        return max_edge
    
    # 2️⃣ Lift both up until LCA
    for j in reversed(range(LOG + 1)):
        if up[u][j] != -1 and up[u][j] != up[v][j]:
            max_edge = max(max_edge, max_up[u][j], max_up[v][j])
            u = up[u][j]
            v = up[v][j]
    
    # 3️⃣ Include edges to LCA
    max_edge = max(max_edge, max_up[u][0], max_up[v][0])
    
    return max_edge
```