```python
import random
import sys

def generate_maze(width, height):
    rows = 2 * height + 1
    cols = 2 * width + 1
    grid = [['#' for _ in range(cols)] for _ in range(rows)]
    visited = [[False] * width for _ in range(height)]
    
    directions = [(0, 1), (1, 0), (0, -1), (-1, 0)]
    
    stack = [(0, 0)]
    visited[0][0] = True
    grid[1][1] = '.'
    
    while stack:
        current_r, current_c = stack[-1]
        unvisited_neighbors = []
        
        for dr, dc in directions:
            next_r = current_r + dr
            next_c = current_c + dc
            if 0 <= next_r < height and 0 <= next_c < width and not visited[next_r][next_c]:
                unvisited_neighbors.append((next_r, next_c, dr, dc))
                
        if unvisited_neighbors:
            next_r, next_c, dr, dc = random.choice(unvisited_neighbors)
            wall_r = 2 * current_r + 1 + dr
            wall_c = 2 * current_c + 1 + dc
            grid[wall_r][wall_c] = '.'
            next_cell_r = 2 * next_r + 1
            next_cell_c = 2 * next_c + 1
            grid[next_cell_r][next_cell_c] = '.'
            visited[next_r][next_c] = True
            stack.append((next_r, next_c))
        else:
            stack.pop()
            
    return grid

def main():
    width, height = 2, 2
        
    maze = generate_maze(width, height)
    for row in maze:
        print(''.join(row))

if __name__ == "__main__":
    main()
```