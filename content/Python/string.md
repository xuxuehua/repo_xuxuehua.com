---
title: "string"
date: 2018-08-18 09:48
collection: 基本变量类型
---



[TOC]

#字符串与文本操作 (python-string-files)

python2中的字符串是byte的有序序列
python3中的字符串是unicode的有序序列
字符串是不可变的
字符串支持下标于切片



## 特点

改变字符串，往往需要O(n)的时间复杂度，其中，n为新字符串的长度。



```
s = ''
for n in range(0, 100000):
	  s += str(n)
```

> 自从Python2.5开始，每次处理字符串的拼接操作时(str1 += str2)，Python首先会检测str1还有没有其他的引用。如果没有的话，就会尝试原地扩充字符串buffer的大小，而不是重新分配一块内存来创建新的字符串并拷贝。这样的话，上述例子中的时间复杂度就仅为O(n)了。







## 非特殊处理 r前缀

在字符串前面加上r或者R前缀，表示该字符串不做特殊处理

```
r'\r \t \n'
```

> 里面所有的转译自负当作普通字符对待



​	

## 元素访问 下标



```python
In [1]: s = 'I love Python'

In [2]: s[0]
Out[2]: 'I'

In [3]: s[-1]
Out[3]: 'n'

In [4]: s[3:8]
Out[4]: 'ove P'

In [5]: s[::-1]
Out[5]: 'nohtyP evol I'


```



### 线性结构

```python
In [6]: s
Out[6]: 'I love Python'

In [7]: for i in s:
   ...:     print(i)
   ...:     
I
 
l
o
v
e
 
P
y
t
h
o
n
```



### 不可变类型

字符串是不可变的 (元组也不可变)

```python
In [8]: s
Out[8]: 'I love Python'

In [9]: s[1]
Out[9]: ' '

In [10]: s[1] = '_'
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-10-8ab2d301e01f> in <module>()
----> 1 s[1] = '_'

TypeError: 'str' object does not support item assignment


```



#### 变量引用

对象被多个变量所指向或者引用，由于int 和string 类型是不可变的，所以新创建的变量为新的，不会影响其他变量的值

变量的赋值，只是表示让变量指向了某个对象，并不表示拷贝对象给变量;而一个对象，可以被多个变量所指向。

对于不可变对象(字符串，整型，元祖等等)，所有指向该对象的变量的值总是一样的，也不会改变。但是通过某些操作(+=等等)更新不可变对象的值时，会返回一个新的对象

变量可以被删除，但是对象无法被删除



```
In [5]: a = 1                                                                                                                                                                                         

In [6]: b = a                                                                                                                                                                                         

In [7]: a = a + 1                                                                                                                                                                                     

In [8]: a                                                                                                                                                                                             
Out[8]: 2

In [9]: b                                                                                                                                                                                             
Out[9]: 1
```





## 字符串的格式化

print style format
format 方法

Print Style Format
`template % tunple 
template % dict`


```python
In [11]: 'I love %s' % ('python',)
Out[11]: 'I love python'

In [12]: 'I love %(name)s' % {'name':'python'}
Out[12]: 'I love python'
```



Print Styple Format Conversation

| 符号 | 说明                              |
| ---- | --------------------------------- |
| %d   | 整数                              |
| %i   | 整数                              |
| %o   | 八进制数                          |
| %x   | 小写16进制整数                    |
| %X   | 大写16进制整数                    |
| %f   | 浮点数                            |
| %F   | 浮点数                            |
| %e   | 小写科学计数法                    |
| %E   | 大写科学计数法                    |
| %c   | 字符，接收unicode编码或者单字符串 |
| %a   | 字符串，使用ascii函数转换         |
| %r   | 字符串，使用repr函数转换          |
| %s   | 字符串，使用str函数转换           |
| ％s  | 字符 %                            |





### %s 推荐的方法

```
In [31]: 'Hello %(name)s!' % {'name': 'Rick'}                                                                                                                              Out[31]: 'Hello Rick!'
```





#### unsupported format character '?' (0xa)

结尾没有s导致该报错

