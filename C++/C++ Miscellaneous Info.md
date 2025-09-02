#cpp #miscellaneous
# Finds number of on (`1`) bits of a number
```cpp
__builtin_popcount(x);

// Note: Versions for long and long long type exist for all builtin functions
__builtin_popcountl(x);
__builtin_popcountll(x);
```
# Finds parity of a number (if number of on bits is even or odd)
```cpp
__builtin_parity(x);
```
# Finds amount of leading zeroes in a number
```cpp
__builtin_clz(x);
```
# Finds amount of training zeroes in a number
```cpp
__builtin_ctz(x);
```
# Integer with 128 bits (honestly just use python at this point)
```cpp
__int128 bi;
```
# Long Long type
```cpp
#define ll long long
```
# Shorthand vector type
```cpp
typedef vector<int> vi; // basically a #define
```
# Doing `N` test cases
```cpp
int n; 
cin >> n;
while (n--) { /* stuff */ }
```

