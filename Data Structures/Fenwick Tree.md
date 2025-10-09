#fenwick_tree
### C++ Implementation
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
