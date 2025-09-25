# Invert directed graph
```python
def inverse_adjlist(adj, directed=True):
    """Inverse a graph in adjacency list form"""
    n = len(adj) - 1  # assume 1-indexed
    new_adj = [[] for _ in range(n+1)]
    if not directed:
        return [neighbors[:] for neighbors in adj]
    for u in range(1, n+1):
        for v in adj[u]:
            new_adj[v].append(u)
    return new_adj
```