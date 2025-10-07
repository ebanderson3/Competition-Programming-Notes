#binary_search #sorted_lists
```python
import bisect
```
## Binary Search
```python
arr = [1,2,4,4,5]

bisect.bisect_left(arr, 4)   # 2 (first index of 4)
bisect.bisect_right(arr, 4)  # 4 (after last 4)
```
## Insert While Keeping Sorted
```python
bisect.insort(arr, 3)   # arr = [1,2,3,4,4,5]
```
