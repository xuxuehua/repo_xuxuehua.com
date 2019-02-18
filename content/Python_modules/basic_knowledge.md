---
title: "basic_knowledge 模块基础"
date: 2018-08-25 00:39
---

[TOC]

# 模块基本使用



## 定义

 .py 文件组成的代码集合就称为模块



模块分为三种：

- 自定义模块
- 内置标准模块（又称标准库）
- 开源模块



自定义模块 和开源模块的使用参考 http://www.cnblogs.com/wupeiqi/articles/4963027.html



## 包

用来从逻辑上组织模块的，本质就是一个目录(含有`__init__`)



导入包的本质，就是解释`__init__` 文件

* `__init__.py` (package_testing 包)

```
print('Package testing file')
```

* My_testing.py

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

当整个程序作为库被import的时候，我们并不需要测试语句。可以使用__name__跳过

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