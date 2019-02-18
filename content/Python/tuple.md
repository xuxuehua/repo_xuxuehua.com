---
title: "tuple"
date: 2018-08-17 21:27
collection: 基本变量类型
---

[TOC]



# 元组

元组是不可变的结构
元组和列表的大多数地方相似



## 定义元组

```python
In [26]: T = ()

In [27]: T = tuple()

In [28]: T = (1, 2, 3)
```

## 元组常用操作



### count 计数

```
In [1]: name  = ('Rick', 'Michelle')

In [3]: name.count('Rick')
Out[3]: 1

In [4]: name.index('Rick')
Out[4]: 0
```



### index 索引

```
In [1]: name  = ('Rick', 'Michelle')

In [4]: name.index('Rick')
Out[4]: 0
```



### len 长度

```
In [33]: t = (1, 2, 3)

In [34]: len(t)
Out[34]: 3
```



### 下标操作及切片

```
In [7]: T
Out[7]: (1, 2, 3, 4, 5)

In [8]: T[3:5]
Out[8]: (4, 5)

In [9]: T[3:]
Out[9]: (4, 5)

In [10]: T[:]
Out[10]: (1, 2, 3, 4, 5)

In [11]: T[3:-2]
Out[11]: ()

In [12]: T[3:-1]
Out[12]: (4,)

In [13]: T[-4:-1]
Out[13]: (2, 3, 4)

In [14]: T[1:6:2]
Out[14]: (2, 4)

In [15]: T[::-1]
Out[15]: (5, 4, 3, 2, 1)

In [16]: T[::2]
Out[16]: (1, 3, 5)
```




### Packing & Unpacking


```python
In [42]: x, y = (1, 2)

In [43]: x
Out[43]: 1

In [44]: y
Out[44]: 2

In [45]: x, *y = (1, 2, 3, 4)

In [46]: x
Out[46]: 1

In [48]: y
Out[48]: [2, 3, 4]

In [49]: x, *_, y = (1, 2, 3, 4)

In [50]: x
Out[50]: 1

In [51]: y
Out[51]: 4

In [52]: _
Out[52]: [2, 3]

In [53]: x, (y, z) = (1, (2, 3))

In [54]: x
Out[54]: 1

In [55]: y
Out[55]: 2

In [56]: z
Out[56]: 3
In [68]: x = 1

In [69]: y = 2

In [70]: x, y = y, x

In [71]: x
Out[71]: 2

In [72]: y
Out[72]: 1
```
