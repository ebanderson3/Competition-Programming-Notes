#cpp #data_structures 
# Policy-based data structures
```cpp
#include <ext/pb_ds/assoc_container.hpp>
#include <ext/pb_ds/tree_policy.hpp>
#include <ext/pb_ds/trie_policy.hpp>

using namespace __gnu_pbds;

typedef tree <
    int,    // type
    null_type,                 // mapped type (null_type for sets)
    std::less<int>,            // for sorting (less_equal for multiset)
    rb_tree_tag,               // red-black tree implementation
    tree_order_statistics_node_update  // policy for order statistics
> ordered_set;

typedef trie<
    std::string,           
    null_type,             
    trie_string_access_traits<>,    // access traits
	pat_trie_tag,                   // tag type
    trie_prefix_search_node_update  // policy
> pat_trie;

int main() {
    ordered_set s;
    s.insert(1);
    s.insert(2);
    s.insert(4);
    s.insert(8);
    s.insert(16);

    // find the element at a specific position (0-indexed)
    auto fifth = s.find_by_order(4);  // returns iterator to 16
    // find the order/position of an element
    int pos = s.order_of_key(4);  // returns 2 (0-indexed position of 4)
    // if its a multiset, it will return the index of the first 4
    pat_trie t;
    t.insert("apple");
    t.insert("application");
    t.insert("apply");
    t.insert("banana");
    t.insert("book");
    
    auto range = t.prefix_range("app"); // Get all strings starting with "app"
    for (auto it = range.first; it != range.second; ++it) cout << *it << " ";  // apple application apply
    return 0;
}
```
<div class="page-break" style="page-break-before: always;"></div>

# Union Find #union_find
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
<div class="page-break" style="page-break-before: always;"></div>

# Fenwick Tree #fenwick_tree
```cpp
class FenwickTree {
private:
	std::vector<int> tree;
	int size;

	int get_lsb(int index) {
	     return index & -index;
	}

public:
	FenwickTree(int n) : size(n), tree(n + 1, 0) {}
	void update(int index, int delta) {
	     while (index <= size) {
	         tree[index] += delta;
	         index += get_lsb(index);
	     }
	}
	
	int prefix_sum(int index) {
	     int sum = 0;
	     while (index > 0) {
	         sum += tree[index];
	         index -= get_lsb(index);
	     }
	     return sum;
	}
	
	int range_sum(int l, int r) {
	     return prefix_sum(r) - prefix_sum(l - 1);
	}
	
	int point_query(int index) {
	     return prefix_sum(index) - prefix_sum(index - 1);
	}

	void range_update(int l, int r, int delta) {
	     update(l, delta);
	     update(r + 1, -delta);
	}

	int lower_bound(int value) {
	     int index = 0;
	     int mask = 1;
	     while (mask <= size) {
	         mask <<= 1;
	     }
	     mask >>= 1;
	     while (mask != 0) {
	         int temp = index + mask;
	         if (temp <= size && tree[temp] < value) {
	             value -= tree[temp];
	             index = temp;
	         }
	         mask >>= 1;
	     }
	     return index + 1;
	}
};
```
<div class="page-break" style="page-break-before: always;"></div>
