---
title: "error"
date: 2018-08-30 15:34
ollection: 异常处理
---

[TOC]



# 异常处理



异常处理一般用于debug程序



## 语法

在try语句块中放入容易犯错的部分，跟上except，说明语句在发生错误的时候会执行的返回值

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



## 异常类型

Exception 能抓住所有错误，不建议使用



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
1. try -> 异常 -> except -> finally
2. try -> 无异常 -> else -> finally



## raise 抛出异常

raise 语句可以抛出异常

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



### 异常种类

| 异常名称                  | 描述                                               |
| ------------------------- | -------------------------------------------------- |
|                           |                                                    |
| BaseException             | 所有异常的基类                                     |
| SystemExit                | 解释器请求退出                                     |
| KeyboardInterrupt         | 用户中断执行(通常是输入^C)                         |
| Exception                 | 常规错误的基类                                     |
| StopIteration             | 迭代器没有更多的值                                 |
| GeneratorExit             | 生成器(generator)发生异常来通知退出                |
| StandardError             | 所有的内建标准异常的基类                           |
| ArithmeticError           | 所有数值计算错误的基类                             |
| FloatingPointError        | 浮点计算错误                                       |
| OverflowError             | 数值运算超出最大限制                               |
| ZeroDivisionError         | 除(或取模)零 (所有数据类型)                        |
| AssertionError            | 断言语句失败                                       |
| AttributeError            | 对象没有这个属性                                   |
| EOFError                  | 没有内建输入,到达EOF 标记                          |
| EnvironmentError          | 操作系统错误的基类                                 |
| IOError                   | 输入/输出操作失败                                  |
| OSError                   | 操作系统错误                                       |
| WindowsError              | 系统调用失败                                       |
| ImportError               | 导入模块/对象失败                                  |
| LookupError               | 无效数据查询的基类                                 |
| IndexError                | 序列中没有此索引(index)                            |
| KeyError                  | 映射中没有这个键                                   |
| MemoryError               | 内存溢出错误(对于Python 解释器不是致命的)          |
| NameError                 | 未声明/初始化对象 (没有属性)                       |
| UnboundLocalError         | 访问未初始化的本地变量                             |
| ReferenceError            | 弱引用(Weak reference)试图访问已经垃圾回收了的对象 |
| RuntimeError              | 一般的运行时错误                                   |
| NotImplementedError       | 尚未实现的方法                                     |
| SyntaxError               | Python 语法错误                                    |
| IndentationError          | 缩进错误                                           |
| TabError                  | Tab 和空格混用                                     |
| SystemError               | 一般的解释器系统错误                               |
| TypeError                 | 对类型无效的操作                                   |
| ValueError                | 传入无效的参数                                     |
| UnicodeError              | Unicode 相关的错误                                 |
| UnicodeDecodeError        | Unicode 解码时的错误                               |
| UnicodeEncodeError        | Unicode 编码时错误                                 |
| UnicodeTranslateError     | Unicode 转换时错误                                 |
| Warning                   | 警告的基类                                         |
| DeprecationWarning        | 关于被弃用的特征的警告                             |
| FutureWarning             | 关于构造将来语义会有改变的警告                     |
| OverflowWarning           | 旧的关于自动提升为长整型(long)的警告               |
| PendingDeprecationWarning | 关于特性将会被废弃的警告                           |
| RuntimeWarning            | 可疑的运行时行为(runtime behavior)的警告           |
| SyntaxWarning             | 可疑的语法的警告                                   |
| UserWarning               | 用户代码生成的警告                                 |



## 自定义异常

```
class MyError(Exception):
    
    def __init__(self, msg):
        self.message = msg
        
try:
    raise MyError('This is my error.')
except MyError as e:
    print(e)
```

