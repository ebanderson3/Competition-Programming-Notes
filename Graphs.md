#graph #cpp
# Shortest Path
## Dijkstra #dijkstra
```cpp
vector<int> dist(N+1, INT_MAX);
dist[1] = 0;
// alternatively, we can do:
// priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>>
priority_queue<pair<int, int>> pq;
pq.push({0,1});
// prev.assign(n, -1);
vector<bool> vis(N+1, false);
while (!pq.empty()) {
     int a = pq.top().second; pq.pop();
     if (vis[a]) continue;
     vis[a] = true;
     for (auto [w, b] : adj[a]) {
         if (dist[a] + w < dist[b]) {
             dist[b] = dist[a] + w;
// prev[b] = a;
             pq.push({-dist[b], b});
         }
    }
```

# Reconstruct the path using `prev[]`
```cpp
vector<int> path;

if (prev[end] == -1 && end != start) return path;

for (int at = end; at != -1; at = prev[at]) {
	path.push_back(at);
	if (at == start) break;
}

reverse(path.begin(), path.end();
return path;
```

# Minimum Spanning Tree #spanning_tree #MST

## Kruskal's MST O($E$ log $E$)
```cpp
sort(edges.begin(), edges.end(), [](auto a, auto b){return a.w < b.w;});

for (auto [a, b, w] : edges) {
	if (!same(a, b)) {
		unite(a, b);
		// Do whatever you need to do with this edge which is present in the MST
	}
}
```

For most graph problems which require finding an MST, a fancy union find isn’t required since input sizes are generally smaller. The following implementation of find() and unite() will suffice.
```cpp
int find(vector<int>& d, int a) {
	if(d[a] == -1) {
	     return a;
	}

	return d[a] = find(d, d[a]);
}

void join(vector<int>& d, int a, int b) {

	a = find(d, a);
	b = find(d, b);
	
	if(a == b) {
	     return;
	}

	d[a] = b;
}
```

## Prim's MST O($E$ log $V$)
```cpp
vector<int> prim(const vector<vector<pii>>& g) {
	int n = g.size();
	vector<int> key(n, INF); // Key values used to pick minimum weight edge
	vector<bool> vis(n, false); // To track vertices included in MST
	vector<int> parent(n, -1); // MST
	priority_queue<pii, vector<pii>, greater<pii>> pq; // Min-heap
	key[0] = 0;
	pq.push({0, 0}); // {key, vertex}
	
	while (!pq.empty()) {
		int u = pq.top().se;
		pq.pop();
		if (vis[u]) continue;
		vis[u] = true;
		
		for (auto& edge : g[u]) {
			int v = edge.fi;
			int w = edge.se;
			
			if (!vis[v] && w < key[v]) {
				 key[v] = w;
				 parent[v] = u;
				 pq.push({key[v], v});
			}
		}
	}
	return parent; // Return MST as parent array
}

// Print MST edges 
for (int i = 1; i < n; i++) cout << mst[i] + 1 << " " << i + 1 << "\n";

// Get total weight
ll total_weight = 0;
for (int i = 1; i < n; i++) {
	for (auto& edge : g[mst[i]]) { 
		if (edge.fi == i) {
			 total_weight += edge.se; break; 
		}
	} 
}
```
<div class="page-break" style="page-break-before: always;"></div>

# Difference Arrays #maze
## 4 directions (N, E, S, W)
```cpp
int dx[] = {-1, 0, 1, 0};
int dy[] = {0, 1, 0, -1};
```
## 8 directions (N , NE, E, SE, S, SW, W, NW)
```cpp
int dx[] = {-1, -1, 0, 1, 1, 1, 0, -1};
int dy[] = {0, 1, 1, 1, 0, -1, -1, -1};
```
## Knight movement
```cpp
int dx[] = {2, 1, -1, -2, -2, -1, 1, 2};
int dy[] = {1, 2, 2, 1, -1, -2, -2, -1};
```
## Bishop movement (diagonal)
```cpp
int dx[] = {-1, -1, 1, 1};
int dy[] = {-1, 1, -1, 1};
```
## Usage (within BFS)
```cpp
for (int i = 0; i < NDIRS; i++) {
	int nx = x + dx[i], ny = y + dy[i];
	if (inbounds(x) && inbounds(y) && !vis[x][y]) // good node
}
```
