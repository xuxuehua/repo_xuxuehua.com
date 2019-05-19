---
title: "decoration"
date: 2018-08-21 21:30
collection: 函数
---

[TOC]

# 装饰器

装饰器的本质就是函数， 可以让其他函数在不需要做任何代码变动的前提下增加额外的功能

装饰器的返回值也是一个函数对象

常用于: 插入日志，性能测试，事务处理，缓存，权限校验等



## 原则

不能修改被装饰的函数的源代码

不能修改被装饰函数的调用方式



## 基本实现

```
def deco(func):
    print('start func')
    return func  # 这里如果没有return就继续执行了
    print('end func')


def test():
    print('this is test')


test = deco(test)
test()

>>>
start func
this is test
```



## @语法糖装饰函数

`test = deco(test)` 

等价于

```
@deco
def test():
    pass
```

```
def deco(func):
    def inside():
        print('start func')
        func()
        print('end func')
    return inside


def test():
    print('this is test')

test = deco(test)
test()

>>>
start func
this is test
end func
```

```
def deco(func):
    def inside():
        print('start func')
        func()
        print('end func')
    return inside


@deco
def test():
    print('this is test')

test()

>>>
start func
this is test
end func
```



## 带参数操作

### 带参数的函数进行装饰

```
def deco(func):
    def inside_deco(a, b):
        print('inside start')
        ret = func(a, b)
        print('inside end')
        return ret  #这是返回func的关键
    return inside_deco


@deco
def myfunc(a, b):
    print('value a %s, b %s' % (a, b))
    return a + b

ret = myfunc(1, 2)
print('ret value', ret)

>>>
inside start
value a 1, b 2
inside end
ret value 3
```



### 参数数量不确定的函数进行装饰

```
def deco(func):
    def inside_deco(*args, **kwargs):
        print('inside start')
        ret = func(*args, **kwargs)
        print('inside end')
        return ret
    return inside_deco


@deco
def myfunc(*args, **kwargs):
    print('args: %s, kwargs: %s' % (args, kwargs))
    return args

ret = myfunc(1, 2, 3, a=1, b=2, c=3)
print('ret value', ret)

>>>
inside start
args: (1, 2, 3), kwargs: {'b': 2, 'a': 1, 'c': 3}
inside end
ret value (1, 2, 3)
```



### 装饰器带参数

```
def outside_deco(deco_params):
    def deco(func):
        def inside_deco(*args, **kwargs):
            print('inside start with %s' % (deco_params))
            ret = func(*args, **kwargs)
            print('inside end with %s' % (deco_params))
            return ret
        return inside_deco
    return deco


@outside_deco('deco params')
def myfunc(*args, **kwargs):
    print('args: %s, kwargs: %s' % (args, kwargs))
    return args

ret = myfunc(1, 2, 3, a=1, b=2, c=3)
print('ret value', ret)

>>>
inside start with deco params
args: (1, 2, 3), kwargs: {'b': 2, 'a': 1, 'c': 3}
inside end with deco params
ret value (1, 2, 3)
```





## 叠放装饰器

如果一个函数被多个装饰器修饰，其实应该是该函数先被最里面的装饰器修饰



```
def outer(func):
    print('Enter outer', func)
    def wrapper():
        print('Running outer')
        func()
    return wrapper

def inner(func):
    print('Enter inner', func)
    def wrapper():
        print('Running inner')
        func()
    return wrapper

@outer
@inner
def main():
    print('Running main')

if __name__ == "__main__":
    main()
    
>>>
Enter inner <function main at 0x10a1ae730>
Enter outer <function inner.<locals>.wrapper at 0x10a1ae7b8>
Running outer
Running inner
Running main
```

> 函数main()先被inner装饰，变成新的函数，变成另一个函数后，再次被装饰器修饰





## 装饰类

一个装饰器可以接收一个类，并返回一个类，起到加工的作用。

```python
def deco(aClass):
    class newClass:
        def __init__(self, age):
            self.total_display = 0
            self.wrapper = aClass(age)

        def display(self):
            self.total_display += 1
            print('total display', self.total_display)
            self.wrapper.display()

    return newClass


@deco
class Bird:
    def __init__(self, age):
        self.age = age

    def display(self):
        print('My age: ', self.age)


eagleLord = Bird(5)

for i in range(3):
    eagleLord.display()

>>>
total display 1
My age:  5
total display 2
My age:  5
total display 3
My age:  5
```





## doc string

在函数语句块的第一行书写，习惯使用三个引号封装

使用特殊属性`__doc__` 访问这个文档

```
In [7]: def add(x, y):
   ...:     """This is doc string"""
   ...:     a = x +y
   ...:     return a
   ...:

In [8]: "name={}\ndoc={}".format(add.__name__, add.__doc__)
Out[8]: 'name=add\ndoc=This is doc string'
```





# wraps 装饰器

使用装饰器会产生我们可能不希望出现的副作用，　例如：改变被修饰函数名称，对于调试器或者对象序列化器等需要使用内省机制的那些工具，可能会无法正常运行；其实调用装饰器后，会将同一个作用域中原来函数同名的那个变量（例如下面的func_1）,重新赋值为装饰器返回的对象；使用＠wraps后，会把与内部函数（被修饰函数，例如下面的func_1）相关的重要元数据全部复制到外围函数