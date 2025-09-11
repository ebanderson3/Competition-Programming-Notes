# Breadth-first Search
```python
from collections import deque

def bfs(start):
    visited = set([start])
    q = deque([start])
    
    while q:
        u = q.popleft()
        
        for v in edges(u):
            if v not in visited:
                visited.add(v)
                q.append(v)
```
# Depth-first Search (Recursive)
```python
def dfs(u, visited):
    visited.add(u)
    
    for v in edges(u):
        if v not in visited:
            dfs(v, visited)
```
# Depth-first Search (Iterative)
```python
def dfs_iter(start, graph):
    visited = set()
    stack = [start]

    while stack:
        u = stack.pop()
        
        if u in visited: continue
        visited.add(u)

        # process node u here if needed
        for v in edges(u):
            if v not in visited:
                stack.append(v)
```
# Dijkstra's Algorithm
```python
import heapq

def dijkstra(n, src):
    dist = [float('inf')] * n
    dist[src] = 0
    pq = [(0, src)]
    
    while pq:
        d, u = heapq.heappop(pq)
        if d > dist[u]: continue
        
        for v, w in edges(u):
            if dist[v] > d + w:
                dist[v] = d + w
                heapq.heappush(pq, (dist[v], v))
```
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
