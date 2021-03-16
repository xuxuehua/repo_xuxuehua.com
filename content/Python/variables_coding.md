---
title: "variables coding 变量和编码"
date: 2018-06-29 13:30
---



[TOC]



# 变量

## 定义

**Variables** are used to store information to be referenced and manipulated in a computer program. They also provide a way of labeling data with a descriptive name, so our programs can be understood more clearly by the reader and ourselves. It is helpful to think of variables as containers that hold information. Their sole purpose is to label and store data in memory. This data can then be used throughout your program.



python 变量的本质就是指针，即先生成对象，然后再将变量指向对象



## 局部变量

通过locals函数查看

```
In [1]: locals()
Out[1]: 
{'__name__': '__main__',
 '__doc__': 'Automatically created module for IPython interactive environment',
 '__package__': None,
 '__loader__': None,
 '__spec__': None,
 '__builtin__': <module 'builtins' (built-in)>,
 '__builtins__': <module 'builtins' (built-in)>,
 '_ih': ['', 'locals()'],
 '_oh': {},
 '_dh': ['/Users/rxu/test_purpose'],
 'In': ['', 'locals()'],
 'Out': {},
 'get_ipython': <bound method InteractiveShell.get_ipython of <IPython.terminal.interactiveshell.TerminalInteractiveShell object at 0x10bfc9a60>>,
 'exit': <IPython.core.autocall.ExitAutocall at 0x10bfc91c0>,
 'quit': <IPython.core.autocall.ExitAutocall at 0x10bfc91c0>,
 '_': '',
 '__': '',
 '___': '',
 '_i': '',
 '_ii': '',
 '_iii': '',
 '_i1': 'locals()'}
```





## 全局变量

通过globals 函数查看

```
In [2]: globals()
Out[2]: 
{'__name__': '__main__',
 '__doc__': 'Automatically created module for IPython interactive environment',
 '__package__': None,
 '__loader__': None,
 '__spec__': None,
 '__builtin__': <module 'builtins' (built-in)>,
 '__builtins__': <module 'builtins' (built-in)>,
 '_ih': ['', 'locals()', 'globals()'],
 '_oh': {1: {...}},
 '_dh': ['/Users/rxu/test_purpose'],
 'In': ['', 'locals()', 'globals()'],
 'Out': {1: {...}},
 'get_ipython': <bound method InteractiveShell.get_ipython of <IPython.terminal.interactiveshell.TerminalInteractiveShell object at 0x10bfc9a60>>,
 'exit': <IPython.core.autocall.ExitAutocall at 0x10bfc91c0>,
 'quit': <IPython.core.autocall.ExitAutocall at 0x10bfc91c0>,
 '_': {...},
 '__': '',
 '___': '',
 '_i': 'locals()',
 '_ii': '',
 '_iii': '',
 '_i1': 'locals()',
 '_1': {...},
 '_i2': 'globals()'}
```





# 变量命名规则

变量名只能是 字母、数字或下划线的任意组合

变量名的第一个字符不能是数字

以下关键字不能声明为变量名
['and', 'as', 'assert', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'exec', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'not', 'or', 'pass', 'print', 'raise', 'return', 'try', 'while', 'with', 'yield']



## 命名风格

选择一种命名的风格，并且严格遵守，可以是camelCase，或者snake_case，或者是其他任何的风格，最重要的是要保持一致，不管是个人开发者还是团队，保持一致的命名风格很重要，不要混合使用。

```
/* Bad */
const page_count = 5
const shouldUpdate = true

/* Good */
const pageCount = 5
const shouldUpdate = true

/* Good as well */
const page_count = 5
const should_update = true
```



## 遵守SID原则

命名应该简短、直观并且具有描述性，遵循SID原则。

- Short - 简短，避免输入太长，但是也应该注意不能简写到失去其原本的意义。
- Intuitive - 直观，并且尽可能接近自然语言。
- Descriptive - 以最有效的方式反映其作用或目的。

```
/* Bad */
const a = 5 // "a" could mean anything
const isPaginatable = a > 10 // "Paginatable" sounds extremely unnatural
const shouldPaginatize = a > 10 // Made up verbs are so much fun!

/* Good */
const postCount = 5
const hasPagination = postCount > 10
const shouldPaginate = postCount > 10 // alternatively
```



