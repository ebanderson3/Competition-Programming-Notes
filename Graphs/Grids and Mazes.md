# Difference Arrays 
#maze
### 4 directions (N, E, S, W)
```cpp
int dx[] = {-1, 0, 1, 0};
int dy[] = {0, 1, 0, -1};
```
### 8 directions (N , NE, E, SE, S, SW, W, NW)
```cpp
int dx[] = {-1, -1, 0, 1, 1, 1, 0, -1};
int dy[] = {0, 1, 1, 1, 0, -1, -1, -1};
```
### Knight movement
```cpp
int dx[] = {2, 1, -1, -2, -2, -1, 1, 2};
int dy[] = {1, 2, 2, 1, -1, -2, -2, -1};
```
### Bishop movement (diagonal)
```cpp
int dx[] = {-1, -1, 1, 1};
int dy[] = {-1, 1, -1, 1};
```
### Usage (within BFS)
```cpp
for (int i = 0; i < NDIRS; i++) {
	int nx = x + dx[i], ny = y + dy[i];
	if (inbounds(x) && inbounds(y) && !vis[x][y]) // good node
}
```

# 2D Neighbor Formulas

### Cardinal Directions
```python
for i in range(4):
	x_off = (100 >> i * 2 & 3) - 1
	y_off = (25 >> i * 2 & 3) - 1
```
### All Adjacent
```python
for i in range(8):
	x_off = (9602 >> i * 2 & 3) - 1
	y_off = (602 >> i * 2 & 3) - 1
```
### Diagonals Only
```python
for i in range(4):
	x_off = (40 >> i * 2 & 3) - 1
	y_off = (10 >> i * 2 & 3) - 1
```
