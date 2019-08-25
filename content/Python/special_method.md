---
title: "special_method 特殊方法"
date: 2018-08-28 14:39
collection: 面向对象
---



[TOC]

# 魔术方法和特殊方法

## 对象的创建与销毁

`__new__` 创建对象
`__init__` 初始化对象
`__del__` 销毁对象 （销毁对象时调用）

```python
class A:
    def __new__(cls):
        print('call __new__')
        return object.__new__(cls)

    def __init__(self):
        print('call __init__')

    def method(self):
        print('call method')

    def __del__(self):
        print('call __del__')

a = A()

a.method()

del a
>>>
call __new__
call __init__
call method
call __del__
```


### 可视化对象

```python
class A:
    def __init__(self, name):
        self.name = name

    def __repr__(self):
        return self.name

    def __str__(self):
        return 'call __str__ name is {0}'.format(self.name)

    def __bytes__(self):
        return 'call __bytes__ name is {0}'.format(self.name).encode('utf-8')

a = A('Rick')

#其实调用的是print(repr(a))
print(a)
print(str(a))
print(bytes(a))
>>>
call __str__ name is Rick
call __str__ name is Rick
b'call __bytes__ name is Rick'
```



## 特殊方法

Python 的多范式依赖于Python对象中的特殊方法



### 运算符计算

Python 中两个对象能否进行加法运算，需要确认相应的对象是否含有`__add__()`方法

```
'abc' + 'xyz'
#实际上执行了如下操作
'abc'.__add__('xyz')
```



其实内置函数也是调用对象的特殊方法

```
len([1, 2, 3])
# 实际上是调用的
[1, 2, 3].__len__()
```

表元素引用方式, 调用`__getitem__()`方法

```
In [1]: li = [1, 2, 3, 4, 5]

In [2]: li[3]
Out[2]: 4

In [3]: li.__getitem__(3)
Out[3]: 4
```

`__call__() `特殊方法的对象都会被当做函数

```
class SampleMore(object):
    def __call__(self, a):
        return a + 5

add = SampleMore()
print(add(2))
print(map(add, [2, 4, 5]))
>>>
7
<map object at 0x10ebd4588>
```



### `__getattr__`

`__getattr__(self, name)` 来查询即时生成的属性。如果通过`__dict__`方法无法找到改属性，Python会调用`__getattr__`方法来即时生成属性。

```
class Bird(object):
    feather = True


class Chicken(Bird):
    fly = False
    def __init__(self, age):
        self.age = age

    def __getattr__(self, item):
        if item == 'adult':
            if self.age > 1.0:
                return True
            else:
                return False
        raise AttributeError(item)

summer = Chicken(2)
print(summer.adult)
summer.age = 0.5
print(summer.adult)
>>>
True
False
```



```
class UrlGenerator(object):
    def __init__(self, root_url):
        self.url = root_url

    def __getattr__(self, item):
        if item == 'get' or item == 'post':
            print self.url
        return UrlGenerator('{}/{}'.format(self.url, item))


url_gen = UrlGenerator('http://xxxx')
url_gen.users.show.get

>>> 
http://xxxx/users/show
```





### `__setattr__`
`__setattr__(self, name, value)` 
可用于修改属性，可用于任意属性。



### `__delattr__`
`__delattr__(self, name)` 
可用于删除属性，可用于任意属性。



### bool 函数

```python
class Grok:
    def __init__(self, val):
        self.val = val

    def __bool__(self):
        return not self.val

grok1 = Grok(True)
grok2 = Grok(False)

print(bool(grok1))
print(bool(grok2))
>>>
False
True
```

```python
class LIST:
    def __init__(self, *args):
        self.val = args

    def __len__(self):
        return len(self.val)

lst = LIST(1, 2, 3)
lst2 = LIST()

print(len(lst))
print(len(lst2))
>>>
3
0
```



### hash与可hash对象

```python
class Grok:
    def __init__(self, val):
        self.val = val

    def __hash__(self):
        return id(self.val)

grok = Grok(123)
print(hash(grok))
>>>
4306027616
```





### 可调用对象

```python
class InjectUser:
    def __init__(self, default_user):
        self.user = default_user

    def __call__(self, fn):
        def wrap(*args, **kwargs):
            if 'user' not in kwargs.keys():
                kwargs['user'] = self.user
            return fn(*args, **kwargs)
        return wrap

@InjectUser('Rick')
def do_something(*args, **kwargs):
    print(kwargs.get('user'))

do_something()
>>>
Rick
```





## 运算符重载



###比较运算符重载

