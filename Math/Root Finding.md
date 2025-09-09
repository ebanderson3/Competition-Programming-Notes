## Bisection
```C++
double find_root(double a, double b) {
	double f_a = f(a);
	
	while (true) {
		double c = (a + b) / 2.0;
		
		if (std::abs(a - b) < EPSILON) {
			return c;
		}
		
		if (a == b) return a;
		if (c == a || c == b) return c;
		
		double f_c = f(c);
		
		if (f_c == 0.0) return c;
		
		if (f_a * f_c >= 0.0) {
			a = c;
			f_a = f_c;
		} else {
			b = c;
		}
	}
}
```

## Newton's Method
```C++
double find_root(double a) {
	double x = a;
	
	for (int i = 0; i < MAX_ITERS; i++) {
		double fx = f(x);
		double fdx = fd(x);
		
		if (fdx == 0.0) return NAN;
		if (std::isnan(fx) || std::isnan(fdx)) return NAN;
		
		double new_x = x - fx / fdx;
		
		if (x == new_x) return new_x;
		
		x = new_x;
	}
	
	return NAN;
}
```
