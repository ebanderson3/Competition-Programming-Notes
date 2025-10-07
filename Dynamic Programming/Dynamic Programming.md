## Fibonacci
```python
def fib(n):
    dp = [0, 1] + [0] * (n-1)
    for i in range(2, n+1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]
```
## 0/1 Knapsack
```python
def knapsack(weights, values, W):
    n = len(weights)
    dp = [0] * (W+1)
    for i in range(n):
        for w in range(W, weights[i]-1, -1):
            dp[w] = max(dp[w], dp[w-weights[i]] + values[i])
    return dp[W]
```
## Unbounded Knapsack (Coin Change)
```python
def coin_change(coins, amount):
    dp = [float('inf')] * (amount+1)
    dp[0] = 0
    for coin in coins:
        for x in range(coin, amount+1):
            dp[x] = min(dp[x], dp[x-coin] + 1)
    return dp[amount] if dp[amount] != float('inf') else -1
```
## Longest Increasing Subsequence (O(n log n))
```python
import bisect

def LIS(arr):
    sub = []
    for x in arr:
        i = bisect.bisect_left(sub, x)
        if i == len(sub):
            sub.append(x)
        else:
            sub[i] = x
    return len(sub)
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
## Edit Distance (Levenshtein)
```python
def edit_distance(a, b):
    n, m = len(a), len(b)
    dp = [[0]*(m+1) for _ in range(n+1)]
    for i in range(n+1):
        dp[i][0] = i
    for j in range(m+1):
        dp[0][j] = j
    for i in range(1, n+1):
        for j in range(1, m+1):
            if a[i-1] == b[j-1]:
                dp[i][j] = dp[i-1][j-1]
            else:
                dp[i][j] = 1 + min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])
    return dp[n][m]
```
## Matrix Chain Multiplication
```python
def matrix_chain(dims):
    n = len(dims) - 1
    dp = [[0]*n for _ in range(n)]
    for l in range(2, n+1):  # chain length
        for i in range(n-l+1):
            j = i+l-1
            dp[i][j] = float('inf')
            for k in range(i, j):
                dp[i][j] = min(dp[i][j], dp[i][k] + dp[k+1][j] + dims[i]*dims[k+1]*dims[j+1])
    return dp[0][n-1]
```
## Partition Equal Subset Sum
```python
def can_partition(nums):
    s = sum(nums)
    if s % 2: return False
    target = s // 2
    dp = [False] * (target+1)
    dp[0] = True
    for num in nums:
        for t in range(target, num-1, -1):
            dp[t] = dp[t] or dp[t-num]
    return dp[target]
```
## Grid DP (Unique Paths)
```python
def unique_paths(m, n):
    dp = [[1]*n for _ in range(m)]
    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = dp[i-1][j] + dp[i][j-1]
    return dp[m-1][n-1]
```
## Subset Sum
```python
def subset_sum(nums, target):
    dp = [False]*(target+1)
    dp[0] = True
    for num in nums:
        for t in range(target, num-1, -1):
            dp[t] = dp[t] or dp[t-num]
    return dp[target]
```