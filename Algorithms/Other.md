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
