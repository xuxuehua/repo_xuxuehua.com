---
title: "higher_order_function 高阶函数"
date: 2018-08-21 21:14
collection: 函数
---

[TOC]



# higher order function 高阶函数



## 定义

变量可以指向函数，函数的参数能够接受变量，那么一个函数就可以接受另一个函数作为参数，这种函数称为高阶函数

```
def add(a, b, f):
    return f(a) + f(b)

ret = add(3, -6, abs)
print(ret)

>>>
9
```



test函数的第一参数f就是一个函数对象

```python
def func(x, y):
    return x+y

def test(f, a, b):
    print('test')
    print(f(a, b))

test(func, 3, 5)
>>>
test
8
```

或者写成下面的

```python
def test(f, a, b):
    print('test')
    print(f(a, b))

test((lambda x, y:x+y), 3, 5 )
>>>
test
8
```



## sorted 函数

```
sorted(iterable[, key][, reverse])
```

返回一个新的列表，对一个可迭代对象的所有元素排序，排序规则为key定义的函数，reverse表示是否翻转



```
In [3]: lst = [1, 2, 3]

In [4]: sorted(lst, key=lambda x: 6-x)
Out[4]: [3, 2, 1]
```





## lamba 匿名函数



lambda后面不能是代码块

```
lambda parameter_list: expression
```



将lambda函数依次作用域每个元素，如果函数返回True，则手机原来的元素6，7

```
In [5]: filter(lambda x: x > 5, [2, 3, 5, 6, 7])
Out[5]: <filter at 0x1032f5240>
In [7]: list(filter(lambda x: x > 5, [2, 3, 5, 6, 7]))
Out[7]: [6, 7]
```



类似上面的，但收集并返回Falsely元素 2，3， 5

```
In [8]: filterfalse(lambda x: x > 5, [2, 3, 5, 6, 7])
Out[8]: <itertools.filterfalse at 0x1032f5438>

In [9]: list(filterfalse(lambda x: x > 5, [2, 3, 5, 6, 7]))
Out[9]: [2, 3, 5]
```



函数返回True，元素到循环器中，一旦函数返回False, 则停止

```
In [10]: takewhile(lambda x: x < 5, [1, 3, 6, 7, 1])
Out[10]: <itertools.takewhile at 0x1032f3a48>

In [11]: list(takewhile(lambda x: x < 5, [1, 3, 6, 7, 1]))
Out[11]: [1, 3]
```



函数返回False， 跳过元素。一旦函数返回True，则开始收集剩下的元素到循环器中

```
In [12]: dropwhile(lambda x: x < 5, [1, 3, 6, 7, 1])
Out[12]: <itertools.dropwhile at 0x1032f3c88>

In [13]: list(dropwhile(lambda x: x < 5, [1, 3, 6, 7, 1]))
Out[13]: [6, 7, 1]
```





## map 函数

```
map(func, *iterable) -> map object
```

map()函数的第一个参数是一个函数对象。

map()的功能是将函数对象依次作用与表的每个元素。每次作用的结果存储与返回的表re中。



```
re = map((lambda x: x+3), [1, 3, 5, 7])
print(re)
print(list(re))
>>>
<map object at 0x10caf3780>
[4, 6, 8, 10]
```

```
from itertools import *

result = map(pow, [1, 2, 3], [1, 2, 3])

for num in result:
    print(num)
>>>
1
4
27
```



map结合lambda表达式

```
In [10]: list_x = [1, 2, 3, 4, 5]

In [11]: r = map(lambda x: x*x, list_x)

In [12]: list(r)
Out[12]: [1, 4, 9, 16, 25]
```



map传入参数的个数要和lambda表达式传入的参数个数要相同， 中间元素个数，取决于较小元素的个数

```
In [17]: list_x = [1, 2, 4, 5, 6]

In [18]: list_y = [1, 2, 3]

In [19]: r = map(lambda x, y: x*x + y, list_x, list_y)

In [20]: list(r)
Out[20]: [2, 6, 19]
```









## filter 函数

过滤掉不需要的的元素

```
filter(function, iterable) -> filter object
```

> function 是有一个参数的函数，返回为bool

filter函数的第一个参数也是函数对象， 将作为参数的函数对象作用于多个元素。
如果函数的返回为True，则该次的元素将被存储到返回的表中。

在python3 中，filter返回的不是表，而是循环对象



```
def func(a):
    if a > 100:
        return True
    else:
        return False

print(filter(func, [10, 20, 101, 400]))
print(list(filter(func, [10, 20, 101, 400])))
>>>
<filter object at 0x10c913780>
[101, 400]
```









## reduce 函数

reduce函数的第一个参数也是函数，但是要求函数自身能够接受两个参数。 reduce可以累进的将函数作用与各个参数

```
from functools import reduce
print(reduce((lambda x,y: x+y), [1, 2, 4, 6, 8]))
>>>
21
```





## Currying 柯里化

将原来接受两个参数的函数变成接受一个参数的函数的过程，新的函数返回一个以原有函数的第二个参数作为参数的函数

即 z = f(x, y)  ->  z = f(x)(y)

```
In [5]: def add(x):
   ...:     def _add(y):
   ...:         return x+y
   ...:     return _add
   ...:

In [6]: add(5)(6)
Out[6]: 11
```





## 三元表达式

```
In [3]: x, y = 2, 1

In [4]: r = x if x > y else y

In [5]: r
Out[5]: 2
```





