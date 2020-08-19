---
title: "generator 生成器"
date: 2018-08-22 11:18
collection: 函数
---

[TOC]





# 迭代

即访问集合内元素的一种方式，可以遍历数据

迭代器不能return，但提供了一种惰性的方式访问数据的方式

内部实现的迭代协议，通过`__iter__` 实现



## 迭代器和可迭代对象

```
from collections.abc import Iterable, Iterator

a = [1, 2]
print(isinstance(a, Iterable))
print(isinstance(a, Iterator))

>>>
True
False
```



# 生成器

生成器(generator)的编写方法和函数定义类似。只是在return的地方改成yield。并且可以有多个yield。

当生成器遇到一个yield的时候，会暂停运行生成器，返回yield的后面的值, 该值一般被next()函数调用。

再次调用的时候，会从刚刚暂停的地方继续运行，直到下一个yield。



## 生成器对象

return 为生成器对象

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



# 生成器应用

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



## 读取大文件

```
def myreadlines(f,newline):
    buf=""
    while True:
        while newline in buf:
            pos = buf.index(newline)
            yield buf[:pos]
            buf = buf[pos+len(newline):]
        chunk =f.read(4096*10)
        if not chunk:
            # 说明已经读到了文件结尾
            yield buf
            break
        buf += chunk

with open("input.txt") as f:
    for line in myreadlines(f, "{|}"):
        print(line)

```

> 若文件里面没有任何换行符，并会不是一个很好的solution



### 其他读取大文件

```py
def retrun_count(fname):
    """计算文件有多少行
    """
    count = 0
    with open(fname) as file:
        for line in file:
            count += 1
    return count
```

> 如果被读取的文件里，根本就没有任何换行符，那么上面的第二个好处就不成立了。当代码执行到 for line in file 时，line 将会变成一个非常巨大的字符串对象，消耗掉非常可观的内存。



若文本都在同一行

```
def return_count_v2(fname):

    count = 0
    block_size = 1024 * 8
    with open(fname) as fp:
        while True:
            chunk = fp.read(block_size)
            # 当文件没有更多内容时，read 调用将会返回空字符串 ''
            if not chunk:
                break
            count += 1
    return count
```

> 与直接循环迭代文件对象不同，每次调用 file.read(chunk_size) 会直接返回从当前位置往后读取 chunk_size 大小的文件内容，不必等待任何换行符出现。
>
> 如果你认真分析一下 return_count_v2 函数，你会发现在循环体内部，存在着两个独立的逻辑：数据生成（read 调用与 chunk 判断） 与 数据消费。而这两个独立逻辑被耦合在了一起。



### 解藕的生成器方法

```
def chunked_file_reader(fp, block_size=1024 * 8):
    """生成器函数：分块读取文件内容
    """
    while True:
        chunk = fp.read(block_size)
        # 当文件没有更多内容时，read 调用将会返回空字符串 ''
        if not chunk:
            break
        yield chunk


def return_count_v3(fname):
    count = 0
    with open(fname) as fp:
        for chunk in chunked_file_reader(fp):
            count += 1
    return count
```

进行到这一步，代码似乎已经没有优化的空间了，但其实不然。iter(iterable) 是一个用来构造迭代器的内建函数，但它还有一个更少人知道的用法。当我们使用 iter(callable, sentinel) 的方式调用它时，会返回一个特殊的对象，迭代它将不断产生可调用对象 callable 的调用结果，直到结果为 setinel 时，迭代终止。

```
def chunked_file_reader(file, block_size=1024 * 8):
    """生成器函数：分块读取文件内容，使用 iter 函数
    """
    # 首先使用 partial(fp.read, block_size) 构造一个新的无需参数的函数
    # 循环将不断返回 fp.read(block_size) 调用结果，直到其为 '' 时终止
    for chunk in iter(partial(file.read, block_size), ''):
        yield chunk
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

