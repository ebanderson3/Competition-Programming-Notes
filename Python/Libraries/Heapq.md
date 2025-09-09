#priority_queue #dijkstra #greedy
```python
import heapq
```
# Basic Usage
```python
h = [5,3,8]
heapq.heapify(h)        # min-heap [3,5,8]
heapq.heappush(h, 2)    # push
x = heapq.heappop(h)    # pop smallest
y = h[0]                # peek smallest (O(1))
```
# Max-Heap Trick
```python
h = []
heapq.heappush(h, -5)
heapq.heappush(h, -3)
heapq.heappush(h, -8)

largest = -heapq.heappop(h)   # 8
```
# Replace / Push-Pop
```python
heapq.heappushpop(h, x)   # push then pop smallest (more efficient than push+pop)
heapq.heapreplace(h, x)   # pop smallest, then push x (always keeps heap size)
```
# Find Largest/Smallest k
```python
h = [5, 1, 8, 3, 2]
heapq.nlargest(2, h)    # [8, 5]
heapq.nsmallest(2, h)   # [1, 2]
```
# Merging Sorted Lists
```python
a = [1, 4, 7]
b = [2, 5, 8]
c = [3, 6, 9]

list(heapq.merge(a, b, c))
# [1,2,3,4,5,6,7,8,9]
```
# Updating priorities #dijkstra #a_star #scheduling
- Push a new `(priority, task)`
- Use a set or dict to ignore outdated ones
```python
import itertools

pq = []                             # heap
entry_finder = {}                   # map task -> entry
REMOVED = "<removed>"
counter = itertools.count()         # unique tie-breaker

def add_task(task, priority=0):
    if task in entry_finder:
        remove_task(task)
    count = next(counter)
    entry = [priority, count, task]
    entry_finder[task] = entry
    heapq.heappush(pq, entry)

def remove_task(task):
    entry = entry_finder.pop(task)
    entry[-1] = REMOVED

def pop_task():
    while pq:
        priority, count, task = heapq.heappop(pq)
        if task is not REMOVED:
            del entry_finder[task]
            return task
    raise KeyError("pop from empty priority queue")
```