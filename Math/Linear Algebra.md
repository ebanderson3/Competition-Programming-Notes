
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
