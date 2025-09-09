```python
import itertools
```
# Infinite counters
```python
itertools.count(start=0, step=1)  # 0, 1, 2, ...
itertools.cycle([1, 2, 3])        # 1, 2, 3, 1, 2, ...
itertools.repeat(10, times=5)     # 10, 10, 10, 10, 10
```
# Combinatorics
```python
arr = [1, 2, 3]

itertools.permutations(arr, 2)
# (1,2), (1,3), (2,1), (2,3), (3,1), (3,2)

itertools.combinations(arr, 2)
# (1,2), (1,3), (2,3)

itertools.combinations_with_replacement(arr, 2)
# (1,1), (1,2), (1,3), (2,2), (2,3), (3,3)

itertools.product(arr, repeat=2)
# (1,1), (1,2), (1,3), (2,1), ..., (3,3)
```
# Accumulation & Reduction
```python
list(itertools.accumulate([1, 2, 3, 4]))
# [1, 3, 6, 10]

import operator
list(itertools.accumulate([1, 2, 3, 4], func=operator.mul))
# [1, 2, 6, 24]  (factorials)

list(itertools.pairwise("abcd"))
# [('a','b'), ('b','c'), ('c','d')]  (Python 3.10+)

```
# Combining Iterables
```python
list(itertools.chain([1, 2], [3, 4]))
# [1, 2, 3, 4]

list(itertools.chain.from_iterable([[1, 2], [3, 4]]))
# [1, 2, 3, 4]

list(itertools.zip_longest("AB", "1234", fillvalue='-'))
# [('A','1'), ('B','2'), ('-','3'), ('-','4')]
```
# Filtering
```python
list(itertools.compress("abcdef", [1,0,1,0,1,0]))
# ['a','c','e']

list(itertools.filterfalse(lambda x: x%2, [1,2,3,4]))
# [2, 4]

list(itertools.takewhile(lambda x: x<5, [1,2,6,3]))
# [1, 2]

list(itertools.dropwhile(lambda x: x<5, [1,2,6,3]))
# [6, 3]
```
# Grouping
```python
[(k, list(g)) for k,g in itertools.groupby("aaabbcaaa")]
# [('a',['a','a','a']), ('b',['b','b']), ('c',['c']), ('a',['a','a','a'])]
```
# Special Utilities
```python
list(itertools.islice(range(10), 2, 8, 2))
# [2, 4, 6]   (like slicing but works on iterators)

list(itertools.starmap(pow, [(2,5), (3,2), (10,3)]))
# [32, 9, 1000]   (map with unpacked arguments)

list(itertools.tee([1,2,3], 2))
# Two independent iterators over the same data
```