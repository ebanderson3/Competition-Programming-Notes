#cpp #input

# Fast IO
```cpp
ios_base::sync_with_stdio(false);
cin.tie(NULL);
```
# Precision
```cpp
cout << setprecision(x);
```
# Fast Input reading
```cpp
scanf(“%d %d”, &a, &b); // scans for two integers and puts them in a and b, useful and fast
```
# Read entire line
- Reads up until newline
- Does not include newline in string
	-  Keep this in mind when mixing `getline` and `cin` in the same program.
```cpp
getline(cin, s);
```
# Read in N letters to Array
## Sample Input
```
4
1
2
3
4
```
## Read In
```c++
int n;
cin >> n;
vector<int> v(n);

// range based for loop, pretty useful for other things
for (auto& i : v) cin >> i;
```