```
ValueError: unsupported format character '?' (0xa) at index 447
```





### %d

```python
In [13]: '%d' % 3.4
Out[13]: '3'
```



### %E

```python
In [14]: '%E' % 0.00000000001
Out[14]: '1.000000E-11'

In [15]: '%E' % 1000000000000
Out[15]: '1.000000E+12'
```



### %g

```python
In [16]: '%g' % 0.001
Out[16]: '0.001'

In [17]: '%g' % 0.00000001
Out[17]: '1e-08'
```





### flags

| Flag | 说明                                                      |
| ---- | --------------------------------------------------------- |
| #    | #代表一个数字，指定宽度，如果宽度不够，会根据一下进行填充 |
| 0    | 使用0填充，仅适用于数字                                   |
| .    | 使用空格填充，默认为行                                    |
| -    | 右边使用空格填充                                          |
| +    | 填充之前增加+，仅仅对于正数                               |



```python
In [20]: '%10d' % 1
Out[20]: '         1'

In [21]: '%010d' % 1
Out[21]: '0000000001'
```





### format 

```
template.format(*args, **kwargs) (1) (2) (3) (4)
```

> template 使用{} 表示变量
> {}或{\d+} 使用*args 按顺序填充
> {key} 使用**kwargs 按key填充
> Format String Syntax




```python
In [24]: '{0}, {name}'.format('Hello', name='World')
Out[24]: 'Hello, World'
```

```
In [44]: '{server}{1}:{0}'.format(8888, '192.168.1.1', server='Web Server: ')
Out[44]: 'Web Server: 192.168.1.1:8888'
```

```
In [45]: '{0}*{1}={2:<2}'.format(3,2,2*3)
Out[45]: '3*2=6 '

In [46]: '{0}*{1}={2:<02}'.format(3,2,2*3)
Out[46]: '3*2=60'

In [47]: '{0}*{1}={2:>02}'.format(3,2,2*3)
Out[47]: '3*2=06'

In [48]: '{:^30}'.format('centered')
Out[48]: '           centered           '

In [49]: '{:*^30}'.format('centered')
Out[49]: '***********centered***********'
```

```
In [50]: 'int:{0:d}; hex:{0:x}; oct:{0:o}; bin:{0:b}'.format(42)
Out[50]: 'int:42; hex:2a; oct:52; bin:101010'
```

```
In [52]: '{:02X}{:02X}{:02X}{:02X}'.format(*[192,168,1,1])
Out[52]: 'C0A80101'
```



### format_map

```
In [4]: s = 'Rick Xu {age} years old'

In [5]: s.format_map({'age': 29})
Out[5]: 'Rick Xu 29 years old'
```



### f-strings   Python3.6新的字符串格式化

```
f ' <text> { <expression> <optional !s, !r, or !a> <optional : format specifier> } <text> ... '
```



* 基本用法

```
>>> name = "Tom"
>>> age = 3
>>> f"His name is {name}, he's {age} years old."
>>> "His name is Tom, he's 3 years old."
```



* 支持表达式

```
# 数学运算
>>> f'He will be { age+1 } years old next year.'
>>> 'He will be 4 years old next year.'

# 对象操作
>>> spurs = {"Guard": "Parker", "Forward": "Duncan"}
>>> f"The {len(spurs)} players are: {spurs['Guard']} the guard, and {spurs['Forward']} the forward."
>>> 'The 2 players are: Parker the guard, and Duncan the forward.'

>>> f'Numbers from 1-10 are {[_ for _ in range(1, 11)]}'
>>> 'Numbers from 1-10 are [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]'
```



* 排版格式

```
>>> def show_players():
    print(f"{'Position':^10}{'Name':^10}")
    for player in spurs:
        print(f"{player:^10}{spurs[player]:^10}")
>>> show_players()
 Position    Name   
  Guard     Parker  
 Forward    Duncan
```



* 数字操作

```
# 小数精度
>>> PI = 3.141592653
>>> f"Pi is {PI:.2f}"
>>> 'Pi is 3.14'

# 进制转换
>>> f'int: 31, hex: {31:x}, oct: {31:o}'
'int: 31, hex: 1f, oct: 37'
```



