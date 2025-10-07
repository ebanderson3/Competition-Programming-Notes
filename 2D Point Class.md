```c++
import math

eps = 1e-9

def sign(x):
    """Returns the sign of x with tolerance eps"""
    return (x > eps) - (x < -eps)

class PT:
    def __init__(self, x=0.0, y=0.0):
        """Initialize a 2D point with coordinates (x, y)"""
        self.x = x
        self.y = y

    def __add__(self, other):
        """Vector addition: returns self + other"""
        return PT(self.x + other.x, self.y + other.y)

    def __sub__(self, other):
        """Vector subtraction: returns self - other"""
        return PT(self.x - other.x, self.y - other.y)

    def __mul__(self, scalar):
        """Scalar multiplication: returns self * scalar"""
        return PT(self.x * scalar, self.y * scalar)

    def __rmul__(self, scalar):
        """Reverse scalar multiplication: returns scalar * self"""
        return PT(self.x * scalar, self.y * scalar)

    def __truediv__(self, scalar):
        """Scalar division: returns self / scalar"""
        return PT(self.x / scalar, self.y / scalar)

    def __eq__(self, other):
        """Equality comparison with tolerance eps"""
        return sign(self.x - other.x) == 0 and sign(self.y - other.y) == 0

    def __ne__(self, other):
        """Inequality comparison with tolerance eps"""
        return not self == other

    def __lt__(self, other):
        """Less than comparison (lexicographic order: x first, then y)"""
        if sign(self.x - other.x) == 0:
            return self.y < other.y
        return self.x < other.x

    def __gt__(self, other):
        """Greater than comparison (lexicographic order: x first, then y)"""
        if sign(self.x - other.x) == 0:
            return self.y > other.y
        return self.x > other.x

    def norm(self):
        """Returns the Euclidean norm (magnitude) of the vector"""
        return math.sqrt(self.x * self.x + self.y * self.y)

    def norm2(self):
        """Returns the squared Euclidean norm of the vector"""
        return self.x * self.x + self.y * self.y

    def perp(self):
        """Returns a perpendicular vector (rotated 90 degrees counterclockwise)"""
        return PT(-self.y, self.x)

    def arg(self):
        """Returns the angle in radians of the vector with respect to x-axis"""
        return math.atan2(self.y, self.x)

    def truncate(self, r):
        """Returns a vector with the same direction but scaled to length r"""
        k = self.norm()
        if abs(k) < eps:
            return PT(self.x, self.y)
        r /= k
        return PT(self.x * r, self.y * r)

    def __repr__(self):
        """String representation of the point for debugging"""
        return f"PT({self.x}, {self.y})"
```
