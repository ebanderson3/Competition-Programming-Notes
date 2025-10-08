## Multiples in a Range
Number of multiples of `k` in `[a..b]`
```python
num = b // k - (a - 1) // k
```
## Sieve of Eratosthenes (Classic)
**What it does:** Finds all prime numbers up to n by iteratively marking multiples of each prime as composite
**Requirements:**
- Memory for boolean array of size n
- Upper bound n known in advance
**When to use:**
- Need all primes up to n
- Multiple primality queries below n
- Preprocessing for other algorithms
- n ≤ 10^7 typically
**Complexity:**
- Time: O(n log log n)
- Space: O(n)
**Key insight:** Only need to check up to √n; start marking from i² as smaller multiples already marked

```python
def sieve_of_eratosthenes(n):
    """
    Find all primes up to n
    Returns: List of primes and boolean array
    """
    if n < 2:
        return [], []
    
    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False
    
    for i in range(2, int(n**0.5) + 1):
        if is_prime[i]:
            for j in range(i*i, n + 1, i):
                is_prime[j] = False
    
    primes = [i for i in range(n + 1) if is_prime[i]]
    return primes, is_prime
```
## Segmented Sieve (For Large Ranges)
**What it does:** Finds all primes in range \[L, R] using segmented approach, useful when R is large but R-L is manageable
**Requirements:**
- Primes up to √R
- Memory for range \[L, R] only
**When to use:**
- Finding primes in specific range
- When R is large (up to 10^12)
- When R-L ≤ 10^6
- Memory-constrained environments
**Complexity:**
- Time: O((R-L+1) log log R + √R log log √R)
- Space: O(R-L+1 + √R)
**Key insight:** Only need primes up to √R to sieve range \[L, R]; process range in segments
```python
def segmented_sieve(L, R):
    """
    Find all primes in range [L, R] where R can be large
    Efficient when R-L is small (≤10^6)
    """
    import math
    
    # Find all primes up to sqrt(R)
    limit = int(math.sqrt(R)) + 1
    base_primes, _ = sieve_of_eratosthenes(limit)
    
    # Create boolean array for [L, R]
    size = R - L + 1
    is_prime = [True] * size
    
    # Special case for 1
    if L == 1:
        is_prime[0] = False
    
    # Mark multiples of each prime
    for prime in base_primes:
        # Find first multiple of prime >= L
        start = max(prime * prime, L + (prime - L % prime) % prime)
        
        for j in range(start, R + 1, prime):
            is_prime[j - L] = False
    
    # Collect primes
    primes = [L + i for i in range(size) if is_prime[i]]
    return primes
```
## Miller-Rabin Primality Test
**What it does:** Tests if a single number is prime using deterministic with witnesses method
**Requirements:**
- Fast modular exponentiation
- Random number generator (for probabilistic version)
**When to use:**
- Testing large numbers for primality
- Cryptographic applications
- When n > 10^9
- Need fast single-number primality test
**Complexity:**
- Time: O(k log³ n) where k is number of rounds
- Space: O(1)
**Key insight:** Based on Fermat's Little Theorem; if n is prime, a^(n-1) ≡ 1 (mod n) with special handling for strong pseudoprimes
```python
def deterministic_miller_rabin(n):
    """
    Deterministic version for n < 2^64
    """
    if n < 2:
        return False
    if n == 2 or n == 3:
        return True
    if n % 2 == 0:
        return False
    
    # Deterministic witnesses for different ranges
    witnesses = []
    if n < 2047:
        witnesses = [2]
    elif n < 1373653:
        witnesses = [2, 3]
    elif n < 9080191:
        witnesses = [31, 73]
    elif n < 25326001:
        witnesses = [2, 3, 5]
    elif n < 3215031751:
        witnesses = [2, 3, 5, 7]
    elif n < 4759123141:
        witnesses = [2, 7, 61]
    elif n < 1122004669633:
        witnesses = [2, 13, 23, 1662803]
    elif n < 2152302898747:
        witnesses = [2, 3, 5, 7, 11]
    else:
        witnesses = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37]
    
    # Same algorithm as above but with specific witnesses
    r, d = 0, n - 1
    while d % 2 == 0:
        r += 1
        d //= 2
    
    for a in witnesses:
        if a >= n:
            continue
        x = pow(a, d, n)
        if x == 1 or x == n - 1:
            continue
        
        for _ in range(r - 1):
            x = pow(x, 2, n)
            if x == n - 1:
                break
        else:
            return False
    
    return True
```
## Trial Division (Simple Primality Test)
**What it does:** Tests primality by checking divisibility up to √n
**Requirements:**
- Basic arithmetic operations only
**When to use:**
- Small numbers (n < 10^6)
- Simple implementation needed
- Educational purposes
- When memory is extremely limited
**Complexity:**
- Time: O(√n)
- Space: O(1)
**Key insight:** All primes > 3 are of form 6k±1; only need to check divisors up to √n

