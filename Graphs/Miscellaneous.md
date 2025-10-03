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

### Python
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
### C++
```cpp
vector<int> topologicalSort(int n, vector<vector<int>>& adj) {
    // Calculate in-degree of each node
    vector<int> in_degree(n, 0);
    for (int i = 0; i < n; i++) {
        for (int neighbor : graph[i]) {
            in_degree[neighbor]++;
        }
    }
    
    // Initialize queue with all nodes having in-degree 0
    queue<int> q;
    for (int i = 0; i < n; i++) {
        if (in_degree[i] == 0) {
            q.push(i);
        }
    }
    
    vector<int> result;
    int count = 0;
    
    while (!q.empty()) {
        int node = q.front();
        q.pop();
        result.push_back(node);
        count++;
    
        for (int neighbor : graph[node]) {
            in_degree[neighbor]--;
            if (in_degree[neighbor] == 0) {
                q.push(neighbor);
            }
        }
    }
    
    if (count != n) {
        // cycle detected
        return {};
    }
    
    return result;
}
```
