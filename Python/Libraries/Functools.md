```python
import functools
```

## Memoization / Caching
```python
# Fast DP / recursion with memoization
@functools.lru_cache(maxsize=None)   # unlimited cache
def fib(n):
    if n < 2: return n
    return fib(n-1) + fib(n-2)

# Clear cache if needed
fib.cache_clear()
```
## Reduce (folding a sequence)
```python
from operator import add, mul

arr = [1, 2, 3, 4]

functools.reduce(add, arr)   # 10 (sum)
functools.reduce(mul, arr)   # 24 (product)

# With initializer
functools.reduce(add, arr, 100)   # 110
```

## Comparators
```python
# Turn old-style cmp into key for sorting
def cmp(a, b): return a - b
arr = [3, 1, 2]
sorted_arr = sorted(arr, key=functools.cmp_to_key(cmp))
# [1, 2, 3]
```
## Cached Property (Python 3.8+)
```python
class Circle:
    def __init__(self, r): self.r = r

    @functools.cached_property
    def area(self):
        print("Calculating once...")
        return 3.14 * self.r * self.r

c = Circle(5)
print(c.area)  # calculates
print(c.area)  # cached
```
## Function Utilities
```python
# Attach metadata to functions
@functools.wraps(func)
def wrapper(*args, **kwargs):
    return func(*args, **kwargs)

# Fix arguments of a function (partial application)
from operator import mul
double = functools.partial(mul, 2)
double(10)   # 20
```
## Total Ordering
```python
# Auto-generate rich comparison methods
@functools.total_ordering
class Node:
    def __init__(self, val): self.val = val
    def __eq__(self, other): return self.val == other.val
    def __lt__(self, other): return self.val < other.val

# Now all comparisons (<, <=, >, >=, ==) work
```