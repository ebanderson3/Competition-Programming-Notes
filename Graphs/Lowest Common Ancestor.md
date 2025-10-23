**What it does:** Finds the lowest common ancestor of two nodes in a tree and calculates distances between nodes. Also allows applying arbitrary functions to edge weights or node values along a path.
**Requirements:**
- Tree as adjacency list
- Designated root node
- Static tree (no modifications after preprocessing)
**When to use:**
- Multiple LCA queries on same tree
- Finding path between two nodes
- Distance queries between nodes
- Path sum/max/min queries (decomposable via LCA)
- Tree path problems in general
**Complexity:**
- Preprocessing: O(n log n) time, O(n log n) space
- Per query: O(log n) time
- Space: O(n log n) for binary lifting table
**Key insight:** Jumps up the tree in powers of 2 for efficient ancestor queries
<div class="page-break" style="page-break-before: always;"></div>

## C++ Implementation

### Basic version
Allows calculating the path length between any two nodes in the tree.
```C++
struct LCA {
	int LOG;
	vector<vector<int>> up;
	vector<int> depth;
	
	LCA(vector<vector<int>>& graph) :
		LOG(bit_width(graph.size())),
		up(graph.size(), vector<int>(LOG + 1, -1)),
		depth(graph.size(), 0)
	{
		vector<pair<int, int>> stack;
		stack.push_back({0, -1}); // 0 is the root node
		
		while (!stack.empty()) {
			auto [u, p] = stack.back();
			stack.pop_back();
			
			up[u][0] = p;
			
			for (int j = 0; j < LOG; j++) {
				if (up[u][j] != -1) {
					up[u][j + 1] = up[up[u][j]][j];
				}
			}
			
			for (int v : graph[u]) {
				if (v == p) continue;
				depth[v] = depth[u] + 1;
				stack.push_back({v, u});
			}
		}
	}
	
	int lca(int u, int v) {
		if (depth[u] < depth[v]) swap(u, v);
		
		int diff = depth[u] - depth[v];
		
		for (int j = 0; j < LOG + 1; j++) {
			if (diff & (1 << j)) {
				u = up[u][j];
			}
		}
		
		if (u == v) return u;
		
		for (int j = LOG; j >= 0; j--) {
			if (up[u][j] != up[v][j]) {
				u = up[u][j];
				v = up[v][j];
			}
		}
		
		return up[u][0];
	}
	
	int distance(int u, int v) {
		int lca_node = lca(u, v);
		
		return depth[u] + depth[v] - 2 * depth[lca_node];
	}
};
```

### With Edge Attributes
Allows evaluating any associative function $f$ on the edge weights along a path.
```C++
struct LCA {
	int LOG;
	vector<vector<int>> up;
	vector<vector<int>> agg;
	vector<int> depth;
	
	LCA(vector<vector<pair<int, int>>>& graph) :
		LOG(bit_width(graph.size())),
		up(graph.size(), vector<int>(LOG + 1, -1)),
		agg(graph.size(), vector<int>(LOG + 1, I)), // I is the identity element for f
		depth(graph.size(), 0)
	{
		vector<pair<int, int>> stack;
		stack.push_back({0, -1}); // 0 is the root node
		
		while (!stack.empty()) {
			auto [u, p] = stack.back();
			stack.pop_back();
			
			up[u][0] = p;
			
			for (int j = 0; j < LOG; j++) {
				int upuj = up[u][j];
				
				if (upuj != -1) {
					up[u][j + 1] = up[upuj][j];
					agg[u][j + 1] = f(agg[u][j], agg[upuj][j]);
				}
			}
			
			for (auto [v, w] : graph[u]) {
				if (v == p) continue;
				depth[v] = depth[u] + 1;
				agg[v][0] = w; // store edge weight to parent
				stack.push_back({v, u});
			}
		}
	}
```
*Implementation continues on next page.*
```C++
	int path_query(int u, int v) {
		if (depth[u] < depth[v]) swap(u, v);
		
		int attrib = I; // I is the identity element for f
		int diff = depth[u] - depth[v];
		
		// Lift u up to the same depth as v
		for (int j = 0; j < LOG + 1; j++) {
			if (diff & (1 << j)) {
				attrib = f(attrib, agg[u][j]);
				u = up[u][j];
			}
		}
		
		if (u == v) return attrib;
		
		// Lift both until just before LCA
		for (int j = LOG; j >= 0; j--) {
			if (up[u][j] != up[v][j]) {
				attrib = f(attrib, agg[u][j]);
				attrib = f(attrib, agg[v][j]);
				u = up[u][j];
				v = up[v][j];
			}
		}
		
		attrib = f(attrib, agg[u][0]);
		attrib = f(attrib, agg[v][0]);
		
		return attrib;
	}
};
```
<div class="page-break" style="page-break-before: always;"></div>

