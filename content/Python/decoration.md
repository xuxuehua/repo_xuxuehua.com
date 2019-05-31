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





# 类的装饰器

## staticmethod 静态方法

静态方法并不能访问私有变量，只是给类方法加了类的属性

即和类以及对象都没有关系

```
class A:
    __val = 3

    @staticmethod
    def print_val():
        print(__val)

a = A()

a.print_val()
A.print_val()
>>>
Traceback (most recent call last):
  File "/Users/xhxu/python/python3/test/9.py", line 10, in <module>
    a.print_val()
  File "/Users/xhxu/python/python3/test/9.py", line 6, in print_val
    print(__val)
NameError: name '_A__val' is not defined
```

静态方法只属于定义他的类，而不属于任何一个具体的对象, 即不能传self，相当于单纯的一个函数

静态方法无需传入self参数，因此在静态方法中无法访问实例变量

静态方法不能直接访问类的静态变量，但可以通过类名引用静态变量

与成员方法的区别是没有 self 参数，并且可以在类不进行实例化的情况下调用。

静态方法和类方法都是通过给类发消息来调用的



```python
class MyClass:
    var1 = "String1"
    @staticmethod   #静态方法
    def staticmd():
        print('I am static method')

MyClass.staticmd()  #调用了静态方法
c = MyClass()
c.staticmd()
>>>
I am static method
I am static method
```



## classmethod 类方法

至少一个cls参数；

执行类方法时，自动将调用该方法的类复制给cls, cls指代调用者即类对象自身,通过这个cls参数我们可以获取和类相关的信息并且可以创建出类的对象

类方法传递类本身， 可以访问私有的类变量

```python
class A:
    __val = 3

    @classmethod
    def get_val(cls):
        return cls.__val

a = A()

print(a.get_val())
print(A.get_val())
>>>
3
3
```

与静态方法一样，类方法可以使用类名调用类方法

与静态方法一样，类成员方法也无法访问实例变量，但可以访问类的静态变量

与成员方法的区别在于所接收的第一个参数不是 self （类实例的指针），而是cls（当前类的具体类型）。

```python
class MyClass:
    val1 = "String1"  #静态变量
    def __init__(self):
        self.val2 = "Value 2"
    @classmethod      #类方法
    def classmd(cls):
        print('Class: ' + str(cls) + ',val1: ' + cls.val1 + ', Cannot access value 2')

MyClass.classmd()
c = MyClass()
c.classmd()
>>> 
Class: <class '__main__.MyClass'>,val1: String1, Cannot access value 2
Class: <class '__main__.MyClass'>,val1: String1, Cannot access value 2
```



```
from time import time, localtime, sleep

class Clock(object):
    """Digital clock
    """

    def __init__(self, hour=0, minute=0, second=0):
        self._hour = hour
        self._minute = minute
        self._second = second

    @classmethod
    def now(cls):
        ctime = localtime(time())
        return cls(ctime.tm_hour, ctime.tm_min, ctime.tm_sec)

    def run(self):
        self._second += 1
        if self._second == 60:
            self._second = 0
            self._minute += 1
            if self._minute == 60:
                self._minute = 0
                self._hour += 1
                if self._hour == 24:
                    self._hour = 0 

    def show(self):
        return '%02d:%02d:%02d' % \
            (self._hour, self._minute, self._second)

def main():
		# 通过类方法创建对象并获取系统时间
    clock = Clock.now()
    while True:
        print(clock.show())
        sleep(1)
        clock.run()

if __name__ == "__main__":
    main()
```





类方法不能访问实例变量, 但可以访问类变量

```
class A:
    __val = 3

    def __init__(self, x):
        self.x = x

    @classmethod
    def get_val(cls):
        print(cls.x)
        return cls.__val

a = A(5)
print(A.get_val())
>>>
Traceback (most recent call last):
  File "/Users/xhxu/python/python3/test/9.py", line 12, in <module>
    print(A.get_val())
  File "/Users/xhxu/python/python3/test/9.py", line 9, in get_val
    print(cls.x)
AttributeError: type object 'A' has no attribute 'x'
```



## property 属性

加上property 装饰器，在函数调用的时候，就不需要加上小括号

property调用的时候，只能调用self参数

将一个类方法转变成一个静态属性,只读属性。

类属性可以直接被类调用，如果想要实现能被类直接调用的方法就可以借助staticmethod和classmethod了，区别在于staticmethod的方法没有self参数，通常用来直接定一个静态类方法，如果想将一个普通动态方法变成类方法就要使用classmethod了。

property 属性必须在setter 和 deleter之前

```python
class A:
    def __init__(self):
        self.__value = 0

    @property # property 可以访问私有的变量
    def value(self):
        if self.__value < 0:
            return 0
        return self.__value

    @value.setter # 加了这个装饰器，才会被认为是属性
    def value(self, val):
        if isinstance(val, (int, float)) and val >= 0:
            self.__value = val
        else:
            self.__value = 0

a = A()
a.value = -1
print(a.value)
a.value = 3
print(a.value)
>>>
0
3
```



