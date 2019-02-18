---
title: "platform"
date: 2018-10-09 00:22
---


[TOC]


# platform

## platform()
获取操作系统类型

```
In [25]: platform.platform()
Out[25]: 'Darwin-17.7.0-x86_64-i386-64bit'
```



## system() 

获取平台系统信息

```
In [26]: platform.system()
Out[26]: 'Darwin'
```



## version()  

操作系统版本

```
In [27]: platform.version()
Out[27]: 'Darwin Kernel Version 17.7.0: Thu Jun 21 22:53:14 PDT 2018; root:xnu-4570.71.2~1/RELEASE_X86_64'
```



## machine()  

计算机类型

```
In [28]: platform.machine()
Out[28]: 'x86_64'
```




## node()     
计算机网络名称

```
In [29]: platform.node()
Out[29]: 'xhxu-mac'
```



## processor()

处理器信息

```
In [30]: platform.processor()
Out[30]: 'i386'
```




## uname()    
综合信息

```
In [31]: platform.uname()
Out[31]: uname_result(system='Darwin', node='xhxu-mac', release='17.7.0', version='Darwin Kernel Version 17.7.0: Thu Jun 21 22:53:14 PDT 2018; root:xnu-4570.71.2~1/RELEASE_X86_64', machine='x86_64', processor='i386')
```




## python_build()  
python 版本信息

```
In [32]: platform.python_build()
Out[32]: ('default', 'Apr 20 2017 15:39:30')
```




## python_version() 
Python主版本信息

```
In [33]: platform.python_version()
Out[33]: '3.5.3'
```




## python_compiler() 
Python编译器信息

```
In [34]: platform.python_compiler()
Out[34]: 'GCC 4.2.1 Compatible Apple LLVM 8.1.0 (clang-802.0.38)'
```




## python_branch()   
获取Python的分支情况

```
In [35]: platform.python_branch()
Out[35]: ''
```