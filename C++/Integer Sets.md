#cpp #set
# Create and insert to Set
```cpp
int s = 0; // empty set
s |= (1<<1); // add 1
s |= (1<<0); // add 0
s |= (1<<7); // add 7
```
# Print Set
```cpp
for (int i = 0; i < 32; i++) {
	if (s&(1<<i)) cout << i << “ “;
}
```
# Check if `8` is in set
```cpp
s & (1<<8);
```
# Find overlap of two sets `s` and `s2` (AND operation)
```cpp
s & s2;
```
# Add two sets `s` and `s2` (OR operation)
```cpp
s | s2;
```
# Subsets of `{0, 1, ..., n-1}`
```cpp
for (int i = 0; i < (1<<n); i++) {
	// process subset
	if (__builtin_popcount(i) == k) {
		// process subset with k elements
	}
}
```
# Subsets of Set x
```cpp
int i = 0;
do {
	// process subset
} while (x=((i-x)&x))
```
