## Simpson's Method

```C++
double integrate(double (*f)(double), double a, double b, double epsilon) {
	const int MAX_ITERS = 30;
	
	double size = std::abs(b - a);
	
	double first_last = f(a) + f(b);
	double four_sum = 0.0;
	double two_sum = 0.0;
	
	double last_answer = INFINITY;
	
	for (int l = 1; l < MAX_ITERS; l++) {
		long segments = 1L << l;
		double slice_width = size / (double) segments;
		
		for (long i = 1; i < segments; i += 2) {
			double x = a + i * slice_width;
			
			four_sum += f(x);
		}
		
		double answer = (slice_width / 3.0) * (first_last + 2.0 * two_sum + 4.0 * four_sum);
		double error = std::abs(last_answer - answer);
		
		if (error <= epsilon) return answer;
		
		two_sum += four_sum;
		four_sum = 0.0;
		last_answer = answer;
	}
	
	return last_answer;
}
```