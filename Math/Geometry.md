## Line Intersection
Find the intersection between the line segment defined by $a_1$ and $a_2$, and the line segment defined by $b_1$ and $b_2$.

```python
def line_intersection(a1, a2, b1, b2) -> bool:
	av = a2 - a1
	bv = b2 - b1
	c = b1 - a1
	
	d = bv.real * av.imag - bv.imag * av.real
	
	if d == 0: # Parallel
	
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
$A$ is the area of the polygon, $i$ is the number of points inside the polygon, and $b$ is the number of points on the boundary of the polygon (the vertexes).
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

## Point-in-Polygon Test
Checks if a given point is inside the polygon.

```python
def point_in_polygon(polygon: list[complex], point: complex) -> bool:
    inside = False
    n = len(polygon)
    x, y = point.real, point.imag

    for i in range(n):
        a = polygon[i]
        b = polygon[(i + 1) % n]
        ax, ay = a.real, a.imag
        bx, by = b.real, b.imag

        # Check if point is between y-coordinates of the edge
        if ((ay > y) != (by > y)) and (x < (bx - ax) * (y - ay) / (by - ay) + ax):
            inside = not inside

    return inside
```

## Polygon Distance Field
Signed and unsigned distance field for a polygon. A distance field represents the distance to closest point on a shape. A signed distance field is negative on the inside and positive on the outside.

```python
def distance_to_polygon(polygon: list[complex], point: complex) -> float:
    min_dist = math.inf
    n = len(polygon)
    
    for i in range(n):
        a = polygon[i]
        b = polygon[(i + 1) % n]
        
        dist = distance_to_line_segment(a, b, point)
        min_dist = min(min_dist, dist)
    
    return min_dist
```

```python
def signed_distance_to_polygon(polygon: list[complex], point: complex) -> float:
    min_dist = math.inf
    n = len(polygon)
    
    for i in range(n):
        a = polygon[i]
        b = polygon[(i + 1) % n]
        
        dist = distance_to_line_segment(a, b, point)
        min_dist = min(min_dist, dist)
    
    if point_in_polygon(polygon, point):
        return -min_dist
    
    return min_dist
```

## Which side of line
Determines which side of a line a given point is on.

**Explanation of sides:**
Imagine standing on (x1, y1) looking toward (x2, y2).
If the point is to your left, the return value is positive.
If it’s to your right, the return value is negative.
Exactly on the line returns 0.

```python
def point_side(px, py, x1, y1, x2, y2):
    """
    Determines which side of the line through (x1, y1) -> (x2, y2) 
    the point (px, py) lies on.

    Returns:
        >0 if point is on one side
        <0 if point is on the other side
        0 if point is exactly on the line
    """
    # Using the 2D cross product
    value = (x2 - x1) * (py - y1) - (y2 - y1) * (px - x1)
    return value
```

## Convex Hull (Finding the smallest convex polygon enclosing a set of points)

**What it does:** Computes the convex hull of a set of 2D points — the smallest convex polygon that contains all the given points.
**Requirements:**
* Input: A list of points in the plane, each represented as a complex number.
* Assumes at least three distinct points (degenerate cases can be handled separately).
**When to use:**
* Computing geometric boundaries or outer shapes in computational geometry.
* Used in collision detection, pattern recognition, shape analysis, and GIS applications.
* A common preprocessing step for algorithms like rotating calipers (e.g., finding the diameter or minimum bounding box).
**Complexity:**
* Time: **O(n log n)** — dominated by sorting the points.
* Space: **O(n)** — to store the hull.
**Key insight:**
Sort points and use the **cross product** to iteratively prune interior points while maintaining a **monotonic chain** that forms the convex boundary.
**Notes:**
* The returned list `hull` is ordered counterclockwise and forms the polygon boundary.
* The algorithm used here (Andrew’s monotone chain) is a clean and efficient variant of Graham’s scan.

```python
def convex_hull(points: list[complex]):
	"""
    Computes the convex hull of a set of 2D points represented as complex numbers.
    Returns the vertices of the convex hull in counterclockwise order.
    """
    # Sort points lexicographically: by real part (x), then imaginary part (y)
    pts = sorted(set(points))
    if len(pts) <= 1:
        return pts

    # Helper: cross product of vectors OA and OB (using complex numbers)
    def cross(o, a, b):
        return ((a - o).conjugate() * (b - o)).imag  # complex product
        # The sign of .imag gives the orientation (positive = counterclockwise)
        
        # If not using complex numbers:
        # return (a[0] - o[0]) * (b[1] - o[1]) - (a[1] - o[1]) * (b[0] - o[0])

    # Build lower hull
    lower = []
    for p in pts:
        while len(lower) >= 2 and cross(lower[-2], lower[-1], p) <= 0:
            lower.pop()
        lower.append(p)

    # Build upper hull
    upper = []
    for p in reversed(pts):
        while len(upper) >= 2 and cross(upper[-2], upper[-1], p) <= 0:
            upper.pop()
        upper.append(p)

    # Concatenate lower and upper parts (excluding last points to avoid duplicates)
    hull = lower[:-1] + upper[:-1]

    return hull
