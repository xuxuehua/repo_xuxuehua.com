---
title: "file_operation 文件操作"
date: 2018-08-19 11:30
---

[TOC]



# 文件操作

## 标准模式

### r 只读打开

只读模式（默认） 文件必须存在

```

```



### w 只写打开

只写文件，若文件存在则文件长度清为0，即该文件内容会消失。若文件不存在则建立该文件。

```

```



### x 创建写入

创建并写入一个新文件， 文件存在会报异常



### a 追加

以附加的方式打开只写文件。

若文件不存在，则会建立该文件，并在尾部追加

如果文件存在，写入的数据会被加到文件尾，即文件原先的内容会被保留。

```

```



### b 二进制模式

字节流，将文件就按照字节理解，与字符编码无关，二进制模式操作时，字节操作用bytes类型



### t 文本

字符流，将文件的字节按照某种字符编码理解，按照字符操作



## 模式

### r+ 可读写

打开可读写的文件，该文件必须存在

```

```





### w+ 清零读写

可读写文件，若文件存在则文件长度清为零，即该文件内容会消失。若文件不存在则建立该文件。

```

```





### a+ 

以附加方式打开可读写的文件。若文件不存在，则会建立该文件，如果文件存在，写入的数据会被加到文件尾后，即文件原先的内容会被保留。

```

```





##  U模式

"U"表示在读取时，可以将 \r \n \r\n自动转换成 \n （与 r 或 r+ 模式同使用）

### rU

```

```



### r+U

```

```





## 二进制模式

表示处理二进制文件（如：FTP发送上传ISO镜像文件，linux可忽略，windows处理二进制文件时需标注）

### rb

读取二进制文件

```python
with open('Rick.jpg', 'rb') as f:
    data = f.read()
    print(type(data))    
```



### wb

```
with open('Rick.jpg', 'wb') as e:
    e.write(data)
```



### ab

```

```







### tell()

打印光标位置

```

```





## 文件对象的方法



### open 打开

```
open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=True, opener=None)
```

#### buffering

-1表示缺省大小的buffer

如果是二进制模式，使用io.DEFAULT_BUFFER_SIZE值，默认为4096或者8192



#### encoding

仅文本模式使用

None表示缺省编码，Linux为UTF-8



#### errors

None和strict表示有编码错误，将抛出ValueError异常



#### newline

换行转换，\r, \n. \r\n

读时，None表示\r, \n, \r\n都被转换为\n，表示不会自动转换通用换行符，其他合法字符表示换行符就是指定字符，就会按照指定字符分行



写时，None表示\n都会被替换为缺省行分隔符os.linesep; 其他合法字符表示\n会被替换为指定的字符



#### closedf

关闭文件描述符，True表示关闭，False会在文件关闭后保持这个描述符



### readline 行读取

读取每行的内容

```

```



### readlines 多行读取

读取整个文件，生成列表

```

```



### read 读取

文件内容替换

```
for line in fileinput.input('filepath', inplace=1):
    line = line.replace('oldtext', 'newtext')
    print(line)
```

修改某行

```
with open('foo.txt', 'r++') as f:
    old = f.read() 
    f.seek(0)
    f.write('new line\n' + old)
line before
```



### write 写入

写入
```
f.write('I like apple!\n')      # 将'I like apple'写入文件并换行
```



### close 关闭

关闭前会调用flush()
```
f.close()  #关闭文件
```





## 查找



### seek 文件指针

移动文件指针位置，offset偏移多少字节，whence从哪里开始

```
seek(offset[, whence])
```



### tell 指针位置



### flush 缓冲

刷新缓冲，即将缓冲区的数据写入磁盘

```
f = open('test.txt', 'w')
f.write('hello1\n')
f.flush()
f.write('hello2\n')
```



实时生成进度条 

```
import sys, time

for i in range(20):
    sys.stdout.write('#');
    sys.stdout.flush()
    time.sleep(0.2)
```



# 上下文管理器





## with语句

为了避免打开文件后忘记关闭，可以通过管理上下文，即：

```
with open('log','r') as f:
	pass
```

如此方式，当with代码块执行完毕时，内部会自动关闭并释放文件资源。

在Python 2.7 后，with又支持同时对多个文件的上下文进行管理，即：