## 避免上下文重复

有时候在一段代码中可能会出现类似意义的变量定义，这个时候要避免命名的重复

```
class MenuItem {
  /* Method name duplicates the context (which is "MenuItem") */
  handleMenuItemClick = (event) => { ... }

  /* Reads nicely as `MenuItem.handleClick()` */
  handleClick = (event) => { ... }
}
```



## 尽量反映预期结果

变量或函数的命名应该能反映预期的结果

```
/* Bad */
const isEnabled = itemCount > 3
return <Button disabled={!isEnabled} />

/* Good */
const isDisabled = itemCount <= 3
return <Button disabled={isDisabled} />
```







# A/HC/LC命名模式

可以遵循A/HC/LC，即

```
prefix? + action (A) + high context (HC) + low context? (LC)
```

|         name         | prefix |    A    |   HC    |    LC    |
| :------------------: | :----: | :-----: | :-----: | :------: |
|       getUser        |        |   get   |  User   |          |
|   getUserMessages    |        |   get   |  User   | Messages |
|  handleClickOutside  |        | handle  |  Click  | Outside  |
| shouldDisplayMessage | should | Display | Message |          |


上下文的顺序可能会影响变量的含义，例如shouldUpdateComponent意味着将要更新一个组件，换一下顺序变成shouldComponentUpdate，意味着组件将做自我更新。



## Prefix前缀

前缀用来增强变量的含义



### is

描述特征或状态，通常是boolean类型

```
const color = 'blue'
const isBlue = color === 'blue' // characteristic
const isPresent = true // state

if (isBlue && isPresent) {
  console.log('Blue is present!')
}
```



### has

描述是否具有某个状态或值，通常是boolean类型

```
/* Bad */
const isProductsExist = productsCount > 0
const areProductsPresent = productsCount > 0

/* Good */
const hasProducts = productsCount > 0
```



### should

反映肯定的条件，加上特定的执行动作

```
function shouldUpdateUrl(url, expectedUrl) {
  return url !== expectedUrl
}
```



### min/max

描述边界或界限时使用

```
/**
 * Renders a random amount of posts within
 * the given min/max boundaries.
 */
function renderPosts(posts, minPosts, maxPosts) {
  return posts.slice(0, randomBetween(minPosts, maxPosts))
}
```



### prev/next

指示前一个或下一个状态

```
function fetchPosts() {
  const prevPosts = this.state.posts

  const fetchedPosts = fetch('...')
  const nextPosts = concat(prevPosts, fetchedPosts)

  this.setState({ posts: nextPosts })
}
```





## Action动作

函数名称的动词部分，是描述函数作用的最终要的部分，如：

### getXXX，表示获取数据

```
function getFruitCount() {
  return this.fruits.length
}
```



### setXXX，表示设值

```
let fruits = 0

function setFruits(nextFruits) {
  fruits = nextFruits
}

setFruits(5)
console.log(fruits) // 5
```



### resetXXX，重置数据

```
const initialFruits = 5
let fruits = initialFruits
setFruits(10)
console.log(fruits) // 10

function resetFruits() {
  fruits = initialFruits
}

resetFruits()
console.log(fruits) // 5
```



### fetchXXX，请求数据

```
function fetchPosts(postCount) {
  return fetch('https://api.dev/posts', {...})
}
```



### removeXXX，移除数据，表示从某处删除某物

```
function removeFilter(filterName, filters) {
  return filters.filter((name) => name !== filterName)
}

const selectedFilters = ['price', 'availability', 'size']
removeFilter('price', selectedFilters)
```



### deleteXXX，删除数据，表示完全清楚某些事物

```
function deletePost(id) {
  return database.find({ id }).delete()
}
```



### composeXXX，从现有数据创建新数据

```
function composePageUrl(pageName, pageId) {
  return (pageName.toLowerCase() + '-' + pageId)
}
```



### handleXXX，处理某个动作

```
function handleLinkClick() {
  console.log('Clicked a link!')
}

link.addEventListener('click', handleLinkClick)
```



## Context上下文

函数或方法通常是某些事物的动作，结合上下文，能够明确其操作的对象，或者要能反映出函数预期的数据类型。

一些特定的情况下允许省略上下文，例如在JavaScript中，filter对Array进行操作很常见，就没必要命名为filterArray了。