同一个对象的不同属性之间可能存在依赖关系。当某个属性被修改时，依赖于该属性的其他属性也会同时变化。

```
class bird(object):
    feather = True

class chicken(bird):
    fly = False
    def __init__(self, age):
        self.age = age

    def getAdult(self):
        if self.age > 1.0:
            return True
        else:
            return False
    adult = property(getAdult)  # 这里定义了adult变量，其等同于将getAdult方法添加@property装饰器的结果指向给adult

summer = chicken(2)

print(summer.adult)
summer.age = 0.5
print(summer.adult)
>>>
True
False
```







```
from abc import ABCMeta, abstractmethod

class Employee(object, metaclass=ABCMeta):
    """Employee
    """

    def __init__(self, name):
        self._name = name

    @property
    def name(self):
        return self._name

    @abstractmethod
    def get_salary(self):
        pass


class Manager(Employee):
    def get_salary(self):
        return 15000.00


class Programmer(Employee):

    def __init__(self, name, working_hour=0):
        super().__init__(name)
        self._working_hour = working_hour

    @property
    def working_hour(self):
        return self._working_hour

    @working_hour.setter
    def working_hour(self, working_hour):
        self._working_hour = working_hour if working_hour > 0 else 0

    def get_salary(self):
        return 150.00* self._working_hour
        
    
class Salesman(Employee):

    def __init__(self, name, sales=0):
        super().__init__(name)
        self._sales = sales

    @property
    def sales(self):
        return self._sales

    @sales.setter
    def sales(self, sales):
        self._sales = sales if sales > 0 else 0

    def get_salary(self):
        return 1200.00 + self._sales * 0.05


def main():
    emps = [Manager('William'), Programmer('Rick'), Salesman('Frank')]
    for emp in emps:
        if isinstance(emp, Programmer):
            emp.working_hour = int(input('Input %s working hours: ' % emp.name))
        elif isinstance(emp, Salesman):
            emp.sales = float(input('Input %s sales rate: ' % emp.name))
        print('%s monthly pay check: %s' % (emp.name, emp.get_salary()))

if __name__ == "__main__":
    main()

```



### setter 装饰器

与属性名同名，并且接受2个参数，第一个是self， 第二个是将要赋值的值。

```
class A:

    def __init__(self):
        self.__val = 0

    @property
    def val(self):
        if self.__val < 0:
            return 0
        return self.__val

    @val.setter
    def val(self, value):
        if isinstance(value, (int, float)) and value >= 0:
            self.__val = value
        else:
            self.__val = 0


a = A()
print(a.val)
a.val = 3
print(a.val)
>>>
0
3
```



### deleter 装饰器 

可以控制是否删除属性，不常用

```
class Person:
    def __init__(self, name, age=18):
        self.name = name
        self.__age = age

    @property
    def age(self):
        return self.__age

    @age.setter
    def age(self, age):
        self.__age = age

    @age.deleter
    def age(self):
        print('del age')

p = Person('Rick')
print(p.age)

p.age = 30
print(p.age)

del p.age
print(p.age)

>>>
18
30
del age
30
```





### property 参数

property()最多可以加载四个参数。

第一个参数是方法名，调用 对象.属性 时自动触发执行方法

第二个参数是方法名，调用 对象.属性 ＝ XXX 时自动触发执行方法

第三个参数是方法名，调用 del 对象.属性 时自动触发执行方法

第四个参数是字符串，调用 对象.属性.`__doc__` ，此参数是该属性的描述信息

negative 为一个特性，用于表示数字的负数。property() 最后一个参数('I am negative one') 表示特性negative的说明文档

```
class Goods(object):

    def __init__(self):
        self.original_price = 100
        self.discount = 0.8

    def get_price(self):
        new_price = self.original_price * self.discount
        return new_price

    def set_price(self, value):
        self.original_price = value

    def del_price(self):
        del self.original_price

    PRICE = property(get_price, set_price, del_price, 'Price description')


obj = Goods()
print(obj.PRICE)
obj.PRICE = 200
print(obj.PRICE)
del obj.PRICE
>>>
80.0
160.0
```



### property 实现的原理

```
class A:

    def __init__(self):
        self.__val = 0

    def get_val(self):
        return self.__val

    def set_val(self, value):
        self.__val = value

    val = property(get_val, set_val)

print(type(A.val))
a = A()

a.val = 3
print(a.val)
>>>
<class 'property'>
3
```





# wraps 装饰器

使用装饰器会产生我们可能不希望出现的副作用，　例如：改变被修饰函数名称，对于调试器或者对象序列化器等需要使用内省机制的那些工具，可能会无法正常运行；其实调用装饰器后，会将同一个作用域中原来函数同名的那个变量（例如下面的func_1）,重新赋值为装饰器返回的对象；使用＠wraps后，会把与内部函数（被修饰函数，例如下面的func_1）相关的重要元数据全部复制到外围函数