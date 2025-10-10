#fenwick_tree
### C++ Implementation
To make it less confusing, this Fenwick tree is 0-indexed unlike the standard Fenwick tree.
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
	
	// Updates the element at index by delta.
	// 0 <= index < n
	void update(int index, int delta) {
		index++;
		
		while (index <= size) {
			tree[index] += delta;
			index += get_lsb(index);
		}
	}
	
	// Computes the sum of elements in the range [0, index].
	// 0 <= index < n
	int prefix_sum(int index) {
		int sum = 0;
		index++;
		
		while (index > 0) {
			sum += tree[index];
			index -= get_lsb(index);
		}
		
		return sum;
	}
	
	// Computes the sum of elements in the range [l, r].
	// 0 <= l < n, 0 <= r < n
	int range_sum(int l, int r) {
		return prefix_sum(r) - prefix_sum(l - 1);
	}
	
	// DO NOT mix the point_query/range_update methods with the
	//  update/prefix_sum/range_sum methods. They are incompatible.
	
	// Retrieves the value of the element at index.
	// 0 <= index < n
	int point_query(int index) {
		return prefix_sum(index) - prefix_sum(index - 1);
	}

	// Updates all elements in the range [l, r] by delta.
	// 0 <= l < n, 0 <= r < n
	void range_update(int l, int r, int delta) {
		update(l, delta);
		update(r + 1, -delta);
	}

	// Finds the smallest index 'i' such that prefix_sum(i) >= value.
	// Returns 0 <= i < n
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
		
		return index;
	}
};
```
