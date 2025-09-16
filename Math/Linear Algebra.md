
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

## Line Intersection

Find the intersection between the line defined by $a_1$ and $a_2$, and the line defined by $b_1$ and $b_2$. Returns t and s.

```python
def line_intersection(a1, a2, b1, b2):
	av = (a2[0] - a1[0], a2[1] - a1[1])
	bv = (b2[0] - b1[0], b2[1] - b1[1])
	c = (b1[0] - a1[0], b1[1] - a1[1])
	
	d = bv[0] * av[1] - bv[1] * av[0]
	t = (c[0] * -bv[1] + bv[0] * c[1]) / d
	s = (av[0] * c[1] - c[0] * av[1]) / d
	
	return t, s
```