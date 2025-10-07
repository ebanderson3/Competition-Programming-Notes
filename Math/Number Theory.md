## Sieve of Eratosthenes
```cpp
vector<bool> prime(n + 1, true);
for (int p = 2; p * p <= n; p++) {
	if (prime[p] == true) {
		for (int i = p * p; i <= n; i += p) {
			prime[i] = false;
		}
	}
}

// prime[i] = if i is prime or not
```

## Number of multiples of k in range
Number of multiples of `k` in `[a..b]`
```python
num = b // k - (a - 1) // k
```