```
/* A pure function operating with primitives */
function filter(predicate, list) {
  return list.filter(predicate)
}

/* Function operating exactly on posts */
function getRecentPosts(posts) {
  return filter(posts, (post) => post.date === Date.now())
}
```





## Singular&Plurals 单复数

变量名称是单数还是复数，取决于值的单数还是复数。

```
/* Bad */
const friends = 'Bob'
const friend = ['Bob', 'Tony', 'Tanya']

/* Good */
const friend = 'Bob'
const friends = ['Bob', 'Tony', 'Tanya']
```





# None

常量None的特殊性，体现在他既不是0，False之类，对应的数据类型为NoneType，遵循单例模式，是唯一的， 即指向的是相同的内存地址。

因为不能创建None对象







# 编码



## ASCII 

ASCII（American Standard Code for Information Interchange，美国标准信息交换代码）是基于拉丁字母的一套电脑编码系统，主要用于显示现代英语和其他西欧语言，其最多只能用 8 位来表示（一个字节），即：2**8 = 256，所以，ASCII码最多只能表示 256 个符号。

![image-20200810090028968](variables_coding.assets/image-20200810090028968.png)



## **非 ASCII 编码**

英语用128个符号编码就够了，但是用来表示其他语言，128个符号是不够的。比如，在法语中，字母上方有注音符号，它就无法用 ASCII 码表示。于是，一些欧洲国家就决定，利用字节中闲置的最高位编入新的符号。比如，法语中的`é`的编码为130（二进制`10000010`）。这样一来，这些欧洲国家使用的编码体系，可以表示最多256个符号。

但是，这里又出现了新的问题。不同的国家有不同的字母，因此，哪怕它们都使用256个符号的编码方式，代表的字母却不一样。比如，130在法语编码中代表了`é`，在希伯来语编码中却代表了字母`Gimel` (`ג`)，在俄语编码中又会代表另一个符号。但是不管怎样，所有这些编码方式中，0--127表示的符号是一样的，不一样的只是128--255的这一段。





### 中文

至于亚洲国家的文字，使用的符号就更多了，汉字就多达10万左右。一个字节只能表示256种符号，肯定是不够的，就必须使用多个字节表达一个符号。比如，简体中文常见的编码方式是 GB2312，使用两个字节表示一个汉字，所以理论上最多可以表示 256 x 256 = 65536 个符号。

为了处理汉字，程序员设计了用于简体中文的GB2312和用于繁体中文的big5

GB2312(1980年)一共收录了7445个字符，包括6763个汉字和682个其它符号。汉字区的内码范围高字节从B0-F7，低字节从A1-FE，占用的码位是72*94=6768。其中有5个空位是D7FA-D7FE。

GB2312 支持的汉字太少。1995年的汉字扩展规范GBK1.0收录了21886个符号，它分为汉字区和图形符号区。汉字区包括21003个字符。2000年的 GB18030是取代GBK1.0的正式国家标准。该标准收录了27484个汉字，同时还收录了藏文、蒙文、维吾尔文等主要的少数民族文字。现在的PC平台必须支持GB18030，对嵌入式产品暂不作要求。所以手机、MP3一般只支持GB2312。

从ASCII、GB2312、GBK 到GB18030，这些编码方法是向下兼容的，即同一个字符在这些方案中总是有相同的编码，后面的标准支持更多的字符。在这些编码中，英文和中文可以统一地处理。区分中文编码的方法是高字节的最高位不为0。按照程序员的称呼，GB2312、GBK到GB18030都属于双字节字符集 (DBCS)。

有的中文Windows的缺省内码还是GBK，可以通过GB18030升级包升级到GB18030。不过GB18030相对GBK增加的字符，普通人是很难用到的，通常我们还是用GBK指代中文Windows内码。



 

## Unicode

世界上存在着多种编码方式，同一个二进制数字可以被解释成不同的符号。因此，要想打开一个文本文件，就必须知道它的编码方式，否则用错误的编码方式解读，就会出现乱码。为什么电子邮件常常出现乱码？就是因为发信人和收信人使用的编码方式不一样。

Unicode（统一码、万国码、单一码）是一种在计算机上使用的字符编码。Unicode 是为了解决传统的字符编码方案的局限而产生的，它为每种语言中的每个字符设定了统一并且唯一的二进制编码，规定虽有的字符和符号最少由 16 位来表示（2个字节），即：2 **16 = 65536

