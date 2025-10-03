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
