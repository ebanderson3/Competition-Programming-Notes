#algorithms #cpp
# Max Subarray #array
## Kadane's Algorithm #dp #dynamic_programming
```cpp
for (int k = 0; k < n; k++) {
	sum = max(array[k],sum+array[k]);
	best = max(best,sum);
}
```
# Max submatrix #matrix
## Using Kadane's algorithm
```cpp
for (int i = 0; i < cols; i++) {
     vector<int> prefix(rows, 0);
     for (int j = i; j < cols; j++) {
         for (int k = 0; k < rows; k++) {
             prefix[k] += grid[k][j];
         }
         int cur = 0, best = INT_MIN; // start kadane’s on prefix sum for col
         for (auto d : prefix) {
             cur = max(cur + d, d);
             best = max(best, cur);
         }
         res = max(res, best);
     }
}
```
# Activity Selection #selection
Max number of events which can be attended as defined by time intervals
```cpp
sort(events.begin(), events.end()); // sort events by ending time

int res = 0;
int le = 0;

for (auto [e, b] : events) {
    if (b >= le) {
         res++;
         le = e;
    }  
}
```
