## Longest Path in a DAG (Directed Acyclic Graph — Topological Dynamic Programming Approach)

**What it does:**
Finds the length (or optionally, the actual sequence) of the longest path between any two vertices in a Directed Acyclic Graph (DAG).
**Requirements:**
* The input graph must be *acyclic* (no directed cycles).
* Graph represented using adjacency list or edge list.
* Optionally, edge weights can be included (defaults to weight = 1 if unweighted).
**When to use:**
* When you need to determine the maximum sequence of dependencies or tasks in a DAG (e.g., scheduling, longest prerequisite chain, project planning).
* When topological ordering is available or easy to compute.
**Complexity:**
* Time: **O(V + E)** — topological sort + single pass through edges.
* Space: **O(V)** — for storing distances and order.
**Key insight:**
By processing nodes in *topological order*, you ensure all dependencies of a vertex are resolved before updating its longest path, allowing dynamic programming over the DAG.

```python
from collections import defaultdict, deque

def longest_path_in_dag(V, edges):
    """
    Finds the length of the longest path in a Directed Acyclic Graph (DAG).
    
    Parameters:
        V (int): Number of vertices (0-indexed nodes: 0 to V-1)
        edges (list[tuple[int, int, int]]): List of edges as (u, v, w)
                                            representing u → v with weight w.
                                            For unweighted DAGs, use w = 1.
    Returns:
        int: The length of the longest path in the DAG.
    """
    # Step 1: Build adjacency list and indegree map
    graph = defaultdict(list)
    indegree = [0] * V
    for u, v, w in edges:
        graph[u].append((v, w))
        indegree[v] += 1

    # Step 2: Topological sort using Kahn’s algorithm
    topo_order = []
    q = deque([i for i in range(V) if indegree[i] == 0])
    
    while q:
        node = q.popleft()
        topo_order.append(node)
        for neigh, _ in graph[node]:
            indegree[neigh] -= 1
            if indegree[neigh] == 0:
                q.append(neigh)
    
    # Step 3: Initialize distances (longest path to each node)
    dist = [-float('inf')] * V
    for node in topo_order:
        # Start nodes (no incoming edges) have distance 0
        if dist[node] == -float('inf'):
            dist[node] = 0
        # Step 4: Relax edges in topological order
        for neigh, w in graph[node]:
            if dist[node] + w > dist[neigh]:
                dist[neigh] = dist[node] + w

    # Step 5: The longest path length is the maximum distance value
    return max(dist)
```
