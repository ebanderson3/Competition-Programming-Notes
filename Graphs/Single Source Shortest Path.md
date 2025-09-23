```
WHICH ALGORITHM TO USE?

1. Graph has negative weights?
   ├─ YES → Can have negative cycles?
   │        ├─ YES → Bellman-Ford (detects cycles)
   │        └─ NO → SPFA (usually faster)
   └─ NO → Continue to #2

2. Graph is a DAG?
   ├─ YES → DAG Shortest Path (O(V+E))
   └─ NO → Continue to #3

3. Weights are only 0 and 1?
   ├─ YES → 0-1 BFS (O(V+E))
   └─ NO → Continue to #4

4. Maximum weight is small (≤100)?
   ├─ YES → Dial's Algorithm
   └─ NO → Continue to #5

5. Need path to specific target?
   ├─ YES → Bidirectional Dijkstra
   └─ NO → Standard Dijkstra
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

# Bellman-Ford Algorithm (Handles Negative Weights)
```python
def bellman_ford(edges, n, source):
    """
    Bellman-Ford for graphs with negative weights
    Can detect negative cycles
    edges: [(u, v, weight), ...]
    Time: O(V * E)
    Space: O(V)
    """
    dist = [float('inf')] * n
    dist[source] = 0
    parent = [-1] * n
    
    # Relax edges V-1 times
    for _ in range(n - 1):
        for u, v, weight in edges:
            if dist[u] != float('inf') and dist[u] + weight < dist[v]:
                dist[v] = dist[u] + weight
                parent[v] = u
    
    # Check for negative cycles
    for u, v, weight in edges:
        if dist[u] != float('inf') and dist[u] + weight < dist[v]:
            return None, None  # Negative cycle detected
    
    return dist, parent
```
<div class="page-break" style="page-break-before: always;"></div>

# 0-1 BFS
```python
from collections import deque

def zero_one_bfs(graph, n, source):
    """
    BFS for graphs with only 0 and 1 weights
    Time: O(V + E)
    Space: O(V)
    Much faster than Dijkstra for 0-1 graphs
    """
    dist = [float('inf')] * n
    dist[source] = 0
    parent = [-1] * n
    
    dq = deque([source])
    
    while dq:
        u = dq.popleft()
        
        for v, weight in graph.get(u, []):
            if dist[u] + weight < dist[v]:
                dist[v] = dist[u] + weight
                parent[v] = u
                
                if weight == 0:
                    dq.appendleft(v)  # Add to front for 0-weight
                else:
                    dq.append(v)  # Add to back for 1-weight
    
    return dist, parent
```