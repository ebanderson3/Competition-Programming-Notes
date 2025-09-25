#cpp #strings #lexicographic
# Compare two strings lexicographically
```c++
strcmp(s1.c_str(), s2.c_str()); // returns 0 if equal, >0 if s1 > s2, <0 if s1 < s2 lexicographically
```
# Prefix function (KMP preprocessing)
- Finds longest prefix-suffix overlaps (used in KMP search, periodicity).
```python
def prefix_function(s):
    n = len(s)
    pi = [0] * n
    for i in range(1, n):
        j = pi[i-1]
        while j > 0 and s[i] != s[j]:
            j = pi[j-1]
        if s[i] == s[j]:
            j += 1
        pi[i] = j
    return pi
```
# Knuth–Morris–Pratt (KMP) Search
```python
def kmp_search(text, pattern):
    pi = prefix_function(pattern)
    j, res = 0, []
    for i in range(len(text)):
        while j > 0 and text[i] != pattern[j]:
            j = pi[j-1]
        if text[i] == pattern[j]:
            j += 1
        if j == len(pattern):
            res.append(i - j + 1)  # match found
            j = pi[j-1]
    return res
```
# Z-function
- Fast substring matching, string comparison.
```python
def z_function(s):
    n = len(s)
    z = [0]*n
    l = r = 0
    for i in range(1, n):
        if i <= r:
            z[i] = min(r-i+1, z[i-l])
        while i+z[i] < n and s[z[i]] == s[i+z[i]]:
            z[i] += 1
        if i+z[i]-1 > r:
            l, r = i, i+z[i]-1
    return z
```
# Rabin–Karp (Rolling Hash)
```python
def rabin_karp(text, pattern, base=257, mod=10**9+7):
    n, m = len(text), len(pattern)
    if m > n: return []
    h, hp, power = 0, 0, 1
    for i in range(m):
        h = (h*base + ord(text[i])) % mod
        hp = (hp*base + ord(pattern[i])) % mod
        power = (power*base) % mod
    res = []
    if h == hp: res.append(0)
    for i in range(m, n):
        h = (h*base + ord(text[i]) - ord(text[i-m])*power) % mod
        if h == hp:
            res.append(i-m+1)
    return res
```
# Manacher’s Algorithm (Longest Palindrome in O(n))
```python
def manacher(s):
    s = "#" + "#".join(s) + "#"
    n = len(s)
    p = [0]*n
    c = r = 0
    for i in range(n):
        mirror = 2*c - i
        if i < r:
            p[i] = min(r-i, p[mirror])
        while i-p[i]-1 >= 0 and i+p[i]+1 < n and s[i-p[i]-1] == s[i+p[i]+1]:
            p[i] += 1
        if i+p[i] > r:
            c, r = i, i+p[i]
    return max(p)  # longest palindrome length
```
# Longest Common Subsequence (LCS)
```python
def LCS(a, b):
    n, m = len(a), len(b)
    dp = [[0]*(m+1) for _ in range(n+1)]
    for i in range(1, n+1):
        for j in range(1, m+1):
            if a[i-1] == b[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    return dp[n][m]
```