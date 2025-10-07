
## Golden Section Search

```C++
template <class F>
double golden_section_search(double a, double b, double epsilon, F f) {
	const double INV_PHI = 2.0 / (1.0 + sqrt(5.0)); // 1 / phi
	const int MAX_ITERS = 200;
	
	for (int i = 0; i < MAX_ITERS; i++) {
		double size = abs(b - a);
		
		if (size < epsilon) break;
		
		double c = b - size * INV_PHI;
        double d = a + size * INV_PHI;
		
		double fc = f(c);
		double fd = f(d);
		
		if (fc < fd) {
            b = d;
        } else {
            a = c;
		}
	}
	
	return (b + a) / 2.0;
}
```