---
title: "list 列表"
date: 2018-08-14 00:16
collection: 基本变量类型
---

[TOC]

# 列表
list 是最常用的线性数据结构
list是一系列元素的有序组合, 元素可以是任意对象，如数字，字符串，对象，列表等
list是可变的
list 可以嵌套任何数据类型





## 列表为可变类型

可变对象(列表，字典，集合等等)的改变，会影响所有指向该对象的变量

```
In [13]: l1 = [1, 2, 3]                                                                                                                                                                               

In [14]: l2 = l1                                                                                                                                                                                      

In [15]: l1.append(4)                                                                                                                                                                                 

In [16]: l1                                                                                                                                                                                           
Out[16]: [1, 2, 3, 4]

In [17]: l2                                                                                                                                                                                           
Out[17]: [1, 2, 3, 4]
```



## 定义列表

```python
In [1]: L = []

In [2]: L = list()

In [3]: L = [1, 2, 3]
```





## 列表常用操作

### 增
#### append 添加

```python
In [4]: L
Out[4]: [1, 2, 3]

In [5]: L.append(4)

In [6]: L
Out[6]: [1, 2, 3, 4]
```



#### extend 追加

```python
In [9]: L = [1, 2, 3, 4]

In [10]: L.extend([5, 6, 7])

In [11]: L
Out[11]: [1, 2, 3, 4, 5, 6, 7]
```



#### insert  插入

```python
In [11]: L
Out[11]: [1, 2, 3, 4, 5, 6, 7]

In [12]: L.insert(0, 0)

In [13]: L
Out[13]: [0, 1, 2, 3, 4, 5, 6, 7]
```



#### \+ 连接

```
In [21]: [1, 2, 3] + [4, 5, 6]
Out[21]: [1, 2, 3, 4, 5, 6]
```



#### \* 重复

```
In [23]: [1, 2, 3] * 3
Out[23]: [1, 2, 3, 1, 2, 3, 1, 2, 3]
```

```
In [25]: [[1, 2, 3]] * 3
Out[25]: [[1, 2, 3], [1, 2, 3], [1, 2, 3]]
```



### 删

#### remove 移除

从左到右查找第一个匹配的value，移除该元素

```python
In [15]: L
Out[15]: [0, 1, 2, 3, 4, 5, 6, 7]

In [16]: L.remove(3)

In [17]: L
Out[17]: [0, 1, 2, 4, 5, 6, 7]
```



#### pop 弹出

从列表尾部弹出一个元素

```python
In [18]: L
Out[18]: [0, 1, 2, 4, 5, 6, 7]

In [19]: L.pop(0)
Out[19]: 0

In [20]: L
Out[20]: [1, 2, 4, 5, 6, 7]
```



#### clear 清除

清除所有元素，剩空列表

```python
In [21]: L
Out[21]: [1, 2, 4, 5, 6, 7]

In [22]: L.clear()

In [23]: L
Out[23]: []
```





### 改

#### reverse 反转

将元素反转

```python
In [26]: L
Out[26]: [0, 1, 2, 3, 4, 3, 4, 5, 6, 7]

In [27]: L.reverse()

In [28]: L
Out[28]: [7, 6, 5, 4, 3, 4, 3, 2, 1, 0]
```



#### sort 排序

默认为升序，加上reverse参数为降序

```python
In [28]: L
Out[28]: [7, 6, 5, 4, 3, 4, 3, 2, 1, 0]

In [29]: L.sort()

In [30]: L
Out[30]: [0, 1, 2, 3, 3, 4, 4, 5, 6, 7]

In [30]: L
Out[30]: [0, 1, 2, 3, 3, 4, 4, 5, 6, 7]

In [31]: L.sort(reverse=True)

In [32]: L
Out[32]: [7, 6, 5, 4, 4, 3, 3, 2, 1, 0]
```



#### in 成员判断

```
In [30]: [3, 4] in [1, 2, [3, 4]]
Out[30]: True
```



#### copy 复制

L1 L2不是同一个object， 即浅copy

```python
In [1]: L = [1, 2, 3]

In [2]: L1 = L.copy()

In [3]: L2 = L

In [4]: L
Out[4]: [1, 2, 3]

In [5]: L1
Out[5]: [1, 2, 3]

In [6]: L2
Out[6]: [1, 2, 3]

In [7]: L.append(4)

In [8]: L
Out[8]: [1, 2, 3, 4]

In [9]: L1
Out[9]: [1, 2, 3]

In [10]: L2
Out[10]: [1, 2, 3, 4]

In [11]: id(L), id(L1), id(L2)
Out[11]: (4500763656, 4500804360, 4500763656)
```







### 查

#### count 查询次数 

```python
In [3]: L
Out[3]: [0, 1, 2, 3, 3, 4, 4, 5, 6, 7]

In [4]: L.count(3)
Out[4]: 2

In [5]: L.count(8)
Out[5]: 0
```

#### index 索引

```python
In [6]: L
Out[6]: [0, 1, 2, 3, 3, 4, 4, 5, 6, 7]

In [7]: L.index(3)
Out[7]: 3

In [8]: L.index(4)
Out[8]: 5

In [9]: L.index(8)
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-9-4d02a661bd55> in <module>()
----> 1 L.index(8)

ValueError: 8 is not in list

```