```python
class Person:
    def __init__(self, age):
        self.age = age

    def __lt__(self, other):
        print('lt')
        return self.age < other.age

    def __le__(self, other):
        print('le')
        return self.age <= other.age

    def __eq__(self, other):
        print('eq')
        return self.age == other.age

    def __ne__(self, other):
        print('ne')
        return self.age != other.age

    def __gt__(self, other):
        print('gt')
        return self.age > other.age

    def __ge__(self, other):
        print('ge')
        return self.age >= other

p1 = Person(18)

p2 = Person(13)

print(p1 > p2)
print(p1 < p2)
>>>
gt
True
lt
False
```





### 算数运算符重载

在Python中，运算符也是通过相应函数实现的

运算符对应的其实就是类中的一些魔术方法（专有方法）。比如加、减、乘、除对应的就是`__add__`、`__sub__`、`__mul__`、`__div__`

```
class Mylist(object):

    def __init__(self, *args):
        self.__mylist= []
        for arg in args:
            self.__mylist.append(arg)

    def __add__(self, other):
        for i in range(len(self.__mylist)):
            self.__mylist[i] = self.__mylist[i] + other

    def show(self):
        print(self.__mylist)


l = Mylist(1, 2, 3, 4)
l.show()
l + 10
l.show()
>>>
[1, 2, 3, 4]
[11, 12, 13, 14]
```





## 反射

过字符串映射或修改程序运行时的状态、属性、方法,



### getattr

getattr(object, name, default=None)

一般情况我们这么用getattr(object,name)。一般来说这里的object是对象，name传的是字符串。

对又是什么呢？可以简单的理解为：在import 引入模块时，通过dir可查询的目标就是对象，而查询得到的结果就是属性，属性就是getattr用法中name部分传入的值。



```
>>> class test:
...     cal=1
...
>>> getattr(test,"cal")
1
>>> test.cal
```

```
>>> dir(test)
['__doc__', '__module__', 'cal']
>>> getattr(test,__doc__)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: getattr(): attribute name must be string
>>> getattr(test,'__doc__')
```





### hasattr

hasattr(object,name)







### setattr

setattr(x, y, v)



### delattr

delattr(x, y)







## with 语句实现的方法

with 语句可以自动关闭资源

任何定义了`__enter__()` 和`__exit__()`方法的对象都可以用于上下文管理器。文件对象f是内置对象，所以f自动带有这两种特殊方法

```python
class Resource:
    def __init__(self):
        print('init')

    def __enter__(self):
        print('enter')
        print(self)
        return self

    def __exit__(self, *args, **kwargs):
        print('exit')

with Resource() as res:
    print(res)
>>>
init
enter
<__main__.Resource object at 0x103995da0>
<__main__.Resource object at 0x103995da0>
exit
```



## 描述器

实现了`__get__`, `__set__`, `__delete__` 的方法成为描述器

```python
class Point:

    def __init__(self, x, y):
        self.x = x
        self.y = y

class Number:

    def __init__(self, name):
        self.name = name

    def __get__(self, instance, cls):
        print('get')
        if instance is not None:
            return instance.__dict__[self.name]

    def __set__(self, instance, value):
        print('set')
        if isinstance(value, (int, float)):
            instance.__dict__[self.name] = value
        else:
            raise TypeError('excepted int or float')

    def __delete__(self, instance):
        del instance.__dict__[self.name]

class Point:
    x = Number('x')
    y = Number('y')
    def __init__(self, x, y):
        self.x = x
        self.y = y

point = Point(1, 2)

print(point.x)  # 其调用Point.x.__get__(point, Point)
print(point.y)
>>>
set
set
get
1
get
2
```



### property 装饰器的实现

通过描述器实现

描述器就是一个传递协议

```python
class Property:
    def __init__(self, fget=None, fset=None, fdel=None):
        self.fget = fget
        self.fset = fset
        self.fdel = fdel

    def __get__(self, instance, cls):
        if self.fget is not None:
            return self.fget(instance)

    def __set__(self, instance, value):
        if self.fset is not None:
            return fset(instance, value)

    def __delete__(self, instance):
        if self.fdel is not None:
            return fdel(instance)

    def getter(self, fn):
        self.fget = fn

    def setter(self, fn):
        self.fset = fn

    def deler(self, fn):
        self.fdel = fn

class Spam:
    def __init__(self, val):
        self.__val = val

    @property
    def val(self):
        return self.__val

    @val.setter
    def val(self, value):
        self.__val = value

s = Spam(3)
print(s.val)
>>>
3
```









## 