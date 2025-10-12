A segment tree allows you to compute queries on intervals of an array in $O(\log n)$ time. It works with any associative function.

**Common Associative Functions:**
- Summation/Addition
- Multiplication
- Minimum and Maximum
- GCD/LCM

**Time Complexity:**
- Creation: $O(n)$
- Query: $O(\log n)$
- Update: $O(\log n)$

**Space Complexity:** $O(n)$
Note that the big-O hides a factor of 4, so the tree is 4 times as big as the original array.

### C++ Implementation
```C++
#define ST_LEFT_BRANCH v * 2, tl, tm
#define ST_RIGHT_BRANCH v * 2 + 1, tm + 1, tr

class SegmentTree {
	int size;
	vector<int> tree;
	
	void build(int v, int tl, int tr, vector<int>& initial_data) {
		if (tl == tr) {
			tree[v] = initial_data[tl];
		} else {
			int tm = (tl + tr) / 2;
			
			build(ST_LEFT_BRANCH, initial_data);
			build(ST_RIGHT_BRANCH, initial_data);
			
			// f is the function you want the segment tree to calculate
			tree[v] = f(tree[v * 2], tree[v * 2 + 1]);
		}
	}
	
	void update(int v, int tl, int tr, int pos, int new_val) {
		if (tl == tr) {
			tree[v] = new_val;
		} else {
			int tm = (tl + tr) / 2;
			
			if (pos <= tm) {
				update(ST_LEFT_BRANCH, pos, new_val);
			} else {
				update(ST_RIGHT_BRANCH, pos, new_val);
			}
			
			// f is the function you want the segment tree to calculate
			tree[v] = f(tree[v * 2], tree[v * 2 + 1]);
		}
	}
	
	int query(int v, int tl, int tr, int l, int r) {
		if (l > r) return 0; // Identity element of function f
		if (l == tl && r == tr) return tree[v];
		
		int tm = (tl + tr) / 2;
		
		// f is the function you want the segment tree to calculate
		return f(
			query(ST_LEFT_BRANCH, l, min(r, tm)),
        	query(ST_RIGHT_BRANCH, max(l, tm + 1), r)
		);
	}
	
public:
	SegmentTree(vector<int>& initial_data) :
		size(initial_data.size()), tree(initial_data.size() * 4, 0)
	{
		build(1, 0, size - 1, initial_data);
	}
	
	// Computes the sum of elements in the range [l, r].
	// 0 <= l < n, 0 <= r < n
	int query(int l, int r) {
		return query(1, 0, size - 1, l, r);
	}
	
	// Changes the element at index to new_val.
	// 0 <= index < n
	void update(int index, int new_val) {
		update(1, 0, size - 1, index, new_val);
	}
};
```