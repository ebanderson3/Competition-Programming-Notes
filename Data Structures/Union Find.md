 #union_find
### C++ Implementation
```cpp
struct UnionFind {
	vector<int> parent;
	vector<int> rank;
	vector<int> sizes;
	int num_sets;
	
	UnionFind(int n) {
		parent.assign(n, 0);
		iota(parent.begin(), parent.end(), 0);
		
		rank.assign(n, 0);
		sizes.assign(n, 1);
		num_sets = n;
	}
	
	int find(int x) {
		if (parent[x] != x) parent[x] = find(parent[x]);
		
		return parent[x];
	}
	
	bool same_set(int x, int y) {
		return find(x) == find(y);
	}
	
	void unite(int x, int y) {
		int root_x = find(x);
		int root_y = find(y);
		
		if (root_x == root_y) return; // Same set already
		
		if (rank[root_x] > rank[root_y]) swap(root_x, root_y); // Make root_y be the tallest
		parent[root_x] = root_y;
		
		if (rank[root_x] == rank[root_y]) rank[root_y]++;
		sizes[root_y] += sizes[root_x];
		
		num_sets--;
	}
	
	int get_size(int x) {
		return sizes[find(x)];
	}
	
	unordered_map<int, vector<int>> get_components() {
		unordered_map<int, vector<int>> components;
		
		for (int i = 0; i < parent.size(); i++) {
			components[find(i)].push_back(i);
		}
		
		return components;
	}
};
```

### Python Implementation
```python
from collections import defaultdict

class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
        self.sizes = [1] * n
        self.num_sets = n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def same_set(self, x, y):
        return self.find(x) == self.find(y)

    def unite(self, x, y):
        root_x = self.find(x)
        root_y = self.find(y)

        if root_x == root_y: return  # Already in the same set

        if self.rank[root_x] > self.rank[root_y]:
            root_x, root_y = root_y, root_x

        self.parent[root_x] = root_y

        if self.rank[root_x] == self.rank[root_y]:
            self.rank[root_y] += 1

        self.sizes[root_y] += self.sizes[root_x]
        self.num_sets -= 1

    def get_size(self, x):
        return self.sizes[self.find(x)]

    def get_components(self):
        components = defaultdict(list)

        for i in range(len(self.parent)):
            components[self.find(i)].append(i)

        return dict(components)
```