将世界上所有的符号都纳入其中。每一个符号都给予一个独一无二的编码，那么乱码问题就会消失。





### Unicode 的问题

需要注意的是，Unicode 只是一个符号集，它只规定了符号的二进制代码，却没有规定这个二进制代码应该如何存储。 

比如，汉字`严`的 Unicode 是十六进制数`4E25`，转换成二进制数足足有15位（`100111000100101`），也就是说，这个符号的表示至少需要2个字节。表示其他更大的符号，可能需要3个字节或者4个字节，甚至更多。

这里就有两个严重的问题，第一个问题是，如何才能区别 Unicode 和 ASCII ？计算机怎么知道三个字节表示一个符号，而不是分别表示三个符号呢？第二个问题是，我们已经知道，英文字母只用一个字节表示就够了，如果 Unicode 统一规定，每个符号用三个或四个字节表示，那么每个英文字母前都必然有二到三个字节是`0`，这对于存储来说是极大的浪费，文本文件的大小会因此大出二三倍，这是无法接受的。





### utf-8

UTF-8 就是在互联网上使用最广的一种 Unicode 的实现方式。

其他实现方式还包括 UTF-16（字符用两个字节或四个字节表示）和 UTF-32（字符用四个字节表示），不过在互联网上基本不用。

UTF-8（8-bit Unicode Transformation Format）是一种针对Unicode的可变长度字符编码，它可以使用1~4个字节表示一个符号，根据不同的符号而变化字节长度，当字符在ASCII码的范围时，就用一个字节表示，所以是兼容ASCII编码的。



UTF-8 的编码规则很简单，只有二条：

1）对于单字节的符号，字节的第一位设为`0`，后面7位为这个符号的 Unicode 码。因此对于英语字母，UTF-8 编码和 ASCII 码是相同的。

2）对于`n`字节的符号（`n > 1`），第一个字节的前`n`位都设为`1`，第`n + 1`位设为`0`，后面字节的前两位一律设为`10`。剩下的没有提及的二进制位，全部为这个符号的 Unicode 码。



#### 声明字符编码 

如果.py文件中包含中文字符（严格的说是含有非anscii字符），则需要在第一行或第二行指定编码声明：

`#-*- coding=utf-8 -*-` 或者 `# encoding=utf-8`

即文件编码为utf-8, 但py文件内的变量是Unicode



## bytes

由于Python的字符串类型是`str`，在内存中以Unicode表示，一个字符对应若干个字节。如果要在网络上传输，或者保存到磁盘上，就需要把`str`变为以字节为单位的`bytes`

Bytes类型可以是ASCII范围内的字符和其他十六进制形式字符数据，但不能用中文等非ASCII字符表示



```
encode(encoding='utf-8', errors='strict') -> bytes
```

```
bytes.decode(encoding='utf-8', errors='strict') -> str
等价于
string.encode()
```



可以使用b前缀定义

```
只能使用基本的ASCII形式 b"abc9"
使用16进制表示 b"\x41\x61"
```



### 切片

```
In [56]: b'xuxuehua.com'[-10:-4:1]
Out[56]: b'xuehua'
```



## bytearray

字节数组

有序的可变序列

```
bytearray.decode(encoding='utf-8', errors='strict') -> str
```





### 切片

```
In [57]: bytearray(b'xuxuehua.com')[-10:-4:1]
Out[57]: bytearray(b'xuehua')
```





###  Python3 bytes string 转换

**Python 3版本中，字符串是以Unicode编码的**

str能表示Unicode 字符集中的所有字符



str是一种高层对象。bytes是一种底层的东西。

如果需要在高层处理，那么就全部使用str。

在与外部通信的时候，比如保存文件，socket通信，使用bytes。



![image-20200810090341221](variables_coding.assets/image-20200810090341221.png)







## 字符编码与转码

在py3中encode,在转码的同时还会把string 变成bytes类型，decode在解码的同时还会把bytes变回string





* Python2 图解

<img src="variables_coding.assets/image-20200810090354915.png" alt="image-20200810090354915" style="zoom:50%;" />





### 默认系统编码为ascii (Python2)



#### string(中文) 转 unicode 

