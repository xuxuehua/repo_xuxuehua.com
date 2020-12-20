---
title: "error"
date: 2018-08-30 15:34
ollection: 异常处理
---

[TOC]

# 异常处理

异常处理一般用于debug程序,以不终止程序，让其照样运行做操作

对于flow-control(流程控制)的代码逻辑，我们一般不用异常处理，直接用条件语句解决就可以了

## 语法

在try语句块中放入容易犯错的部分，跟上except，说明语句在发生错误的时候会执行的返回值

如果抛出异常，会被程序捕获（catch），程序还会继续运行

```
re = iter(range(5))

try:
    for i in range(100):
        print(re.__next__())
except StopIteration:
    print('End at here', i)

print('hahaha')
>>>
0
1
2
3
4
End at here 5
hahaha
```

当程序中存在多个except block时，最多只有一个except block会被执行。换句话说，如果多个
except声明的异常类型都与实际相匹配，那么只有最前面的except block会被执行，其他则被忽略。

```
try:
    s = input('please enter two numbers separated by comma: ')
    num1 = int(s.split(',')[0].strip())
    num2 = int(s.split(',')[1].strip())
except (ValueError, IndexError) as err:
    print('Error: {}'.format(err))
print('continue')
```

或者

```
try:
    s = input('please enter two numbers separated by comma: ')
    num1 = int(s.split(',')[0].strip())
    num2 = int(s.split(',')[1].strip())

except ValueError as err:
    print('Value Error: {}'.format(err))
except IndexError as err:
    print('Index Error: {}'.format(err))
print('continue')
```



```
In [3]: try:
   ...:     1/0
   ...: except Exception as e:
   ...:     print(e)
   ...: else:
   ...:     print('else section')
   ...: 
division by zero

In [4]: try:
   ...:     1/1
   ...: except Exception as e:
   ...:     print(e)
   ...: else:
   ...:     print('else section')
   ...: 
else section
```





## 异常类型

