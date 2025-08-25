#dynamic_programming #dp #cpp
# Common Patterns
## Longest increasing (or decreasing) subsequence #subsequence
Ideas to look out for in problem statement:
- You can choose to keep or throw away any given element
- End result should be in strictly ascending or descending order (or the length of something in this order)
- Usually the sizes or heights of things is the input

### O($n^2$) solution
- Make sure to check the constraints to make sure it's viable

```cpp
// dp[i] is the LIS that ends at i
vector<int> dpLIS(v.size(), 1);

for (int i = 0; i < v.size(); i++) {
	for (int j = i-1; j >= 0; j--) {
		if (v[j] < v[i]) {
			dp[i] = max(dpLIS[i], 1 + dpLIS[j]); // for LDS, just flip the comparison around
		}
	}
}
```

### O($n$ log $n$)
```cpp
// dp[i] is the value which is the minimum value that a LIS of length i will end at
vector<int> dpLIS(v.size()+1, INT_MAX);
dp[0]=INT_MIN;

for (auto i : v) {
	int l = upper_bound(dp.begin(), dp.end(), i) - dp.begin();
	if (dp[l-1] < i && i < dp[l]) dp[l] = i;
}
```
<div class="page-break" style="page-break-before: always;"></div>

## Counting Problems
Ideas to look out for in problem statement:
- Almost always, a giveaway is that it will tell you the answer needs to be modulo some big number

Typically, these can be solved quite easily by combining a recursive function with a global cache

## "Coin" problems
Ideas to look out for in problem statement:
- You are given a bunch of objects all of which have a certain value attached to them
- Often the question will be if you can make up a certain value with some property (using the minimum amount of objects, a value close to another number, etc.)
- One dimensional in nature

### Generating possible sums (assuming unlimited supply)
```cpp
// poss is a bitset where poss[i] indicates if i is a possible sum
// poss[0] is always 1 to start out with

for (auto v : values) poss |= (poss << v);
```

### Minimum amount of coins (assuming limited supply)
```cpp
// generates all possible sums (assuming limited supply)
// dp[i] = minimum amount of coins to get to i
for (auto c : coins) {
	for (int i = 10000 - c; i >= 0; i--) {
		if (dp[i] != INT_MAX) dp[i+c] = min(dp[i+c], 1 + dp[i]);
	}
}
```

### Minimum amount of coins (assuming unlimited supply)
```cpp
// generates all possible sums (assuming unlimited supply of each coin)
// dp[i] is the minimum amount of coins to get to i
for (int i = 0; i < 10001; i++) {
	for (auto c : coins) {
		if (dp[i-c] < 0) continue;
		dp[i] = min(dp[i], 1 + dp[i-c]);
	}
}
```
<div class="page-break" style="page-break-before: always;"></div>

## Knapsack problems
Ideas to look out for in problem statement:
- Objects have two attributes to them, canonical examples are weight and value
- Goal is often to select a subset which maximizes one attribute while not exceeding a constraint on the other

### 0/1 Knapsack
- Only one of each item
- You can choose to take it or not
```cpp
// dp[i][j] = considering the first i items, what is the maximum value we can obtain if our knapsack is capacity j
vector<vector<int>> dp(N+1, vector<int>(W+1, 0));

for (int i = 1; i <= N; i++) {
	// iterate backwards to ensure each item is only used at most once
    for (int j = W; j >= w[i-1]; j--) { 
        dp[j] = max(dp[j], dp[j - w[i-1]] + v[i-1]);
    }
}
```

### Complete Knapsack
- Unlimited supply of all items
```cpp
for (int i = 1; i <= N; i++) {
	// iterate forwards to allow for item i to be used more than once
    for (int j = w[i-1]; j <= W; j++) { 
        dp[j] = max(dp[j], dp[j - w[i-1]] + v[i-1]);
    }
}
```