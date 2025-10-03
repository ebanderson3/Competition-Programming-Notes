#cpp #algorithms

# Sort #sort
- `cmp_func` needs to be defined above (or can be a lambda)
```c++
sort(v.begin(), v.end(), [](auto a, auto b) { return a < b; });
```
# Min/Max #minmax
- Returns an iterator, so remember to dereference
```c++
*min/max_element(v.begin(), v.end();
```
# Upper Bound #bounds 
- Finds first element bigger than `x` in O(log $n$)
```c++
upper_bound(v.begin(), v.end(), x);
```
# Lower Bound #bounds 
- Finds first element at least `x` in O(log $n$)
```c++
lower_bound(v.begin(), v.end(), x); 
```
# Rotate #rotate
- Rotates range by `n` to the left
```c++
rotate(v.begin(), v.begin() + n, v.end());
```
- Rotates range by `n` to the right
```c++
rotate(v.rbegin(), v.rbegin() + n, rv.end());
```
# Reverse #reverse
- Reverses range
```c++
reverse(v.begin(), v.end());
```
# Min/Max comparison #minmax
```c++
max/min(a, b); 

// Find max/min of 3 elements
max/min(max/min(a, b), c);
```