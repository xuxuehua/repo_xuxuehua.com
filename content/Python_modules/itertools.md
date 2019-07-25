---
title: "itertools"
date: 2018-10-05 02:58
---


[TOC]


# itertools

## chain()

连接两个循环器

```
In [2]: from itertools import chain

In [3]: chain([1, 2, 3], [4, 5, 6])
Out[3]: <itertools.chain at 0x10c411b70>

In [4]: list(chain([1, 2, 3], [4, 5, 6]))
Out[4]: [1, 2, 3, 4, 5, 6]
```







## combinations()

```
In [6]: from itertools import combinations

In [7]: list(combinations('abc', 2))
Out[7]: [('a', 'b'), ('a', 'c'), ('b', 'c')]
```



## combinations_with_replacement()

只允许两次选出的元素重复

```
In [8]: from itertools import combinations_with_replacement

In [9]: list(combinations_with_replacement('abc', 2))
Out[9]: [('a', 'a'), ('a', 'b'), ('a', 'c'), ('b', 'b'), ('b', 'c'), ('c', 'c')]
```



## compress()

根据[1, 1, 1, 0] 的真假值情况，选择第一个参数“ABCD”中的元素

```
In [10]: from itertools import compress

In [11]: list(compress('ABCD', [1, 1, 1, 0]))
Out[11]: ['A', 'B', 'C']
```



## islice()

切片操作， 返回一个迭代器

```
In [42]: from itertools import islice

In [43]: l = range(20)

In [44]: l
Out[44]: range(0, 20)

In [45]: t = iter(l)

In [46]: for i in islice(t, 5, 10):
    ...:     print(i)
    ...:
5
6
7
8
9

In [47]: for i in t:
    ...:     print(i)
    ...:
10
11
12
13
14
15
16
17
18
19
```





## groupby()

将key()函数作用于原循环器的各个元素，根据key函数结果，将拥有相同函数结果的元素分配到一个新的循环器中。

```
from itertools import *

def height_class(h):
    if h > 180:
        return 'tall'
    elif h < 160:
        return 'short'
    else:
        return 'middle'

friends = [191, 158, 159, 154, 170, 177, 181, 187, 190]

friends = sorted(friends, key = height_class)

for m, n in groupby(friends, key = height_class):
    print(m)
    print(list(n))
>>>

middle
[170, 177]
short
[158, 159, 154]
tall
[191, 181, 187, 190]
```



## permutations()

从'abcd'中挑选两个元素称为新的循环

```
In [4]: from itertools import permutations

In [5]: list(permutations('abc', 2))
Out[5]: [('a', 'b'), ('a', 'c'), ('b', 'a'), ('b', 'c'), ('c', 'a'), ('c', 'b')]
```



## product()

多个循环器集合的笛卡尔积，相当于嵌套循环

```
In [1]: from itertools import product

In [2]: product('abc', [1, 2])
Out[2]: <itertools.product at 0x10a399ea0>

In [3]: list(product('abc', [1, 2]))
Out[3]: [('a', 1), ('a', 2), ('b', 1), ('b', 2), ('c', 1), ('c', 2)]
```







