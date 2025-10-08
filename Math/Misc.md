## Matrix multiplication 
#matrix

```cpp
int rowsA = A.size();
vector<vector<int>> result(rowsA, vector<int>(colsB, 0));

for (int i = 0; i < rowsA; ++i) {
    for (int j = 0; j < colsB; ++j) {
        for (int k = 0; k < colsA; ++k) {
            result[i][j] += A[i][k] * B[k][j];
        }
    }
}

return result;
```

## Binary Exponentiation

```python
def binary_pow(base, power):
	result = 1
	two_power = base
	
	while power != 0:
		if power % 2 == 1:
			result *= two_power
		
		two_power *= two_power
		power >>= 1
	
	return result
```

## Chinese Remainder Theorem

```c++
long long modular_inverse(long long a, long long m) {
    long long m0 = m, t, q;
    long long x0 = 0, x1 = 1;
    
    if (m == 1) return 0;
    
    while (a > 1) {
        q = a / m;
        t = m;
        m = a % m;
        a = t;
        t = x0;
        x0 = x1 - q * x0;
        x1 = t;
    }
    
    if (x1 < 0) x1 += m0;
    return x1;
}

long long chinese_remainder_theorem(const vector<long long>& remainders, const vector<long long>& moduli) {
    long long product_of_moduli = 1;
    int num_equations = remainders.size();
    
    for (int i = 0; i < num_equations; i++) {
        product_of_moduli *= moduli[i];
    }
    
    long long solution = 0;
    
    for (int i = 0; i < num_equations; i++) {
        long long product_excluding = product_of_moduli / moduli[i];
        long long inverse = modular_inverse(product_excluding, moduli[i]);
        solution += remainders[i] * inverse * product_excluding;
    }
    
    return solution % product_of_moduli;
}
```

## Fast Fibonacci
$$
\begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix}^n
= \begin{pmatrix} F_{n+1} & F_n \\ F_n & F_{n-1} \end{pmatrix}
$$
Where $F_n$ is the nth term of the Fibonacci sequence. The matrix powers can be quickly calculated using Binary Exponentiation.
