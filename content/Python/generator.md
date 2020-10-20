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



### next

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



### send 传入值到生成器内部

可以传入值到生成器内部，同时还可以重启生成器执行到下一个yield位置

```
def get_func():
    html = yield "https://xuxuehua.com"
    print('get_func html:', html)
    yield 2
    yield 3
    return "Rick"


if __name__ == '__main__':
    gen = get_func()
    url = gen.send(None)
    # url = next(gen)
    html = 'Xu'
    print(gen.send(html))

>>>
get_func html: Xu
2
```

> 需要通过send 发送一个None激活生成器，或者通过next方法 调用激活生成器



### close 方法

```
def get_func():
    try:
        html = yield "https://xuxuehua.com"
        print('get_func html:', html)
    except Exception:
        pass
    yield 2
    yield 3
    return "Rick"


if __name__ == '__main__':
    gen = get_func()
    print(next(gen))
    gen.close()
    next(gen)

>>>
https://xuxuehua.com
Traceback (most recent call last):
  File "/Users/rxu/test_purpose/socket_client.py", line 16, in <module>
    next(gen)
StopIteration  
```

> 这里的异常是最后一个next(gen) 抛出的





### throw 方法

```
def get_func():
    yield "https://xuxuehua.com"
    yield 2
    yield 3
    return "Rick"


if __name__ == '__main__':
    gen = get_func()
    print(next(gen))
    gen.throw(Exception, 'url value')

>>>
https://xuxuehua.com
Traceback (most recent call last):
  File "/Users/rxu/test_purpose/socket_client.py", line 11, in <module>
    gen.throw(Exception, 'url value')
  File "/Users/rxu/test_purpose/socket_client.py", line 2, in get_func
    yield "https://xuxuehua.com"
Exception: url value
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

yield from 后面是一个iterable 对象

```
def g1(iterable):
    yield iterable


def g2(iterable):
    yield from iterable


for value in g1(range(10)):
    print('g1: ', value)

for value in g2(range(10)):
    print('g2: ', value)

>>>
g1:  range(0, 10)
g2:  0
g2:  1
g2:  2
g2:  3
g2:  4
g2:  5
g2:  6
g2:  7
g2:  8
g2:  9
```



yield from后面加上可迭代对象，他可以把可迭代对象里的每个元素一个一个的yield出来，对比yield来说代码更加简洁，结构更加清晰。

```
a_str = 'ABC'
a_list = [1, 2, 3]
a_dict = {"name": "rick", "age": 18}
gen = (i for i in range(1, 3))


def gen_by_yield_from(*args, **kwargs):
    for item in args:
        yield from item


def gen_by_yield(*args, **kwargs):
    for item in args:
        for i in item:
            yield i


yield_from_result = gen_by_yield_from(a_str, a_list, a_dict, gen)
yield_result = gen_by_yield(a_str, a_list, a_dict, gen)
print(list(yield_from_result))
print(list(yield_result))

>>>
['A', 'B', 'C', 1, 2, 3, 'name', 'age', 1, 2]
['A', 'B', 'C', 1, 2, 3, 'name', 'age']
```



### 协程核心

yield from（委托生成器）将调用方和子生成器之间建立了一个双向通道， 即调用方可以通过`send()`直接发送消息给子生成器，而子生成器yield的值，也是直接返回给调用方。

```
# 子生成器
def average_gen():
    total = 0
    count = 0
    average = 0
    while True:
        new_num = yield average
        count += 1
        total += new_num
        average = total/count

# 委托生成器
def proxy_gen():
    while True:
        yield from average_gen()

# 调用方
if __name__ == '__main__':
    calculate_average = proxy_gen()
    next(calculate_average)
    print(calculate_average.send(10))
    print(calculate_average.send(20))
    print(calculate_average.send(30))

>>>
10.0
15.0
20.0
```





```
# 子生成器
def average_gen():
    total = 0
    count = 0
    average = 0
    while True:
        new_num = yield average
        if new_num is None:
            break
        count += 1
        total += new_num
        average = total/count

    # 每一次return，都意味着当前协程结束。
    return total, count, average


# 委托生成器
def proxy_gen():
    while True:
        # 只有子生成器要结束（return）了，yield from左边的变量才会被赋值，后面的代码才会执行。
        total, count, average = yield from average_gen()
        print("count=%s, total=%s, average=%s" % (count, total, average))


# 调用方
if __name__ == '__main__':
    calc_average = proxy_gen()
    next(calc_average)
    print(calc_average.send(10))
    print(calc_average.send(20))
    print(calc_average.send(30))
    # 结束协程
    calc_average.send(None)
    # 如果此处再调用calc_average.send(10)，由于上一协程已经结束，将重开一协程

>>>
10.0
15.0
20.0
count=3, total=60, average=20.0
```



### yield from 原理

```
"""
_i：子生成器，同时也是一个迭代器
_y：子生成器生产的值
_r：yield from 表达式最终的值
_s：调用方通过send()发送的值
_e：异常对象
"""

_i = iter(EXPR)

try:
    _y = next(_i)
except StopIteration as _e:
    _r = _e.value

else:
    while 1:
        try:
            _s = yield _y
        except GeneratorExit as _e:
            try:
                _m = _i.close
            except AttributeError:
                pass
            else:
                _m()
            raise _e
        except BaseException as _e:
            _x = sys.exc_info()
            try:
                _m = _i.throw
            except AttributeError:
                raise _e
            else:
                try:
                    _y = _m(*_x)
                except StopIteration as _e:
                    _r = _e.value
                    break
        else:
            try:
                if _s is None:
                    _y = next(_i)
                else:
                    _y = _i.send(_s)
            except StopIteration as _e:
                _r = _e.value
                break
RESULT = _r
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

