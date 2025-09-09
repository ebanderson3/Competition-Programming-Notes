```python
from collections import Counter, defaultdict, deque
```
# Counter
```python
Counter("aabccc")   # {'a':2, 'b':1, 'c':3}
```
# defaultdict
```python
d = defaultdict(int)
d["x"] += 1         # no KeyError, default=0
```
# Double-ended queue (deque) #queues #BFS #sliding_window
```python
dq = deque([1,2,3])
dq.append(4)        # [1,2,3,4]
dq.appendleft(0)    # [0,1,2,3,4]
dq.pop()            # [0,1,2,3]
dq.popleft()        # [1,2,3]
```
