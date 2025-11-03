
## Increasing Recursion Limit
```python
import sys
sys.setrecursionlimit(10**6)
```

## Increase int parse length limit
```python
import sys
sys.set_int_max_str_digits(10_000_000)
```

## Itertools batched replacement
```python
iterator = iter(iterable)

while batch := tuple(itertools.islice(iterator, n)):
	# Do stuff
```