* 与原始字符串联合使用

```
>>> fr'hello\nworld'
'hello\\nworld'
```

## 

* `{}`内不能包含反斜杠`\`

```
f'His name is {\'Tom\'}'
SyntaxError: f-string expression part cannot include a backslash

# 而应该使用不同的引号，或使用三引号。
>>> f"His name is {'Tom'}"
'His name is Tom'
```



* 不能与`'u'`联合使用

`'u'`是为了与Python2.7兼容的，而Python2.7不会支持f-strings，因此与`'u'`联合使用不会有任何效果。



* 如何插入大括号？

```
>>> f"{{ {10 * 8} }}"
'{ 80 }'
>>> f"{{ 10 * 8 }}"
'{ 10 * 8 }'
```



* 与`str.format()`的一点不同

使用`str.format()`，非数字索引将自动转化为字符串，而f-strings则不会。

```
>>> "Guard is {spurs[Guard]}".format(spurs=spurs)
'Guard is Parker'

>>> f"Guard is {spurs[Guard]}"
Traceback (most recent call last):
  File "<pyshell#34>", line 1, in <module>
    f"Guard is {spurs[Guard]}"
NameError: name 'Guard' is not defined

>>> f"Guard is {spurs['Guard']}"
'Guard is Parker'
```







## 字符串连接 

### join

将s中的元素，以str为分割符，合并成为一个字符串

```python
In [26]: L
Out[26]: ['I', 'love', 'Python']

In [27]: ''.join(L)
Out[27]: 'IlovePython'

In [28]: '_'.join(L)
Out[28]: 'I_love_Python'

In [30]: L
Out[30]: ['I', 'love', 'Python']

In [31]: for i in L:
    ...:     ret += i
    ...:     ret += ''
    ...: ret
    ...: 
Out[31]: 'IlovePython'
```



### \+ 连接

```
In [27]: 'Hello' + 'World'
Out[27]: 'HelloWorld'
```



## 字符串分割

### split

sep 指分隔符，默认为空格

maxsplit 指分割次数， -1表示遍历整个字符串

```
split(sep=None, maxsplit=-1)
```





分割成列表

```python
In [32]: s = 'I Love Python'

In [33]: s.split()
Out[33]: ['I', 'Love', 'Python']

In [34]: s.split('o')
Out[34]: ['I L', 've Pyth', 'n']

In [37]: S
Out[37]: 'root:*:0:0:System Administrator:/var/root:/bin/sh'

In [38]: S.split(':')
Out[38]: ['root', '*', '0', '0', 'System Administrator', '/var/root', '/bin/sh']

In [39]: S.split(':')[0]
Out[39]: 'root'

In [40]: username, _ = S.split(':', 1)

In [41]: username
Out[41]: 'root'

In [42]: new_s = 'URL:http://xuxuehua.com'

In [43]: k, v = new_s.split(':', 1)

In [44]: k, v
Out[44]: ('URL', 'http://xuxuehua.com')
```



#### 多种分隔符分割

```
In [80]: import re

In [81]: s
Out[81]: 'ab;cd|efg|hi,jkl|mn\topq;rst,uvw\txyz'

In [82]: re.split(r'[,;\t|]+', s)
Out[82]: ['ab', 'cd', 'efg', 'hi', 'jkl', 'mn', 'opq', 'rst', 'uvw', 'xyz']
```









### rsplit

```
rsplit(sep=None, maxsplit=-1)
```

> sep 指分隔符，默认为空格
>
> maxsplit 指分割次数， -1表示遍历整个字符串



```python
In [45]: new_s
Out[45]: 'URL:http://xuxuehua.com'

In [46]: new_s.rsplit(':')
Out[46]: ['URL', 'http', '//xuxuehua.com']

In [47]: new_s.rsplit(':', 1)
Out[47]: ['URL:http', '//xuxuehua.com']
```



### splitlines

```
splitlines([keepends])
```

> 按照行来切分字符串
>
> keepends表示是否保留行分隔符
>
> 行分隔符包括: \n, \r\n, \r 等



```python
In [51]: s
Out[51]: '\nI Love Python\n I love Linux either\n'

