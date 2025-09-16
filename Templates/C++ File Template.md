
# Template
```cpp
#pragma GCC optimize("O3")
#pragma GCC target("sse,sse2,sse3,ssse3,sse4,popcnt,abm,mmx,avx,avx2,fma")
#pragma GCC optimize("unroll-loops")

#include <bits/stdc++.h>

using namespace std;

int main() {
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	cout.tie(nullptr);

	// return 0 can be omitted in C++
}
```
# Compile
```sh
g++ -g -O2 -o test -std=gnu++23 <file>.cpp
```

## Makefile
```Makefile
%: %.cpp input.txt
	g++-15 -g -O2 -std=gnu++23 $<
	./a.out < input.txt

```
