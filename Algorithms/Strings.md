#cpp #strings #lexicographic
## Compare two strings lexicographically
```c++
strcmp(s1.c_str(), s2.c_str()); // returns 0 if equal, >0 if s1 > s2, <0 if s1 < s2 lexicographically
```
## Prefix function (KMP preprocessing)
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
## Knuth–Morris–Pratt (KMP) Search
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
## Z-function
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
## Rabin–Karp (Rolling Hash)
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
## Manacher’s Algorithm (Longest Palindrome in O(n))
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
## Longest Common Subsequence (LCS)
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

## Aho–Corasick Algorithm (Efficient multi-pattern string matching)

**What it does:**  
Finds all occurrences of multiple patterns within a given text in linear time relative to the text length, after building a finite automaton from the pattern set.
**Requirements:**
- A set of string patterns (dictionary)
- A target text to search through
- Data structures: trie (for pattern storage), failure links (for transitions on mismatches), and output lists (for matches)
**When to use:**
- Searching for multiple keywords simultaneously (e.g., spam filtering, plagiarism detection, DNA sequence analysis, intrusion detection systems).
- When you need faster matching than running multiple individual substring searches.
**Complexity:**
- Time: **O(N + M + Z)** where
    - _N_ = total length of all patterns,
    - _M_ = length of the text,
    - _Z_ = number of matches found.
- Space: **O(Σ × N)** where Σ is the alphabet size.
**Key insight:** Build a trie of all patterns and use failure links (similar to KMP) to efficiently transition between patterns upon mismatches without re-checking characters.
### Constructing the state machine
```C++
#include <bits/stdc++.h>
using namespace std;

struct AhoCorasick {
    static const int ALPHABET = 26; // assuming lowercase 'a'–'z'
    struct Node {
        array<int, ALPHABET> next{};
        int link = -1;                // failure link
        vector<int> out;              // pattern indices ending here
        Node() { next.fill(-1); }
    };

    vector<Node> trie;

    AhoCorasick() { trie.emplace_back(); }

    void insert(const string &s, int id) {
        int v = 0;
        for (char c : s) {
            int idx = c - 'a';
            if (trie[v].next[idx] == -1) {
                trie[v].next[idx] = trie.size();
                trie.emplace_back();
            }
            v = trie[v].next[idx];
        }
        trie[v].out.push_back(id);
    }

    void build() {
        queue<int> q;
        trie[0].link = 0;

        for (int c = 0; c < ALPHABET; ++c) {
            int u = trie[0].next[c];
            if (u != -1) {
                trie[u].link = 0;
                q.push(u);
            } else {
                trie[0].next[c] = 0;
            }
        }

        while (!q.empty()) {
            int v = q.front(); q.pop();
            for (int c = 0; c < ALPHABET; ++c) {
                int u = trie[v].next[c];
                if (u != -1) {
                    trie[u].link = trie[trie[v].link].next[c];
                    for (int pat : trie[trie[u].link].out)
                        trie[u].out.push_back(pat);
                    q.push(u);
                } else {
                    trie[v].next[c] = trie[trie[v].link].next[c];
                }
            }
        }
    }
};
```
### Using the state machine
```C++
// Use the state machine to find the substrings in a string
vector<pair<int,int>> search(AhoCorasick &ac, const string &text) {
    vector<pair<int,int>> matches; // (position, pattern_id)
    int v = 0;

    for (int i = 0; i < (int)text.size(); ++i) {
        int c = text[i] - 'a';
        v = ac.trie[v].next[c];

        for (int pat_id : ac.trie[v].out)
            matches.emplace_back(i, pat_id);
    }

    return matches;
}

// Example usage
int main() {
    AhoCorasick ac;
    vector<string> patterns = {"he", "she", "his", "hers"};
    for (int i = 0; i < (int)patterns.size(); ++i)
        ac.insert(patterns[i], i);
    ac.build();

    string text = "ahishers";
    auto matches = search(ac, text);

    for (auto [pos, id] : matches)
        cout << "Pattern \"" << patterns[id] << "\" found ending at index " << pos << "\n";
}
```