In [52]: s.splitlines()
Out[52]: ['', 'I Love Python', ' I love Linux either']

In [53]: s.splitlines(True)
Out[53]: ['\n', 'I Love Python\n', ' I love Linux either\n']

In [54]: s.splitlines(False)
Out[54]: ['', 'I Love Python', ' I love Linux either']
```

### partition 

```
partition(sep) 
-> (head, sep, tail)
```

> 从左至右，遇到分隔符就把字符串分隔成: 头，分隔符，尾三元组
>
> 若没有分隔符，返回头和两个空元组
>
> sep为分隔符，必须指定



```python
In [58]: s
Out[58]: 'root:*:0:0:System Administrator:/var/root:/bin/sh'

In [59]: s.partition(':')
Out[59]: ('root', ':', '*:0:0:System Administrator:/var/root:/bin/sh')

In [60]: h, _, t = s.partition(':')

In [61]: t
Out[61]: '*:0:0:System Administrator:/var/root:/bin/sh'

In [62]: h, _, t = t.partition(':')

In [63]: t
Out[63]: '0:0:System Administrator:/var/root:/bin/sh'
```



### rpartition 

```
rpartition(sep) 
-> (head, sep, tail)
```

> 从右至左，遇到分隔符就把字符串分隔成: 头，分隔符，尾三元组
>
> 若没有分隔符，返回头和两个空元组
>
> sep为分隔符，必须指定



```python
In [64]: s
Out[64]: 'root:*:0:0:System Administrator:/var/root:/bin/sh'

In [65]: s.rpartition(':')
Out[65]: ('root:*:0:0:System Administrator:/var/root', ':', '/bin/sh')

In [66]: h, _, t = s.rpartition(':')

In [67]: t
Out[67]: '/bin/sh'
```

## 字符串修改

(字符串是不可修改的，这里是返回一个新的字符串)

### replace 

```
replace(old, new[, count]) -> str
```

负数操作会替换所有

```python
In [146]: s 
Out[146]: 'I love Python'

In [147]: s.replace('o', 'x')
Out[147]: 'I lxve Pythxn'

In [148]: s.replace('o', 'x', 1)
Out[148]: 'I lxve Python'

In [149]: s.replace('o', 'x', -1)
Out[149]: 'I lxve Pythxn'
```



```
In [2]: s                                                                                                                                             
Out[2]: 'I love Python'

In [3]: s.replace('love', 'hate').replace('Python', 'C#')                                                                                             
Out[3]: 'I hate C#'π
```



### capitalize

首单词的首字母大写

```python
In [68]: s = 'i love python'

In [69]: s.capitalize()
Out[69]: 'I love python'
```

### titile

每个单词首字母大写

```python
In [70]: s
Out[70]: 'i love python'

In [71]: s.title()
Out[71]: 'I Love Python'
```

### upper

```python
In [72]: s
Out[72]: 'i love python'

In [73]: s.upper()
Out[73]: 'I LOVE PYTHON'
```

### lower

```python
In [77]: S
Out[77]: 'I love python'

In [78]: S.lower()
Out[78]: 'i love python'
```

### swapcase

互换大小写

```python
In [82]: S
Out[82]: 'I Love Python'

In [83]: S.swapcase()
Out[83]: 'i lOVE pYTHON'
```

### center

```
center(width, [fillchar])
```

> width 打印宽度
>
> fillchar 填充字符



```python
In [87]: s
Out[87]: 'Python'

In [88]: s.center(20)
Out[88]: '       Python       '

In [89]: s.center(20, '*')
Out[89]: '*******Python*******'
```

### ljust

```
ljust(width, [fillchar])
```



```python
In [91]: s
Out[91]: 'Python'

In [92]: s.ljust(20)
Out[92]: 'Python              '

In [93]: s.ljust(20, '*')
Out[93]: 'Python**************'
```



### rjust

```
rjust(width, [fillchar])
```



```python
In [96]: s
Out[96]: 'Python'

