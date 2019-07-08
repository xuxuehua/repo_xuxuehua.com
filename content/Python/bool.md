---
title: "bool 布尔"
date: 2018-08-13 23:27
collection: 基本变量类型
---

[TOC]



# 布尔值

```
In [22]: bool(None)
Out[22]: False

In [23]: bool(True)
Out[23]: True

In [24]: bool([])
Out[24]: False
```



## `__len__`

不存在`__bool__` ， 返回结果由`__len__` 决定

```
In [32]: class Test():
    ...:     def __len__(self):
    ...:         return False
    ...:
    ...: print(bool(Test()))
False

In [33]: class Test():
    ...:     def __len__(self):
    ...:         return True
    ...:
    ...: print(bool(Test()))
True
```



## `__bool__`

```
In [34]: class Test():
    ...:     def __len__(self):
    ...:         return False
    ...:
    ...:     def __bool__(self):
    ...:         return True
    ...:
    ...: print(bool(Test()))
True
```



