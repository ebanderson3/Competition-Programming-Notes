#python #data_structures 
See [[Data Structures C++]] instead

- All containers use` len(container)` for size
- Most have `.count(x)` to return how many times `x` appears (works for lists, tuples, strings — but **not** for sets or dicts).
- Lists support `.pop()` which removes and returns last element in O(1)
- `del container[i]` or `.remove(x)` removes by index or value
- `not container` is the Pythonic way to check if empty
```python
# Hash table (unordered_map/map equivalent)
d = {}                     # dict, O(1) average for insert/lookup
d[key] = value
d.get(key, default)        # safe lookup
# Resizable array (vector equivalent)
lst = []                   
lst.append(x)              # push_back
lst.pop()                  # pop_back, returns last element
lst.remove(x)              # removes first occurrence of x
# Set (unique, unordered, hash-based by default)
s = set()                  
s.add(x)                   # insert
x in s                     # membership test
# Note: not sorted by default (unlike C++ set)
# use "sorted(s)" if you need ordering
# Multiset (C++ multiset equivalent)
from collections import Counter
ms = Counter()             
ms[x] += 1                 # insert x
ms[x]                      # count of x
list(ms.elements())        # expand back into list with duplicates
# Stack (LIFO)
stack = []                 
stack.append(x)            # push
stack[-1]                  # top
stack.pop()                # pop returns top element
# Queue (FIFO)
from collections import deque
q = deque()
q.append(x)                # push (to back)
q.popleft()                # pop from front
q[0]                       # front
# Deque (double-ended queue)
dq = deque()
dq.append(x)               # push_back
dq.appendleft(x)           # push_front
dq.pop()                   # pop_back
dq.popleft()               # pop_front
# Priority queue (heap)
import heapq
pq = []                    # by default, min-heap
heapq.heappush(pq, x)
heapq.heappop(pq)          # smallest element
# Max-heap trick
heapq.heappush(pq, -x)
-max(heapq.heappop(pq))    # negate again
```