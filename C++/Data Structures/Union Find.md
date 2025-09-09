 #union_find

# C++ Implementation
Modular class

```cpp
class UnionFind {
private:
	vector<int> parent;
	vector<int> rank;
	vector<int> size;
	int num_sets;  
public:
	UnionFind(int n) {
	     parent.resize(n);
	     rank.resize(n, 0);
	     size.resize(n, 1);
	     num_sets = n;
	
	     for (int i = 0; i < n; ++i) {
	         parent[i] = i;
	     }
	}
	
	int find(int x) {
	     if (parent[x] != x) {
	         parent[x] = find(parent[x]);
	     }
	     return parent[x];
	}
	
	void unite(int x, int y) {
	     int root_x = find(x);
	     int root_y = find(y);
	
	     if (root_x != root_y) {
	         if (rank[root_x] > rank[root_y]) {
	             parent[root_y] = root_x;
	             size[root_x] += size[root_y];
	         } else if (rank[root_x] < rank[root_y]) {
	             parent[root_x] = root_y;
	             size[root_y] += size[root_x];
	         } else {
	             parent[root_y] = root_x;
	             size[root_x] += size[root_y];
	             rank[root_x]++;
	         }
	         num_sets--;
	     }
	}
	
	int get_size(int x) {
	     int root_x = find(x);
	     return size[root_x];
	}
	
	int get_num_sets() const {
	     return num_sets;
	}
	
	unordered_map<int, vector<int>> get_components() {
	     unordered_map<int, vector<int>> components;
	     for (int i = 0; i < parent.size(); ++i) {
	         int root = find(i);
	         components[root].push_back(i);
	     }
	     return components;
	}
};
```
