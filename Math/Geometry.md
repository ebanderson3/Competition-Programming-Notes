## Line Intersection
Find the intersection between the line segment defined by $a_1$ and $a_2$, and the line segment defined by $b_1$ and $b_2$.

```python
def line_intersection(a1, a2, b1, b2) -> bool:
	av = a2 - a1
	bv = b2 - b1
	c = b1 - a1
	
	d = bv.real * av.imag - bv.imag * av.real
	t = (c.real * -bv.imag + bv.real * c.imag) / d
	s = (av.real * c.imag - c.real * av.imag) / d
	
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
	v = b - a
	k = p - a
	
	t = k.real * v.real + k.imag * v.imag # k dot v
	t /= abs(v) * abs(v)
	
	return a + v * t
```

```python
def closest_point_on_line_segment(a: complex, b: complex, p: complex) -> complex:
	v = b - a
	k = p - a
	
	t = k.real * v.real + k.imag * v.imag # k dot v
	t /= abs(v) * abs(v)
	
	t = min(1, max(0, t))
	
	return a + v * t
```

```python
def distance_to_line_segment(a: complex, b: complex, p: complex) -> float:
	v = b - a
	k = p - a
	
	t = k.real * v.real + k.imag * v.imag # k dot v
	t /= abs(v) * abs(v)
	
	t = min(1, max(0, t))

	return abs(a + v * t - p)
```
