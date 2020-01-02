---
title: "built_in_function"
date: 2018-08-24 11:04
collection: 函数
---

[TOC]

# 内置函数

## abs 绝对值

```python
print(abs(-1))
>
1
```

## bin 二进制

把十进制数转二进制

返回值为字符串

```
In [8]: bin(1)
Out[8]: '0b1'

In [9]: bin(2)
Out[9]: '0b10'

In [10]: bin(4)
Out[10]: '0b100'

In [11]: bin(255)
Out[11]: '0b11111111'
```

## bool 布尔计算

```
In [1]: bool(1)
Out[1]: True

In [2]: bool(0)
Out[2]: False

In [3]: bool('abc')
Out[3]: True

In [4]: bool('')
Out[4]: False

In [5]: bool([1, 2, 3])
Out[5]: True

In [6]: bool([])
Out[6]: False

In [7]: bool({1, 2, 3})
Out[7]: True

In [8]: bool({})
Out[8]: False

In [9]: bool(None)
Out[9]: False
```

## bytes

```
In [14]: a = bytes('abc', encoding='utf-8')

In [15]: type(a), a
Out[15]: (bytes, b'abc')

In [16]: a.capitalize()
Out[16]: b'Abc'

In [17]: a
Out[17]: b'abc'
```

## callable

是否可调用

```
In [18]: def a():
    ...:     pass
    ...:
    ...:

In [19]: callable(a)
Out[19]: True

In [20]: callable(1)
Out[20]: False
```

## chr 返回字符

转换对应的ascii的值， 输入的是数字

```
In [21]: chr(97)
Out[21]: 'a'

In [22]: chr(98)
Out[22]: 'b'

In [23]: chr(99)
Out[23]: 'c'
```

## ceil

向上取整

## divmod 取余

divmod(a, b) 返回a除以b的商和余数，返回一个元组

其等价于`tuple(x//y, x%y)`

```python
print(divmod(8, 3))
>
(2, 2)
```

## enumerate 枚举

迭代一个序列，返回索引数字和元素构成的二元组

```
enumerate(seq, start=0)
```

> start表示索引开始的数字，默认为0

```
In [151]: for i in enumerate('abcdefg', start=2):
     ...:     print(i)
     ...:
(2, 'a')
(3, 'b')
(4, 'c')
(5, 'd')
(6, 'e')
(7, 'f')
(8, 'g')
```

## filter

```
In [32]: filter(lambda n: n> 5, range(10))
In [32]: filter(lambda n: n> 5, range(10))
Out[32]: <filter at 0x10c677da0>

In [33]: x = filter(lambda n: n> 5, range(10))

In [34]: for i in x:
    ...:     print(i)
    ...:
6
7
8
9
```

```
filter(lambda x: True if x else False, [self.author, self.publisher, self.price])
```

## floor

向下取整

## float

将一个字符串转换成浮点数

## help()

```python
help('print')
> 
Help on built-in function print in module builtins:

print(...)
    print(value, ..., sep=' ', end='\n', file=sys.stdout, flush=False)

    Prints the values to a stream, or to sys.stdout by default.
    Optional keyword arguments:
    file:  a file-like object (stream); defaults to the current sys.stdout.
    sep:   string inserted between values, default a space.
    end:   string appended after the last value, default a newline.
    flush: whether to forcibly flush the stream.
```

## hex 十六进制

数字转16进制

返回值为字符串

```
In [38]: hex(10)
Out[38]: '0xa'

In [39]: hex(15)
Out[39]: '0xf'

In [40]: hex(255)
Out[40]: '0xff'
```

## int 整数

将一个数值或字符串转换成整数，可以指定进制

## isinstance 判断类型

判断对象是否数据某种类型或者元组中列出的某个类型

返回值为布尔类型

```
In [19]: isinstance(6, str)
Out[19]: False

In [20]: isinstance('6', str)
Out[20]: True
```

## issubclass

判断类型是否是某种类型的子类或者元组中列出的某个类型的子类

返回值为布尔类型

## iter 迭代器

```
iter(iterable)
```

```
In [154]: t = iter(range(5))

In [155]: next(t)
Out[155]: 0

In [156]: next(t)
Out[156]: 1

In [157]: next(t)
Out[157]: 2

In [158]: next(t)
Out[158]: 3

In [159]: next(t)
Out[159]: 4

In [160]: next(t)
---------------------------------------------------------------------------
StopIteration                             Traceback (most recent call last)
<ipython-input-160-f843efe259be> in <module>
----> 1 next(t)

StopIteration:
```

## join 连接

```python
list = ['Hello', 'World']
str = '#'
print(str.join(list))
>
Hello#World
```

## len 对象长度

返回一个集合类型的元素个数

## min 最小

## max 最大

## next 获取迭代器

