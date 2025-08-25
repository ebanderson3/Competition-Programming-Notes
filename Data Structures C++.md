#cpp #data_structures
See [[Data Structures Python]] instead

# Notes
- All containers have a `.size()` to return the size
- Some have `.count(x)` which will return the quantity of x in the structure (multiset)
- Some can do `.pop_back()` to remove last element in O(1) (vector, string)
- `.erase(x)` *x is either iterator or type of the container*
- `.empty()` *boolean to determine whether the container is empty or not*

# Structures
```c++
unordered_map/map<from_t, to_t> um; // hash table, um[from_t] = to_t
vector<type_t> vl; // resizable array, .push_back() to append
set<type_t> s; // a set which can only have one of each element, .insert() to add, also automatically sorted, O(log n) insertion  
multiset<type_t> ms; // if you want the sorting ability but you have can more than one of something  
stack<type_t> st; // a stack, first in first out, .push() on a stack, .top() to access top, .pop() does not return top like in python  
queue<type_t> q; // a queue, .push() to push, .front() to access front, .pop() is the same deal  
string s; // string of characters, .length() for length, + operator concatenates  
deque<type_t> d; // .push_front() and .push_back(), also .pop_front() and .pop_back() obviously  
priority_queue<type_t> pq; // binary heap, max heap by default  
        // if you want a min heap, multiply by -1 before and after insertion
```