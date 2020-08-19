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

## 特点

字典和集合的内部结构都是一张哈希表。

对于字典而言，这张表存储了哈希值(hash)、键和值这3个元素。 



假设列表有n个元素，而查找的过程要遍历列表，那么时间复杂度就为O(n)。即使我们先对列表进行排序， 然后使用二分查找，也会需要O(logn)的时间复杂度，更何况，列表的排序还需要O(nlogn)的时间。 

但如果我们用字典来存储这些数据，那么查找就会非常便捷高效，只需O(1)的时间复杂度就可以完成。原因 也很简单，刚刚提到过的，字典的内部组成是一张哈希表，你可以直接通过键的哈希值，找到其对应的值。 



dict 属于mapping 类型

```
from collections.abc import Mapping, MutableMapping

a = {}
print(isinstance(a, MutableMapping))

>>>
True
```




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

### 插入元素

每次向字典或集合插入一个元素时，Python会首先计算键的哈希值(hash(key))，再和 mask = PyDicMinSize - 1做与操作，计算这个元素应该插入哈希表的位置index = hash(key) & mask。如果哈希表中 此位置是空的，那么这个元素就会被插入其中。 

而如果此位置已被占用，Python便会比较两个元素的哈希值和键是否相等。 

若两者都相等，则表明这个元素已经存在，如果值不同，则更新值。

若两者中有一个不相等，这种情况我们通常称为哈希冲突(hash collision)，意思是两个元素的键不相 等，但是哈希值相等。这种情况下，Python便会继续寻找表中空余的位置，直到找到位置为止。 

值得一提的是，通常来说，遇到这种情况，最简单的方式是线性寻找，即从这个位置开始，挨个往后寻找空 位。当然，Python内部对此进行了优化 

```
In [1]: d = {}

In [2]: d['a'] = 1

In [3]: d
Out[3]: {'a': 1}
```



### 查找元素

和前面的插入操作类似，Python会根据哈希值，找到其应该处于的位置;然后，比较哈希表这个位置中元素的哈希值和键，与需要查找的元素是否相等。如果相等，则直接返回;如果不等，则继续查找，直到找到空位或者抛出异常为止。

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



### 元素排序

```
d = {'b': 1, 'a': 2, 'c': 10}

d_sorted_by_key = sorted(d.items(), key=lambda x: x[0]) # 根据字典键的升序排序 
d_sorted_by_value = sorted(d.items(), key=lambda x: x[1]) # 根据字典值的升序排序 d_sorted_by_key

[('a', 2), ('b', 1), ('c', 10)]
d_sorted_by_value
[('b', 1), ('a', 2), ('c', 10)]
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

对于删除操作，Python会暂时对这个位置的元素，赋于一个特殊的值，等到重新调整哈希表的大小时，再 将其删除。 

不难理解，哈希冲突的发生，往往会降低字典和集合操作的速度。因此，为了保证其高效性，字典和集合内 的哈希表，通常会保证其至少留有1/3的剩余空间。随着元素的不停插入，当剩余空间小于1/3时，Python 会重新获取更大的内存空间，扩充哈希表。不过，这种情况下，表内所有的元素位置都会被重新排放。 

虽然哈希冲突和哈希表大小的调整，都会导致速度减缓，但是这种情况发生的次数极少。所以，平均情况 下，这仍能保证插入、查找和删除的时间复杂度为O(1)。 

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



pythonic usage

若第一个参数不存在，则获取第二个参数作为value

若第一个参数存在，则抛出第一个参数

```
In [4]: d
Out[4]: {'a': 5, 'b': 2}

In [5]: d.pop('c', 'a')
Out[5]: 'a'

In [6]: d
Out[6]: {'a': 5, 'b': 2}

In [7]: d.pop('a', 'a')
Out[7]: 5

In [8]: d
Out[8]: {'b': 2}
```



详见Flask  blueprint register 用法

```
    def route(self, rule, **options):
        """Like :meth:`Flask.route` but for a blueprint.  The endpoint for the
        :func:`url_for` function is prefixed with the name of the blueprint.
        """

        def decorator(f):
            endpoint = options.pop("endpoint", f.__name__)
            self.add_url_rule(rule, endpoint, f, **options)
            return f

        return decorator
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



# 字典解析

## 基本语法

```
ret = {exprK:exprV for item in iterator}
```

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









# Example

## dict 实现 switch 方法

```
day = 0

switcher = {
    0: 'Sunday',
    1: 'Monday', 
    2: 'Tuesday'
}

day_name = switcher.get(day, 'Unknown')
print(day_name)
>>>
Sunday
```



推荐方法

```
day = 7

def get_sunday():
    return 'Sunday'

def get_monday():
    return 'Monday'

def get_tuesday():
    return 'Tuesday'

def get_default():
    return 'Unknown'

switcher = {
    0: get_sunday,
    1: get_monday,
    2: get_tuesday
}

day_name = switcher.get(day, get_default)()
print(day_name)
>>>
Unknown
```

