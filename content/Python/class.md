---
title: "class 类"
date: 2018-09-03 01:33
collection: 面向对象
---

[TOC]

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





## 方法定义 

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



















## 属性 

### 实例变量

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





### 私有变量

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





### 类变量

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



### 私有类变量

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



### 内置特殊方法

#### `__init__`

构造方法, 通过类创建对象时，自动触发执行

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

## 



#### `__dict__`

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



#### `__doc__` 

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



#### `__name__`

`__name__`: 类名， 即对象的名称

查询对象所属的类和类名称

```
In [12]: a = [1, 2, 3]

In [13]: print(a.__class__)
<class 'list'>

In [15]: print(a.__class__.__name__)
list
```



#### `__new__`

创建实例



#### `__module__`

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



#### `__bases__`

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



#### `__class__`

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





#### `__del__`

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



#### `__call__`

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



#### `__str__`

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



#### `__getitem__、__setitem__、__delitem__`

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



#### `__iter__`

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





### 查询对象的属性

除了使用dir()来查询对象属性，还可以使用built-in函数来确认

```
# attr_name是一个字符串
hasattr(obj, attr_name) 

In [10]: a = [1, 2, 3]

In [11]: print(hasattr(a, 'append'))
True
```





## 类的装饰器

### staticmethod 静态方法

静态方法并不能访问私有变量，只是给类方法加了类的属性

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

静态方法只属于定义他的类，而不属于任何一个具体的对象

即不能传self，相当于单纯的一个函数，

静态方法无需传入self参数，因此在静态方法中无法访问实例变量

静态方法不能直接访问类的静态变量，但可以通过类名引用静态变量

与成员方法的区别是没有 self 参数，并且可以在类不进行实例化的情况下调用。



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



### classmethod 类方法

至少一个cls参数；执行类方法时，自动将调用该方法的类复制给cls, cls指代调用者即类对象自身

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



### property 属性

加上property 装饰器，在函数调用的时候，就不需要加上小括号

property调用的时候，只能调用self参数

将一个类方法转变成一个静态属性,只读属性。

类属性可以直接被类调用，如果想要实现能被类直接调用的方法就可以借助staticmethod和classmethod了，区别在于staticmethod的方法没有self参数，通常用来直接定一个静态类方法，如果想将一个普通动态方法变成类方法就要使用classmethod了。

property 属性必须再setter 和 deleter之前

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





#### setter 装饰器

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



#### deleter 装饰器 

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





#### property 参数

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



#### property 实现的原理

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









## 实例化

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



### 实例化类原理

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





## 封装

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



### 数据及行为封装

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





## type 类

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







## 元类

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







## instance() 函数判断对象类型

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