```

## Convexity Check for Polygon (Complex-number representation)

**What it does:** Determines whether a polygon described by complex-number vertices is **convex** or **concave** by checking the direction of each consecutive turn using cross products.
**Requirements:**
* Vertices are ordered **clockwise** or **counterclockwise**
* Polygon is **simple** (edges don’t self-intersect)
**When to use:**
* In computational geometry problems using complex numbers for 2D points
* Before performing convex hull or area computations
* To validate input polygons for graphics, physics, or mesh algorithms
**Complexity:**
* Time: **O(n)** (linear in number of vertices)
* Space: **O(1)** (constant extra memory)
**Key insight:**
For three consecutive vertices `(a, b, c)`, the **cross product sign** of vectors `(b−a)` and `(c−b)` must remain **consistent** throughout the polygon for it to be convex.

```python
def is_convex_polygon_complex(points: list[complex]):
    """
    Determine if a polygon (given by complex vertices) is convex.
    points: list[complex] — vertices in order (clockwise or counterclockwise)
    Returns True if convex, False if concave.
    """
    n = len(points)
    if n < 3:
        return False  # not enough vertices to form a polygon

    prev_sign = 0
    for i in range(n):
        a, b, c = points[i], points[(i + 1) % n], points[(i + 2) % n]
        # Cross product (b - a) × (c - b)
        cross = (b - a).real * (c - b).imag - (b - a).imag * (c - b).real

        if cross != 0:  # ignore collinear edges
            sign = 1 if cross > 0 else -1
            if prev_sign == 0:
                prev_sign = sign
            elif sign != prev_sign:
                return False  # direction changes ⇒ concave

    return True  # all turns consistent ⇒ convex
```

## Minimum Enclosing Circle (Smallest Circle Covering All Points)

**What it does:** Finds the smallest circle that encloses a given set of 2D points.
**When to use:**
* Computational geometry problems, e.g., bounding regions.
* Collision detection in games or simulations.
* Geospatial clustering or facility location problems.
#### Helper functions
```python
import random
import math
from typing import List, Tuple

Point = Tuple[float, float]

def dist(p1: Point, p2: Point) -> float:
    return math.hypot(p1[0] - p2[0], p1[1] - p2[1])

def circle_from(p1: Point, p2: Point, p3: Point = None) -> Tuple[Point, float]:
    """
    Returns the circle center and radius defined by 2 or 3 points.
    If only p1 and p2 are given, returns circle with diameter p1-p2.
    """
    if p3 is None:
        # Circle from 2 points: center is midpoint, radius is half distance
        cx = (p1[0] + p2[0]) / 2
        cy = (p1[1] + p2[1]) / 2
        r = dist(p1, p2) / 2
        return (cx, cy), r
    # Circle from 3 points: use circumcircle formula
    A = p2[0] - p1[0]
    B = p2[1] - p1[1]
    C = p3[0] - p1[0]
    D = p3[1] - p1[1]
    E = A*(p1[0]+p2[0]) + B*(p1[1]+p2[1])
    F = C*(p1[0]+p3[0]) + D*(p1[1]+p3[1])
    G = 2*(A*(p3[1]-p2[1]) - B*(p3[0]-p2[0]))
    if G == 0:  # Collinear points
        return circle_from(p1, p2)
    cx = (D*E - B*F) / G
    cy = (A*F - C*E) / G
    r = dist((cx, cy), p1)
    return (cx, cy), r

def is_in_circle(p: Point, c: Tuple[Point, float]) -> bool:
    center, radius = c
    return dist(p, center) <= radius + 1e-9  # handle floating-point errors
