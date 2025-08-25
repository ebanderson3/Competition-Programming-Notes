#python #data_structures 
See [[Data Structures C++]] instead

- All containers use` len(container)` for size
- Most have `.count(x)` to return how many times `x` appears (works for lists, tuples, strings — but **not** for sets or dicts).
- Lists support `.pop()` which removes and returns last element in O(1)
- `del container[i]` or `.remove(x)` removes by index or value
- `not container` is the Pythonic way to check if empty
# Hash table (unordered_map/map equivalent) #map #unordered_map #hash
```python
d = {}                     # dict, O(1) average for insert/lookup
d[key] = value
d.get(key, default)        # safe lookup
```
# Resizable array (vector equivalent) #vector #array
```python
lst = []                   
lst.append(x)              # push_back
lst.pop()                  # pop_back, returns last element
lst.remove(x)              # removes first occurrence of x
```
# Set (unique, unordered, hash-based by default) #set
```python
s = set()                  
s.add(x)                   # insert
x in s                     # membership test
# Note: not sorted by default (unlike C++ set)
# use "sorted(s)" if you need ordering
```
# Multiset (C++ multiset equivalent) #multiset #set
```python
from collections import Counter
ms = Counter()             
ms[x] += 1                 # insert x
ms[x]                      # count of x
list(ms.elements())        # expand back into list with duplicates
```
# Stack (LIFO) #stack 
```python
stack = []                 
stack.append(x)            # push
stack[-1]                  # top
stack.pop()                # pop returns top element
```
# Queue (FIFO) #queue #deque
```python
from collections import deque
q = deque()
q.append(x)                # push (to back)
q.popleft()                # pop from front
q[0]                       # front
```
# Deque (double-ended queue) #deque
```python
dq = deque()
dq.append(x)               # push_back
dq.appendleft(x)           # push_front
dq.pop()                   # pop_back
dq.popleft()               # pop_front
```
# Priority queue (heap) #heap #priority_queue
```python
import heapq
pq = []                    # by default, min-heap
heapq.heappush(pq, x)
heapq.heappop(pq)          # smallest element
```
# Max-heap trick #heap 
```python
heapq.heappush(pq, -x)
-max(heapq.heappop(pq))    # negate again
```