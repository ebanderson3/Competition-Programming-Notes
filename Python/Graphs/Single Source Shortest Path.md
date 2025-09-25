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
**What it does:** Finds shortest paths from source to all other nodes in weighted graph with non-negative weights
**Requirements:**
- Non-negative edge weights
- Adjacency list with weights
- Priority queue (min-heap)
**When to use:**
- Single-source shortest paths
- GPS/routing problems
- Network routing
- Game pathfinding
- When all weights are non-negative
**Complexity:**
- Time: O((V + E) log V) with binary heap
- Space: O(V)
**Key insight:** Greedily expand shortest known distance using priority queue
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
**What it does:** Finds shortest paths from source to all nodes, handles negative weights and detects negative cycles

**Requirements:**
- Edge list format: \[(u, v, weight), ...]
- Can have negative weights
- Source node
**When to use:**
- Graphs with negative edge weights
- Detecting negative cycles
- Currency arbitrage detection
- When Dijkstra can't be used due to negative weights
**Complexity:**
- Time: O(V × E)
- Space: O(V)
**Key insight:** Relax all edges V-1 times; if Vth iteration changes distances, negative cycle exists
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
**What it does:** Finds shortest paths in graphs where all edge weights are either 0 or 1
**Requirements:**
- Graph with only 0 and 1 edge weights
- Deque data structure
- Adjacency list representation
**When to use:**
- Unweighted/binary weighted graphs
- Grid problems with free/costly moves
- State graphs with free transitions
- Much faster than Dijkstra for 0-1 graphs
**Complexity:**
- Time: O(V + E)
- Space: O(V)
**Key insight:** Use deque; add 0-weight edges to front, 1-weight edges to back
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