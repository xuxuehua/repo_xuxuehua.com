---
title: "set 集合"
date: 2018-08-19 10:51
collection: 基本变量类型
---


[TOC]





# 集合

集合的含义和数学上的含义相同
集合不是线性的
集合的元素是唯一的
集合的元素是可以hash 的



## 特点

字典和集合的内部结构都是一张哈希表。 

而对集合来说，区别就是哈希表内没有键和值的配对，只有单一的元素了。 

性能很高



初始化了含有100,000个元素的产品，并分别计算了使用列表和集合来统计产品价格数量的运行时间

```
def find_unique_price_using_list(products):
      unique_price_list = []
      for _, price in products: # A
          if price not in unique_price_list: #B
              unique_price_list.append(price)
      return len(unique_price_list)

def find_unique_price_using_set(products):
      unique_price_set = set()
      for _, price in products:
          unique_price_set.add(price)
      return len(unique_price_set)

import time
id = [x for x in range(0, 100000)]
price = [x for x in range(200000, 300000)]
products = list(zip(id, price))

# 计算列表版本的时间
start_using_list = time.perf_counter()
find_unique_price_using_list(products)
end_using_list = time.perf_counter()
print("time elapse using list: {}".format(end_using_list - start_using_list)) ## 输出
time elapse using list: 41.61519479751587

# 计算集合版本的时间
start_using_set = time.perf_counter() find_unique_price_using_set(products)
end_using_set = time.perf_counter()
print("time elapse using set: {}".format(end_using_set - start_using_set)) # 输出
time elapse using set: 0.008238077163696289
```




## 定义集合

```python
In [73]: s = set()

In [74]: s = set({1, 2, 3})

In [75]: s
Out[75]: {1, 2, 3}
```



## 增

### add

若元素存在，就什么都不做

```python
In [76]: s
Out[76]: {1, 2, 3}

In [77]: s.add(4)

In [78]: s
Out[78]: {1, 2, 3, 4}

In [79]: s.add(3)
```



### update

参数必须是可迭代对象

修改后立即生效

```python
In [80]: s
Out[80]: {1, 2, 3, 4}

In [81]: s.update([3, 4, 5, 6])

In [82]: s
Out[82]: {1, 2, 3, 4, 5, 6}
```

### 

## 查

### len

```
In [2]: s
Out[2]: {1, 2, 3}

In [3]: len(s)
Out[3]: 3
```



### in

```
In [2]: s
Out[2]: {1, 2, 3}

In [4]: 3 in s
Out[4]: True

In [5]: 4 not in s
Out[5]: True
```



## 删

### remove 

key不存在抛出异常

```python
In [84]: s
Out[84]: {1, 2, 3, 4, 5, 6}

In [85]: s.remove(1)

In [86]: s
Out[86]: {2, 3, 4, 5, 6}

In [87]: s.remove(10)
---------------------------------------------------------------------------
KeyError                                  Traceback (most recent call last)
<ipython-input-87-99f2b84d3df8> in <module>()
----> 1 s.remove(10)

KeyError: 10
```



### discard

移除一个元素

元素不存在，什么都不做

```python
In [88]: s
Out[88]: {2, 3, 4, 5, 6}

In [89]: s.discard(3)

In [90]: s
Out[90]: {2, 4, 5, 6}

In [91]: s.discard(10)

In [92]: s
Out[92]: {2, 4, 5, 6}
```



### clear

```python
In [99]: s
Out[99]: {1, 2, 3, 4, 5, 6}

In [100]: s.clear()

In [101]: s
Out[101]: set()
```



### pop

移除并返回任意的元素， 删除集合中最后一个元素，可是集合本身是无序的，你无法知道会删除哪 

个元素 

空集会返回key error异常

```python
In [1]: s = set({1, 2, 3})

In [2]: s.pop()
Out[2]: 1

In [3]: s
Out[3]: {2, 3}
```


# 集合运算

## union 并集

