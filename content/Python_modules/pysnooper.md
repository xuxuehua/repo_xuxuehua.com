---
title: "pysnooper debug工具"
date: 2020-08-23 09:42
---
[toc]



# Pysnooper

无需为了查看变量的值，使用print打印变量的值，从而修改了原有的代码。

接口的运行过程以日志的形式保存，方便随时查看。

可以根据需要，设置函数调用的函数的层数，方便将注意力集中在需要重点关注的代码段。

多个函数的日志，可以设置日志前缀表示进行标识，方便查看时过滤。



## hello world

```
import pysnooper


@pysnooper.snoop()
def number_to_bits(number):
    if number:
        bits = []
        while number:
            number, remainder = divmod(number, 2)
            bits.insert(0, remainder)
        return bits
    else:
        return [0]


if __name__ == '__main__':
    number_to_bits(6)

>>>
Source path:... /Users/rxu/test_purpose/socket_server.py
Starting var:.. number = 6
09:41:44.247860 call         5 def number_to_bits(number):
09:41:44.248175 line         6     if number:
09:41:44.248219 line         7         bits = []
New var:....... bits = []
09:41:44.248251 line         8         while number:
09:41:44.248289 line         9             number, remainder = divmod(number, 2)
Modified var:.. number = 3
New var:....... remainder = 0
09:41:44.248319 line        10             bits.insert(0, remainder)
Modified var:.. bits = [0]
09:41:44.248364 line         8         while number:
09:41:44.248401 line         9             number, remainder = divmod(number, 2)
Modified var:.. number = 1
Modified var:.. remainder = 1
09:41:44.248432 line        10             bits.insert(0, remainder)
Modified var:.. bits = [1, 0]
09:41:44.248473 line         8         while number:
09:41:44.248509 line         9             number, remainder = divmod(number, 2)
Modified var:.. number = 0
09:41:44.248539 line        10             bits.insert(0, remainder)
Modified var:.. bits = [1, 1, 0]
09:41:44.248575 line         8         while number:
09:41:44.248611 line        11         return bits
09:41:44.248641 return      11         return bits
Return value:.. [1, 1, 0]
Elapsed time: 00:00:00.000851

Process finished with exit code 0

```



## snoop

以装饰器的形式使用该工具，其包含了四个参数，参数包括output, variables, depth, prefix，



### output

output参数。该参数指定函数运行过程中产生的中间结果的保存位置，若该值为空，则将中间结果输出到控制台。

若使用默认参数，则将中间结果输出到控制台，若填写该参数，则将中间结果写入到该参数指定的目录下，如运行以下代码，其中间结果会保存在装饰器snoop中设置日志保存的路径中，注意这里不会自动创建目录，所以需要事先创建目录，如测试代码中填写路径后需要创建log目录。

```
import pysnooper


def add(num1, num2):
    return num1 + num2


@pysnooper.snoop("./log/debug.log", prefix="--*--")
def multiplication(num1, num2):
    sum_value = 0
    for i in range(0, num1):
        sum_value = add(sum_value, num2)
    return sum_value


value = multiplication(3, 4)
```



文件中记录了各行代码的执行过程及局部变量的变化。在debug时，通过分析该文件，就可以跟踪每一步的执行过程及局部变量的变化，这样就能快速的定位问题所在；由于运行的中间结果保存在文件中，方便随时分析其运行的中间结果，也便于共享。

./log/debug.log

