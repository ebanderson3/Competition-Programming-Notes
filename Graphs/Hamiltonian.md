Passes through each node once.
# Hamiltonian Path using Dynamic Programming (Bitmask DP)
**What it does:** Finds if a Hamiltonian path exists (visiting each node exactly once) using bitmask DP
**Requirements:**
- Adjacency list representation
- Small number of nodes (n ≤ 20 typically)
- Nodes numbered 0 to n-1
**When to use:**
- Finding Hamiltonian path/cycle
- Traveling Salesman Problem (TSP)
- Path covering all nodes
- Small graphs where exponential complexity is acceptable
**Complexity:**
- Time: O(n² × 2ⁿ)
- Space: O(n × 2ⁿ)
**Key insight:** Use bitmask to represent visited nodes, dp[mask][i] = can reach node i visiting exactly nodes in mask
```python
def hamiltonian_path_dp(graph, n):
    """
    Finds if Hamiltonian path exists using DP with bitmasks
    Time: O(n^2 * 2^n), Space: O(n * 2^n)
    graph: adjacency list {node: [neighbors]}
    n: number of nodes (0 to n-1)
    Returns: True if Hamiltonian path exists
    """
    # dp[mask][i] = True if there's a path visiting nodes in mask ending at i
    dp = [[False] * n for _ in range(1 << n)]
    parent = [[-1] * n for _ in range(1 << n)]
    
    # Initialize: single node paths
    for i in range(n):
        dp[1 << i][i] = True
    
    # Fill DP table
    for mask in range(1 << n):
        for last in range(n):
            if not dp[mask][last]:
                continue
            
            # Try to extend path to unvisited neighbors
            for next_node in graph.get(last, []):
                if mask & (1 << next_node):  # Already visited
                    continue
                
                new_mask = mask | (1 << next_node)
                dp[new_mask][next_node] = True
                parent[new_mask][next_node] = last
    
    # Check if any path visits all nodes
    full_mask = (1 << n) - 1
    for end in range(n):
        if dp[full_mask][end]:
            # Reconstruct path
            path = reconstruct_path(parent, full_mask, end, n)
            return True, path
    
    return False, []

def reconstruct_path(parent, mask, end, n):
    """Helper to reconstruct path from parent array"""
    path = []
    current = end
    
    while mask:
        path.append(current)
        new_mask = mask ^ (1 << current)
        if new_mask == 0:
            break
        current = parent[mask][current]
        mask = new_mask
    
    return path[::-1]
```
<div class="page-break" style="page-break-before: always;"></div>

# Hamiltonian Path with Specific Start and End
**What it does:** Finds Hamiltonian path from specific start to end node using backtracking
**Requirements:**
- Adjacency list representation
- Specified start and end nodes
- Small to medium graphs (backtracking)
**When to use:**
- Fixed endpoint Hamiltonian path
- Route planning visiting all locations
- When DP approach uses too much memory
- Need actual path, not just existence
**Complexity:**
- Time: O(n!) worst case, often much better with pruning
- Space: O(n) for recursion stack and visited array
**Key insight:** Backtrack with pruning, early termination when all nodes visited but not at end
```python
def hamiltonian_path_fixed_endpoints(graph, n, start, end):
    """
    Finds Hamiltonian path from start to end node
    graph: adjacency list {node: [neighbors]}
    n: number of nodes
    start, end: starting and ending nodes
    Returns: (exists, path)
    """
    path = [start]
    visited = [False] * n
    visited[start] = True
    
    def backtrack(node, count):
        # Found valid path
        if count == n and node == end:
            return True
        
        # Pruning: if all visited but not at end
        if count == n:
            return False
        
        for neighbor in graph.get(node, []):
            if not visited[neighbor]:
                path.append(neighbor)
                visited[neighbor] = True
                
                if backtrack(neighbor, count + 1):
                    return True
                
                path.pop()
                visited[neighbor] = False
        
        return False
    
    if backtrack(start, 1):
        return True, path
    return False, []
```
