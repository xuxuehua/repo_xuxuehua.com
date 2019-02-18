---
title: "inspect 检测模块"
date: 2019-01-30 23:17
---


[TOC]



# inspect 

对函数进行检测

```
In [14]: import inspect

In [15]: def add(x:int, y:int, *args, **kwargs) -> int:
    ...:     return x + y
    ...:

In [16]: sign = inspect.signature(add)

In [17]: type(sign)
Out[17]: inspect.Signature

In [18]: sign.parameters
Out[18]:
mappingproxy({'args': <Parameter "*args">,
              'kwargs': <Parameter "**kwargs">,
              'x': <Parameter "x:int">,
              'y': <Parameter "y:int">})

In [19]: sign.return_annotation
Out[19]: int

In [20]: sign.parameters['x'], type(sign.parameters['x'])
Out[20]: (<Parameter "x:int">, inspect.Parameter)
```



## .isfunction()

检测是否为函数



## .ismethod()

是否是类方法



## .isgenerator()

是否是生成器对象



## .isgeneratorfunction()

是否是生成器函数



## .isclass()

是否是类



## .ismodule()

是否是模块

```
In [15]: def add(x:int, y:int, *args, **kwargs) -> int:
    ...:     return x + y
    ...:

In [21]: inspect.ismodule(add)
Out[21]: False
```



## .isbuiltin()

是否是内建对象