---
title: "tuple"
date: 2018-08-17 21:27
collection: 基本变量类型
---

[TOC]



# 元组

元组是不可变的结构
元组和列表的大多数地方相似




参考源码
列表:https://github.com/python/cpython/blob/3.7/Include/listobject.h
元祖:https://github.com/python/cpython/blob/3.7/Include/tupleobject.h



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



## List vs Tuple

相同的元素，但是元组的存储空间，却比列表要少16字节

由于列表是动态的，所以它需要存储指针，来指向对应的元素(上述例子中，对于int型，8字节)。另外，由于列表可变，所以需要额外存储已经分配的长度大小(8字节)，这样才可以实时追踪列表空间的使用情况，当空间不足时，及时分配额外空间。

```
lst = [1, 2, 3]
lst.__sizeof__()
Out[1]:
64

tup = (1, 2, 3)
tup.__sizeof__()
Out[2]:
48
```



```
l = []
l.__sizeof__() // 空列表的存储空间为40字节
40
l.append(1)
l.__sizeof__()
72 // 加入了元素1之后，列表为其分配了可以存储4个元素的空间 (72 - 40)/8 = 4 l.append(2)
l.__sizeof__()
72 // 由于之前分配了空间，所以加入元素2，列表空间不变
l.append(3)
l.__sizeof__()
72 // 同上
l.append(4)
l.__sizeof__()
72 // 同上
l.append(5)
l.__sizeof__()
104 // 加入元素5之后，列表的空间不足，所以又额外分配了可以存储4个元素的空间
```



Python每次分配空间时都会额外多分配一些，这样的机制(over-allocating)保证了其操作的高效 性:增加/删除的时间复杂度均为O(1)。 

但是对于元组，情况就不同了。元组长度大小固定，元素不可变，所以存储空间固定。



是计算初始化一个相同元素的列表和元组分别所需的时间。我们可以看到，元组的初始化速 度，要比列表快5倍。 

```
  python3 -m timeit 'x=(1,2,3,4,5,6)'
  20000000 loops, best of 5: 9.97 nsec per loop
  python3 -m timeit 'x=[1,2,3,4,5,6]'
  5000000 loops, best of 5: 50.1 nsec per loop
```

但如果是索引操作的话，两者的速度差别非常小，几乎可以忽略不计。 

```
  python3 -m timeit -s 'x=[1,2,3,4,5,6]' 'y=x[3]'
  10000000 loops, best of 5: 22.2 nsec per loop
  python3 -m timeit -s 'x=(1,2,3,4,5,6)' 'y=x[3]'
  10000000 loops, best of 5: 21.9 nsec per loop
```



### 空列表创建

区别主要在于list()是一个function call，Python的function call会创建stack，并且进行一系列参数检查的
操作，比较expensive，反观[]是一个内置的C函数，可以直接被调用，因此效率高。

```
# option A 
empty_list = list()
  
# option B
empty_list = []
```



```
python -m timeit 'empty_list = list()'
10000000 loops, best of 3: 0.0829 usec per loop
python -m timeit 'empty_list = []'
10000000 loops, best of 3: 0.0218 usec per loop
python -m timeit 'empty_list = ()'
100000000 loops, best of 3: 0.0126 usec per loop
```



### 使用场景

如果存储的数据和数量不变，比如你有一个函数，需要返回的是一个地点的经纬度，然后直接传给前端
渲染，那么肯定选用元组更合适

```
def get_location():
      .....
      return (longitude, latitude)
```





如果存储的数据或数量是可变的，比如社交平台上的一个日志功能，是统计一个用户在一周之内看了哪
些用户的帖子，那么则用列表更合适。

```
viewer_owner_id_list = [] # 里面的每个元素记录了这个viewer一周内看过的所有owner的id
records = queryDB(viewer_id) # 索引数据库，拿到某个viewer一周内的日志 for record in records:
      viewer_owner_id_list.append(record.id)
```