```

### Welzl’s randomized algorithm
**When to use:**
- When speed is valued over a deterministic output.
**Complexity:**
* Time: O(n) expected (Welzl’s randomized algorithm)
* Space: O(n) for recursion stack and point storage
**Key insight:** A minimal circle is uniquely defined by at most 3 points on its boundary. Welzl’s algorithm recursively builds this circle by incrementally adding points.

```python
def mec_helper(points: List[Point], boundary: List[Point], n: int) -> Tuple[Point, float]:
    """
    Recursive helper using Welzl's algorithm.
    points[:n] are points yet to consider
    boundary contains points on the current circle boundary (<=3)
    """
    if n == 0 or len(boundary) == 3:
        # Base case: circle defined by points in boundary
        if len(boundary) == 0:
            return ((0, 0), 0)
        elif len(boundary) == 1:
            return (boundary[0], 0)
        elif len(boundary) == 2:
            return circle_from(boundary[0], boundary[1])
        else:
            return circle_from(boundary[0], boundary[1], boundary[2])
    
    # Pick a random point
    idx = random.randint(0, n-1)
    points[idx], points[n-1] = points[n-1], points[idx]
    p = points[n-1]

    # Compute circle without p
    d = mec_helper(points, boundary, n-1)

    # If p is inside, it's already valid
    if is_in_circle(p, d):
        return d

    # Otherwise, p must be on boundary of the minimal circle
    return mec_helper(points, boundary + [p], n-1)

def minimum_enclosing_circle(points: List[Point]) -> Tuple[Point, float]:
    """
    Computes the minimal enclosing circle of a set of points.
    Returns (center, radius)
    """
    shuffled_points = points[:]
    random.shuffle(shuffled_points)
    return mec_helper(shuffled_points, [], len(shuffled_points))
```

### Deterministic Brute-Force
**When to use:**
* Competitive programming contests where deterministic results are required.
* Applications where reproducibility is critical.
* Smaller datasets (O(n³) worst-case is acceptable).
**Complexity:**
* Time: O(n³) worst-case
* Space: O(1) extra (excluding input)
**Key insight:** The minimal circle is defined by at most 3 points. By checking all pairs and triples, we can deterministically find the smallest enclosing circle.

```python
def minimum_enclosing_circle(points: List[Point]) -> Tuple[Point, float]:
    """
    Deterministic algorithm for the minimal enclosing circle.
    Checks all pairs and triples of points to find the smallest circle.
    Returns (center, radius)
    """
    if not points:
        return ((0, 0), 0)
    # Start with trivial circle around first point
    mec_center, mec_radius = points[0], 0

    n = len(points)
    for i in range(n):
        p1 = points[i]
        if not is_in_circle(p1, (mec_center, mec_radius)):
            mec_center, mec_radius = p1, 0
        for j in range(i):
            p2 = points[j]
            c, r = circle_from(p1, p2)
            if all(is_in_circle(p, (c, r)) for p in points[:i+1]):
                if r < mec_radius or mec_radius == 0:
                    mec_center, mec_radius = c, r
            for k in range(j):
                p3 = points[k]
                c, r = circle_from(p1, p2, p3)
                if all(is_in_circle(p, (c, r)) for p in points[:i+1]):
                    if r < mec_radius or mec_radius == 0:
                        mec_center, mec_radius = c, r
    return mec_center, mec_radius
```

### Hybrid Deterministic Version
**When to use:**
* Competitive programming when deterministic behavior is required.
* Large datasets where the O(n³) brute-force approach is too slow.
* Any application needing fast, reproducible results.
**Complexity:**
* Time: O(n) expected, O(n³) worst-case (rare, only for very specific point sets)
* Space: O(n) recursion stack
**Key insight:** Welzl’s algorithm only depends on the points that lie outside the current circle (boundary), so processing points in a fixed order still produces the minimal circle efficiently.

```python
def mec_helper(points: List[Point], boundary: List[Point], n: int) -> Tuple[Point, float]:
    if n == 0 or len(boundary) == 3:
        if len(boundary) == 0:
            return ((0, 0), 0)
        elif len(boundary) == 1:
            return (boundary[0], 0)
        elif len(boundary) == 2:
            return circle_from(boundary[0], boundary[1])
        else:
            return circle_from(boundary[0], boundary[1], boundary[2])

    p = points[n-1]
    d = mec_helper(points, boundary, n-1)
    if is_in_circle(p, d):
        return d
    return mec_helper(points, boundary + [p], n-1)

def minimum_enclosing_circle(points: List[Point]) -> Tuple[Point, float]:
    """
    Deterministic hybrid MEC: efficient like Welzl but reproducible.
    Returns (center, radius)
    """
    # Fixed deterministic order (no random shuffle)
    return mec_helper(points, [], len(points))
```




