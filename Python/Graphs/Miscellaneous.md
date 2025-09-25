# Topological Sort (Kahn's Algorithm)
**What it does:** Orders nodes in a directed acyclic graph (DAG) such that all edges point from earlier to later nodes
**Requirements:**
- Directed Acyclic Graph (DAG)
- In-degree array for all nodes
- Adjacency list or edge function
**When to use:**
- Task scheduling with dependencies
- Course prerequisite ordering
- Build system dependencies
- Detecting cycles (if sort incomplete, cycle exists)
- DP on DAGs
**Complexity:**
- Time: O(V + E)
- Space: O(V) for queue and result
**Key insight:** Process nodes with no incoming edges first, remove them and repeat
```python
from collections import deque

def topo_sort(n, indeg):
    q = deque([u for u in range(n) if indeg[u] == 0])
    order = []
    
    while q:
        u = q.popleft()
        order.append(u)
        
        for v in edges(u):
            indeg[v] -= 1
            
            if indeg[v] == 0:
                q.append(v)
    
    return order
```