#### len

```

```



#### enumerate

可以在每次循环中同时得到下标和元素

```
In [5]: s = 'abcdefghijkl'

In [6]: for (index, char) in enumerate(s):
   ...:     print(index, char)
   ...:     
0 a
1 b
2 c
3 d
4 e
5 f
6 g
7 h
8 i
9 j
10 k
11 l
```

#### sort

```

```





### 下标操作及切片

#### 更新数值

```
In [29]: L
Out[29]: [0, 1, 2, 3, 3, 4, 4, 5, 6, 7]

In [30]: L[0] = 10

In [31]: L
Out[31]: [10, 1, 2, 3, 3, 4, 4, 5, 6, 7]
```



#### 范围取值

```
In [31]: L
Out[31]: [10, 1, 2, 3, 3, 4, 4, 5, 6, 7]

In [32]: L[3:5]
Out[32]: [3, 3]

In [33]: L[:5]
Out[33]: [10, 1, 2, 3, 3]

In [34]: L[3:]
Out[34]: [3, 3, 4, 4, 5, 6, 7]

In [35]: L[:]
Out[35]: [10, 1, 2, 3, 3, 4, 4, 5, 6, 7]

In [36]: L[3: -2]
Out[36]: [3, 3, 4, 4, 5]

In [37]: L[-4:-1]
Out[37]: [4, 5, 6]

In [38]: L[2:6:2]
Out[38]: [2, 3]

In [39]: L[6:2:-1]
Out[39]: [4, 4, 3, 3]

In [40]: L[::-1]
Out[40]: [7, 6, 5, 4, 4, 3, 3, 2, 1, 10]

In [41]: L[::2]
Out[41]: [10, 2, 3, 4, 6]
```





# 列表解析/列表生成器

列表解析是python的重要的语法糖
列表解析的速度比for in迭代快

```
ret = [expression for item in iterator]
```

等价于 
```python
ret = []
for item in iterator:
    ret.append(expression)
```




```python
In [2]: L
Out[2]: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

In [3]: [x+1 for x in L]
Out[3]: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

```



## 特点

迭代时间快

```python
In [10]: timeit.timeit('[x+1 for x in range(10)]')

Out[10]: 2.861458881001454

In [11]: timeit.timeit('''
    ...: l=[]
    ...: for x in range(10):
    ...:     l.append(x+1)
    ...: ''')
Out[11]: 3.5815405790053774
```



## 带条件操作

```
[expr for item in iterable if cond1 if cond2]
```

等价于

```
ret = []
for item in iterable:
	if cond1:
		if cond2:
			ret.append(expr)
```



列表推导式总共有两种形式：

```
[x for x in data if condition]
```

> 此处if主要起条件判断作用，data数据中只有满足if条件的才会被留下，最后统一生成为一个数据列表



```
[exp1 if condition else exp2 for x in data]
```

> 此处if...else主要起赋值作用，当data中的数据满足if条件时将其做exp1处理，否则按照exp2处理，最后统一生成为一个
> 数据列表







```python
Out[12]: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

In [13]: [x+1 for x in L if x%2 ==0]
Out[13]: [1, 3, 5, 7, 9]

In [15]: [x+1 for x in L if x%2 == 0 if x>2]
Out[15]: [5, 7, 9]

```



```

d = {20600000000005: 'new_type_string2', 20600000000006: 'new_type_string1'}
d_new = {20600000000005: 'new_type_string2'}

[{'id': k, 'text': v, 'type': type_value, 'is_new': True} if k in d_new.keys() else {'id': k, 'text': v, 'type': type_value, 'is_new': False} for k, v in d.items()]

>>>
[{'id': 20600000000005,
  'text': 'new_type_string2',
  'type': 'app',
  'is_new': True},
 {'id': 20600000000006,
  'text': 'new_type_string1',
  'type': 'app',
  'is_new': False}]
```

> 有数据错乱问题，需要测试



## 笛卡尔积

```
[expression for x in X for y in Y]
```

等价于
```python
ret = []
for x in X:
    for y in Y:
        ret.append(expression)
```


```python
In [17]: l1 = [1, 2, 3]

In [18]: l2 = [4, 5, 6]

In [19]: [(x, y) for x in l1 for y in l2]
Out[19]: [(1, 4), (1, 5), (1, 6), (2, 4), (2, 5), (2, 6), (3, 4), (3, 5), (3, 6)]
```





# example



## list to dict

```

In [59]: results = {}                                                                                                                                                                                           
In [60]: a                                                                                                                                                                                                      Out[60]: ['application_name', 'duration_minutes']

In [61]: for cluster in clusters: 
    ...:     results[cluster] = {a[i]: a[i] for i in range(len(a))}  
    ...:                                                                                                                                                                                                        
In [62]: results                                                                                                                                                                                                Out[62]: 
{'1': {'application_name': 'application_name',
  'duration_minutes': 'duration_minutes'},
 '2': {'application_name': 'application_name',
  'duration_minutes': 'duration_minutes'},
 '3': {'application_name': 'application_name',
  'duration_minutes': 'duration_minutes'}}
```


