#cpp #math
# Summation of first n numbers from 1..n #summation
```cpp
int sum = (n * (n+1)) / 2; 
```
# Matrix multiplication #matrix
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

# Find all Primes up to a certain limit #prime
## Sieve of Erastosthenes
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
# Area of polygon defined by Point Coordinates #area
## Shoelace algorithm
```cpp
double shoelace_area(const std::vector<Point>& polygon) {
    double area = 0.0;
    int n = polygon.size();
    
    for (int i = 0; i < n; ++i) {
        int j = (i + 1) % n;
        area += (polygon[i].x * polygon[j].y) - (polygon[j].x * polygon[i].y);
    }
    
    return std::abs(area) / 2.0;
}
```