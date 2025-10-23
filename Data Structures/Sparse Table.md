## Sparse Table (for Range Minimum/Maximum Queries)
**What it does:** Preprocesses an array to answer range minimum or maximum queries in constant time, given that the array is static (no updates).
**Requirements:**
- Input array of size `n` (typically integers or comparable values)
- Array is static (no modifications after preprocessing)
- Preprocessing step is performed once; queries are then answered in O(1)
- Supports range minimum query (RMQ) or range maximum query (RMXQ)
**When to use:**
- When you have many range queries (e.g., millions) on a static array
- When you need O(1) per query after preprocessing
- Commonly used in competitive programming for problems involving RMQ, such as finding the lowest point in a range, or in LCA (Lowest Common Ancestor) via Euler tour + RMQ
**Complexity:**
- Time: O(n log n) for preprocessing, O(1) per query
- Space: O(n log n) due to storing table entries for all powers of two
**Key insight:** Precompute the minimum (or maximum) value for all intervals of length 2^k starting at each index, so any range \[l, r\] can be covered by two overlapping intervals of power-of-two lengths whose union is \[l, r\], enabling constant-time lookup.

```cpp
#include <vector>
#include <algorithm>
#include <climits>

class SparseTable {
private:
    std::vector<std::vector<int>> st;  // st[i][j] = min/max in range [i, i + 2^j - 1]
    std::vector<int>& arr;
    int n;
    bool is_min_query;  // true for RMQ, false for RMXQ

    // Helper: comparison function
    bool cmp(int a, int b) const {
        return is_min_query ? (a < b) : (a > b);
    }

public:
    SparseTable(std::vector<int>& input_arr, bool use_min_query = true)
        : arr(input_arr), n(input_arr.size()), is_min_query(use_min_query) {
        // Handle edge case
        if (n == 0) return;

        // Compute log2(n) + 1 for number of levels
        int k = 0;
        while ((1 << k) <= n) ++k;

        st.resize(n, std::vector<int>(k, 0));

        // Base case: intervals of length 1
        for (int i = 0; i < n; ++i) {
            st[i][0] = arr[i];
        }

        // Build table: for each power of two
        for (int j = 1; (1 << j) <= n; ++j) {
            for (int i = 0; i + (1 << j) <= n; ++i) {
                int left = st[i][j - 1];
                int right = st[i + (1 << (j - 1))][j - 1];
                st[i][j] = cmp(left, right) ? left : right;
            }
        }
    }

    // Query: returns min or max in [l, r] (inclusive)
    int query(int l, int r) {
        if (l > r) return is_min_query ? INT_MAX : INT_MIN;

        int len = r - l + 1;
        int j = 0;
        while ((1 << (j + 1)) <= len) ++j;

        int left_part = st[l][j];
        int right_part = st[r - (1 << j) + 1][j];

        return cmp(left_part, right_part) ? left_part : right_part;
    }
};

// Example usage:
// SparseTable rmq(arr, true);  // for range minimum query
// SparseTable rmx(arr, false); // for range maximum query
// int result = rmq.query(2, 5); // min in arr[2..5]
```
