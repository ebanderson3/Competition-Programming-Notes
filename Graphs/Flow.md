# Dinic's Algorithm (Faster Maximum Flow)
**What it does:** Finds maximum flow from source to sink in a flow network using level graphs and blocking flows
**Requirements:**
- Flow network with capacities
- Source and sink nodes
- Adjacency list with edge capacities
**When to use:**
- Maximum flow problems
- Minimum cut problems
- Bipartite matching
- Network capacity problems
- Assignment problems
**Complexity:**
- Time: O(V² × E) general, O(E × √V) for unit capacities
- Space: O(V + E)
**Key insight:** Build level graph with BFS, send blocking flows with DFS, repeat until no augmenting path
```python
class Dinic:
    def __init__(self, n):
        self.n = n
        self.graph = [[] for _ in range(n)]
        self.edges = []
    
    def add_edge(self, u, v, cap):
        """Add edge and reverse edge"""
        self.graph[u].append(len(self.edges))
        self.edges.append([v, cap])
        self.graph[v].append(len(self.edges))
        self.edges.append([u, 0])  # Reverse edge with 0 capacity
    
    def bfs(self, s, t):
        """Build level graph"""
        self.level = [-1] * self.n
        self.level[s] = 0
        queue = deque([s])
        
        while queue:
            v = queue.popleft()
            for i in self.graph[v]:
                to, cap = self.edges[i]
                if self.level[to] < 0 and cap > 0:
                    self.level[to] = self.level[v] + 1
                    queue.append(to)
        
        return self.level[t] >= 0
    
    def dfs(self, v, t, f):
        """Send flow using DFS"""
        if v == t:
            return f
        
        for i in range(self.iter[v], len(self.graph[v])):
            self.iter[v] = i
            edge_id = self.graph[v][i]
            to, cap = self.edges[edge_id]
            
            if self.level[v] < self.level[to] and cap > 0:
                d = self.dfs(to, t, min(f, cap))
                if d > 0:
                    self.edges[edge_id][1] -= d
                    self.edges[edge_id ^ 1][1] += d
                    return d
        return 0
    
    def max_flow(self, s, t):
        """Find maximum flow from s to t"""
        flow = 0
        while self.bfs(s, t):
            self.iter = [0] * self.n
            while True:
                f = self.dfs(s, t, float('inf'))
                if f == 0:
                    break
                flow += f
        return flow
```

# Minimum Cost Maximum Flow
**What it does:** Finds maximum flow with minimum total cost where edges have both capacity and cost
**Requirements:**
- Flow network with capacities and costs
- Source and sink nodes
- No negative cycles initially
**When to use:**
- Transportation problems with costs
- Assignment problems with preferences
- Supply chain optimization
- Minimum cost bipartite matching
- Network flow with economic constraints
**Complexity:**
- Time: O(V × E² × log V) with Dijkstra and potentials
- Space: O(V + E)
**Key insight:** Use successive shortest path algorithm with potentials to handle negative costs efficiently
```python
import heapq

class MinCostMaxFlow:
    def __init__(self, n):
        self.n = n
        self.graph = [[] for _ in range(n)]
    
    def add_edge(self, u, v, cap, cost):
        """Add edge with capacity and cost"""
        self.graph[u].append([v, cap, cost, len(self.graph[v])])
        self.graph[v].append([u, 0, -cost, len(self.graph[u]) - 1])
    
    def min_cost_flow(self, s, t, max_flow):
        """Find minimum cost for given flow amount"""
        res = 0
        h = [0] * self.n  # Potential
        
        while max_flow > 0:
            # Dijkstra with potentials
            dist = [float('inf')] * self.n
            dist[s] = 0
            parent = [(-1, -1)] * self.n
            pq = [(0, s)]
            
            while pq:
                d, v = heapq.heappop(pq)
                if d > dist[v]:
                    continue
                
                for i, (to, cap, cost, _) in enumerate(self.graph[v]):
                    if cap > 0:
                        new_cost = dist[v] + cost + h[v] - h[to]
                        if new_cost < dist[to]:
                            dist[to] = new_cost
                            parent[to] = (v, i)
                            heapq.heappush(pq, (dist[to], to))
            
            if dist[t] == float('inf'):
                return -1  # No more augmenting paths
            
            # Update potentials
            for i in range(self.n):
                if dist[i] < float('inf'):
                    h[i] += dist[i]
            
            # Find minimum capacity along path
            flow = max_flow
            v = t
            while v != s:
                pv, pi = parent[v]
                flow = min(flow, self.graph[pv][pi][1])
                v = pv
            
            # Update flow
            max_flow -= flow
            res += flow * h[t]
            v = t
            while v != s:
                pv, pi = parent[v]
                self.graph[pv][pi][1] -= flow
                rev_idx = self.graph[pv][pi][3]
                self.graph[v][rev_idx][1] += flow
                v = pv
        
        return res
```

```python
from collections import defaultdict, deque

class MaxFlowMinCut:
    def __init__(self, n):
        self.n = n
        self.graph = defaultdict(lambda: defaultdict(int))
        self.original_graph = defaultdict(lambda: defaultdict(int))
    
    def add_edge(self, u, v, capacity):
        """Add edge with given capacity"""
        self.graph[u][v] += capacity
        self.original_graph[u][v] += capacity
    
    def bfs(self, source, sink, parent):
        """BFS to find augmenting path from source to sink"""
        visited = set([source])
        queue = deque([source])
        
        while queue:
            u = queue.popleft()
            
            for v in self.graph[u]:
                if v not in visited and self.graph[u][v] > 0:
                    visited.add(v)
                    parent[v] = u
                    if v == sink:
                        return True
                    queue.append(v)
        
        return False
    
    def ford_fulkerson(self, source, sink):
        """
        Find maximum flow using Ford-Fulkerson
        Time: O(E * max_flow) for integer capacities
        Space: O(V)
        """
        parent = {}
        max_flow = 0
        
        # Find augmenting paths
        while self.bfs(source, sink, parent):
            # Find minimum capacity along path
            path_flow = float('inf')
            s = sink
            
            while s != source:
                path_flow = min(path_flow, self.graph[parent[s]][s])
                s = parent[s]
            
            # Update residual capacities
            v = sink
            while v != source:
                u = parent[v]
                self.graph[u][v] -= path_flow
                self.graph[v][u] += path_flow
                v = parent[v]
            
            max_flow += path_flow
            parent = {}
        
        return max_flow
    
    def find_min_cut(self, source, sink):
        """
        Find minimum cut after computing max flow
        Returns: (cut_value, source_side, sink_side, cut_edges)
        """
        # Run max flow first
        max_flow = self.ford_fulkerson(source, sink)
        
        # Find reachable nodes from source in residual graph
        visited = set()
        queue = deque([source])
        visited.add(source)
        
        while queue:
            u = queue.popleft()
            for v in self.graph[u]:
                if v not in visited and self.graph[u][v] > 0:
                    visited.add(v)
                    queue.append(v)
        
        # Source side = reachable nodes, Sink side = rest
        source_side = visited
        sink_side = set(range(self.n)) - visited
        
        # Find cut edges (edges from source side to sink side)
        cut_edges = []
        for u in source_side:
            for v in self.original_graph[u]:
                if v in sink_side and self.original_graph[u][v] > 0:
                    cut_edges.append((u, v, self.original_graph[u][v]))
        
        return max_flow, list(source_side), list(sink_side), cut_edges0
```