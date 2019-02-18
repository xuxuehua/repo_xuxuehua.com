---
title: "chattr"
date: 2018-12-22 20:00
---


[TOC]



# chattr

文件有隐藏属性，隐藏属性对系统有很大的帮助。尤其是在系统安全（Security）方面

chattr命令不能保护/、/dev、/tmp、/var目录。

lsattr命令是显示chattr命令设置的文件属性。



## Usage

```
chattr [-RVf] [mode]  [file]
```



## Options

### \+ 增加

增加某个特殊参数，其他原本存在的参数不动。 

### \- 删除

删除某个特殊参数，其他原本存在的参数不动。 

### = 指定

设置一定，且仅有后面接的参数 



### -R 递归

递归的列出文件夹中所有文件的属性



### -V 版本

查看chattr版本



### -f 隐藏错误  

抑制大多数错误消息



### -v 设置文件版本 

设置文件的版本号or代号





## 控制属性

### a 仅仅增加数据

即append，设定该参数后，只能向文件中添加数据，而不能删除，多用于服务器日志文件安全，只有root才能设定这个属性。

如果是登录文件，就更需要 +a参数，使之可以增加但不能修改与删除原有的数据。



### A access time

当设置了A属性时，这个文件（或目录）的存取时间atime（access）将不可被修改，可避免例如手提电脑有磁盘I/O错误的情况发生



### c 自动压缩和解压缩

即compresse，设定文件是否经压缩后再存储。读取时需要经过自动解压操作。

这个属性设置之后，将会自动将此文件“压缩”，在读取的时候将会自动解压缩，但在存储的时候，将会先进行压缩后再存储（对于大文件有用）



### C 

no copy on write





### d 转储

即no dump，设定文件不能成为dump程序的备份目标。





### D 同步目录更新

synchronous directory updates



### e 

extent format





### i 锁定所有权限

immutable，作用很大。它可以让一个文件“不能被删除、改名、设置连接，也无法写入或新增数据”。对于系统安全性有相当大的帮助

因为它可以让一个文件无法被更改，对于需要很高系统安全性的人来说，相当重要。



```
[root@linuxtmp]# touch attrtest
[root@linuxtmp]# chattr +i attrtest
[root@linuxtmp]# rm attrtest
rm: remove write-protected regular empty file `attrtest'? y
rm: cannot remove `attrtest': Operation not permitted
```



### j  日志journal记录

即journal，设定此参数使得当通过mount参数：data=ordered 或者 data=writeback 挂 载的文件系统，文件在写入时会先被记录(在journal中)。如果filesystem被设定参数为 data=journal，则该参数自动失效。 





### s 保密删除

保密性地删除文件或目录，即硬盘空间被全部收回。



### S 同步写入磁盘

这个功能有点类似sync。就是将数据同步写入磁盘中。可以有效地避免数据流失





### t

no  tail-merging



### T

top of directory hierarchy



### u 还原删除

与s相反，当设定为u时，数据内容其实还存在磁盘中，可以用于undeletion。





 