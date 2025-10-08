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
**Key insight:** Use bitmask to represent visited nodes, dp\[mask\] = can reach node i visiting exactly nodes in mask
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

# Traveling Salesman Problem (Brute-force)

```python
def solve_tsp_brute_force(graph):
    """
    Solves the Traveling Salesman Problem using a brute-force approach.

    Args:
        graph (list of lists): A 2D list representing the distance matrix
                               where graph[i][j] is the distance from
                               city i to city j.

    Returns:
        tuple: A tuple containing:
            - list: The optimal path (tour) as a list of city indices.
            - float: The total distance of the optimal path.
    """
    num_cities = len(graph)
    if num_cities == 0:
        return [], 0

    # Start and end the tour at the first city (index 0)
    start_city = 0
    other_cities = list(range(1, num_cities))

    # Initialize variables to store the best path found so far
    min_distance = sys.maxsize # Represents infinity
    best_path = None

    # Generate all possible permutations of the other cities
    for path_permutation in itertools.permutations(other_cities):
        current_distance = 0
        
        # 1. Calculate distance from the start city to the first city in the permutation
        last_city = start_city
        first_city_in_path = path_permutation[0]
        current_distance += graph[last_city][first_city_in_path]
        last_city = first_city_in_path

        # 2. Calculate the distance through the permutation of cities
        for i in range(len(path_permutation) - 1):
            from_city = path_permutation[i]
            to_city = path_permutation[i+1]
            current_distance += graph[from_city][to_city]
            last_city = to_city
        
        # 3. Calculate distance from the last city back to the start
        current_distance += graph[last_city][start_city]

        # 4. Check if this path is the new shortest path
        if current_distance < min_distance:
            min_distance = current_distance
            # Construct the full path including the start/end city
            best_path = [start_city] + list(path_permutation) + [start_city]

    # Handle the trivial case of a single city
    if best_path is None and num_cities > 0:
        best_path = [start_city, start_city]
        min_distance = 0

    return best_path, min_distance
```

# Traveling Salesman Problem (Held-Karp)

```python
def solve_tsp_held_karp(graph):
    """
    Solves the Traveling Salesman Problem using the Held-Karp algorithm.

    This dynamic programming approach finds the optimal tour.

    Args:
        graph (list of lists): A 2D list representing the distance matrix
                               where graph[i][j] is the distance from
                               city i to city j.

    Returns:
        tuple: A tuple containing:
            - list: The optimal path (tour) as a list of city indices.
            - float: The total distance of the optimal path.
    """
    n = len(graph)
    if n <= 1:
        return [0], 0

    # Memoization table to store costs of subpaths.
    # Key: (frozenset_of_visited_cities, last_city_in_path)
    # Value: cost of this subpath
    memo = {}

    # Path reconstruction table
    # Key: (frozenset_of_visited_cities, last_city_in_path)
    # Value: the second-to-last city in the path (predecessor)
    path_memo = {}

    # --- Step 1: Initialize base cases ---
    # Cost to travel from the start city (0) to any other city `k`.
    # This is the base case for paths of length 1.
    for k in range(1, n):
        # The set of visited cities only contains {k}, starting from 0.
        subset = frozenset([k])
        memo[(subset, k)] = graph[0][k]

    # --- Step 2: Iteratively build up solutions for larger subpaths ---
    # Iterate through subset sizes, from 2 up to n-1
    for subset_size in range(2, n):
        # Generate all subsets of this size that don't include the start city (0)
        for subset_tuple in itertools.combinations(range(1, n), subset_size):
            subset = frozenset(subset_tuple)
            for k in subset:
                # Find the minimum cost to reach city `k` through the current `subset`.
                # The path must come from a city `m` in the rest of the subset.
                prev_subset = subset - {k}
                min_cost = float('inf')
                best_predecessor = -1

                for m in prev_subset:
                    cost = memo[(prev_subset, m)] + graph[m][k]
                    if cost < min_cost:
                        min_cost = cost
                        best_predecessor = m

                memo[(subset, k)] = min_cost
                path_memo[(subset, k)] = best_predecessor

    # --- Step 3: Calculate the final tour cost ---
    # The final path must visit all cities {1, 2, ..., n-1} and then return to 0.
    full_set = frozenset(range(1, n))
    min_tour_cost = float('inf')
    last_city_in_tour = -1

    for k in range(1, n):
        # Cost of visiting all cities and ending at `k`, plus the cost to return to 0
        tour_cost = memo[(full_set, k)] + graph[k][0]
        if tour_cost < min_tour_cost:
            min_tour_cost = tour_cost
            last_city_in_tour = k

    # --- Step 4: Reconstruct the optimal path ---
    if last_city_in_tour == -1: # Handles case for n=2
      if n==2: return [0, 1, 0], graph[0][1] + graph[1][0]
      return [], float('inf')

    optimal_path = [0]
    current_set = full_set
    current_last_city = last_city_in_tour

    while current_set:
        optimal_path.insert(1, current_last_city)
        predecessor = path_memo[(current_set, current_last_city)]
        current_set = current_set - {current_last_city}
        current_last_city = predecessor
        
    optimal_path.append(0) # Complete the cycle

    return optimal_path, min_tour_cost
```