### With Node Attributes
Allows evaluating any associative function $f$ on the node values along a path.
```C++
struct LCA {
	int LOG;
	vector<vector<int>> up;
	vector<vector<int>> agg;
	vector<int> depth;

	LCA(vector<vector<int>>& graph, const vector<int>& values) :
		LOG(bit_width(graph.size())),
		up(graph.size(), vector<int>(LOG + 1, -1)),
		attrib_up(graph.size(), vector<int>(LOG + 1, I)), // I is the identity element for f
		depth(graph.size(), 0)
	{
		vector<pair<int, int>> stack;
		stack.push_back({0, -1}); // 0 is the root node
		
		while (!stack.empty()) {
			auto [u, p] = stack.back();
			stack.pop_back();
			
			up[u][0] = p;
			
			if (p == -1) agg[u][0] = values[u];
			else agg[u][0] = f(values[u], values[p]); // combine node with its parent
			
			for (int j = 0; j < LOG; j++) {
				int upuj = up[u][j];
				
				if (upuj != -1) {
					up[u][j + 1] = up[upuj][j];
					agg[u][j + 1] = f(agg[u][j], agg[upuj][j]);
				}
			}
			
			for (int v : graph[u]) {
				if (v == p) continue;
				depth[v] = depth[u] + 1;
				stack.push_back({v, u});
			}
		}
	}
```
*Implementation continues on next page.*
```C++
	int path_query(int u, int v) {
		if (depth[u] < depth[v]) swap(u, v);
		
		int attrib = I; // I is the identity element for f
		int diff = depth[u] - depth[v];
		
		// Lift u up to the same depth as v
		for (int j = 0; j < LOG + 1; j++) {
			if (diff & (1 << j)) {
				attrib = f(attrib, agg[u][j]);
				u = up[u][j];
			}
		}
		
		if (u == v) {
			attrib = f(attrib, agg[u][0]); // include the node itself
			return attrib;
		}
		
		// Lift both until just before LCA
		for (int j = LOG; j >= 0; j--) {
			if (up[u][j] != up[v][j]) {
				attrib = f(attrib, agg[u][j]);
				attrib = f(attrib, agg[v][j]);
				u = up[u][j];
				v = up[v][j];
			}
		}
		
		// Include both immediate parents and the LCA node itself
		attrib = f(attrib, agg[u][0]);
		attrib = f(attrib, agg[v][0]);
		attrib = f(attrib, agg[up[u][0]][0]);
		
		return attrib;
	}
};
```
<div class="page-break" style="page-break-before: always;"></div>

### With Edge Attributes & Non-Associative Functions
Generated by AI, may not actually work.
```C++
#include <bits/stdc++.h>
using namespace std;

struct LCA {
	using F = function<int(int,int)>; // Operation (non-associative allowed)
	int LOG;
	vector<vector<int>> up;
	vector<vector<int>> up_val, down_val;
	vector<int> depth;
	F combine;
	int id;

	LCA(const vector<vector<pair<int,int>>>& g, F comb, int identity, int root = 0)
		: LOG(bit_width(g.size())),
		  up(g.size(), vector<int>(LOG + 1, -1)),
		  up_val(g.size(), vector<int>(LOG + 1, identity)),
		  down_val(g.size(), vector<int>(LOG + 1, identity)),
		  depth(g.size(), 0),
		  combine(comb),
		  id(identity)
	{
		vector<pair<int,int>> st;
		st.push_back({root, -1});

		while (!st.empty()) {
			auto [u, p] = st.back();
			st.pop_back();

			up[u][0] = p;

			for (int j = 0; j < LOG; j++) {
				if (up[u][j] != -1) {
					up[u][j+1] = up[up[u][j]][j];
					up_val[u][j+1] = combine(up_val[u][j], up_val[up[u][j]][j]);
					down_val[u][j+1] = combine(down_val[up[u][j]][j], down_val[u][j]);
				}
			}

			for (auto [v, w] : g[u]) {
				if (v == p) continue;
				depth[v] = depth[u] + 1;
				up_val[v][0] = w;   // edge u→v
				down_val[v][0] = w; // same for reverse
				st.push_back({v, u});
			}
		}
	}

	int lca(int u, int v) const {
		if (depth[u] < depth[v]) swap(u, v);
		int diff = depth[u] - depth[v];
		for (int j = 0; j <= LOG; j++)
			if (diff & (1 << j))
				u = up[u][j];
		if (u == v) return u;
		for (int j = LOG; j >= 0; j--) {
			if (up[u][j] != up[v][j]) {
				u = up[u][j];
				v = up[v][j];
			}
		}
		return up[u][0];
	}
```
*Implementation continues on next page.*
```C++
	int query_path(int u, int v) const {
		int l = lca(u, v);
		int up_res = query_up(u, l);
		int down_res = query_down(v, l);
		return combine(up_res, down_res);
	}

private:
	int query_up(int u, int anc) const {
		int res = id;
		int diff = depth[u] - depth[anc];
		for (int j = 0; j <= LOG; j++) {
			if (diff & (1 << j)) {
				res = combine(res, up_val[u][j]);
				u = up[u][j];
			}
		}
		return res;
	}

	int query_down(int v, int anc) const {
		int res = id;
		int diff = depth[v] - depth[anc];
		for (int j = 0; j <= LOG; j++) {
			if (diff & (1 << j)) {
				res = combine(down_val[v][j], res);
				v = up[v][j];
			}
		}
		return res;
	}
};
```
<div class="page-break" style="page-break-before: always;"></div>

