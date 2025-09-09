```python
from decimal import Decimal, getcontext
getcontext().prec = 50
Decimal(1) / Decimal(7)   # 50-digit precision
```
