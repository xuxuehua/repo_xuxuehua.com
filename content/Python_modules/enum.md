---
title: "enum"
date: 2019-06-29 18:13
---
[TOC]



# enum 枚举类型

枚举标签名不能重复， 但是数值可以相同， 后面相同的标签表示第一个枚举的别名





## Enum



### 保证常量不可变



```
from enum import Enum

class VIP(Enum):
    YELLOW = 1
    GREEN = 2
    BLACK = 3 
    RED = 4


print(VIP.YELLOW)
VIP.YELLOW = 6

>>>
VIP.YELLOW
Traceback (most recent call last):
  File "2.py", line 11, in <module>
    VIP.YELLOW = 6
  File "/Users/rxu/.pyenv/versions/3.5.3/lib/python3.5/enum.py", line 311, in __setatt
r__
    raise AttributeError('Cannot reassign members.')
AttributeError: Cannot reassign members.
```



### value 获取值

```
from enum import Enum

class VIP(Enum):
    YELLOW = 1
    GREEN = 2
    BLACK = 3 
    RED = 4


print(VIP.YELLOW.value)
>>>
1
```



### name 获取标签

```
from enum import Enum

class VIP(Enum):
    YELLOW = 1
    GREEN = 2
    BLACK = 3 
    RED = 4


print(VIP.YELLOW.name)
>>>
YELLOW
```



### 枚举名字

```
from enum import Enum

class VIP(Enum):
    YELLOW = 1
    GREEN = 2
    BLACK = 3 
    RED = 4


print(VIP['YELLOW'])
>>>
VIP.YELLOW
```



### 遍历枚举

```
from enum import Enum

class VIP(Enum):
    YELLOW = 1
    GREEN = 2
    BLACK = 3 
    RED = 4


for i in VIP.__members__:
    print(i)

for i in VIP.__members__.items():
    print(i)
    
>>>
YELLOW
GREEN
BLACK
RED
('YELLOW', <VIP.YELLOW: 1>)
('GREEN', <VIP.GREEN: 2>)
('BLACK', <VIP.BLACK: 3>)
('RED', <VIP.RED: 4>)
```





## IntEnum

对数值有整型限制



### unique 唯一值

```
from enum import Enum
from enum import IntEnum, unique

@unique
class VIP(IntEnum):
    YELLOW = 1
    GREEN = 1
    BLACK = 3 
    RED = 4

>>>
Traceback (most recent call last):
  File "2.py", line 5, in <module>
    class VIP(IntEnum):
  File "/Users/rxu/.pyenv/versions/3.5.3/lib/python3.5/enum.py", line 573, in unique
    (enumeration, alias_details))
ValueError: duplicate values found in <enum 'VIP'>: GREEN -> YELLOW
```

