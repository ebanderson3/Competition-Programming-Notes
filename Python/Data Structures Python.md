#python #data_structures #ai
See [[C++ Data Structures]] instead

- All containers use` len(container)` for size
- Most have `.count(x)` to return how many times `x` appears (works for lists, tuples, strings — but **not** for sets or dicts).
- Lists support `.pop()` which removes and returns last element in O(1)
- `del container[i]` or `.remove(x)` removes by index or value
- `not container` is the Pythonic way to check if empty
## Hash table (unordered_map/map equivalent)
#map #unordered_map #hash
```python
d = {}                     # dict, O(1) average for insert/lookup
d[key] = value             # insert
d[key]                     # lookup
d.get(key, default)        # safe lookup
key in d                   # membership test by key
d.items()                  # iterable of (key, value) tuples
d.keys()                   # set-like iterable of keys
d.values()                 # iterable of values
# The elements in a dict are ordered by insertion order
```
## Resizable array (vector equivalent)
#vector #array
```python
lst = []                   
lst[i]                     # get ith element
lst.append(x)              # push_back
lst.pop()                  # pop_back, returns last element
lst.remove(x)              # removes first occurrence of x
```
## Set (unique, unordered, hash-based by default)
#set
```python
s = set()                  
s.add(x)                   # insert
x in s                     # membership test
a | b                      # Union a and b
a & b                      # Intersection of a and b (common elements)
a - b                      # Everything in a, except anything in b
set().union(*iterable)     # Union all sets from iterable
# Note: not sorted by default (unlike C++ set)
# use "sorted(s)" if you need ordering
# Note that sorted() re-sorts on every call, use sparingly
```
## Multiset (C++ multiset equivalent)
#multiset #set
```python
from collections import Counter
ms = Counter()             
ms[x] += 1                 # insert x
ms[x]                      # count of x
list(ms.elements())        # expand back into list with duplicates
```
## Dictionary
```python
my_dict = {}               
my_dict["name"] = "Alice"          # insert "Alice" at "name"
my_dict["age"] = 25
my_dict.pop("age")                 # remove key
for key in my_dict:                # loop through each key
	pass
for value in my_dict.values():     # loop through each value
	pass
for key, value in my_dict.items(): # loop through each key and value in dictioanry
    print(key, ":", value)
```
## Stack (LIFO) #stack 
```python
stack = []                 
stack.append(x)            # push
stack[-1]                  # top
stack.pop()                # pop returns top element
```
## Queue (FIFO)
#queue #deque
```python
from collections import deque
q = deque()
q.append(x)                # push (to back)
q.popleft()                # pop from front
q[0]                       # front
```
## Deque (double-ended queue)
#deque
```python
dq = deque()
dq.append(x)               # push_back
dq.appendleft(x)           # push_front
dq.pop()                   # pop_back
dq.popleft()               # pop_front
```
## Priority queue (heap)
#heap #priority_queue
```python
import heapq
pq = []                    # by default, min-heap
heapq.heappush(pq, x)
heapq.heappop(pq)          # smallest element
pq = heapq.heapify(l)      # convert arbitary list to heap
```
## Max-heap trick #heap 
```python
heapq.heappush(pq, -x)
-max(heapq.heappop(pq))    # negate again
```
