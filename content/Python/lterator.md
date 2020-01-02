---
title: "lterator"
date: 2018-08-22 11:45
collection: 函数
---

[TOC]

# 迭代器

## 定义

可以直接作用于for循环的对象称为可迭代对象，Iterable

可以被next() 函数调用并不断返回下一个值的对象称为迭代器，Iterator

### isinstance

判断iterable对象

```
In [22]: from collections import Iterable

In [23]: isinstance([], Iterable)
Out[23]: True

In [24]: isinstance({}, Iterable)
Out[24]: True

In [25]: isinstance('abc', Iterable)
Out[25]: True

In [26]: isinstance((x for x in range(10)), Iterable)
Out[26]: True

In [27]: isinstance(100, Iterable)
Out[27]: False
```

### iter()

iter() 函数把list， dict， str等iterable变成iterator

```
In [28]: from collections import Iterator

In [29]: isinstance([], Iterator)
Out[29]: False

In [30]: isinstance(iter([]), Iterator)
Out[30]: True
```

## 判断iter类型

```
def is_iterable(param):
    try:
        iter(param)
        return True
    except TypeError:
        return False


params = [
    1234,
    '1234',
    [1, 2, 3, 4],
    set([1, 2, 3, 4]),
    {1:1, 2:2, 3:3, 4:4},
    (1, 2, 3, 4)
]

for param in params:
    print('{} is iterable? {}'.format(param, is_iterable(param)))

>>>
1234 is iterable? False
1234 is iterable? True
[1, 2, 3, 4] is iterable? True
{1, 2, 3, 4} is iterable? True
{1: 1, 2: 2, 3: 3, 4: 4} is iterable? True
(1, 2, 3, 4) is iterable? True
```

## 特点

Python的`Iterator`对象表示的是一个数据流，Iterator对象可以被`next()`函数调用并不断返回下一个数据，直到没有数据时抛出`StopIteration`错误。可以把这个数据流看做是一个有序序列，但我们却不能提前知道序列的长度，只能不断通过`next()`函数实现按需计算下一个数据，所以`Iterator`的计算是惰性的，只有在需要返回下一个数据时它才会计算。

`Iterator`甚至可以表示一个无限大的数据流，例如全体自然数。而使用list是永远不可能存储全体自然数的。

Python的`for`循环本质上就是通过不断调用`next()`函数实现的，例如：

```
In [72]: for i in [1, 2, 3, 4, 5]:
    ...:     pass
    ...:
```

实际上完全等价于：

```
# 首先获得Iterator对象:
it = iter([1, 2, 3, 4, 5])
# 循环:
while True:
    try:
        # 获得下一个值:
        x = next(it)
    except StopIteration:
        # 遇到StopIteration就退出循环
        break
```

如range(Python3)， readlines(Python3)就是迭代器

文件操作，就是用的迭代器

```
for i in f:
    pass
```