```
>>> import sys
>>> sys.getdefaultencoding()
'ascii'
>>> '你好'.decode('gbk')
u'\u6d63\u72b2\u30bd'
>>> print(u'\u6d63\u72b2\u30bd'.encode('gbk'))
你好
```



#### string(英文) 转 unicode 

无论是什么平台什么编码格式都能转换为unicode格式

- 默认编码集为ascii



Python 2

```
#-*- coding:utf-8 -*-

import sys
print('system default encoding: ', sys.getdefaultencoding())

s = "你好"
s_to_unicode = s.decode('utf-8')
print('s_to_unicode', type(s_to_unicode))
print(s_to_unicode)

s_to_gbk = s_to_unicode.encode('gbk')
print(s_to_gbk)


gbk_to_utf8 = s_to_gbk.decode('gbk').encode('utf-8')
print(gbk_to_utf8)
>>>
('system default encoding: ', 'ascii')
('s_to_unicode', <type 'unicode'>)
你好
���
你好
```





### GBK 转 utf-8

GBK 先decode 变成Unicode，然后再encode变成utf-8

```

```





### utf-8 转 GBK

utf-8 先decode 变成unicode，然后再encode 变成GBK

默认编码集为utf-8

```

```







### 默认系统编码为utf-8 (Python3)

```
In [19]: import sys

In [20]: sys.getdefaultencoding()
Out[20]: 'utf-8'
```



#### str 转 bytes

```
In [29]: 'ABC'.encode('ascii')
Out[29]: b'ABC'

In [30]: 'ABC'.encode('utf-8')
Out[30]: b'ABC'

In [31]: '你好'.encode('ascii')
---------------------------------------------------------------------------
UnicodeEncodeError                        Traceback (most recent call last)
<ipython-input-31-539ec64f1dbf> in <module>()
----> 1 '你好'.encode('ascii')

UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)

In [32]: '你好'.encode('utf-8')
Out[32]: b'\xe4\xbd\xa0\xe5\xa5\xbd'
```

> 纯英文的`str`可以用`ASCII`编码为`bytes`，内容是一样的，含有中文的`str`可以用`UTF-8`编码为`bytes`。含有中文的`str`无法用`ASCII`编码，因为中文编码的范围超过了`ASCII`编码的范围，Python会报错。



#### bytes 转 str

```
In [33]: b'ABC'.decode('ascii')
Out[33]: 'ABC'

In [34]: b'ABC'.decode('utf-8')
Out[34]: 'ABC'

In [35]: b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8')
Out[35]: '中文'

In [36]: b'\xe4\xb8\xad\xe6\x96\x87'.decode('ascii')
---------------------------------------------------------------------------
UnicodeDecodeError                        Traceback (most recent call last)
<ipython-input-36-5b063d2fad44> in <module>()
----> 1 b'\xe4\xb8\xad\xe6\x96\x87'.decode('ascii')

UnicodeDecodeError: 'ascii' codec can't decode byte 0xe4 in position 0: ordinal not in range(128)
```



如果`bytes`中只有一小部分无效的字节，可以传入`errors='ignore'`忽略错误的字节：

```
In [37]: b'\xe4\xb8\xad\xff'.decode('utf-8')
---------------------------------------------------------------------------
UnicodeDecodeError                        Traceback (most recent call last)
<ipython-input-37-cd8de1b11dcd> in <module>()
----> 1 b'\xe4\xb8\xad\xff'.decode('utf-8')

UnicodeDecodeError: 'utf-8' codec can't decode byte 0xff in position 3: invalid start byte

In [38]: b'\xe4\xb8\xad\xff'.decode('utf-8', errors='ignore')
Out[38]: '中'
```



#### unicode 转 gbk

```
In [41]: s = u'中国'

In [42]: s.encode('gbk')
Out[42]: b'\xd6\xd0\xb9\xfa'
```



#### gbk 转 unicode 

```
In [47]: s = u'中国'

In [48]: s_utf8 =  s.encode('UTF-8')

In [49]: s_utf8.decode('utf-8')
Out[49]: '中国'
```



#### Unicode码对应的中文

如果type(text) is bytes，那么

```text
text.decode('unicode_escape')
```

如果type(text) is str，那么

```text
text.encode('latin-1').decode('unicode_escape')
```





# Appendix

https://github.com/kettanaito/naming-cheatsheet