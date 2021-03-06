---
title: "sys"
date: 2018-07-02 18:05
---

[TOC]



# sys



## argv

```
import sys
 
print(sys.argv)
 
 
#输出
$ python test.py helo world
>>>
['test.py', 'helo', 'world']  #把执行脚本时传递的参数获取到了
```



## path

打印python全局环境变量

第三方库在site-packages目录里

```
import sys

print(sys.path)
>>>
['/Users/xhxu/python/python3/test', '/Users/xhxu/python/python3', '/Users/xhxu/.pyenv/versions/3.5.3/lib/python35.zip', '/Users/xhxu/.pyenv/versions/3.5.3/lib/python3.5', '/Users/xhxu/.pyenv/versions/3.5.3/lib/python3.5/plat-darwin', '/Users/xhxu/.pyenv/versions/3.5.3/lib/python3.5/lib-dynload', '/Users/xhxu/.pyenv/versions/env3.5.3/lib/python3.5/site-packages', '/Users/xhxu/.pyenv/versions/env3.5.3/lib/python3.5/site-packages/setuptools-28.8.0-py3.5.egg', '/Users/xhxu/.pyenv/versions/env3.5.3/lib/python3.5/site-packages/pip-9.0.1-py3.5.egg']
```



### append

添加环境变量

```
sys.path.append(BASE_DIR)
```



## version

 获取Python解释程序的版本信息



## exit(n)

```
退出程序，正常退出时exit(``0``)
```



## getrefcount()

引用计数

```
In [1]: import sys

In [2]: a = []

In [3]: b = a

In [4]: sys.getrefcount(a)
Out[4]: 27
```



## platform

返回操作系统平台名称

```
In [13]: sys.platform
Out[13]: 'darwin'
```



## path

系统环境变量PYTHONPATH

```
In [39]: sys.path
Out[39]:
['/Users/rxu/.pyenv/versions/3.5.3/envs/general_3.5.3/bin',
 '/Users/rxu/.pyenv/versions/3.5.3/lib/python35.zip',
 '/Users/rxu/.pyenv/versions/3.5.3/lib/python3.5',
 '/Users/rxu/.pyenv/versions/3.5.3/lib/python3.5/plat-darwin',
 '/Users/rxu/.pyenv/versions/3.5.3/lib/python3.5/lib-dynload',
 '',
 '/Users/rxu/.pyenv/versions/3.5.3/envs/general_3.5.3/lib/python3.5/site-packages',
 '/Users/rxu/.pyenv/versions/3.5.3/envs/general_3.5.3/lib/python3.5/site-packages/IPython/extensions',
 '/Users/rxu/.ipython']
```



## maxint         

最大的``Int``值



## setrecursionlimit

Python 默认递归约995次

这里可以设置最大递归次数

```
sys.setrecursionlimit(1000000)
```



```
>>> def factorial(n):
...     return 1 if n<=1 else n*factorial(n-1)

>>> factorial(5000)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 2, in factorial
  File "<stdin>", line 2, in factorial
  File "<stdin>", line 2, in factorial
  [Previous line repeated 995 more times]
RecursionError: maximum recursion depth exceeded in comparison

>>> sys.setrecursionlimit(1000000)
>>> factorial(5000)


```

>  mac内存是16G，如果把递归层数设定到1百万，大概跑到35000层左右，服务就挂了



## getsizeof

```
In [3]: from sys import getsizeof

In [4]: getsizeof("merged.csv")
Out[4]: 59

In [5]: ls -lah merged.csv
-rw-r--r--  1 rxu  staff   994B Feb 27 18:54 merged.csv
```

