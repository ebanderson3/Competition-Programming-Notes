# Topological Sort (Kahn's Algorithm)
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
