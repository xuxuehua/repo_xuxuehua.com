---
title: "io"
date: 2019-02-01 12:39
---

[TOC]

# io

## StringIO

字符串写入内存

一般来说， 磁盘的操作比内存的操作要慢得多，内存足够的情况下，一般优化的思路减少磁盘IO的过程，可以提高效率

```
from io import StringIO
# 内存中构建
sio = StringIO() #像文件对象一样操作
print(sio.readable(), sio.writable(), sio.seekable())

sio.write('Rick \n Xu')
sio.seek(0)
print(sio.readline())
print(sio.getvalue())  #无视指针，输出全部内容
sio.close()

>>>
True True True
Rick

Rick
 Xu
```

## BytesIO

二进制写入内存

```
from io import BytesIO
bio = BytesIO()

print(bio.readable(), bio.writable(), bio.seekable())
bio.write(b'Rick \n Xu')
bio.seek(0)
print(bio.readline())
print(bio.getvalue())
bio.close()

>>>
True True True
b'Rick \n'
b'Rick \n Xu'
```