[https://docs.python.org/3/library/exceptions.html#bltin-exceptions](https://docs.python.org/3/library/exceptions.html#bltin-exceptions)

| 异常名称                      | 描述                                |
| ------------------------- | --------------------------------- |
|                           |                                   |
| BaseException             | 所有异常的基类                           |
| SystemExit                | 解释器请求退出                           |
| KeyboardInterrupt         | 用户中断执行(通常是输入^C)                   |
| Exception                 | 常规错误的基类                           |
| StopIteration             | 迭代器没有更多的值                         |
| GeneratorExit             | 生成器(generator)发生异常来通知退出           |
| StandardError             | 所有的内建标准异常的基类                      |
| ArithmeticError           | 所有数值计算错误的基类                       |
| FloatingPointError        | 浮点计算错误                            |
| OverflowError             | 数值运算超出最大限制                        |
| ZeroDivisionError         | 除(或取模)零 (所有数据类型)                  |
| AssertionError            | 断言语句失败                            |
| AttributeError            | 对象没有这个属性                          |
| EOFError                  | 没有内建输入,到达EOF 标记                   |
| EnvironmentError          | 操作系统错误的基类                         |
| IOError                   | 输入/输出操作失败                         |
| OSError                   | 操作系统错误                            |
| WindowsError              | 系统调用失败                            |
| ImportError               | 导入模块/对象失败                         |
| LookupError               | 无效数据查询的基类                         |
| IndexError                | 序列中没有此索引(index)                   |
| KeyError                  | 映射中没有这个键                          |
| MemoryError               | 内存溢出错误(对于Python 解释器不是致命的)         |
| NameError                 | 未声明/初始化对象 (没有属性)                  |
| UnboundLocalError         | 访问未初始化的本地变量                       |
| ReferenceError            | 弱引用(Weak reference)试图访问已经垃圾回收了的对象 |
| RuntimeError              | 一般的运行时错误                          |
| NotImplementedError       | 尚未实现的方法                           |
| SyntaxError               | Python 语法错误                       |
| IndentationError          | 缩进错误                              |
| TabError                  | Tab 和空格混用                         |
| SystemError               | 一般的解释器系统错误                        |
| TypeError                 | 对类型无效的操作                          |
| ValueError                | 传入无效的参数                           |
| UnicodeError              | Unicode 相关的错误                     |
| UnicodeDecodeError        | Unicode 解码时的错误                    |
| UnicodeEncodeError        | Unicode 编码时错误                     |
| UnicodeTranslateError     | Unicode 转换时错误                     |
| Warning                   | 警告的基类                             |
| DeprecationWarning        | 关于被弃用的特征的警告                       |
| FutureWarning             | 关于构造将来语义会有改变的警告                   |
| OverflowWarning           | 旧的关于自动提升为长整型(long)的警告             |
| PendingDeprecationWarning | 关于特性将会被废弃的警告                      |
| RuntimeWarning            | 可疑的运行时行为(runtime behavior)的警告     |
| SyntaxWarning             | 可疑的语法的警告                          |
| UserWarning               | 用户代码生成的警告                         |

### BaseException

The base class for all built-in exceptions. It is not meant to be directly inherited by user-defined classes (for that, use [`Exception`](https://docs.python.org/3/library/exceptions.html#Exception)). If [`str()`](https://docs.python.org/3/library/stdtypes.html#str) is called on an instance of this class, the representation of the argument(s) to the instance are returned, or the empty string when there were no arguments.

except后面省略异常类型，这表示与任意异常相匹配(包括系统异常等)

```
try:
    s = input('please enter two numbers separated by comma: ')
    num1 = int(s.split(',')[0].strip())
    num2 = int(s.split(',')[1].strip())

except ValueError as err:
    print('Value Error: {}'.format(err))
except IndexError as err:
    print('Index Error: {}'.format(err))
except:
    print('Other error')
print('continue')
```

### Exception

All built-in, non-system-exiting exceptions are derived from this class. All user-defined exceptions should also be derived from this class.

Exception是其他所有非系统异常的基类，能够匹配任意非系统异常， 常用于except最后一个异常处理

```python
try:
    s = input('please enter two numbers separated by comma: ')
    num1 = int(s.split(',')[0].strip())
    num2 = int(s.split(',')[1].strip())

except ValueError as err:                   
     print('Value Error: {}'.format(err))
except IndexError as err:
    print('Index Error: {}'.format(err))
except Exception as err:
    print('Other error: {}'.format(err))
print('continue')
```

## 异常处理顺序

无法将异常交给合适的对象，异常将继续向上层抛出，直到捕捉或者造成主程序出错

```
def test_func():
    try:
        m = 1/0
    except NameError:
        print('Catch NameError in the sub-function')

try:
    test_func()
except ZeroDivisionError:
    print('Catch error in the main program')
>>>
Catch error in the main program
```

## finally

finally是无论是否有异常，最后都要做的一件事

无论发生什么情况，
finally block中的语句都会被执行，哪怕前面的try和excep block中使用了return语句

1. try -> 异常 -> except -> finally
2. try -> 无异常 -> else -> finally

可用于文件读取， 但with open可以最后自动关闭文件

```
import sys
try:
    f = open('file.txt', 'r')
    .... # some data processing
except OSError as err:
    print('OS error: {}'.format(err))
except:
    print('Unexpected error:', sys.exc_info()[0])
finally:
    f.close()
```



### 注意点

不要在finally内部抛出异常，否则try中的异常会被覆盖

不要在finally 中return，否则try except中的return会被覆盖





## raise 抛出异常

raise 语句可以抛出异常,一旦抛出异常，那么程序就会终止

```
print('test')
raise StopIteration
print('yes')
>>>
Traceback (most recent call last):
  File "/Users/xhxu/python/machine_learning/test.py", line 2, in <module>
    raise StopIteration
StopIteration
test
```



## 自定义异常

```
class MyInputError(Exception):
        """Exception raised when there're errors in input""" 
        def __init__(self, value): # 自定义异常类型的初始化
                self.value = value
        def __str__(self): # 自定义异常类型的string表达形式
            return "{} is invalid input".format(repr(self.value))

try:
    raise MyInputError(1) # 抛出MyInputError这个异常
except MyInputError as err:
    print('error: {}'.format(err))
```



## raise  (re-raise exception)

```

In [6]: try: 
   ...:     1/0 
   ...: except Exception as e: 
   ...:     if not e: 
   ...:         print('OK') 
   ...:     else: 
   ...:         raise 
   ...:  
   ...:          
   ...:                                                                                                                                                   ---------------------------------------------------------------------------
ZeroDivisionError                         Traceback (most recent call last)
<ipython-input-6-b7d9bfb2c27b> in <module>
      1 try:
----> 2     1/0
      3 except Exception as e:
      4     if not e:
      5         print('OK')

ZeroDivisionError: division by zero
```



## 关闭异常自动关联上下文

使用 raise...from None

```
>>> try: 
...     print(1 / 0) 
... except Exception as exc: 
...     raise RuntimeError("Something bad happened") from None 
... 
Traceback (most recent call last):
  File "<stdin>", line 4, in <module>
RuntimeError: Something bad happened
```



```
>>> try: 
...     print(1 / 0) 
... except Exception as exc: 
...     raise RuntimeError("Something bad happened") 
... 
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
ZeroDivisionError: division by zero

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "<stdin>", line 4, in <module>
RuntimeError: Something bad happened
```



# assert

Python 的 assert 语句，可以说是一个 debug 的好工具，主要用于测试一个条件是否满足。如果测试的条件满足，则什么也不做，相当于执行了 pass 语句；如果测试条件不满足，便会抛出异常 AssertionError，并返回具体的错误信息（optional）。

assert 并不适用 run-time error 的检查。比如你试图打开一个文件，但文件不存在；再或者是你试图从网上下载一个东西，但中途断网了了等等，这些情况下，还是应该使用错误异常，进行正确处理。

使用 assert 时，一定不要加上括号，否则无论表达式对与错，assert 检查永远不会 fail。

## 单行

这里的`__debug__`是一个常数。如果 Python 程序执行时附带了`-O`这个选项，比如`Python test.py -O`，那么程序中所有的 assert 语句都会失效，常数`__debug__`便为 False；反之`__debug__`则为 True。

```
assert 1 == 2
```

它就相当于下面这两行代码：

```
if __debug__:
    if not expression: raise AssertionError
```

## 多行

```
assert 1 == 2,  'assertion is wrong'
```

它就相当于下面这两行代码：

```
if __debug__:
    if not expression1: raise AssertionError(expression2)
```







# 错误处理





## 减少使用Exception

catch异常的时候，你不应该使用Exception这么宽泛的类型。你应该正好catch可能发生的那种异常A。

使用宽泛的异常类型有很大的问题，因为它会不经意的catch住另外的异常（比如B）。你的代码逻辑是基于判断A是否出现，可你却catch所有的异常（Exception类），所以当其它的异常B出现的时候，你的代码就会出现莫名其妙的问题，因为你以为A出现了，而其实它没有。

这种bug，有时候甚至使用debugger都难以发现。





## 精确catch

try { … } catch里面，应该包含尽量少的代码。比如，如果`foo`和`bar`都可能产生异常A，你的代码应该尽可能写成：

```
try {
  foo();
} catch (A e) {...}

try {
  bar();
} catch (A e) {...}
```

而不是

```
try {
  foo();
  bar();
} catch (A e) {...}
```

第一种写法能明确的分辨是哪一个函数出了问题，而第二种写法全都混在一起。明确的分辨是哪一个函数出了问题，有很多的好处。比如，如果你的catch代码里面包含log，它可以提供给你更加精确的错误信息，这样会大大地加速你的调试过程。



# FAQ



## Too broad exception clause

1. 关闭编译器中代码检测中有关检测 Exception 的选项
2. 在 try 语句前加入 # noinspection PyBroadException

```python
# noinspection PyBroadException
try:
    literal_value = (
        self.spark_sql(select_sql).collect()[0][variable]
    )
except Exception:
    pass
```



## xxx is too complex

flake8 offers an extra option: –max-complexity, which will emit a warning if the McCabe complexity of a function is higher than the value. By default it’s deactivated:

```
$ flake8 --max-complexity 12 coolproject
coolproject/mod.py:97:1: F401 'shutil' imported but unused
coolproject/mod.py:625:17: E225 missing whitespace around operator
coolproject/mod.py:729:1: F811 redefinition of unused 'readlines' from line 723
coolproject/mod.py:939:1: C901 'Checker.check_all' is too complex (12)
coolproject/mod.py:1028:1: F841 local variable 'errors' is assigned to but never used
coolproject/mod.py:1204:1: C901 'selftest' is too complex (14)
```

This feature is quite useful to detect over-complex code. According to McCabe, anything that goes beyond 10 is too complex

See https://en.wikipedia.org/wiki/Cyclomatic_complexity





For disable temporarily

```
# flake8: noqa: C901
def somefn(...): ...
```

