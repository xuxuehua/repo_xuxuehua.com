---
title: "stat"
date: 2018-10-10 11:14
---


[TOC]

# stat

stat查一个文件或目录的三个时间



## Synopsis 语法

```
stat [OPTION]... FILE...
```



## Options

### -L, --dereference

follow links



### -Z, --context

 print the SELinux security context



### -f, --file-system

display file system status instead of file status



```
$stat -f /dev/
  File: "/dev/"
    ID: 0        Namelen: 255     Type: tmpfs
Block size: 4096       Fundamental block size: 4096
Blocks: Total: 124777     Free: 124738     Available: 124738
Inodes: Total: 124777     Free: 124220
```



### -c  --format=FORMAT

use the specified FORMAT instead of the default; output a newline after each use of FORMAT

主要有以下几个：

```
%A：用文件权限代码来表示权限
%a：用数字代码来表示权限
%F：用八进制表示文件权限
%G：文件拥有者的组名 
%g：文件拥有者的属组id(gid) 
%i：inode编号 
%n：文件名 
%s：文件大小 
%U：文件拥有者名称 
%u：文件拥有者的id(uid) 
%x: 取用时间
%y: 修改时间
%z: 属性改动时间
```





```
$stat -c "Access time is %x" file_new 
Access time is 2016-12-31 22:34:50.000000000 +0800
```



### --printf=FORMAT

 like  --format,  but  interpret backslash escapes, and do not output a mandatory trailing newline.  If you
​              want a newline, include \n in FORMAT.

### -t, --terse

print the information in terse form

​       

### --help 

​      

### --version

output version information and exit



# example



## 查看文件

```
# stat 1.py
  File: ‘1.py’
  Size: 278       	Blocks: 8          IO Block: 4096   regular file
Device: fd01h/64769d	Inode: 646849      Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Context: unconfined_u:object_r:admin_home_t:s0
Access: 2018-03-04 13:00:19.291533019 +0000
Modify: 2018-03-04 13:00:17.066513273 +0000
Change: 2018-03-04 13:00:17.066513273 +0000
 Birth: -
```

> Access访问时间 atime=access time
>
> Modify修改时间 mtime=modifiy time
>
> Change状态改动时间 ctime=change time





以简明格式显示`<file>`的状态信息。

```
stat -t <file>
```





## 查看目录

```
# stat test
  File: ‘test’
  Size: 4096      	Blocks: 8          IO Block: 4096   directory
Device: fd01h/64769d	Inode: 646725      Links: 8
Access: (0755/drwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
Context: unconfined_u:object_r:admin_home_t:s0
Access: 2018-10-10 03:33:22.943072238 +0000
Modify: 2018-09-21 16:12:05.137138213 +0000
Change: 2018-09-21 16:12:05.137138213 +0000
 Birth: -
```

> Access访问时间 atime=access time
>
> Modify修改时间 mtime=modifiy time
>
> Change状态改动时间 ctime=change time



## 查看系统

显示`<file>`所在文件系统的状态信息。

```
stat -f <file>
```

