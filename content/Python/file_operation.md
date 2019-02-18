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

创建并写入一个新文件， 要求文件事先不存在



### a 追加

以附加的方式打开只写文件。

若文件不存在，则会建立该文件，并在尾部追加

如果文件存在，写入的数据会被加到文件尾，即文件原先的内容会被保留。

```

```



### b 二进制模式

字节流，将文件就按照字节理解，与字符编码无关，二进制模式操作时，字节操作用bytes类型



### t 文本

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

```

```



### wb

```

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






