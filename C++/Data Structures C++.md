#cpp #data_structures
See [[Data Structures Python]] instead
# Notes
- All containers have a `.size()` to return the size
- Some have `.count(x)` which will return the quantity of x in the structure (multiset)
- Some can do `.pop_back()` to remove last element in O(1) (vector, string)
- `.erase(x)` *x is either iterator or type of the container*
- `.empty()` *boolean to determine whether the container is empty or not*

# Structures

## Maps #map #unordered_map
```c++
unordered_map/map<from_t, to_t> um; // hash table, um[from_t] = to_t
```
## Vector #vector
```c++
vector<type_t> vl; // resizable array, .push_back() to append
```
## Set #set
```c++
set<type_t> s; // a set which can only have one of each element, .insert() to add, also automatically sorted, O(log n) insertion
```
## Multiset #multiset
```c++
multiset<type_t> ms; // if you want the sorting ability but you have can more than one of something
```
## Stack #stack
```c++
stack<type_t> st; // a stack, first in first out, .push() on a stack, .top() to access top, .pop() does not return top like in python  
```
## Queue  #queue
```c++
queue<type_t> q; // a queue, .push() to push, .front() to access front, .pop() is the same deal  
```
## String #string
```c++
string s; // string of characters, .length() for length, + operator concatenates  
```
## Double-ended queue #deque
```c++
deque<type_t> d; // .push_front() and .push_back(), also .pop_front() and .pop_back() obviously  
```
## Priority Queue #priority_queue #heap
```c++
priority_queue<type_t> pq; // binary heap, max heap by default  
        // if you want a min heap, multiply by -1 before and after insertion
```
## Bitset #bitset
```c++
bitset<7> bs(x); // makes an indexable bitset of length 7 using number x

bitset<7> bss(string(“0011001”)); // bss[0] = 0, bss[6] = 1 (right to left)

.count(); // is hamming weight

// natively supports bitwise operations (&, |, <<, >>, ^)
```