```
--*--Source path:... /Users/rxu/test_purpose/socket_server.py
--*--Starting var:.. num1 = 3
--*--Starting var:.. num2 = 4
--*--09:48:26.618534 call         9 def multiplication(num1, num2):
--*--09:48:26.619492 line        10     sum_value = 0
--*--New var:....... sum_value = 0
--*--09:48:26.619742 line        11     for i in range(0, num1):
--*--New var:....... i = 0
--*--09:48:26.620071 line        12         sum_value = add(sum_value, num2)
--*--Modified var:.. sum_value = 4
--*--09:48:26.620392 line        11     for i in range(0, num1):
--*--Modified var:.. i = 1
--*--09:48:26.620740 line        12         sum_value = add(sum_value, num2)
--*--Modified var:.. sum_value = 8
--*--09:48:26.621197 line        11     for i in range(0, num1):
--*--Modified var:.. i = 2
--*--09:48:26.621614 line        12         sum_value = add(sum_value, num2)
--*--Modified var:.. sum_value = 12
--*--09:48:26.622118 line        11     for i in range(0, num1):
--*--09:48:26.622511 line        13     return sum_value
--*--09:48:26.622681 return      13     return sum_value
--*--Return value:.. 12
--*--Elapsed time: 00:00:00.004546
```



### watch

watch参数。该参数是vector类型, 因为在默认情况下，装饰器只跟踪局部变量，要跟踪非局部变量，则可以通过该字段来指定。默认值为空vector。

在默认参数的情况下，使用该工具只能查看局变量的变化过程，当需要查看局部变量以外变量时，则可以通过watch参数进行设置，比如下方代码，在Foo类型，需要查看类实例的变量self.num1, self.num2, self.sum_value,则可以看将该变量设置当参数传入snoop的装饰器中。



```
import pysnooper


class Foo(object):
    def __init__(self):
        self.num1 = 0
        self.num2 = 0
        self.sum_value = 0

    def add(self, num1, num2):
        return num1 + num2

    @pysnooper.snoop(output="./log/debug.log", watch=("self.num1", "self.num2", "self.sum_value"))
    def multiplication(self, num1, num2):
        self.num1 = num1
        self.num2 = num2
        sum_value = 0
        for i in range(0, num1):
            sum_value = self.add(sum_value, num2)
        self.sum_value = sum_value
        return sum_value


foo = Foo()
foo.multiplication(3, 4)
```



```
Source path:... /Users/rxu/test_purpose/socket_server.py
Starting var:.. self = <__main__.Foo object at 0x1045efd00>
Starting var:.. num1 = 3
Starting var:.. num2 = 4
Starting var:.. self.num1 = 0
Starting var:.. self.num2 = 0
Starting var:.. self.sum_value = 0
09:53:22.096165 call        14     def multiplication(self, num1, num2):
09:53:22.097750 line        15         self.num1 = num1
Modified var:.. self.num1 = 3
09:53:22.097941 line        16         self.num2 = num2
Modified var:.. self.num2 = 4
09:53:22.098229 line        17         sum_value = 0
New var:....... sum_value = 0
09:53:22.098537 line        18         for i in range(0, num1):
New var:....... i = 0
09:53:22.098807 line        19             sum_value = self.add(sum_value, num2)
Modified var:.. sum_value = 4
09:53:22.099083 line        18         for i in range(0, num1):
Modified var:.. i = 1
09:53:22.099344 line        19             sum_value = self.add(sum_value, num2)
Modified var:.. sum_value = 8
09:53:22.099600 line        18         for i in range(0, num1):
Modified var:.. i = 2
09:53:22.099868 line        19             sum_value = self.add(sum_value, num2)
Modified var:.. sum_value = 12
09:53:22.100130 line        18         for i in range(0, num1):
09:53:22.100395 line        20         self.sum_value = sum_value
Modified var:.. self.sum_value = 12
09:53:22.100556 line        21         return sum_value
09:53:22.100815 return      21         return sum_value
Return value:.. 12
Elapsed time: 00:00:00.004948
```





### depth

depth参数。该参数表示需要追踪的函数调用的深度。在很多时候，我们在函数中会调用其他函数，通过该参数就可以指定跟踪调用函数的深度。默认值为1。



```
import pysnooper


def add(num1, num2):
    return num1 + num2


@pysnooper.snoop("./log/debug.log", depth=2)
def multiplication(num1, num2):
    sum_value = 0
    for i in range(0, num1):
        sum_value = add(sum_value, num2)
    return sum_value


value = multiplication(3, 4)
```