In [97]: s.rjust(20)
Out[97]: '              Python'

In [98]: s.rjust(20, '*')
Out[98]: '**************Python'
```

### zfill

```
zfill(width)
```

> width 打印宽度，居右，左边用0填充



```python
In [103]: s
Out[103]: 'Python'

In [104]: s.zfill(10)
Out[104]: '0000Python'

In [105]: s.zfill(11)
Out[105]: '00000Python'
```

### strip 

常用于处理文本, 去除空格和去掉首尾的str字符串换行符

```
strip([chars]) -> str
```

> chars 不指定，默认为空白字符



```python
In [25]: a = '\n abc \n'

In [26]: a.strip()
Out[26]: 'abc'
```



### rstrip

表示只去掉尾部的str字符串。

```python
In [113]: s
Out[113]: 'abc \n  '

In [114]: s.rstrip()
Out[114]: 'abc'
```



### lstrip

表示只去掉开头的str字符串

```python
In [24]: a = '\n abc \n'

In [25]: a.lstrip()
Out[25]: 'abc \n'
```



### expandtabs
```
In [1]: s = 'Rick \t Xu'

In [2]: s.expandtabs(tabsize=20)
Out[2]: 'Rick                 Xu'
```



## 字符串判断

### len

```
In [41]: s
Out[41]: 'this is Rick'

In [42]: len(s)
Out[42]: 12
```



### startswith

```python
In [124]: s
Out[124]: 'I love Python'

In [125]: s.startswith('I')
Out[125]: True

In [126]: s.startswith('l')
Out[126]: False
```

### endswith

```python
In [127]: s
Out[127]: 'I love Python'

In [128]: s.endswith('n')
Out[128]: True

In [129]: s.endswith('o')
Out[129]: False

```

### count

```
count(sub[, start[, end]]) -> int
```



```python
In [130]: s
Out[130]: 'I love Python'

In [131]: s.count('l')
Out[131]: 1

In [132]: s.count('o')
Out[132]: 2
```

### index 

```
index(sub[, start[, end]]) -> int
```

从左开始，查找sub在str中第一次出现的位置。如果str中不包含sub，出错

```python
In [135]: s
Out[135]: 'I love Python'

In [136]: s.index('o')
Out[136]: 3

In [145]: s.index('x')
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-145-a677ba2c36a5> in <module>()
----> 1 s.index('x')

ValueError: substring not found
```



### rindex

```
rindex(sub[, start[, end]]) -> int
```

从右开始，查找sub在str中第一次出现的位置。如果str中不包含sub，返回 -1

```python
In [137]: s
Out[137]: 'I love Python'

In [138]: s.rindex('o')
Out[138]: 11
```



### find  

```
find(sub[, start[, end]]) -> int
```

从左开始，查找sub在str中第一次出现的位置。如果str中不包含sub，返回-1

```python
In [139]: s
Out[139]: 'I love Python'

In [140]: s.find('l')
Out[140]: 2

In [141]: s.find('o')
Out[141]: 3

In [142]: s.find('Python')
Out[142]: 7

In [143]: s.find('xuxu')
Out[143]: -1
```



### rfind

```
rfind(sub[, start[, end]]) -> int
```

从右开始，查找sub在str中第一次出现的位置。如果str中不包含sub，返回 -1

```
In [2]: s = 'I love Python'

In [3]: s.rfind('I')
Out[3]: 0

In [4]: s.rfind('o')
Out[4]: 11

In [5]: s.rfind('xxh')
Out[5]: -1

```









### isalnum

如果所有的字符都是字母或数字

```
In [6]: 'ab23'.isalnum()
Out[6]: True
```

### isalpha

如果所有的字符都是字母

```
In [7]: 'abA'.isalpha()
Out[7]: True
```

### isdecimal

```
In [8]: '1A'.isdecimal()
Out[8]: False
```

### isdigit

如果所有的字符都是数字

```
In [9]: '1A'.isdigit()
Out[9]: False
```

### isidentifier

```
In [10]: 'a1A'.isidentifier()
Out[10]: True