```python
In [1]: s1 = set({1, 2, 3})

In [2]: s1
Out[2]: {1, 2, 3}

In [3]: s2 = set({2, 3, 4})

In [4]: s2
Out[4]: {2, 3, 4}

In [5]: s1.union(s2)
Out[5]: {1, 2, 3, 4}

```



## intersection 交集

```python
In [6]: s1
Out[6]: {1, 2, 3}

In [7]: s2
Out[7]: {2, 3, 4}

In [8]: s1.intersection(s2)
Out[8]: {2, 3}
```



## difference 差集

```python
In [9]: s1
Out[9]: {1, 2, 3}

In [10]: s2
Out[10]: {2, 3, 4}

In [11]: s1.difference(s2)
Out[11]: {1}

In [15]: s2.difference(s1)
Out[15]: {4}
```



## symmetric_difference 对称差集

两个集合中不重复的元素集合

```python
In [12]: s1
Out[12]: {1, 2, 3}

In [13]: s2
Out[13]: {2, 3, 4}

In [14]: s1.symmetric_difference(s2)
Out[14]: {1, 4}

In [16]: s2.symmetric_difference(s1)
Out[16]: {1, 4}
```





## symmetric_difference_update()

移除当前集合中在另外一个指定集合相同的元素，并将另外一个指定集合中不同的元素插入到当前集合中



## 运算符计算 (-  &  | )

```
s1 = set(['a', 'b', 'c'])

s2 = set("cdefg")

ret = s1.difference(s2)
print(ret)

print(s1 - s2)
print(s1 & s2)
print(s1 | s2)

>>>
{'b', 'a'}
{'b', 'a'}
{'c'}
{'a', 'g', 'f', 'e', 'd', 'b', 'c'}
```





# 集合判断

## issubset 子集 <=

```python
In [20]: s
Out[20]: {4, 5, 6}

In [21]: s.issubset({3, 4, 5, 6})
Out[21]: True
```

```
In [75]: s = ({4, 5, 6})

In [76]: s
Out[76]: {4, 5, 6}

In [77]: s <= ({3, 4, 5, 6})
Out[77]: True
```



## 真子集 < 

```
In [78]: s1 = ({4, 5, 6})

In [79]: s2 = ({3, 4, 5, 6})

In [80]: s1
Out[80]: {4, 5, 6}

In [81]: s2
Out[81]: {3, 4, 5, 6}

In [82]: s1 < s2
Out[82]: True
```



## issuperset 父集 >=

```python
In [22]: {0, 1, 2}.issuperset([1])
Out[22]: True

In [23]: {0, 1, 2}.issuperset([0, 1])
Out[23]: True

In [24]: {0, 1, 2}.issuperset([2, 3])
Out[24]: False

In [25]: {0, 1, 2}.issuperset(())

Out[25]: True
```

```
In [83]: ({1, 2, 3}) >= ({1})
Out[83]: True
```

 

## isdisjoint 

当前集合和另一个集合没有交集

无交集返回真

```python
In [2]: {0, 1, 2}.isdisjoint([1])
Out[2]: False

In [3]: {0, 1, 2}.isdisjoint([0, 1])
Out[3]: False

In [4]: {0, 1, 2}.isdisjoint([3, 4])
Out[4]: True

In [5]: {0, 1, 2}.isdisjoint(())
Out[5]: True
```



# 集合解析

## 基本语法

```
{expression for item in iterator}
```

等价于


```python
ret = set()
for item in iterator:
    ret.add(expression)
```




```python
In [20]: s = {1, 3, 5}

In [21]: {x+1 for x in s}
Out[21]: {2, 4, 6}

In [22]: {x+1 for x in range(10)}
Out[22]: {1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

In [23]: {x+1 for x in [2, 2, 2, 2]}
Out[23]: {3}
```







# fronzenset （dict key用法）

不可变集合，无序，不重复

可以作为dict的key

```
s = set(['a', 'b'])
s.add('a')
print(s)

s = frozenset("abcdefg")
print(s)

>>>
{'b', 'a'}
frozenset({'b', 'f', 'd', 'c', 'a', 'e', 'g'})
```

