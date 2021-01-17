---
title: "functools"
date: 2019-01-31 18:38
---

[TOC]

# partial 偏函数

把函数部分的参数固定下来，相当于部分的参数添加了一个固定的默认值，形成一个新的函数并返回

从partial生成的新函数，是对原函数的封装

```python
In [1]: import functools

In [2]: def add(x, y) -> int:
   ...:     return x + y
   ...:

In [3]: newadd = functools.partial(add, y=5)

In [4]: newadd(7)
Out[4]: 12

In [5]: newadd(7, y=6)
Out[5]: 13

In [6]: newadd(y=10, x=6)
Out[6]: 16

In [7]: import inspect

In [8]: inspect.signature(newadd)
Out[8]: <Signature (x, *, y=5) -> int>
```



## 本质

```
In [9]: def partial(func, *args, **keywords):
   ...:     def newfunc(*fargs, **fkeywords):
   ...:         newkeywords = keywords.copy()
   ...:         newkeywords.update(fkeywords)
   ...:         return func(*(args + fargs), **newkeywords)
   ...:     newfunc.func = func
   ...:     newfunc.args = args
   ...:     newfunc.keywords = keywords
   ...:     return newfunc
   ...:

In [10]: def add(x, y):
    ...:     return x + y
    ...:

In [11]: foo = partial(add, 4)

In [12]: foo(5)
Out[12]: 9
```





# lru_cache

Python**自带的缓存**

实现于 functool 模块中的 lru_cache 装饰器。

```
@functools.lru_cache(maxsize=None, typed=False) 
```

> - maxsize：最多可以缓存多少个此函数的调用结果，如果为None，则无限制，设置为 2 的幂时，性能最佳
> - typed：若为 True，则不同参数类型的调用将分别缓存。



* 斐波那契数列

```

In [9]: import timeit  
   ...:   
   ...: def fib(n):  
   ...:     if n < 2:  
   ...:         return n  
   ...:     return fib(n - 2) + fib(n - 1)  
   ...:   
   ...:   
   ...:   
   ...: print(timeit.timeit(lambda :fib(40), number=1))                                                                                                                              
37.009518885000034

In [10]:                                                                                                                                                                             
In [10]: import timeit  
    ...: from functools import lru_cache  
    ...:   
    ...: @lru_cache(None)  
    ...: def fib(n):  
    ...:     if n < 2:  
    ...:         return n  
    ...:     return fib(n - 2) + fib(n - 1)  
    ...:   
    ...: print(timeit.timeit(lambda :fib(500), number=1))                                                                                                                            0.000514715000463184
```







# OrderedDict

## 实现base64

```
from collections import OrderedDict

base_tbl = b"ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"
alphabet = OrderedDict(zip(base_tbl, range(64)))

def base64decode(src:bytes):
    ret = bytearray()
    length = len(src)

    step = 4 # 对齐，每次取4个
    for offset in range(0, length, step):
        tmp = 0x00
        block = src[offset:offset + step]

        # 开始移位计算
        for i in range(4):
            index = alphabet.get(block[-i-1])
            if index is not None:
                tmp += index << i*6 

        ret.extend(tmp.to_bytes(3, 'big'))
    return bytes(ret.rstrip(b'\x00'))

txt = 'TWFu'

txt = txt.encode()
print(txt)

print(base64decode(txt).decode())

>>>
b'TWFu'
Man
```