```
Source path:... /Users/rxu/test_purpose/socket_server.py
Starting var:.. num1 = 3
Starting var:.. num2 = 4
09:55:35.132415 call         9 def multiplication(num1, num2):
09:55:35.134114 line        10     sum_value = 0
New var:....... sum_value = 0
09:55:35.134277 line        11     for i in range(0, num1):
New var:....... i = 0
09:55:35.134520 line        12         sum_value = add(sum_value, num2)
    Starting var:.. num1 = 0
    Starting var:.. num2 = 4
    09:55:35.134764 call         4 def add(num1, num2):
    09:55:35.135171 line         5     return num1 + num2
    09:55:35.135336 return       5     return num1 + num2
    Return value:.. 4
Modified var:.. sum_value = 4
09:55:35.135588 line        11     for i in range(0, num1):
Modified var:.. i = 1
09:55:35.135863 line        12         sum_value = add(sum_value, num2)
    Starting var:.. num1 = 4
    Starting var:.. num2 = 4
    09:55:35.136135 call         4 def add(num1, num2):
    09:55:35.136544 line         5     return num1 + num2
    09:55:35.136693 return       5     return num1 + num2
    Return value:.. 8
Modified var:.. sum_value = 8
09:55:35.136932 line        11     for i in range(0, num1):
Modified var:.. i = 2
09:55:35.137195 line        12         sum_value = add(sum_value, num2)
    Starting var:.. num1 = 8
    Starting var:.. num2 = 4
    09:55:35.137497 call         4 def add(num1, num2):
    09:55:35.137834 line         5     return num1 + num2
    09:55:35.137974 return       5     return num1 + num2
    Return value:.. 12
Modified var:.. sum_value = 12
09:55:35.138204 line        11     for i in range(0, num1):
09:55:35.138479 line        13     return sum_value
09:55:35.138671 return      13     return sum_value
Return value:.. 12
Elapsed time: 00:00:00.006555
```





### prefix

prefix参数。该参数用于指定该函数接口的中间结果前缀。当多个函数都使用的该装饰器后，会将这些函数调用的中间结果保存到一个文件中，此时就可以通过前缀过滤不同函数调用的中间结果。默认值为空字符串。

```
import pysnooper


def add(num1, num2):
    return num1 + num2


@pysnooper.snoop("./log/debug.log", prefix="--*--")
def multiplication(num1, num2):
    sum_value = 0
    for i in range(0, num1):
        sum_value = add(sum_value, num2)
    return sum_value


value = multiplication(3, 4)
```



```
--*--Source path:... /Users/rxu/test_purpose/socket_server.py
--*--Starting var:.. num1 = 3
--*--Starting var:.. num2 = 4
--*--09:48:26.618534 call         9 def multiplication(num1, num2):
--*--09:48:26.619492 line        10     sum_value = 0
--*--New var:....... sum_value = 0
--*--09:48:26.619742 line        11     for i in range(0, num1):
--*--New var:....... i = 0
--*--09:48:26.620071 line        12         sum_value = add(sum_value, num2)
--*--Modified var:.. sum_value = 4
--*--09:48:26.620392 line        11     for i in range(0, num1):
--*--Modified var:.. i = 1
--*--09:48:26.620740 line        12         sum_value = add(sum_value, num2)
--*--Modified var:.. sum_value = 8
--*--09:48:26.621197 line        11     for i in range(0, num1):
--*--Modified var:.. i = 2
--*--09:48:26.621614 line        12         sum_value = add(sum_value, num2)
--*--Modified var:.. sum_value = 12
--*--09:48:26.622118 line        11     for i in range(0, num1):
--*--09:48:26.622511 line        13     return sum_value
--*--09:48:26.622681 return      13     return sum_value
--*--Return value:.. 12
--*--Elapsed time: 00:00:00.004546
```





