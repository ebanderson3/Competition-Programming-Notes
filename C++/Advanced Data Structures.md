#cpp #data_structures 

## Policy-based data structures
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
