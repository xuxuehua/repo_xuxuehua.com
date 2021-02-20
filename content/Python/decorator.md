---
title: "decorator"
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
        func()   # 调用的时候真正调用的是test函数
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



### Pipeline 的一种实现

```
class Pipe:
    def __init__(self, func):
        self.func = func

    def __ror__(self, other):
        def generator():
            for obj in other:
                if obj is not None:
                    yield self.func(obj)
        return generator()


@Pipe
def event_filter(num):
    return num if num % 2 == 0 else None


@Pipe
def multiply_by_three(num):
    return num*3


@Pipe
def convert_to_string(num):
    return 'The Number: %s' % num


@Pipe
def echo(item):
    print(item)
    return item


def force(sqs):
    for item in sqs: pass


nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

force(nums | event_filter | multiply_by_three | convert_to_string | echo )

>>>
The Number: 6
The Number: 12
The Number: 18
The Number: 24
The Number: 30
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

如果一个函数被多个装饰器修饰，其实应该是该函数先被最里面的装饰器修饰, 即从里到外执行

```
@deco1
@deco2
@deco3
def func()
    pass


等价于

deco1(deco2(deco3(func)))
```

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





### make html tag

```
def make_html_tag(tag, *args, **kwargs):
    def real_decorator(func):
        css_class = " class='{}'".format(kwargs['css_class']) if 'css_class' in kwargs else ""

        def wrapper(*args, **kwargs):
            return "<"+tag+css_class+">" + func(*args, **kwargs) + "</"+tag+">"
        return wrapper
    return real_decorator


@make_html_tag(tag="b", css_class="bold_css")
@make_html_tag(tag="i", css_class="italic_css")
def hello():
    return "hello world"


print(hello())

>>>
<b class='bold_css'><i class='italic_css'>hello world</i></b>
```





## 类装饰器

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

类装饰器主要依赖于函数`__call__()`, 每当调用一个类实例，函数`__call__()` 就会被执行一次

```
class Count:
    def __init__(self, func):
        self.func = func
        self.num_calls = 0

    def __call__(self, *args, **kwargs):
        self.num_calls += 1
        print('Num of calls is : {}'.format(self.num_calls))
        return self.func(*args, **kwargs)


@Count
def example():
    print('hello world')


example()
example()

>>>
Num of calls is : 1
hello world
Num of calls is : 2
hello world
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

静态方法不能直接访问类的静态变量，但可以通过类名引用静态变量, 因为没有 self 参数，可以在类不进行实例化的情况下调用。也因此在静态方法中无法访问实例变量

如果在方法中不需要访问任何实例方法和属性，纯粹地通过传入参数并返回数据的功能性方法，那么它就适合用静态方法来定义，它节省了实例化对象的开销成本，往往这种方法放在类外面的模块层作为一个函数存在也是没问题的，而放在类中，仅为这个类服务

静态方法和类方法都是通过给类发消息来调用的

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

至少一个cls参数

执行类方法时，自动将调用该方法的类复制给cls, cls指代调用者即类对象自身,

通过这个cls参数可以获取和类相关的信息并且可以创建出类的对象

类方法传递类本身， 可以访问私有的类变量

类函数最常用的功能是实现不同的 init 构造函数

与静态方法一样，类方法可以使用类名调用类方法

与静态方法一样，类成员方法也无法访问实例变量，但可以访问类的静态变量

作为工厂方法创建实例对象，例如内置模块 datetime.date 类中就有大量使用类方法作为工厂方法，以此来创建date对象

```
class date:

    def __new__(cls, year, month=None, day=None):
        self = object.__new__(cls)
        self._year = year
        self._month = month
        self._day = day
        return self

    @classmethod
    def fromtimestamp(cls, t):
        y, m, d, hh, mm, ss, weekday, jday, dst = _time.localtime(t)
        return cls(y, m, d)

    @classmethod
    def today(cls):
        t = _time.time()
        return cls.fromtimestamp(t)
```



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



如果希望在方法裡面调用静态类，那么把方法定义成类方法是合适的，因为要是定义成静态方法，那么你就要显示地引用类A，这对继承来说可不是一件好事情。

classmethod增加了一个对实际调用类的引用，这带来了很多方便的地方：

