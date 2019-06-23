---
title: "collections"
date: 2019-06-20 10:23
---
[TOC]

# defaultdict

defaultdict属于内建函数dict的一个子类



构建的是一个类似*dictionary*的对象，其中*keys*的值，自行确定赋值，但是*values*的类型，是*function_factory*的类实例，而且具有默认值。

比如*default(int)*则创建一个类似dictionary对象，里面任何的*values*都是*int*的实例，而且就算是一个不存在的`key, d[key] `也有一个默认值，这个默认值是*int()*的默认值0.

 



## usage

defaultdict可以接受一个内建函数list作为参数

list()本身是内建函数，但是再经过更新后，python里面所有东西都是对象，所以list改编成了类，引入list的时候产生一个类的实例。

```
In [10]: from collections import defaultdict

In [11]: s = [('yellow', 1), ('blue', 2), ('yellow', 3), ('blue', 4), ('red', 1)]

In [12]: d = defaultdict(list)

In [13]: d
Out[13]: defaultdict(list, {})

In [14]: for k, v in s:
    ...:     d[k].append(v)
    ...:

In [15]: list(d.items())
Out[15]: [('yellow', [1, 3]), ('blue', [2, 4]), ('red', [1])]
```

