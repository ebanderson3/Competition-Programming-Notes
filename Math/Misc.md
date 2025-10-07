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