1. 方法可以判断出自己是通过基类被调用，还是通过某个子类被调用
2. 通过子类调用时，方法可以返回子类的实例而非基类的实例
3. 通过子类调用时，方法可以调用子类的其他classmethod

一般来说classmethod可以完全替代staticmethod。staticmethod唯一的好处是调用时它返回的是一个真正的函数，而且每次调用时返回同一个实例（classmethod则会对基类和子类返回不同的bound method实例），但这点几乎从来没有什么时候是有用的。不过，staticmethod可以在子类上被重写为classmethod，反之亦然，因此也没有必要提前将staticmethod全部改为classmethod，按需要使用即可。



```
class A:

    @staticmethod
    def m1()
        pass

    @staticmethod
    def m2():
        A.m1() # bad

    @classmethod
    def m3(cls):
        cls.m1() # good
```





## property 属性

用来实现属性可管理性的build-in数据类型

加上property 装饰器，在函数调用的时候，就不需要加上小括号

property调用的时候，只能调用self参数

可以将一个类方法转变成一个静态属性, 只读属性。

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

# functions.wraps 方法

使用装饰器会产生我们可能不希望出现的副作用

例如：改变被修饰函数名称，对于调试器或者对象序列化器等需要使用内省机制的那些工具，可能会无法正常运行；其实调用装饰器后，会将同一个作用域中原来函数同名的那个变量（例如下面的func_1）,重新赋值为装饰器返回的对象；

使用＠wraps后，会把与内部函数（被修饰函数，例如下面的func_1）相关的重要元数据全部复制到外围函数



* 这里函数的元信息改变了

```
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print('Wrapper of decorator')
        func(*args, **kwargs)
    return wrapper


@my_decorator
def greet(message):
    print(message)


greet('hello world')
print(greet.__name__)

>>>
Wrapper of decorator
hello world
wrapper
```



* 使用内置的wraps 保留原函数的元信息

```
from functools import wraps


def my_decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print('Wrapper of decorator')
        func(*args, **kwargs)
    return wrapper


@my_decorator
def greet(message):
    print(message)


greet('hello world')
print(greet.__name__)

>>>
Wrapper of decorator
hello world
greet
```

# example

## 认证

```
from functools import wraps


def authenticate(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        request = args[0]
        if check_user_logged_in(request):
            return func(*args, **kwargs)
        else:
            raise Exception('Authentication failed')
    return wrapper


@authenticate
def post_message(request, ...):
    pass
```



## 日志记录

在实际工作中，如果你怀疑某些函数的耗时过长，导致整个系统的latency(延迟)增加，所以想在线上测试某些函数的执行时间，那么，装饰器就是一种很常用的手段。

```
import time
import functools


def log_executed_time(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        res = func(*args, **kwargs)
        end = time.perf_counter()
        print('{} took {} ms'.format(func.__name__, (end - start) * 1000))
        return res
    return wrapper

@log_executed_time
def calculate_similarity(items)
    pass
```

## 输入合理性检查

在大型公司的机器学习框架中，调用机器集群进行模型训练前，往往会用装饰器对其输入(往往是很长的json文件)进行合理性检查。这样就可以大大避免，输入不正确对机器造成的巨大开销。

```
import functools
def validation_check(input):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        ... # 检查输入是否合法

@validation_check
def neural_network_training(param1, param2, ...):
...
```



## 缓存

关于缓存装饰器的用法，其实十分常见，以Python内置的LRU cache为例来说明

LRU cache，在Python中的表示形式是@lru_cache。@lru_cache会缓存进程中的函数参数和结果，当缓 存满了以后，会删除least recenly used 的数据。 

正确使用缓存装饰器，往往能极大地提高程序运行效率。为什么呢?我举一个常见的例子来说明。

大型公司服务器端的代码中往往存在很多关于设备的检查，比如你使用的设备是安卓还是iPhone，版本号 是多少。这其中的一个原因，就是一些新的feature，往往只在某些特定的手机系统或版本上才有(比如 Android v200+)。 

这样一来，我们通常使用缓存装饰器，来包裹这些检查函数，避免其被反复调用，进而提高程序运行效率，
比如写成下面这样

```
@lru_cache
def check(param1, param2): # 检查用戶设备类型，版本号等等
    pass
```
