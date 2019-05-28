---
title: "class 类"
date: 2018-09-03 01:33
collection: 面向对象
---

[TOC]



# 类之间的关系

类和类之间的关系有三种：is-a、has-a和use-a关系

- is-a关系也叫继承或泛化，比如学生和人的关系、手机和电子产品的关系都属于继承关系。
- has-a关系通常称之为关联，比如部门和员工的关系，汽车和引擎的关系都属于关联关系；关联关系如果是整体和部分的关联，那么我们称之为聚合关系；如果整体进一步负责了部分的生命周期（整体和部分是不可分割的，同时同在也同时消亡），那么这种就是最强的关联关系，我们称之为合成关系。
- use-a关系通常称之为依赖，比如司机有一个驾驶的行为（方法），其中（的参数）使用到了汽车，那么司机和汽车的关系就是依赖关系。



使用一种叫做[UML](https://zh.wikipedia.org/wiki/统一建模语言)（统一建模语言）的东西来进行面向对象建模，其中一项重要的工作就是把类和类之间的关系用标准化的图形符号描述出来。

利用类之间的这些关系，我们可以在已有类的基础上来完成某些操作，也可以在已有类的基础上创建新的类，这些都是实现代码复用的重要手段。

![img](https://snag.gy/pDjZHr.jpg)





# 类的定义

```python
class ClassName:
    pass

# 等价于

class ClassName(object):
    pass
```

```python
class Person:          #定义一个类Person
    def SayHi(self):   #类成员函数必须要有一个参数self,表示类的实例(对象)自身
        print('Hi')

p = Person()           #定义类Person的对象p, 用p来访问类的成员变量和成员函数
p.SayHi()              #调用类Person的成员函数SayHi() 
>>>
hi
```

## 自定义类的新的属性

```
class A:

    def __init__(self, x):
        self.x = x

a = A(5)
a.name = True
print(a.name)
>>>
True


```





## 方法定义 (类中的函数)

写在类中的函数，我们通常称之为（对象的）方法

```python
class ClassName:
    def method_name(self):
        pass
```



### 私有方法

`__val` 但不以__结尾的

`__private_method`：两个下划线开头，声明该方法为私有方法，不能在类地外部调用。在类的内部调用 `self.__private_methods`

```
class A:
    def __init__(self, x):
        self.__val = x

    def __add(self, i):
        self.__val += i

    def get_val(self):
        return self.__val

    def increase(self):
        self.__add(1)

a = A(5)

a.__add
>>>
Traceback (most recent call last):
  File "/Users/xhxu/python/python3/test/10.py", line 15, in <module>
    a.__add()
AttributeError: 'A' object has no attribute '__add'
```

```
class A:
    def __init__(self, x):
        self.__val = x

    def __add(self, i):
        self.__val += i

    def get_val(self):
        return self.__val

    def increase(self):
        self.__add(1)

a = A(5)

print(a.get_val())
>>>
5
```

在类内部对行为进行封装, increase 对__add封装

```
class A:
    def __init__(self, x):
        self.__val = x

    def __add(self, i):
        self.__val += i

    def get_val(self):
        return self.__val

    def increase(self):
        self.__add(1)

a = A(5)
print(a.get_val())

a.increase()
print(a.get_val())
>>>
5
6
```



#### 私有方法原理

python并没有真正的私有，变量改名 _classname__val来绕过

```
class A:
    def __init__(self, x):
        self.__val = x

    def __add(self, i):
        self.__val += i

    def get_val(self):
        return self.__val

    def increase(self):
        self.__add(1)

a = A(5)
print(a.get_val())

a.increase()
print(a.get_val())

print(dir(a))
print(a._A__val)
>>>
5
6
['_A__add', '_A__val', '__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', 'get_val', 'increase']
6
```

















# 类的属性 

## 实例变量

定义在方法中的变量，只作用于当前实例的类。

实例变量的作用域，就是实例本身

类可以访问；类内部可以访问；派生类中可以访问

```
class C:

    def __init__(self):
        self.var = 'public var'

    def func(self):
        print(self.var)


class D(C):

    def show(self):
        print(self.var)

obj = C()
obj.var   # 对象的属性引用
obj.func()

obj_son = D()
obj_son.show()
>>>
public var
public var
```





## 私有变量

仅类内部可以访问

```
class C:

    def __init__(self):
        self.__var = 'private var'

    def func(self):
        print(self.__var)


class D(C):

    def show(self):
        print(self.__var)

obj = C()
obj.__var # 通过对象访问    ==> 错误
obj.func() # 类内部访问        ==> 正确

obj_son = D()
obj_son.show() # 派生类中访问  ==> 错误
```





## 类变量

类变量对所有的实例都是可见的，可以共享，且初值都是一样的

定义在方法体之外，可以通过类名访问，对所有方法共享

实例变量没有，会找类变量调用

类变量通常不作为实例变量使用

```python
class A:
    val = 3
    def __init__(self, x):
        self.x = 3

a1 = A(5)
print(a1.val)

a2 = A(9)
print(a2.val)

a1.val += 1
print(a1.val)
>>>
3
3
4
```



对不可变对象赋值，就会变成新的变量

```
class A:
    val = [1, 2, 3]
    val_s = 'a'

    def __init__(self):
        pass

a1 = A()
a2 = A()
a1.val.append(4)
print(a1.val, id(a1.val))
print(a2.val, id(a2.val))

a3 = A()
a4 = A()
a3.val_s = 's'
print(a3.val_s, id(a3.val_s))
print(a4.val_s, id(a4.val_s))
>>>
[1, 2, 3, 4] 4442997640
[1, 2, 3, 4] 4442997640
s 4437293632
a 4437455120
```



## 私有类变量

私有类变量不能被实例访问

类内部可以通过方法访问

`__private_attrs`：两个下划线开头，声明该属性为私有，不能在类的外部被使用或直接访问。在类内部的方法中使用时 `self.__private_attrs`。

```python
class C:
    __name = 'private var'

    def func(self):
        print('func in C', C.__name)


class D(C):

    def show(self):
        print('show in D', C.__name)

C.__name # 类访问 错误
obj = C()
obj.func() # 类内部可以访问

obj_son = D()
obj_son.show() # 派生类中访问错误
```



## `__slots__` 属性

动态语言允许我们在程序运行时给对象绑定新的属性或方法，当然也可以对已经绑定的属性和方法进行解绑定。但是如果我们需要限定自定义类型的对象只能绑定某些属性，可以通过在类中定义`__slots__`变量来进行限定。需要注意的是`__slots__`的限定只对当前类的对象生效，对子类并不起任何作用。









# 内置特殊方法

## `__init__`

构造方法, 通过类创建对象时，自动触发执行， 可用于绑定属性

不能有返回值，也就是None

```python
class ClassName:
    def __init__(self):
        pass
```

```python
class MyString:
    def __init__(self):      # __init__构造函数, 通过构造函数对类进行初始化操作
        self.str = "MyString"

    def output(self):
        print(self.str)

s = MyString()
s.output()
>>>
MyString
```



构造方法无参数，可以省略构造方法

```
class A:
    __val = 3

    def get_val(self):
        return self.__val

a = A()
print(a.get_val())
a.__val
>>>
Traceback (most recent call last):
  File "/Users/xhxu/python/python3/test/9.py", line 9, in <module>
    a.__val
AttributeError: 'A' object has no attribute '__val'
3
```





## `__dict__`

类或对象中的所有成员信息

对象的属性可能来自与其类定义，叫做类属性(class attribute)。 

类属性可能来自类定义自身，也可能根据类定义继承来的。一个对象的属性还可能是该对象实例定义的，叫做对象属性(object attribute)

对象的属性存储在对象的`__dict__`属性中，`__dict__`为一个词典，键为属性名，对应的值为属性本身。

```
class bird(object):
    feather = True

class chicken(bird):
    fly = False
    def __init__(self, age):
        self.age = age

summer = chicken(2)

print(bird.__dict__)
print(chicken.__dict__)
print(summer.__dict__)

# 利用__class__属性找到对象的类，然后调用类的__base__属性来查询父类
summer.__dict__['age'] = 3
print(summer.__dict__['age'])

summer.age = 5
print(summer.age)
>>>
{'__weakref__': <attribute '__weakref__' of 'bird' objects>, '__doc__': None, '__module__': '__main__', 'feather': True, '__dict__': <attribute '__dict__' of 'bird' objects>}
{'__doc__': None, '__init__': <function chicken.__init__ at 0x10f1c6488>, '__module__': '__main__', 'fly': False}
{'age': 2}
3
5
```

```
class Exam:
    'hahaha'

    def __init__(self, name, score):
        self.name = name
        self.score = score
        Exam.name = "hehehe"

print(Exam.__dict__)
>>>
{'__module__': '__main__', '__weakref__': <attribute '__weakref__' of 'Exam' objects>, '__dict__': <attribute '__dict__' of 'Exam' objects>, '__init__': <function Exam.__init__ at 0x10f056488>, '__doc__': 'hahaha'}

```



## `__doc__` 

`__doc__` 类的文档字符串

```
class Exam:
    'hahaha'

    def __init__(self, name, score):
        self.name = name
        self.score = score
        Exam.name = "hehehe"

print(Exam.__doc__)
>>>
hahaha
```



## `__name__`

`__name__`: 类名， 即对象的名称

查询对象所属的类和类名称

```
In [12]: a = [1, 2, 3]

In [13]: print(a.__class__)
<class 'list'>

In [15]: print(a.__class__.__name__)
list
```



## `__new__`

创建实例



## `__module__`

`__module__`:   类定义所在的模块（类的全名是'`__main__`.className'，如果类位于一个导入模块mymod中，那么className.`__module__` 等于 mymod）

```
class Exam:
    'hahaha'

    def __init__(self, name, score):
        self.name = name
        self.score = score
        Exam.name = "hehehe"

print(Exam.__module__)
>>>
__main__
```



## `__bases__`

`__bases__` : 类的所有父类构成元素（包含了一个由所有父类组成的元组）

```
class Exam:
    'hahaha'

    def __init__(self, name, score):
        self.name = name
        self.score = score
        Exam.name = "hehehe"

print(Exam.__bases__)
>>>
(<class 'object'>,)
```



## `__class__`

表示当前操作的对象的类是什么

```
class A:

    def __init__(self):
        self.name = 'Rick'


obj = A()
print(obj.__class__)
>>>
<class '__main__.A'>
```





## `__del__`

析构方法，当对象在内存中被释放时，自动触发执行。

通常用于收尾工作，如数据库链接打开的临时文件

```python
class MyString:
    def __init__(self):  #构造函数
        self.str = "MyString"
    def __del__(self):   #析构函数
        print("Bye")

    def output(self):
        print(self.str)

s = MyString()

s.output()
del s   # 删除对象
>>>
MyString
Bye
```

> 此方法一般无须定义，因为Python是一门高级语言，程序员在使用时无需关心内存的分配和释放，因为此工作都是交给Python解释器来执行，所以，析构函数的调用是由解释器在进行垃圾回收时自动触发执行的。



## `__call__`

对象后面加括号，触发执行。

```
class A:

    def __init__(self):
        pass

    def __call__(self, *args, **kwargs):
        print('__call__')

obj = A()
obj()
>>>
__call__
```

构造方法的执行是由创建对象触发的，即：对象 = 类名() ；而对于 `__call__` 方法的执行是由对象后加括号触发的，即：对象() 或者 类()()



## `__str__` 默认输出其返回值

如果一个类中定义了`__str__`方法，那么在打印 对象 时，默认输出该方法的返回值。

```
class A:

    def __str__(self):
        return 'Rick'

obj = A()
print(obj)
>>>
Rick
```



## `__getitem__、__setitem__、__delitem__`

用于索引操作，如字典。以上分别表示获取、设置、删除数据

```
class A:

    def __getitem__(self, item):
        print(item)

    def __setitem__(self, key, value):
        print(key, value)

    def __delitem__(self, key):
        print(key)

obj = A()

ret = obj['key1'] # 自动触发执行 __getitem__
obj['key2'] = 'Value2' # 自动触发执行 __getitem__
del obj['key1'] # 自动触发执行 __getitem__
>>>
key1
key2 Value2
key1
```



## `__iter__`

用于迭代器，之所以列表、字典、元组可以进行for循环，是因为类型内部定义了 `__iter__`

```
class A:

    def __init__(self, sq):
        self.sq = sq

    def __iter__(self):
        return iter(self.sq)

obj = A([1, 2, 3, 4])

for i in obj:
    print(i)
    
>>>
1
2
3
4
```





## 查询对象的属性

除了使用dir()来查询对象属性，还可以使用built-in函数来确认

```
# attr_name是一个字符串
hasattr(obj, attr_name) 

In [10]: a = [1, 2, 3]

In [11]: print(hasattr(a, 'append'))
True
```











# 实例化

`__init__` 只是初始化类变量的作用

```python
class A:
    def __init__(self, x):
        self.x = x

# 实例化
a = A(5)

# 绑定实力变量
print(a.x)
>>>
5
```



# 实例化类原理

```
class A:
    def __new__(cls, *args, **kwargs):
        print("call __new__")
        print("type cls", type(cls))
        return object.__new__(cls)

    def __init__(self, x):
        print("call __init__")
        print("type self", type(self))
        s1 = set (dir(self))
        self.x = x
        s2 = set(dir(self))
        print(s2 - s1)


a = A(5)
>>>
call __new__
type cls <class 'type'>
call __init__
type self <class '__main__.A'>
{'x'}

```

> cls 代表class A这个对象
>
> dir 会返回所有属性



若new方法不返回，init方法不执行

```
class A:
    def __new__(cls, *args, **kwargs):
        print("call __new__")
        print("type cls", type(cls))
        # return object.__new__(cls)

    def __init__(self, x):
        print("call __init__")
        print("type self", type(self))
        s1 = set (dir(self))
        self.x = x
        s2 = set(dir(self))
        print(s2 - s1)


a = A(5)
>>>
call __new__
type cls <class 'type'>

```





# 封装

封装只能在类的内部访问

```python
class A:
    def __init__(self, x):
        self.__value = x

    def __add(self):
        self.__value += i

    def get_value(self):
        return self.__value

    def __increase(self):
        self.__add(1)

a = A(5)
print(a.get_value())
print(a.__value)
>>>
5
Traceback (most recent call last):
  File "/Users/xhxu/python/python3/test/3.py", line 17, in <module>
    print(a.__value)
AttributeError: 'A' object has no attribute '__value'
```



## 数据及行为封装

```python
class Door():
    def __init__(self, number, status):
        self.number = number
        self.status = status
        
    def open(self):
        self.status = 'opening'
        
    def close(self):
        self.status = 'closed'
        
door1 = Door(1, 'closed')
```



```
from time import sleep

class Clock(object):
    """My Clock"""

    def __init__(self, hour=0, minute=0, second=0):
        """Initial method
        :param hour: hour:
        :param minute: minute
        :param second: second
        """
        self._hour = hour
        self._minute = minute
        self._second = second

    def run(self):
        """Working on Clock
        """
        self._second += 1
        if self._second == 60:
            self._second = 0
            self.minute += 1
            if self._minute == 60:
                self._minute = 0
                self._hour += 1
                if self._hour == 24:
                    self._hour = 0
    
    def show(self):
        """Display time
        """
        return '%02d:%02d:%02d' % \
            (self._hour, self._minute, self._second)

def main():
    clock = Clock(23, 59, 48)
    while True:
        print(clock.show())
        sleep(1)
        clock.run()


if __name__ == "__main__":
    main()    
```



# type 类

类 是由 type 类实例化产生

type 有解释器封装生成

```
def func(self):
    print 'hello wupeiqi'
  
Foo = type('Foo',(object,), {'func': func})
#type第一个参数：类名
#type第二个参数：当前类的基类
#type第三个参数：类的成员
```

```
def func(self):
    print("hello %s"%self.name)

def __init__(self,name,age):
    self.name = name
    self.age = age
Foo = type('Foo',(object,),{'func':func,'__init__':__init__})

f = Foo("jack",22)
f.func()
```





type类中实现的创建类

类中有一个属性 __metaclass__，其用来表示该类由 谁 来实例化创建，所以，我们可以为 __metaclass__ 设置一个type类的派生类，从而查看 类 创建的过程。

![img](https://cdn.pbrd.co/images/HCW5oNi.png)







# 元类

类的生成 调用 顺序依次是 `__new__` --> `__init__` --> `__call__`

```
class MyType(type):
    def __init__(self,*args,**kwargs):

        print("Mytype __init__",*args,**kwargs)

    def __call__(self, *args, **kwargs):
        print("Mytype __call__", *args, **kwargs)
        obj = self.__new__(self)
        print("obj ",obj,*args, **kwargs)
        print(self)
        self.__init__(obj,*args, **kwargs)
        return obj

    def __new__(cls, *args, **kwargs):
        print("Mytype __new__",*args,**kwargs)
        return type.__new__(cls, *args, **kwargs)

print('here...')
class Foo(object,metaclass=MyType):


    def __init__(self,name):
        self.name = name

        print("Foo __init__")

    def __new__(cls, *args, **kwargs):
        print("Foo __new__",cls, *args, **kwargs)
        return object.__new__(cls)

f = Foo("Alex")
print("f",f)
print("fname",f.name)

自定义元类
```







# instance() 函数判断对象类型

使用instance()函数检测一个给定的对象是否属于（继承）某个类或类型，是为True，否为False

```python
class MyClass:
    val1 = "String1"   #静态变量
    def __init__(self):
        self.val2 = "Value 2"

c = MyClass()
print(isinstance(c, MyClass))
l = [1, 2, 3, 4]
print(isinstance(l, list))
>>> 
True
True
```

