
## Template
Only use the `pragmas` when performance is of the utmost concern. They can sometimes cause the program to compile weirdly and produce incorrect results.
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
## Compile
```sh
g++ -g -O2 -o test -std=gnu++23 <file>.cpp
```
## Makefile
Compiles all C++ files in the current directory when running `make`.
```Makefile
all: $(basename $(wildcard *.cpp))

%: %.cpp
	g++ -g -o $@ -O2 -std=gnu++23 $<
```
## Valgrind
Checks for memory leaks and illegal accesses.
```sh
valgrind --leak-check=yes ./<exe>
```