```
with open('log1') as obj1, open('log2') as obj2:
	pass
```





### 实现原理

```
class A:

    def __enter__(self):
        a = 1
        return a

    def __exit__(self):
        b = 2


with A() as obj_a:
    pass
```

> 通过debug 模式，obj_a是返回`__enter__` 的返回值， 但是一旦执行了`__exit__` 结束了上下文管理





```
class MyResource:

    def __enter__(self):
        print('Connect to resource')
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        print('Disconnect to resource')

    def query(self):
        print('Query data')


with MyResource() as resource:
    resource.query()
```





### 单词统计

```
d = {}

with open('sample', encoding='utf-8') as f:
    for line in f:
        words = line.split()
        for word in map(str.lower, words):
            d[word] = d.get(word, 0) + 1

print(sorted(d.item(), key=lambda item: item[1], reverse=True))
```





## 基于类的上下文管理器

基于类的上下文管理器更加 flexible，适用于大型的系统开发

```
class FileManager:
    def __init__(self, name, mode):
        print('calling __init__ method')
        self.name = name
        self.mode = mode 
        self.file = None
        
    def __enter__(self):
        print('calling __enter__ method')
        self.file = open(self.name, self.mode)
        return self.file
 
 
    def __exit__(self, exc_type, exc_val, exc_tb):
        print('calling __exit__ method')
        if self.file:
            self.file.close()
            
with FileManager('test.txt', 'w') as f:
    print('ready to write to file')
    f.write('hello world')
    
>>>
calling __init__ method
calling __enter__ method
ready to write to file
calling __exit__ method
```

> 用类来创建上下文管理器时，必须保证这个类包括方法`”__enter__()”`和方法`“__exit__()”`。其中，方法`“__enter__()”`返回需要被管理的资源，方法`“__exit__()”`里通常会存在一些释放、清理资源的操作，比如这个例子中的关闭文件等等
>
> 如果你需要处理可能发生的异常，可以在`“__exit__()”`添加相应的代码,可以自行定义相关的操作对异常进行处理，而处理完异常后，也别忘了加上`“return True”`这条语句，否则仍然会抛出异常



```
class Foo:
    def __init__(self):
        print('__init__ called')        
 
    def __enter__(self):
        print('__enter__ called')
        return self
    
    def __exit__(self, exc_type, exc_value, exc_tb):
        print('__exit__ called')
        if exc_type:
            print(f'exc_type: {exc_type}')
            print(f'exc_value: {exc_value}')
            print(f'exc_traceback: {exc_tb}')
            print('exception handled')
        return True
    
with Foo() as obj:
    raise Exception('exception raised').with_traceback(None)
 
>>>
__init__ called
__enter__ called
__exit__ called
exc_type: <class 'Exception'>
exc_value: exception raised
exc_traceback: <traceback object at 0x1046036c8>
exception handled
```



### 数据库的连接操作

```
class DBConnectionManager: 
    def __init__(self, hostname, port): 
        self.hostname = hostname 
        self.port = port 
        self.connection = None
  
    def __enter__(self): 
        self.connection = DBClient(self.hostname, self.port) 
        return self
  
    def __exit__(self, exc_type, exc_val, exc_tb): 
        self.connection.close() 
  
with DBConnectionManager('localhost', '8080') as db_client: 
```

> 方法`“__init__()”`负责对数据库进行初始化，也就是将主机名、接口（这里是 localhost 和 8080）分别赋予变量 hostname 和 port；
>
> 方法`“__enter__()”`连接数据库，并且返回对象 DBConnectionManager；
>
> 方法`“__exit__()”`则负责关闭数据库的连接。





## 基于生成器的上下文管理器

基于生成器的上下文管理器更加方便、简洁，适用于中小型程序



使用装饰器 contextlib.contextmanager，来定义自己所需的基于生成器的上下文管理器，用以支持 with 语句。

```
from contextlib import contextmanager

@contextmanager
def file_manager(name, mode):
    try:
        f = open(name, mode)
        yield f

    finally:
        f.close()

with file_manager('test.txt', 'w') as f:
    f.write('Hello World')
```

> 函数 file_manager() 是一个生成器，当我们执行 with 语句时，便会打开文件，并返回文件对象 f；当 with 语句执行完后，finally block 中的关闭文件操作便会执行。