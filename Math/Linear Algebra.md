## System of 2 Equations
To solve this system:
$$
\begin{cases}
A_1 x + B_1 y = C_1\\
A_2 x + B_2 y = C_2
\end{cases}
$$
```python
det = A1 * B2 - A2 * B1
if det == 0: # No solution

det_x = C1 * B2 - C2 * B1
det_y = A1 * C2 - A2 * C1

x = det_x / det
y = det_y / det
```

## System of 3 Equations
To solve this system:
$$
\begin{cases}  
A_1 x + B_1 y + C_1 z = D_1 \\
A_2 x + B_2 y + C_2 z = D_2 \\
A_3 x + B_3 y + C_3 z = D_3
\end{cases}  
$$
```python
# determinant of 3x3 coefficient matrix
det = (
    A1 * (B2 * C3 - B3 * C2)
  - B1 * (A2 * C3 - A3 * C2)
  + C1 * (A2 * B3 - A3 * B2)
)

if det == 0:
    # No unique solution (either no solution or infinitely many)
    pass

# determinants for Cramer's rule (replace the appropriate column with RHS D's)
det_x = (
    D1 * (B2 * C3 - B3 * C2)
  - B1 * (D2 * C3 - D3 * C2)
  + C1 * (D2 * B3 - D3 * B2)
)

det_y = (
    A1 * (D2 * C3 - D3 * C2)
  - D1 * (A2 * C3 - A3 * C2)
  + C1 * (A2 * D3 - A3 * D2)
)

det_z = (
    A1 * (B2 * D3 - B3 * D2)
  - B1 * (A2 * D3 - A3 * D2)
  + D1 * (A2 * B3 - A3 * B2)
)

x = det_x / det
y = det_y / det
z = det_z / det
```

## Gaussian Elimination (Solving a system of N linear equations)
**What it does:** Solves a system of N linear equations with N unknowns by transforming the augmented matrix into row-echelon form and performing back-substitution. Returns the unique solution if it exists or an empty list if the system is singular.
**Requirements:**
- An augmented matrix of size ($N \times (N+1)$) representing the system `[A|b]`.
- All coefficients should be numeric (int or float).
- The system should ideally have a unique solution.
**Complexity:**
- Time: $O(N^3)$
- Space: $O(N^2)$
**Key insight:** Use pivoting and elimination to systematically zero out columns and solve for variables via back-substitution.

```python
def gauss(a):
    """
    Solves a system of N linear equations using Gaussian elimination.
    a: augmented matrix [ [A|b] ], size N x (N+1)
    Returns: list of solutions or [] if no unique solution
    """
    n = len(a)
    for i in range(n):
        # Find pivot row with maximum absolute value in current column
        mx = max(range(i, n), key=lambda r: abs(a[r][i]))
        if abs(a[mx][i]) < 1e-9: return []  # No unique solution
        a[i], a[mx] = a[mx], a[i]  # Swap pivot row

        # Normalize pivot row
        a[i] = [x / a[i][i] for x in a[i]]

        # Eliminate current column from other rows
        for j in range(n):
            if j != i:
                fac = a[j][i]
                a[j] = [a[j][k] - fac * a[i][k] for k in range(n+1)]

    # Extract solutions from last column
    return [row[-1] for row in a]
```

## Matrix Inversion using Gaussian Elimination (Used to find the inverse of a square matrix)
**What it does:** Computes the inverse of a given square matrix, if it exists, such that $A \cdot A^{-1} = I$.
**Requirements:**
* Input must be a square matrix $n \times n$ (list of lists or 2D array).
* The matrix must be non-singular (determinant ≠ 0).
* Familiarity with basic row operations (swap, multiply, add multiples).
**When to use:**
* Solving linear systems $Ax = b$ via $x = A^{-1}b$.
* Transformations in computer graphics, robotics, or physics simulations.
* Numerical methods requiring inverse computation.
**Complexity:**
* Time: $O(n^3)$
* Space: $O(n^2)$
**Key insight:** Transform the matrix into the identity matrix via row operations while performing the same operations on the identity matrix to produce the inverse.

```python
def invert_matrix(matrix):
    """
    Computes the inverse of a square matrix using Gaussian elimination.
    Returns the inverse as a new matrix if invertible, else raises ValueError.
    """
    n = len(matrix)
    
    # Create an augmented matrix [A | I]
    augmented = [row[:] + [1 if i == j else 0 for j in range(n)] for i, row in enumerate(matrix)]
    
    # Forward elimination
    for i in range(n):
        # Find pivot and swap if needed
        pivot_row = max(range(i, n), key=lambda r: abs(augmented[r][i]))
        if abs(augmented[pivot_row][i]) < 1e-12:
            raise ValueError("Matrix is singular and cannot be inverted")
        augmented[i], augmented[pivot_row] = augmented[pivot_row], augmented[i]
        
        # Normalize pivot row
        pivot_val = augmented[i][i]
        augmented[i] = [x / pivot_val for x in augmented[i]]
        
        # Eliminate other rows
        for j in range(n):
            if j != i:
                factor = augmented[j][i]
                augmented[j] = [augmented[j][k] - factor * augmented[i][k] for k in range(2*n)]
    
    # Extract the inverse matrix from the augmented matrix
    inverse = [row[n:] for row in augmented]
    return inverse
```