next 对下一个迭代器取下一个元素，如果元素全都取过了，会抛出异常

```
next(iterable[, default])
```

```
In [154]: t = iter(range(5))

In [155]: next(t)
Out[155]: 0

In [156]: next(t)
Out[156]: 1

In [157]: next(t)
Out[157]: 2

In [158]: next(t)
Out[158]: 3

In [159]: next(t)
Out[159]: 4

In [160]: next(t)
---------------------------------------------------------------------------
StopIteration                             Traceback (most recent call last)
<ipython-input-160-f843efe259be> in <module>
----> 1 next(t)

StopIteration:
```

## oct 八进制

返回值为字符串

## ord 返回整数

将字符串（一个字符）转换成对应的编码（整数）

转换字符对应的ascii

```
In [24]: ord('a')
Out[24]: 97

In [25]: ord('b')
Out[25]: 98

In [26]: ord('c')
Out[26]: 99
```

## pow 幂

pow(x, y) 返回x的y次幂

```
In [41]: pow(2, 8)
Out[41]: 256

In [42]: pow(2, 10)
Out[42]: 1024
```

## range

```
range(start, stop[, step])
```

range 进行循环控制

```
In [1]: s = 'abcdefghijkl'

In [2]: for i in range(0, len(s), 2):
   ...:     print(s[i])
   ...:     
a
c
e
g
i
k
```

## reversed 翻转

返回一个翻转元素的迭代器

```
In [149]: list(reversed("13579"))
Out[149]: ['9', '7', '5', '3', '1']
```

## round

小数点精度

```
In [43]: round(3.1415)
Out[43]: 3

In [44]: round(3.1415, 2)
Out[44]: 3.14
```

## sorted 排序

```
sorted(iterable[, key][, reverse])
```

返回一个新的列表，默认为升序， reverse表示反转

```
In [45]: a = {6:2, 8:0, 1:4, -7:6, 11:99, 51:3}

In [46]: sorted(a.items(), key=lambda x: x[1])
Out[46]: [(8, 0), (6, 2), (51, 3), (1, 4), (-7, 6), (11, 99)]
```

## split 分割

```python
str = 'Hello World'
print(str.split(' '))
>
['Hello', 'World']
```

## splitlines

```python
str = 'Hello World'
print(str.splitlines())
>
['Hello World']
```

## str

将指定的对象转换成字符串形式，可以指定编码

## sum 求和

对可迭代对象的所有数值元素求和

```
In [148]: sum(range(1, 100, 2))
Out[148]: 2500
```

## type 类型

返回类型，而不是字符串

```python
str = 'Hello'
print(type(str))
num = 8
print(type(num))
list = [1, 2, 3]
print(type(list))
>
<class 'str'>
<class 'int'>
<class 'list'>
```

## vars

**vars()** 函数返回对象object的属性和属性值的字典对象

```
In [7]: class Rick: 
   ...:     age=18 
   ...:                                                                                                 

In [8]: print(vars(Rick))                                                                               
{'age': 18, '__weakref__': <attribute '__weakref__' of 'Rick' objects>, '__module__': '__main__', '__dict__': <attribute '__dict__' of 'Rick' objects>, '__doc__': None}

In [9]: rick = Rick()                                                                                   

In [10]: print(vars(rick))                                                                              
{}
```

## zip 拉链函数

把多个可迭代对象合并在一起， 返回一个迭代器

将每次从不同对象中取到的元素合并成一个元组

### 聚合

```
In [47]: a = [1, 2, 3]

In [48]: b = ['a', 'b', 'c']

In [59]: for i in zip(a, b):
    ...:     print(i)
    ...:
(1, 'a')
(2, 'b')
(3, 'c')
```

```python
ta = [1, 2, 3]
tb = [4, 5, 6]
tc = ['a', 'b', 'c']

for (a, b, c) in zip(ta, tb, tc):
    print(a, b, c)
>>>
1 4 a 
2 5 b
3 6 c
```

### 分解

```python
ta = [1, 2, 3]
tb = [4, 5, 6]

zipped = zip(ta, tb)
print(zipped)

na, nb = zip(*zipped)
print(na, nb)
>>>
<zip object at 0x1034855c8>
(1, 2, 3) (4, 5, 6)
```

## `__import__` 动态加载

`__import__()`函数用于动态加载类和函数 。

如果一个模块经常变化就可以使用 `__import__()` 来动态载入

```
__import__(name[, globals[, locals[, fromlist[, level]]]])
```

```
a.py
### 
#!/usr/bin/env python    
#encoding: utf-8  

import os  

print ('在 a.py 文件中 %s' % id(os))
```

```
test.py
###
#!/usr/bin/env python    
#encoding: utf-8  

import sys  
__import__('a')        # 导入 a.py 模块

>>>
在 a.py 文件中 4394716136
```
