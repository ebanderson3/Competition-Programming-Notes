#cpp #combinatorics 

## Generate all permutations
#permuatation
Make sure container is sorted first.
```cpp
do {
	// process permutation
while (next_permutation(v.begin(), v.end()));
```

## Generate all combinations 
#combination
Returns all combinations of size `k`.
```cpp
void backtrack(const std::vector<int>& arr, std::vector<int>& combination, int start, int k) {

    if (combination.size() == k) {
        // process combination
    }

    for (int i = start; i < arr.size(); ++i) {
        combination.push_back(arr[i]); // Include the current element
        backtrack(arr, combination, i + 1, k); // Recur for the next elements
        combination.pop_back(); // Backtrack (exclude the current element)
    }
}
  
void generate_combinations(const std::vector<int>& arr, int k) {
    std::vector<int> combination;
    backtrack(arr, combination, 0, k);
}
```