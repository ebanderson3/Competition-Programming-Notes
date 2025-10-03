# Fast Input
```python
import sys
input = sys.stdin.readline
```
# Fast Output
```python
import sys
sys.stdout.write(str(x) + "\n")
```
### Reading Integers
```python
n = int(input())              # single integer
a, b = map(int, input().split())   # multiple integers
arr = list(map(int, input().split()))  # list of integers
```
# Reading multiple lines
```python
# n lines, one integer each
n = int(input())
arr = [int(input()) for _ in range(n)]

# Grid with n lines, each with multiple integers
n = int(input())
matrix = [list(map(int, input().split())) for _ in range(n)]
```
# Strings
```python
s = input().strip()         # remove trailing newline/space
chars = list(s)             # convert string to list of characters
```
# Output
```python
print(x)                    # normal print
print(x, y)                 # space-separated
print(*arr)                 # unpack list with spaces
print(" ".join(map(str, arr)))  # join list of ints
```
# Reading Until EOF
```python
import sys
for line in sys.stdin:
    arr = list(map(int, line.split()))

# OR

while True:
	try:
		line = input()
	except:
		break
```
