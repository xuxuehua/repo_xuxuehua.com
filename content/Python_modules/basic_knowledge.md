---
title: "basic_knowledge 模块基础"
date: 2018-08-25 00:39
---

[TOC]

# 模块基本使用



## 路径

大型工程尽可能使用绝对路径

独立项目，所有模块从项目根目录开始追溯，称相对的绝对路径



### 查看包路径

```
$ python -c "print('\n'.join(__import__('sys').path))" 

/Users/rxu/.pyenv/versions/3.7.0/lib/python37.zip
/Users/rxu/.pyenv/versions/3.7.0/lib/python3.7
/Users/rxu/.pyenv/versions/3.7.0/lib/python3.7/lib-dynload
/Users/rxu/.pyenv/versions/test_only_3.7.0/lib/python3.7/site-packages
```

OR

```
In [1]: import sys                                                                                                                                                                   
In [2]: from pprint import pprint                                                                                                                                                    
In [3]: pprint(sys.path)                                                                                                                                                             ['/Users/rxu/.pyenv/versions/3.7.0/envs/test_only_3.7.0/bin',
 '/Users/rxu/.pyenv/versions/3.7.0/lib/python37.zip',
 '/Users/rxu/.pyenv/versions/3.7.0/lib/python3.7',
 '/Users/rxu/.pyenv/versions/3.7.0/lib/python3.7/lib-dynload',
 '',
 '/Users/rxu/.pyenv/versions/3.7.0/envs/test_only_3.7.0/lib/python3.7/site-packages',
 '/Users/rxu/.pyenv/versions/3.7.0/envs/test_only_3.7.0/lib/python3.7/site-packages/IPython/extensions',
 '/Users/rxu/.ipython']
```



## 定义

 .py 文件组成的代码集合就称为模块

模块分为三种：

- 自定义模块
- 内置标准模块（又称标准库）
- 开源模块

自定义模块 和开源模块的使用参考 http://www.cnblogs.com/wupeiqi/articles/4963027.html

## 包

用来从逻辑上组织模块的，本质就是一个目录(含有`__init__`)

导入包的本质，就是解释`__init__` 文件， 即运行该文件

`__init__.py` (package_testing 包)

```
print('Package testing file')
```

My_testing.py

```
import package_testing

>>>
Package testing file
```

导入包就是执行`__init__` 代码，无法执行包内的模块和函数。因此需要在`__init__` 中加载指定的函数

```
from . import test1
```



## 搜索路径

Python默认会通过下列路径搜索需要的模块

1. 程序所在的文件夹
2. 操作系统环境变量PYTHONPATH所包含的路径
3. 标准库的安装路径



## 导入方法

import 的本质就是路径检索

导入模块的本质就是把Python文件解释一遍

```
import module_name
import module_name_1, module_name_2
from modules import *
from modules import logger as my_logger
```

### 优化多个调用

当导入的模块被多次函数调用，建议是使用

```
from module_name import my_function
```

## 使用`__name__`

当整个程序作为库被import的时候，我们并不需要测试语句。可以使用`__name__`跳过, 这样不会执行该模块下面定义的调用方法和函数

`__name__`是Python中一个隐含的变量它代表了模块的名字

只有被Python解释器直接执行的模块的名字才是`__main__`

```python
def lib_func(a):
    return a + 10

def lib_func_another(b):
    return b + 20

if __name__ == '__main__':
    test = 101
    print(lib_func(test))

print('__name__:', __name__)

>>>
111
__name__: __main__
```



# 代码模块化

有些人吵着闹着要让程序“模块化”，结果他们的做法是把代码分部到多个文件和目录里面，然后把这些目录或者文件叫做“module”。他们甚至把这些目录分放在不同的VCS repo里面。结果这样的作法并没有带来合作的流畅，而是带来了许多的麻烦。

真正的模块化，并不是文本意义上的，而是逻辑意义上的。一个模块应该像一个电路芯片，它有定义良好的输入和输出。实际上一种很好的模块化方法早已经存在，它的名字叫做“函数”。每一个函数都有明确的输入（参数）和输出（返回值），同一个文件里可以包含多个函数，所以你其实根本不需要把代码分开在多个文件或者目录里面，同样可以完成代码的模块化。







