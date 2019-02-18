---
title: "dict"
date: 2018-08-18 10:12
collection: 基本变量类型
---

[TOC]



# 字典



字典是一种无序集合
字典是一种kv结构
value 可以是任何对象
key是唯一的
key必须是可hash 对象




## 定义字典

```python
In [26]: d = {}

In [27]: d = dict()

In [28]: d = {'a':1, 'b':2}
```



dict(**kwargs) 使用name=value初始化一个字典

```
In [86]: x = dict(a=3)

In [87]: x
Out[87]: {'a': 3}

In [88]: type(x)
Out[88]: dict
```





### fromkeys 不常用

初始化一个新的字典

```
In [37]: dict.fromkeys([1, 2, 3], 'test')
Out[37]: {1: 'test', 2: 'test', 3: 'test'}
```



* 坑，少用

```
In [38]: dict.fromkeys([6, 7, 8], [1, {'name': 'rick'}, 2])
Out[38]: 
{6: [1, {'name': 'rick'}, 2],
 7: [1, {'name': 'rick'}, 2],
 8: [1, {'name': 'rick'}, 2]}

In [39]: x = dict.fromkeys([6, 7, 8], [1, {'name': 'rick'}, 2])

In [40]: x[7][1]['name'] = 'Rick'

In [41]: x
Out[41]: 
{6: [1, {'name': 'Rick'}, 2],
 7: [1, {'name': 'Rick'}, 2],
 8: [1, {'name': 'Rick'}, 2]}
```







## 字典的常用操作

### 添加元素

```
In [1]: d = {}

In [2]: d['a'] = 1

In [3]: d
Out[3]: {'a': 1}
```



### 查找元素

#### get

返回key对应的value

未找到不会报错

```
In [7]: d
Out[7]: {'a': 1, 'b': 2, 'c': 3}

In [8]: d.get('a')
Out[8]: 1

In [9]: d.get('d')

In [10]: 
```



#### in

```
In [10]: d
Out[10]: {'a': 1, 'b': 2, 'c': 3}

In [11]: 'a' in d
Out[11]: True
```



#### keys

```
In [12]: d
Out[12]: {'a': 1, 'b': 2, 'c': 3}

In [13]: d.keys()
Out[13]: dict_keys(['a', 'b', 'c'])
```



#### values

```
In [14]: d.values()
Out[14]: dict_values([1, 2, 3])
```



#### items

```
In [18]: d
Out[18]: {'a': 1, 'b': 2, 'c': 3}

In [19]: d.items()
Out[19]: dict_items([('a', 1), ('b', 2), ('c', 3)])
```



### 变更元素

#### setdefault

返回key对应的value

key不存在，则添加kv，value为default，如果没有设置，缺省为None

```
In [20]: d
Out[20]: {'a': 1, 'b': 2, 'c': 3}

In [21]: d.setdefault('c')
Out[21]: 3

In [22]: d.setdefault('d')

In [23]: d
Out[23]: {'a': 1, 'b': 2, 'c': 3, 'd': None}


In [31]: d.setdefault('e', 5)
Out[31]: 5

In [32]: d
Out[32]: {'a': 1, 'b': 2, 'c': 3, 'd': None, 'e': 5}
```



#### update

key不存在，就添加

key存在，覆盖已经存在key的value

```
In [33]: d
Out[33]: {'a': 1, 'b': 2, 'c': 3, 'd': None, 'e': 5}

In [34]: x = {'d': 5}

In [35]: d.update(x)

In [36]: d
Out[36]: {'a': 1, 'b': 2, 'c': 3, 'd': 5, 'e': 5}
```





### 访问元素


#### key

```python
In [29]: d
Out[29]: {'a': 1, 'b': 2}

In [30]: d['a'] = 5

In [31]: d
Out[31]: {'a': 5, 'b': 2}

In [32]: d['c']
---------------------------------------------------------------------------
KeyError                                  Traceback (most recent call last)
<ipython-input-32-3e4d85f12902> in <module>()
----> 1 d['c']

KeyError: 'c'

In [2]: d.get('a')
Out[2]: 1

In [3]: d.get('c', 'cannot found c')
Out[3]: 'cannot found c'
```

#### keys, values, items 遍历字典

```python
In [33]: d
Out[33]: {'a': 5, 'b': 2}

In [34]: d.keys()
Out[34]: dict_keys(['a', 'b'])

In [35]: d.values()
Out[35]: dict_values([5, 2])

In [36]: d.items()
Out[36]: dict_items([('a', 5), ('b', 2)])

In [2]: d
Out[2]: {'a': 1, 'b': 2, 'c': 3}

In [3]: for k, v in d.items():
   ...:     print('{0} -> {1}'.format(k, v))
   ...:     
c -> 3
b -> 2
a -> 1
```



```
In [94]: d
Out[94]: {'a': 1, 'b': 2}

In [95]: for k in d.keys():
    ...:     print(k)
    ...:
a
b

In [96]: for v in d.values():
    ...:     print(v)
    ...:
1
2
```



```
In [97]: d
Out[97]: {'a': 1, 'b': 2}

In [99]: for item in d.items():
    ...:     print(type(item), item)
    ...:
    ...:
<class 'tuple'> ('a', 1)
<class 'tuple'> ('b', 2)
```



### 删除元素

#### pop

key存在，移除并返回value

key不存在，返回给定的default值

default为设置，抛出keyerror异常

```python
In [37]: d
Out[37]: {'a': 5, 'b': 2}

In [38]: d.pop('a')
Out[38]: 5

In [39]: d
Out[39]: {'b': 2}

In [40]: d.pop('c')
---------------------------------------------------------------------------
KeyError                                  Traceback (most recent call last)
<ipython-input-40-adf338c8a0db> in <module>()
----> 1 d.pop('c')

KeyError: 'c'

In [41]: d.pop('c', True)
Out[41]: True
```

#### popitem 

随机删除任意的键值对

```python
In [44]: d
Out[44]: {'a': 1, 'b': 2}

In [45]: d.popitem()
Out[45]: ('a', 1)

In [46]: d.popitem()
Out[46]: ('b', 2)

In [47]: d
Out[47]: {}
```

#### del 

关键字 删除

实际上删除的是名称，而不是对象

```python
In [4]: d
Out[4]: {'a': 1, 'b': 2}

In [5]: del d['a']

In [6]: d
Out[6]: {'b': 2}
```



#### clear

清空字典

```
In [90]: d
Out[90]: {'a': 1, 'b': 2}

In [91]: d.clear()

In [92]: d
Out[92]: {}
```



## 字典解析

### 基本语法

`ret = {exprK:exprV for item in iterator}`

等价于

```python
ret = dict()
for item in iterator:
    ret.update({exprK: exprV})
```

```python
In [24]: d = {'a': 1, 'b': 2}

In [25]: {k:v for k, v in d.items()}
Out[25]: {'a': 1, 'b': 2}

In [26]: l1
Out[26]: [1, 2, 3]

In [27]: l2
Out[27]: [4, 5, 6]

In [28]: {x:y for x in l1 for y in l2}
Out[28]: {1: 6, 2: 6, 3: 6}

In [29]: {k:v for k, v in [('a', 1), ('b', 2)]}
Out[29]: {'a': 1, 'b': 2}

```