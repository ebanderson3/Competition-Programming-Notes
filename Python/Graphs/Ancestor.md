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
    depth = [0]*(n+1)
    up = [[-1]*(LOG+1) for _ in range(n+1)]

    def dfs(u, p):
        up[u][0] = p
        for j in range(1, LOG+1):
            if up[u][j-1] != -1:
                up[u][j] = up[up[u][j-1]][j-1]
        for v in adj[u]:
            if v != p:
                depth[v] = depth[u] + 1
                dfs(v, u)

    dfs(root, -1)
    return up, depth, LOG

def lca(u, v, up, depth, LOG):
    if depth[u] < depth[v]:
        u, v = v, u
    diff = depth[u] - depth[v]
    for j in range(LOG+1):
        if diff & (1 << j):
            u = up[u][j]
    if u == v:
        return u
    for j in reversed(range(LOG+1)):
        if up[u][j] != up[v][j]:
            u = up[u][j]
            v = up[v][j]
    return up[u][0]

def distance(u, v, up, depth, LOG):
    """Distance between u and v in edges"""
    w = lca(u, v, up, depth, LOG)
    return depth[u] + depth[v] - 2*depth[w]
```