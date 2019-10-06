---
title: "pdb"
date: 2019-09-26 08:42
---
[TOC]



# PDB

在程序中相应的地方打印，的确是调试程序的一个常用手段，但这只适用于小型程序。因为你每次都 得重新运行整个程序，或是一个完整的功能模块，才能看到打印出来的变量值。如果程序不大，每次运行都 非常快，那么使用print()，的确是很方便的。 

但是，如果我们面对的是大型程序，运行一次的调试成本很高。特别是对于一些tricky的例子来说，它们通 常需要反复运行调试、追溯上下文代码，才能找到错误根源。这种情况下，仅仅依赖打印的效率自然就很低 了。 

很多情况下，单一语言的IDE，对混合代码并不支持UI形式的断点调试功能，或是只对某些功能 模块支持。另外，考虑到不少代码已经挪到了类似Jupyter的Notebook中，往往就要求开发者使用命令行的 形式，来对代码进行调试。 

而Python的pdb，正是其自带的一个调试库。它为Python程序提供了交互式的源代码调试功能，是命令行 版本的IDE断点调试器，完美地解决了我们刚刚讨论的这个问题。 



## 使用

只需在程序中加入`import pdb`和`pdb.set_trace()`这两行代码就行了

```
a = 1 
b = 2
import pdb
pdb.set_trace()
c = 3 
print(a+b+c)
```

运行这个程序时时，它的输出界面是下面这样的，表示程序已经运行到了“pdb.set_trace()”这行，并且暂停了下来，等待用户输入

```
> /Users/rxu/coding/python/python3/my_test_file/my_pdb_test.py(5)<module>()
-> c = 3
(Pdb) 
```



### p 打印

在IDE断点调试器中可以执行的一切操作，比如打印，语法是"p
<expression>"

```
(Pdb) p a
1
(Pdb) p b
2
(Pdb) p c
*** NameError: name 'c' is not defined
(Pdb) 
```



### n 执行到下一行

表示继续执行代码到下一行

```
(Pdb) n
> /Users/rxu/coding/python/python3/my_test_file/my_pdb_test.py(6)<module>()
-> print(a+b+c)
(Pdb) n
6
```



### l 列出上下11行源码

命令”l“，则表示列举出当前代码行上下的11行源代码，方便开发者熟悉当前断点周围的代码状态

```
(Pdb) l
  1     a = 1
  2     b = 2
  3     import pdb
  4     pdb.set_trace()
  5     c = 3
  6  -> print(a+b+c)
[EOF]
```



### s 进入代码内部

就是 step into 的意思，即进入相对应的代码内部

```
def func():
    print('enter func()')

a = 1
b = 2
import pdb
pdb.set_trace()

func()
c = 3
print(a+b+c)

>>>
-> func()
(Pdb) s
--Call--
> /Users/rxu/coding/python/python3/my_test_file/my_pdb_test.py(1)func()
-> def func():
(Pdb) l
  1  -> def func():
  2         print('enter func()')
  3  
  4     a = 1
  5     b = 2
  6     import pdb
  7     pdb.set_trace()
  8  
  9     func()
 10     c = 3
 11     print(a+b+c)
(Pdb) 
```



### r 继续执行

表示step out，即继续执行，直到当前的函数完成返回



### b 断点

可以用来设置断点。比方 说，我想要在代码中的第10行，再加一个断点，那么在pdb模式下输入”b 11“即可 



### c 执行到断点

表示一直执行程序，直到遇到下一个断点





