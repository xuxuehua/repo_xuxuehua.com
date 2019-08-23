---
title: "basic_knowledge"
date: 2018-06-11 02:14
---

[TOC]



# Basic knowledge 



## 语言类型

![img](https://snag.gy/w1efAd.jpg)





### 编译型语言

* 一个负责翻译的程序对源代码进行转换，生成可执行的代码，其过程称为编译Compile。负责编译的程序称为编译器，生成的可执行文件可以直接运行
* 为了方便管理，通常将代码分散在各个源文件中，但只有等所有的源文件编译成功后，将目标文件打包成可执行文件，类似于将包含可执行代码的目标文件连接起来，成为链接Link。负责链接的程序叫链接程序Linker
* 链接完成之后，一般可以得到最终的可执行文件了。
* 如C，C++



### 解释型语言

* 程序执行到源程序的某条指令，一个成为解释程序的外壳程序将源代码转换成二进制代码以供执行。
* 解释型程序离不开解释程序，但其省却了编译步骤，修改调试也方便。但其将编译的过程放到执行过程中，就决定了解释型语言比编译型语言慢很多
* 如Basic， Javascript, Python
* Java语言比较接近解释型语言的特性，生成的代码是介于机器码和Java源代码之间的中间代码，运行时候由JVM解释执行，保留了源代码的高抽象，可移植特点





### 低级语言

* 汇编语言面向机器，是针对特定机器的机器指令的助剂符
* 汇编语言是无法独立于机器(特定的CPU体系结构)的
* 但汇编语言也是要经过翻译成机器指令才能执行的，所以也有将运行在一种机器上的汇编语言翻译成运行在另一种机器上的机器指令的方法，那就是交叉汇编技术。

### 高级语言

* 高级语言是从人类的逻辑思维角度出发的计算机语言，抽象程度大大提高，需要经过编译成特定机器上的目标代码才能执行，一条高级语言的语句往往需要若干条机器指令来完成
* 高级语言不依赖于机器，是指在不同的机器或平台上高级语言的程序本身不变，而通过编译器编译得到的目标代码去适应不同的机器



### 动态语言

* 动态类型语言：动态类型语言是指在运行期间才去做数据类型检查的语言，也就是说，在用动态类型的语言编程时，永远也不用给任何变量指定数据类型，该语言会在你第一次赋值给变量时，在内部将数据类型记录下来。Python和Ruby就是一种典型的动态类型语言，其他的各种脚本语言如VBScript也多少属于动态类型语言。



### 静态语言

* 静态类型语言：静态类型语言与动态类型语言刚好相反，它的数据类型是在编译其间检查的，也就是说在写程序时要声明所有变量的数据类型，C/C++是静态类型语言的典型代表，其他的静态类型语言还有C#、JAVA等。





### 强类型定义语言

* 强制数据类型定义的语言。也就是说，一旦一个变量被指定了某个数据类型，如果不经过强制转换，那么它就永远是这个数据类型了。举个例子：如果你定义了一个整型变量a,那么程序根本不可能将a当作字符串类型处理。强类型定义语言是类型安全的语言。
* 强类型定义语言在速度上可能略逊色于弱类型定义语言，但是强类型定义语言带来的严谨性能够有效的避免许多错误
* Python，Java是强类型定义语言



### 弱类型定义语言

* 数据类型可以被忽略的语言。它与强类型定义语言相反, 一个变量可以赋不同数据类型的值，即可以隐式转换类型。
* VBScript是弱类型定义语言， PHP， Javascript， Perl









## 编译

* 编译是将源程序翻译成可执行的目标代码，翻译与执行是分开的；而解释是对源程序的翻译与执行一次性完成，不生成可存储的目标代码。这只是表象，二者背后的最大区别是：对解释执行而言，程序运行时的控制权在解释器而不在用户程序；对编译执行而言，运行时的控制权在用户程序。



## 解释

* 解释具有良好的动态特性和可移植性，比如在解释执行时可以动态改变变量的类型、对程序进行修改以及在程序中插入良好的调试诊断信息等，而将解释器移植到不同的系统上，则程序不用改动就可以在移植了解释器的系统上运行。同时解释器也有很大的缺点，比如执行效率低，占用空间大，因为不仅要给用户程序分配空间，解释器本身也占用了宝贵的系统资源。



## Python特点

* 线程不能利用多CPU问题，这是Python被人诟病最多的一个缺点，GIL即全局解释器锁（Global Interpreter Lock），是[计算机程序设计语言](http://zh.wikipedia.org/wiki/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1%E8%AF%AD%E8%A8%80)[解释器](http://zh.wikipedia.org/wiki/%E8%A7%A3%E9%87%8A%E5%99%A8)用于[同步](http://zh.wikipedia.org/wiki/%E5%90%8C%E6%AD%A5)[线程](http://zh.wikipedia.org/wiki/%E7%BA%BF%E7%A8%8B)的工具，使得任何时刻仅有一个线程在执行，Python的线程是操作系统的原生线程。在Linux上为pthread，在Windows上为Win thread，完全由操作系统调度线程的执行。一个python解释器进程内有一条主线程，以及多条用户程序的执行线程。即使在多核CPU平台上，由于GIL的存在，所以禁止多线程的并行执行。



## Python解释器

### CPython

当我们从[Python官方网站](https://www.python.org/)下载并安装好Python 2.7后，我们就直接获得了一个官方版本的解释器：CPython。这个解释器是用C语言开发的，所以叫CPython。在命令行下运行`python`就是启动CPython解释器。

CPython是使用最广的Python解释器。教程的所有代码也都在CPython下执行。



#### GIL (Global Interpreter Lock)

全局解释器锁，保证变量运算和读取，在同一时刻只有一个线程执行。

这个解释器锁是有必要的，因为cpython解释器的内存管理不是线程安全的, 即同一时刻，Python 主程序只允许有一个线程执行，所以 Python 的并发，是通过多线程的切换完成的。

为了解决由此带来的 race condition 等问题，Python 便引入了全局解释器锁，也就是同一时刻，只允许一个线程执行。当然，在执行 I/O 操作时，如果一个线程被 block 了，全局解释器锁便会被释放，从而让另一个线程能够继续执行



GIL虽然是一个假的多线程，但是在处理一些IO操作(文件读写，网络请求)可以提高效率，建议使用多线程提高效率。但CPU计算操作不建议使用多线程，建议使用多进程。

GIL，是最流行的 Python 解释器 CPython 中的一个技术术语。它的意思是全局解释器锁，本质上是类似操作系统的 Mutex。每一个 Python 线程，在 CPython 解释器中执行时，都会先锁住自己的线程，阻止别的线程执行。

一是设计者为了规避类似于内存管理这样的复杂的竞争风险问题（race condition）；

二是因为 CPython 大量使用 C 语言库，但大部分 C 语言库都不是原生线程安全的（线程安全会降低性能和增加复杂度）。



##### GIL 原理

![img](https://snipboard.io/C14cu2.jpg)

一个 GIL 在 Python 程序的工作示例。其中，Thread 1、2、3 轮流执行，每一个线程在开始执行时，都会锁住 GIL，以阻止别的线程执行；同样的，每一个线程执行完一段后，会释放 GIL，以允许别的线程开始利用资源。

CPython 中还有另一个机制，叫做 check_interval，意思是 CPython 解释器会去轮询检查线程 GIL 的锁住情况。每隔一段时间，Python 解释器就会强制当前线程去释放 GIL，这样别的线程才能有执行的机会。

**GIL 的设计，主要是为了方便 CPython 解释器层面的编写者，而不是 Python 应用层面的程序员**。作为 Python 的使用者，我们还是需要 lock 等工具，来确保线程安全。

```
n = 0 
lock = threading.Lock()

def foo():
    global n
    with lock:
        n += 1
```



##### GIL 特点

Python 的 GIL，是通过 CPython 的解释器加的限制。如果你的代码并不需要 CPython 解释器来执行，就不再受 GIL 的限制。

事实上，很多高性能应用场景都已经有大量的 C 实现的 Python 库，例如 NumPy 的矩阵运算，就都是通过 C 来实现的，并不受 GIL 影响。

所以，大部分应用情况下，你并不需要过多考虑 GIL。因为如果多线程计算成为性能瓶颈，往往已经有 Python 库来解决这个问题了。

换句话说，如果你的应用真的对性能有超级严格的要求，比如 100us 就对你的应用有很大影响，那我必须要说，Python 可能不是你的最优选择。

当然，可以理解的是，我们难以避免的有时候就是想临时给自己松松绑，摆脱 GIL，比如在深度学习应用里，大部分代码就都是 Python 的。在实际工作中，如果我们想实现一个自定义的微分算子，或者是一个特定硬件的加速器，那我们就不得不把这些关键性能（performance-critical）代码在 C++ 中实现（不再受 GIL 所限），然后再提供 Python 的调用接口。

总的来说，你只需要重点记住，绕过 GIL 的大致思路有这么两种就够了：

1. 绕过 CPython，使用 JPython（Java 实现的 Python 解释器）等别的实现；
2. 把关键性能代码，放到别的语言（一般是 C++）中实现。



在python3中，GIL不使用ticks计数，改为使用计时器（执行时间达到阈值后interval=15毫秒，当前线程释放GIL），这样对CPU密集型程序更加友好，但依然没有解决GIL导致的同一时间只能执行一个线程的问题，所以效率依然不尽如人意。多核多线程比单核多线程更差，原因是单核下多线程，每次释放GIL，唤醒的那个线程都能获取到GIL锁，所以能够无缝执行，但多核下，CPU0释放GIL后，其他CPU上的线程都会进行竞争，但GIL可能会马上又被CPU0拿到，导致其他几个CPU上被唤醒后的线程会醒着等待到切换时间后又进入待调度状态，这样会造成线程颠簸(thrashing)，导致效率更低。

经常会听到老手说：“python下想要充分利用多核CPU，就用多进程”，原因是什么呢？原因是：每个进程有各自独立的GIL，互不干扰，这样就可以真正意义上的并行执行，所以在python中，多进程的执行效率优于多线程(仅仅针对多核CPU而言)。所以我们能够得出结论：多核下，想做并行提升效率，比较通用的方法是使用多进程，能够有效提高执行效率。



​	

### IPython

IPython是基于CPython之上的一个交互式解释器，也就是说，IPython只是在交互方式上有所增强，但是执行Python代码的功能和CPython是完全一样的。好比很多国产浏览器虽然外观不同，但内核其实都是调用了IE。

CPython用`>>>`作为提示符，而IPython用`In [序号]:`作为提示符。

### PyPy

PyPy是另一个Python解释器，它的目标是执行速度。PyPy采用[JIT技术](http://en.wikipedia.org/wiki/Just-in-time_compilation)，对Python代码进行动态编译（注意不是解释），所以可以显著提高Python代码的执行速度。

绝大部分Python代码都可以在PyPy下运行，但是PyPy和CPython有一些是不同的，这就导致相同的Python代码在两种解释器下执行可能会有不同的结果。如果你的代码要放到PyPy下执行，就需要了解[PyPy和CPython的不同点](http://pypy.readthedocs.org/en/latest/cpython_differences.html)。

### Jython

Jython是运行在Java平台上的Python解释器，可以直接把Python代码编译成Java字节码执行。

### IronPython

IronPython和Jython类似，只不过IronPython是运行在微软.Net平台上的Python解释器，可以直接把Python代码编译成.Net的字节码。

 

## pyc 文件

PyCodeObject和pyc文件。

我们在硬盘上看到的pyc自然不必多说，而其实PyCodeObject则是Python编译器真正编译成的结果。我们先简单知道就可以了，继续向下看。

当python程序运行时，编译的结果则是保存在位于内存中的PyCodeObject中，当Python程序运行结束时，Python解释器则将PyCodeObject写回到pyc文件中。

当python程序第二次运行时，首先程序会在硬盘中寻找pyc文件，如果找到，则直接载入，否则就重复上面的过程。

所以我们应该这样来定位PyCodeObject和pyc文件，我们说pyc文件其实是PyCodeObject的一种持久化保存方式。



## 安装



### centos 

#### Python 3

```
$ sudo yum install -y https://centos7.iuscommunity.org/ius-release.rpm
$ sudo yum update
```

Python 34

```
$ sudo yum install -y python34u python34u-libs python34u-devel python34u-pip
$ which -a python3.4
/bin/python3.4
/usr/bin/python3.4
```

Python 35

```
$ sudo yum install -y python35u python35u-libs python35u-devel python35u-pip
$ which -a python3.5
/bin/python3.5
/usr/bin/python3.5
```











## 注释

单行注视：# 被注释内容

多行注释：""" 被注释内容 """,   ''' 被注释内容 '''



 

## 用户输入 input 方法

input() 函数暂停程序运行，同时等待键盘输入;直到回车被按下，函数的参数即为提示语，输入的类型永远是字符串型(str)





### 多行输入

 ```
sentinel = 'end' # 遇到这个就结束
lines = []
for line in iter(input, sentinel):
    lines.append(line)
 ```



### 带提示的多行输入

```
from functools import partial

inputNew = partial(input,'Input something pls:\n')
sentinel = 'end' # 遇到这个就结束
lines = []
for line in iter(inputNew, sentinel):
    lines.append(line)
```





## 软件目录结构规范



### 特点

设计一个层次清晰的目录结构，就是为了达到以下两点:

1. 可读性高: 不熟悉这个项目的代码的人，一眼就能看懂目录结构，知道程序启动脚本是哪个，测试目录在哪儿，配置文件在哪儿等等。从而非常快速的了解这个项目。
2. 可维护性高: 定义好组织规则后，维护者就能很明确地知道，新增的哪个文件和代码应该放在什么目录之下。这个好处是，随着时间的推移，代码/配置的规模增加，项目结构不会混乱，仍然能够组织良好。



### 目录组织方式

一个较好的Python工程目录结构，已经有一些得到了共识的目录结构。在Stackoverflow的[这个问题](http://stackoverflow.com/questions/193161/what-is-the-best-project-structure-for-a-python-application)上，能看到大家对Python目录结构的讨论。

假设你的项目名为foo, 我比较建议的最方便快捷目录结构这样就足够了:

```
Foo/
|-- bin/
|   |-- foo
|
|-- conf/
|
|-- foo/
|   |-- tests/
|   |   |-- __init__.py
|   |   |-- test_main.py
|   |
|   |-- __init__.py
|   |-- main.py
|
|-- docs/
|   |-- conf.py
|   |-- docs.md
|
|-- setup.py
|-- requirements.txt
|-- README.md
```

> `bin/`: 存放项目的一些可执行文件，当然你可以起名`script/`之类的也行。
>
> `conf/`: 存放配置文件
>
> `foo/`: 存放项目的所有源代码。(1) 源代码中的所有模块、包都应该放在此目录。不要置于顶层目录。(2) 其子目录`tests/`存放单元测试代码； (3) 程序的入口最好命名为`main.py`。
>
> `docs/`: 存放一些文档。
> `setup.py`: 安装、部署、打包的脚本。
> `requirements.txt`: 存放软件依赖的外部Python包列表。
> `README`: 项目说明文件。



#### README

目的是能简要描述该项目的信息，让读者快速了解这个项目。

它需要说明以下几个事项:

1. 软件定位，软件的基本功能。
2. 运行代码的方法: 安装环境、启动命令等。
3. 简要的使用说明。
4. 代码目录结构说明，更详细点可以说明软件的基本原理。
5. 常见问题说明。

我觉得有以上几点是比较好的一个`README`。在软件开发初期，由于开发过程中以上内容可能不明确或者发生变化，并不是一定要在一开始就将所有信息都补全。但是在项目完结的时候，是需要撰写这样的一个文档的。

可以参考Redis源码中[Readme](https://github.com/antirez/redis#what-is-redis)的写法，这里面简洁但是清晰的描述了Redis功能和源码结构。



#### setup.py

用`setup.py`来管理代码的打包、安装、部署问题。业界标准的写法是用Python流行的打包工具[setuptools](https://pythonhosted.org/setuptools/setuptools.html#developer-s-guide)来管理这些事情。这种方式普遍应用于开源项目中。不过这里的核心思想不是用标准化的工具来解决这些问题，而是说，**一个项目一定要有一个安装部署工具**，能快速便捷的在一台新机器上将环境装好、代码部署好和将程序运行起来。

setuptools的[文档](https://pythonhosted.org/setuptools/setuptools.html#developer-s-guide)比较庞大，刚接触的话，可能不太好找到切入点。学习技术的方式就是看他人是怎么用的，可以参考一下Python的一个Web框架，flask是如何写的: [setup.py](https://github.com/mitsuhiko/flask/blob/master/setup.py)

当然，简单点自己写个安装脚本（`deploy.sh`）替代`setup.py`也未尝不可。



#### requirements.txt

这个文件存在的目的是:

1. 方便开发者维护软件的包依赖。将开发过程中新增的包添加进这个列表中，避免在`setup.py`安装依赖时漏掉软件包。
2. 方便读者明确项目使用了哪些Python包。

这个文件的格式是每一行包含一个包依赖的说明，通常是`flask>=0.10`这种格式，要求是这个格式能被`pip`识别，这样就可以简单的通过 `pip install -r requirements.txt`来把所有Python包依赖都装好了。具体格式说明： [点这里](https://pip.readthedocs.org/en/1.1/requirements.html)。



#### 配置文件

1. 模块的配置都是可以灵活配置的，不受外部配置文件的影响。
2. 程序的配置也是可以灵活控制的。

能够佐证这个思想的是，用过nginx和mysql的同学都知道，nginx、mysql这些程序都可以自由的指定用户配置。

所以，不应当在代码中直接`import conf`来使用配置文件。上面目录结构中的`conf.py`，是给出的一个配置样例，不是在写死在程序中直接引用的配置文件。可以通过给`main.py`启动参数指定配置路径的方式来让程序读取配置内容。当然，这里的`conf.py`你可以换个类似的名字，比如`settings.py`。或者你也可以使用其他格式的内容来编写配置文件，比如`settings.yaml`之类的。



### 开源软件

如果你想写一个开源软件，目录该如何组织，可以参考[这篇文章](http://www.jeffknupp.com/blog/2013/08/16/open-sourcing-a-python-project-the-right-way/)。





## 垃圾回收

Python的某个对象的引用计数降为0时，说明没有任何引用指向改对象，该对象就要被垃圾回收



在垃圾回收的时候，Python不能执行其他的任何任务。当Python运行时，会记录其中分配对象(object allocation)和取消分配对象(object deallocaiton)的次数，当两者差值高于某个阀值的时候，垃圾回收会自动启动。

```
#700是垃圾回收的阀值，可以使用set_threshold方法重新设置
#后面两个10是与分代回收相关的阀值
In [36]: import gc

In [37]: print(gc.get_threshold())
(700, 10, 10)
```



### 分代回收

* Python将所有对象分为0， 1， 2三代。所有新建的对象都是0代。当某一代对象经历过垃圾回收，依然存活，那么它就会被归入下一代对象。

```
In [36]: import gc

In [37]: print(gc.get_threshold())
(700, 10, 10)
# 每10次0代垃圾回收，会配合一次1代垃圾回收；而每10次1代垃圾回收，会有一次2代垃圾回收
```





### 孤立的引用环

* 引用环的存在会给上面垃圾回收机制带来很大的困难。

* 创建两个表对象，并引用对方，构成引用环。删除a，b引用之后，这两个对象不可能再从程序中调用，就没有什么用处了。但是引用环的存在，这两个对象的引用计数都没有降到0，不会被垃圾回收。

```
In [39]: a = []

In [40]: b = [a]

In [41]: a.append(b)

In [42]: del a

In [43]: del b
```





## 执行

### 脚本与命令结合

下面方法运行脚本，脚本结束后，会直接进入命令行。这样做的好处是脚本的对象不会被清空，可以通过命令行直接调用

```
$ python -i script.py
```



## 测试Python性能

```
	python -m cProfile -s time PYTHON_SCRIPT
```

