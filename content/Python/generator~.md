---
title: "generator 生成器"
date: 2018-08-22 11:18
collection: 函数
---

[TOC]

# 生成器

## 定义

生成器(generator)的编写方法和函数定义类似。只是在return的地方改成yield。并且可以有多个yield。
当生成器遇到一个yield的时候，会暂停运行生成器，返回yield的后面的值, 该值一般被next()函数调用。再次调用的时候，会从刚刚暂停的地方继续运行，直到下一个yield。



## 生成器对象

生成器对象是一个可迭代对象，是一个迭代器



## 生成器调用

生成器只有在调用的时候才会生成相应的数据

```
In [18]: g = ((i*2 for i in range(2)))

In [19]: g.__next__()
Out[19]: 0

In [20]: g.__next__()
Out[20]: 2

In [21]: g.__next__()
---------------------------------------------------------------------------
StopIteration                             Traceback (most recent call last)
<ipython-input-21-42e506b10868> in <module>()
----> 1 g.__next__()

StopIteration:
```



# 应用

## 斐波那契数列

函数生成方法

```
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        print(b)
        a, b = b, a+b
        n = n + 1

fib(10)

>>>
1
1
2
3
5
8
13
21
34
55
```



生成器方法

```
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a+b
        n = n + 1

f = fib(10)
print(f.__next__())
print(f.__next__())
print(f.__next__())
print(f.__next__())

>>>
1
1
2
3
```



## yield from

```
In [197]: def inc():
     ...:     yield from range(100)
     ...:

In [198]: foo = inc()

In [199]: next(foo)
Out[199]: 0

In [200]: next(foo)
Out[200]: 1

In [201]: next(foo)
Out[201]: 2
```



```
In [202]: def counter(n):
     ...:     for x in range(n):
     ...:         yield x
     ...:

In [203]: def inc(n):
     ...:     yield from counter(n)
     ...:

In [204]: foo = inc(10)

In [205]: next(foo)
Out[205]: 0

In [206]: next(foo)
Out[206]: 1

In [207]: next(foo)
Out[207]: 2

In [208]: next(foo)
Out[208]: 3
```



### 读取log

```
def read(log):
		with open(log, encoding="utf-8") as f:
				yield from f
				

def generate_result(log):
		result = {}
		for line in read(log):
				line = line.split(" ")
				if len(line) > 8:
						key = line[8]
						value = result.get(key, 0)
						result[key] = value + 1
		print(result)
```



 

## 协程 coroutine

比进程，线程轻量级

是在用户空间调度函数的一种实现

asyncio是协程的实现

生成器在 Python 2 的版本上，是协程的一种重要实现方式；而 Python 3.7 引入 async await 语法糖后，生成器实现协程的方式就已经落后了









# 生成器表达式

生成器本身并没有返回任何值，只是返回了一个生成器对象



其返回一个生成器，为惰性求值，需要的时候才计算值

```
(返回值 for 元素 in 可迭代对象 if 条件)
```



将列表生成式的中括号变成小括号即是生成器

```
In [5]: type([i*2 for i in range(10)])
Out[5]: list

In [6]: type((i*2 for i in range(10)))
Out[6]: generator
```





```
In [143]: g = ("{:04}".format(i) for i in range(1, 11))

In [144]: next(g)
Out[144]: '0001'

In [145]: next(g)
Out[145]: '0002'

In [146]: next(g)
Out[146]: '0003'
```





## 字符串连接

```
In [83]: l = ['abc', 123, 45, 'xyz']

In [84]: (str(x) for x in l)
Out[84]: <generator object <genexpr> at 0x104a62de0>

In [85]: ''.join(str(x) for x in l)
Out[85]: 'abc12345xyz'
```

