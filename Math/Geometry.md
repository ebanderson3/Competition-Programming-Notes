## Line Intersection
Find the intersection between the line segment defined by $a_1$ and $a_2$, and the line segment defined by $b_1$ and $b_2$.

```python
def line_intersection(a1, a2, b1, b2) -> bool:
	av = (a2[0] - a1[0], a2[1] - a1[1])
	bv = (b2[0] - b1[0], b2[1] - b1[1])
	c = (b1[0] - a1[0], b1[1] - a1[1])
	
	d = bv[0] * av[1] - bv[1] * av[0]
	t = (c[0] * -bv[1] + bv[0] * c[1]) / d
	s = (av[0] * c[1] - c[0] * av[1]) / d
	
	return 0 <= t <= 1 and 0 <= s <= 1
```

## Polygon Area (Shoelace Algorithm)
Find area of polygon from list of vertexes.

```python
def polygon_area(vertexes) -> float:
	area = 0
	
	for i in range(len(vertexes)):
		j = (i + 1) % len(vertexes)
		
		area += (vertexes[i][0] * vertexes[j][1]) - (vertexes[j][0] * vertexes[i][1]);
	
	return abs(area) / 2
```

## Pick's Theorem
$A$ is the area of the polygon, $i$ is the number of points inside the polygon, and $b$ is the number of points on the boundary of the polygon.
$$A = i + \frac{b}{2} - 1$$
$$i = A - \frac{b}{2} + 1$$

## Closest Point on Line
Find the closest point on a line or line segment.

```python
def closest_point_on_line(a: complex, b: complex, p: complex) -> complex:
	r = b - a
	
	k = p - a
	t = k.real * r.real + k.imag * r.imag # k dot r
	t /= abs(r) * abs(r)
	
	return a + r * t

def closest_point_on_line_segment(a: complex, b: complex, p: complex) -> complex:
	r = b - a
	
	k = p - a
	t = k.real * r.real + k.imag * r.imag # k dot r
	t /= abs(r) * abs(r)
	
	t = min(1, max(0, t))
	
	return a + r * t
```
