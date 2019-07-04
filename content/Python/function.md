---
title: "function"
date: 2018-08-20 01:18
collection: 函数
---



[TOC]





# 函数

函数是组织好的，可重复使用，实现单一功能的代码段
函数有输入（参数）和输出（返回值）
Python中，函数是一等对象



## 函数定义 

```python
def fn():
    pass
```



### 变量作用域

#### 全局与局部变量

在子程序中定义的变量称为局部变量，在程序的一开始定义的变量称为全局变量。

全局变量作用域是整个程序，局部变量作用域是定义该变量的子程序。

当全局变量与局部变量同名时：

在定义局部变量的子程序内，局部变量起作用；在其它地方全局变量起作用。

```python
a = 100        #全局变量
def setNum():
    a = 10     #局部变量
    print(a)   #打印局部变量

setNum()       #调用函数
print(a)       #打印全局变量
```

```
x = 0
def grandpa():
    x = 1
    print('grandpa', x)
    def dad():
        x = 2
        print('father', x)
        def son():
            x = 3
            print('son', x)
        son()
    dad()
grandpa()

>>>
grandpa 1
father 2
son 3
```



#### 避免使用全局变量

在实际开发中，我们应该尽量减少对全局变量的使用，因为全局变量的作用域和影响过于广泛，可能会发生意料之外的修改和使用，除此之外全局变量比局部变量拥有更长的生命周期，可能导致对象占用的内存长时间无法被[垃圾回收](https://zh.wikipedia.org/wiki/垃圾回收_(計算機科學))。事实上，减少对全局变量的使用，也是降低代码之间耦合度的一个重要举措，同时也是对[迪米特法则](https://zh.wikipedia.org/zh-hans/得墨忒耳定律)的践行。减少全局变量的使用就意味着我们应该尽量让变量的作用域在函数的内部，但是如果我们希望将一个局部变量的生命周期延长，使其在函数调用结束后依然可以访问，这时候就需要使用[闭包](https://zh.wikipedia.org/wiki/闭包_(计算机科学))，



## 函数参数

非默认非可变参数，可变位置参数，可变关键字参数
默认参数不要和可变参数放在一起


```python
def add(x, y):
    return x + y

print(add(3, 4))
>>>
7
```

参数引用 (在函数体内赋值，不会影响外部变量)

```python
x = 1
y = 2

def swap(x, y):
    x, y = y, x

swap(x, y)
print(x, y)
>>>
1 2
```

直传递

```python
L = [1, 2, 3]

def append(item):
    L.append(item)

append(4)
print(L)
>>>
[1, 2, 3, 4]
```

### 位置参数 

位置参数，通过参数传递的位置来决定

```python
def add(x, y):
    print('x = {0}'.format(x))
    print('y = {0}'.format(y))
    return x + y

print(add(1, 2))
>>>
x = 1
y = 2
3
```

### 关键字参数

关键字参数，通过参数名称来决定

```python
def add(x, y):
    print('x = {0}'.format(x))
    print('y = {0}'.format(y))
    return x + y

print(add(y=2, x=1))
>>>
x = 1
y = 2
3
```

### 位置，关键字参数混合使用

位置参数和关键字参数是在调用的时候决定的

```python
def add(x, y):
    print('x = {0}'.format(x))
    print('y = {0}'.format(y))
    return x + y

print(add(1, y=2))
>>>
x = 1
y = 2
3
```

关键字参数必须在位置参数之后

```python
def add(x, y):
    print('x = {0}'.format(x))
    print('y = {0}'.format(y))
    return x + y

print(add(x=1, 2))
>>>
  File "/Users/xhxu/python/python3/test/1.py", line 6
    print(add(x=1, 2))
                  ^
SyntaxError: positional argument follows keyword argument
```

### 默认参数

如果参数赋予了默认值，且该参数最终没有被传递，将使用该默认值

```python
def inc(x, i=1):
    return x + i

print(inc(5))
print(inc(5, 2))
>>>
6
7
```



### 可变参数 (可变位置参数)

函数定义时，参数名前加\*代表次参数是可变的位置参数
函数调用是，参数列表封装成一个元组传递给args

使用列表的调用方法

```python
def sum(L):
    ret = 0
    for x in L:
        ret += x
    return ret

print(sum([1, 2, 3, 4, 5]))
>>>
15
```

使用可变参数的调用方法 (调用参数的时候，自动组装成元组)

```python
def sum(*args):
    ret = 0
    for x in args:
        ret += x
    return ret

print(sum(1, 2, 3, 4, 5))
>>>
16
```





### 关键字参数 (可变关键字参数)

使用可变关键字参数  (调用参数的时候，自动组装成字典)

```python
def print_info(**kwargs):
    for k, v in kwargs.items():
        print('{0} -> {1}'.format(k, v))

print(print_info(a=1, b=2))
>>>
b -> 2
a -> 1
```

调用的时候, 会决定参数是位置参数还是关键字参数

```python
def print_info(**kwargs):
    for k, v in kwargs.items():
        print('{0} -> {1}'.format(k, v))

print_info(1, 2, 3)
>>>
  File "/Users/xhxu/python/python3/test/1.py", line 5, in <module>
    print_info(1, 2, 3)
TypeError: print_info() takes 0 positional arguments but 3 were given
```

### 可变参数和关键字参数混合使用

```python
def print_info(*args, **kwargs):
    for x in args:
        print(x)
    for k, v in kwargs.items():
        print('{0} -> {1}'.format(k, v))

print_info(1, 2, 3, a=4, b=5)
>>>
1
2
3
a -> 4
b -> 5
```

### 可变参数和非可变参数

```python
def print_info(x, y, *args, **kwargs):
    print('x = {0}'.format(x))
    print('y = {0}'.format(y))
    for x in args:
        print(x)
    for k, v in kwargs.items():
        print('{0} -> {1}'.format(k, v))

print_info(1, 2, 3, 4, 5, 6, a=7, b=8)
>>>
x = 1
y = 2
3
4
5
6
b -> 8
a -> 7
```





### 参数解包

#### 位置参数解包

```python
def add(x, y):
    print('x = {0}'.format(x))
    print('y = {0}'.format(y))
    return x + y

L = [1, 2]
print(add(*L))
>>>
x = 1
y = 2
3
```





#### 关键字参数解包

```python
def add(x, y):
    print('x = {0}'.format(x))
    print('y = {0}'.format(y))
    return x + y

d = {'x': 1, 'y': 2}
print(add(**d))
>>>
x = 1
y = 2
3
```





## 闭包

推荐在框架，包，类库中推荐使用闭包



所谓的闭包就是一个包含有环境变量取值的函数对象。环境变量取值被保存在函数对象的`__closure__`属性中

即内层函数引用到了外层函数的自由变量，就形成了闭包

函数是一个对象，所以可以作为某个函数的返回结果

```
def curve_pre():
    a = 25
    def curve(x):
        return a*x*x
    return curve

f = curve_pre()
print(f.__closure__)
print(f.__closure__[0].cell_contents)
print(f(2))
>>>
(<cell at 0x10b60c468: int object at 0x10b2e4210>,)
25
100
```







`__closure__`包含一个元组，而第一个cell包含的就是闭包的环境变量b的取值

```
def line_conf():
    b = 15
    def line(x):
        return 2*x+b  #这里只是调用b，不是重新定义b
    return line

b = 5
my_line = line_conf()
print(my_line.__closure__)
print(my_line.__closure__[0].cell_contents)
>>>
(<cell at 0x10dfd2fd8: int object at 0x10dce7830>,)
15
```



```
def f1():
    a = 10
    def f2():
        a = 20
    return f2

f = f1()
print(f)
print(f.__closure__)
>>>
<function f1 at 0x1050937b8>
None
```





函数line 与环境变量a, b 构成闭包

```
def line_conf(a, b):
    def line(x):
        return a*x+ b
    return line

line1 = line_conf(1, 1)
line2 = line_conf(4, 5)
print(line1(5), line2(5))
>>>
6 25
```



### nonlocal 

nonlocal 关键字，将变量标记为不在本地作用域定义，而在上面某一级局部作用域中定义，但不是在全局作用域中定义

```
In [183]: def counter():
     ...:     count = 0
     ...:     def inc():
     ...:         nonlocal count # 没有nonlocal会报错
     ...:         count += 1
     ...:         return count
     ...:     return inc
     ...:
     ...:

In [184]: foo = counter()

In [185]: foo()
Out[185]: 1

In [186]: foo()
Out[186]: 2

In [187]: foo()
Out[187]: 3
```

```
origin = 0 

def factory(pos):
    def go(step):
        nonlocal pos
        new_pos = pos + step
        pos = new_pos
        return new_pos
    return go

tourist = factory(origin)
print(tourist(2))
print(tourist.__closure__[0].cell_contents)
print(tourist(3))
print(tourist.__closure__[0].cell_contents)
print(tourist(5))
print(tourist.__closure__[0].cell_contents)
>>>
2
2
5
5
10
10
```







## 函数调用和参数传递


### 函数调用
整数变量传递给函数
对于基本数据类型的变量，变量传递给函数后，函数会在内存中复制一个新的变量，从而不影响原来的变量。（我们称此为值传递）

```
a = 1
def change_integer(a):
    a += 1
    return a

print(change_integer(a))
print(a)
>>>
2
1
```



### 参数传递

表传递给函数
对于表来说，表传递给函数的是一个指针，指针指向序列在内存中的位置，在函数中对表的操作将在原有内存中进行，从而影响原有变量。 （我们称此为指针传递）

```
b = [1, 2, 3]
def change_list(b):
    b[0] = b[0] + 1
    return b

print(change_list(b))
print(b)
>>>
[2, 2, 3]
[2, 2, 3]
```



函数的参数传递，本质上传递的就是引用

```
def f(x):
    x = 100
    print(x)

a = 1
f(a)
print(a)
>>>
100
1
```

如果传递的是可变(mutable)对象，那么改变函数参数，有可能改变原对象
```
def f(x):
    x[0] = 100
    print(x)

a = [1, 2, 3]
f(a)
print(a)
>>>
[100, 2, 3]
[100, 2, 3]
```





## 函数返回值

函数在执行过程中只要遇到return语句，就会停止执行并返回结果

如果未在函数中指定return,那这个函数的返回值为None 

多个return只执行第一个语句



return = 0, 返回None

return = 1, 返回object

return > 1, 返回tuple

```
def test1():
    pass


def test2():
    return 0


def test3():
    return 0, 10, 'hello'


t1 = test1()
t2 = test2()
t3 = test3()

print(type(t1), t1)
print(type(t2), t2)
print(type(t3), t3)

>>>
<class 'NoneType'> None
<class 'int'> 0
<class 'tuple'> (0, 10, 'hello')
```



## 匿名函数 Lambda

匿名函数就是不需要显式的指定函数

冒号用于分割参数列表和表达式

```
def calc(n):
    return n**n
print(calc(10))

```

换成匿名函数

```
calc = lambda n:n**n
print(calc(10))
```



匿名函数主要是和其它函数搭配使用

```
res = map(lambda x:x**2,[1,5,7,4,8])
for i in res:
    print(i)
    
>>>
1
25
49
16
64
```





## 函数递归 (少用)

在函数内部，可以调用其他函数。如果一个函数在内部调用自身本身，这个函数就是递归函数。

递归函数深度越深，效率越低，若递归函数复杂，反复压栈，栈内存很快就溢出了



递归函数必须明确结束条件

```
def calc(n):
    print(n)
    if int(n/2) ==0:
        return n
    return calc(int(n/2))
 
calc(10)
 
>>>
10
5
2
1
```



## 函数式编程

函数式编程要求使用函数，可以把运算过程定义为不同的函数

```
var result = subtract(multiply(add(1, 2), 3), 4)
```





## 函数注解 annotations

Python 3.5 引入

对函数的参数进行类型注解

对函数的返回值进行类型注解

对函数做辅助说明，并不对函数参数进行类型检查

默认保存在`__annotations__` 属性中

```
In [9]: def add(x:int, y:int) -> int:
   ...:     """
   ...:     :param x:int
   ...:     :param y:int
   ...:     :return:int
   ...:     """
   ...:     return x + y
   ...:

In [10]: help(add)


In [11]: add('Rick', 'Xu')
Out[11]: 'RickXu'

In [12]: add.__annotations__
Out[12]: {'return': int, 'x': int, 'y': int}
```



### inspect 模块

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