## Python Implementation

### Basic version
Allows calculating the path length between any two nodes in the tree.
```python
import sys
sys.setrecursionlimit(10**7)

def build_lca(adj, root=1):
    n = len(adj) - 1
    LOG = (n).bit_length()
    depth = [0] * (n + 1)
    up = [[-1] * (LOG + 1) for _ in range(n + 1)]

    def dfs(u, p):
        up[u][0] = p
        
        for j in range(LOG):
            if up[u][j] != -1:
                up[u][j + 1] = up[up[u][j]][j]
        
        for v in adj[u]:
            if v != p:
                depth[v] = depth[u] + 1
                dfs(v, u)

    dfs(root, -1)
    return up, depth, LOG
```

```python
def lca(u, v, up, depth, LOG):
    if depth[u] < depth[v]:
        u, v = v, u
    
    diff = depth[u] - depth[v]
    
    for j in range(LOG+1):
        if diff & (1 << j):
            u = up[u][j]
    
    if u == v: return u
    
    for j in reversed(range(LOG+1)):
        if up[u][j] != up[v][j]:
            u = up[u][j]
            v = up[v][j]
    
    return up[u][0]
```

```python
def distance(u, v, up, depth, LOG):
    """Distance between u and v in edges"""
    w = lca(u, v, up, depth, LOG)
    
    return depth[u] + depth[v] - 2*depth[w]
```

### With Edge Attributes
LCA can be modified to allow evaluating any associative function $f$ on the costs of path.

Example with `max`:
```python
import sys
sys.setrecursionlimit(10**7)

def build_lca_with_max(adj, root=1):
    """
    adj[u] = list of (v, w) pairs where w is edge weight
    """
    n = len(adj) - 1
    LOG = n.bit_length()
    
    depth = [0] * (n + 1)
    up = [[-1] * (LOG + 1) for _ in range(n + 1)]
    max_up = [[0] * (LOG + 1) for _ in range(n + 1)]  # maximum edge weight to ancestor

    def dfs(u, p):
        for v, w in adj[u]:
            if v == p:
                continue
            depth[v] = depth[u] + 1
            up[v][0] = u
            max_up[v][0] = w
            # compute higher ancestors
            for j in range(1, LOG + 1):
                if up[v][j - 1] != -1:
                    up[v][j] = up[up[v][j - 1]][j - 1]
                    max_up[v][j] = max(max_up[v][j - 1], max_up[up[v][j - 1]][j - 1])
            dfs(v, u)

    dfs(root, -1)
    return up, max_up, depth, LOG

```

```python
def max_edge_on_path(u, v, up, max_up, depth, LOG):
    """
    Returns maximum edge weight on path between u and v
    """
    if depth[u] < depth[v]:
        u, v = v, u
    
    max_edge = 0
    
    # 1️⃣ Lift u up to same depth as v
    diff = depth[u] - depth[v]
    for j in range(LOG + 1):
        if diff & (1 << j):
            max_edge = max(max_edge, max_up[u][j])
            u = up[u][j]
    
    if u == v:
        return max_edge
    
    # 2️⃣ Lift both up until LCA
    for j in reversed(range(LOG + 1)):
        if up[u][j] != -1 and up[u][j] != up[v][j]:
            max_edge = max(max_edge, max_up[u][j], max_up[v][j])
            u = up[u][j]
            v = up[v][j]
    
    # 3️⃣ Include edges to LCA
    max_edge = max(max_edge, max_up[u][0], max_up[v][0])
    
    return max_edge
```
