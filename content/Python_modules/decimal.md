---
title: "decimal"
date: 2018-10-20 00:12
---


[TOC]


# decimal

浮点计算

```python
from decimal import Decimal

print(Decimal('1.0') / Decimal('3.0'))  #浮点计算


from decimal import Decimal
from decimal import getcontext    # getcontext() 获取当前环境
getcontext().prec = 6
print(Decimal('1.0') / Decimal('3.0'))  
>
0.3333333333333333333333333333
0.333333
```