```python
def is_prime_trial(n):
    """
    Simple primality test by trial division
    """
    if n < 2:
        return False
    if n == 2:
        return True
    if n % 2 == 0:
        return False
    
    for i in range(3, int(n**0.5) + 1, 2):
        if n % i == 0:
            return False
    return True

def optimized_trial_division(n):
    """
    Optimized with 6k±1 optimization
    """
    if n <= 1:
        return False
    if n <= 3:
        return True
    if n % 2 == 0 or n % 3 == 0:
        return False
    
    i = 5
    while i * i <= n:
        if n % i == 0 or n % (i + 2) == 0:
            return False
        i += 6
    
    return True
```
## Linear Sieve (Optimal for Dense Prime Generation)
**What it does:** Generates all primes up to n with exactly O(n) operations, also finds smallest prime factor
**Requirements:**
- Memory for SPF array
- Sequential processing
**When to use:**
- Need smallest prime factors
- Optimal time complexity required
- Preprocessing for factorization
- Computing multiplicative functions
**Complexity:**
- Time: O(n) - exactly n operations
- Space: O(n)
**Key insight:** Each composite number is marked exactly once by its smallest prime factor

```python
def linear_sieve(n):
    """
    Generate primes with exactly O(n) operations
    Also computes smallest prime factor for each number
    """
    spf = [0] * (n + 1)  # smallest prime factor
    primes = []
    
    for i in range(2, n + 1):
        if spf[i] == 0:  # i is prime
            spf[i] = i
            primes.append(i)
        
        j = 0
        while j < len(primes) and primes[j] <= spf[i] and i * primes[j] <= n:
            spf[i * primes[j]] = primes[j]
            j += 1
    
    return primes, spf
```

## Modular Multiplicative Inverse
**What it does:** Finds the number `x` such that `(n * x) % m == 1`, meaning `x` is the multiplicative inverse of `n` under modulo `m`.
**Requirements:**
- `n` and `m` must be integers.
- An inverse exists **only if** `gcd(n, m) == 1` (i.e., `n` and `m` are coprime).
- Uses the **Extended Euclidean Algorithm** to compute coefficients satisfying Bézout’s identity.
**When to use:**
- Modular division in algorithms (e.g., combinatorics mod prime, modular arithmetic problems).
- Cryptographic systems such as RSA.
- Implementing modular inverses for modular linear equations or modular inverses of factorials.
**Complexity:**
- Time: `O(log m)`
- Space: `O(1)`
**Key insight:** The Extended Euclidean Algorithm not only computes `gcd(a, b)` but also finds integers `x` and `y` such that `a*x + b*y = gcd(a, b)` — when `gcd(a, b) == 1`, `x` is the modular inverse of `a` modulo `b`.

```python
def mod_inverse(n, m):
    """
    Returns the modular multiplicative inverse of n under modulo m.
    If inverse doesn't exist, returns None.
    """
    # Extended Euclidean Algorithm
    a0, a1 = m, n
    x0, x1 = 0, 1  # Coefficients for Bézout's identity

    while a1 != 0:
        q = a0 // a1
        a0, a1 = a1, a0 - q * a1
        x0, x1 = x1, x0 - q * x1

    # If gcd != 1, no inverse exists
    if a0 != 1:
        return None

    # Normalize result to be in range [0, m)
    return x0 % m
```