In [11]: '1A'.isidentifier()
Out[11]: False
```

### isnumeric

```
In [12]: '11A'.isnumeric()
Out[12]: False
```

### istitle

如果所有的词的首字母都是大写

```
In [13]: 'My name'.istitle()
Out[13]: False

In [14]: 'My Name'.istitle()
Out[14]: True
```



### isupper

如果所有的字符都是大写字母

```
In [15]: 'aA'.isupper()
Out[15]: False
```



### islower

如果所有的字符都是小写字母

```
In [8]: s = 'abc'

In [9]: s.islower()
Out[9]: True
```



### isspace

如果所有的字符都是空格

```
In [6]: s = " " 

In [7]: s.isspace()
Out[7]: True
```



## STR 与 BYTES

Python3 中严格区分了文本和二进制数据
Python2 中并没有严格区分
文本数据使用str类型，底层实现是unicode
二进制数据使用bytes类型，底层是bytes
str使用encode 方法转化为bytes
bytes使用decode方法转化为str



* python2

```python
In [1]: s = '中文'

In [2]: s
Out[2]: '\xe4\xb8\xad\xe6\x96\x87'

In [3]: s.encode()
---------------------------------------------------------------------------
UnicodeDecodeError                        Traceback (most recent call last)
<ipython-input-2-1194a7b23c15> in <module>()
----> 1 s.encode()

UnicodeDecodeError: 'ascii' codec can't decode byte 0xe4 in position 0: ordinal not in range(128)
```

* python3

```python
In [5]: s
Out[5]: '中文'

In [6]: s.encode()
Out[6]: b'\xe4\xb8\xad\xe6\x96\x87'

In [7]: s.encode('utf-8')
Out[7]: b'\xe4\xb8\xad\xe6\x96\x87'

In [8]: b = s.encode()

In [9]: b.decode()
Out[9]: '中文'
```







## 转换

### string to dict 

Starting in Python 2.6 you can use the built-in [`ast.literal_eval`](https://docs.python.org/library/ast.html#ast.literal_eval):

```py
>>> import ast
>>> ast.literal_eval("{'muffin' : 'lolz', 'foo' : 'kitty'}")
{'muffin': 'lolz', 'foo': 'kitty'}
```

This is safer than using `eval`. As its own docs say:

```
>>> help(ast.literal_eval)
Help on function literal_eval in module ast:

literal_eval(node_or_string)
    Safely evaluate an expression node or a string containing a Python
    expression.  The string or node provided may only consist of the following
    Python literal structures: strings, numbers, tuples, lists, dicts, booleans,
    and None.
```

For example:

```py
>>> eval("shutil.rmtree('mongo')")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<string>", line 1, in <module>
  File "/opt/Python-2.6.1/lib/python2.6/shutil.py", line 208, in rmtree
    onerror(os.listdir, path, sys.exc_info())
  File "/opt/Python-2.6.1/lib/python2.6/shutil.py", line 206, in rmtree
    names = os.listdir(path)
OSError: [Errno 2] No such file or directory: 'mongo'
>>> ast.literal_eval("shutil.rmtree('mongo')")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/opt/Python-2.6.1/lib/python2.6/ast.py", line 68, in literal_eval
    return _convert(node_or_string)
  File "/opt/Python-2.6.1/lib/python2.6/ast.py", line 67, in _convert
    raise ValueError('malformed string')
ValueError: malformed string
```



# 优化

```
s = ''
for n in range(0, 100000):
    s += str(n)
```



```
l = []
for n in range(0, 100000):
    l.append(str(n))
s = ' '.join(l)
```



直观上看似乎第二种方法的复杂度高一倍，但实际运行了下，第二种方法效率略高，当调高到50万的时候第二种的效率比第一种高出两倍以上。

join的运行时间只占了很小一部分，不到10%，复杂度推测大致是O(logn)。综上，list的扩充速度优于str，且join的速度在大数面前可忽略。



推荐方法

```
s = " ".join(map(str, range(0, 10000))) 
```

