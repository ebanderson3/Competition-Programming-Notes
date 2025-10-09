
#cpp #data_structures
See [[Data Structures Python]] instead
## Notes
- All containers have a `.size()` to return the size
- Some have `.count(x)` which will return the quantity of x in the structure (multiset)
- Some can do `.pop_back()` to remove last element in O(1) (vector, string)
- `.erase(x)` *x is either iterator or type of the container*
- `.empty()` *boolean to determine whether the container is empty or not*

## Basic Structures

### Maps
#map #unordered_map
```c++
unordered_map/map<from_t, to_t> um; // hash table, um[from_t] = to_t
```
### Vector
#vector
```c++
vector<type_t> vl; // resizable array, .push_back() to append
```
### Set
#set
```c++
set<type_t> s; // a set which can only have one of each element, .insert() to add, also automatically sorted, O(log n) insertion
```
### Multiset
#multiset
```c++
multiset<type_t> ms; // if you want the sorting ability but you have can more than one of something
```
### Stack
#stack
```c++
stack<type_t> st; // a stack, first in first out, .push() on a stack, .top() to access top, .pop() does not return top like in python  
```
### Queue
#queue
```c++
queue<type_t> q; // a queue, .push() to push, .front() to access front, .pop() is the same deal  
```
### String
#string
```c++
string s; // string of characters, .length() for length, + operator concatenates  
```
### Double-ended queue
#deque
```c++
deque<type_t> d; // .push_front() and .push_back(), also .pop_front() and .pop_back() obviously  
```
### Priority Queue
#priority_queue #heap
```c++
priority_queue<type_t> pq; // binary heap, max heap by default  
        // if you want a min heap, multiply by -1 before and after insertion
```
### Bitset
#bitset
```c++
bitset<7> bs(x); // makes an indexable bitset of length 7 using number x

bitset<7> bss(string(“0011001”)); // bss[0] = 0, bss[6] = 1 (right to left)

.count(); // is hamming weight

// natively supports bitwise operations (&, |, <<, >>, ^)
```

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
