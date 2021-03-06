---
title: "re"
date: 2018-08-26 19:30
---

[TOC]

# Regular Expression

正则表达式主要功能是从字符串string中通过特定模式pattern， 搜索到想要的内容



## 分类

### BRE

基本正则，grep， sed， vim等常用

### ERE

扩展正则， egrep (grep -E)， sed -r 等

### PCRE

Perl Compatible Regular Expressions

几乎所有高级语言都是PCRE或其变种，Python 自1.6使用SRE，其可认为是PCRE的子集







## 常用符号

### 单个字符

| 表达式 | 描述                                                 |
| ------ | ---------------------------------------------------- |
| .      | 任意的一个字符, 除了换行符\n                         |
| a\|b   | 字符a或者字符b                                       |
| [afg]  | a或者f或者g的一个字符 ， 内部为或关系                |
| [0-4]  | 0-4范围内的一个字符                                  |
| [a-f]  | a-f 范围内的一个字符                                 |
| [^m]   | 不是m的一个字符                                      |
| a[cf]d | 字符集操作，中括号内为或的关系, 若内部添加^,表示取反 |
| \b     | 匹配单词边界                                         |
| \B     | 不匹配单词边界                                       |
| \s     | 小写字母 一个空白字符                                |
| \S     | 大写字母 一个非空白字符                              |
| \d     | 小写字母 等同于`[0-9]`                               |
| \D     | 大写字母 等同于 `[^0-9]`                             |
| \w     | 单词字符 等同于` [0-9a-zA-Z_]`                       |
| \W     | 非单词字符 等同于 `[^0-9a-zA-Z_]`                    |



### 重复

| 表达式| 描述|
|-|-|
| * | 重复 >=0 次， |
| + | 重复 >=1 次|
| ? | 重复 0 或者 1次， 非贪婪匹配 |
| {m} | 重复m次。a{4}相当于aaaa|
| {m, n} | 重复m到n次。a{2, 5} 表示a重复2到5次|



### 位置

边界匹配

| 表达式  |       描述       |
| :-----: | :--------------: |
|    ^    |  字符串的开始位  |
|    $    | 字符串的结尾位置 |
| ^ab.*c$ |    匹配abeec     |



```
'.'     默认匹配除\n之外的任意一个字符，若指定flag DOTALL,则匹配任意字符，包括换行
'^'     匹配字符开头，若指定flags MULTILINE,这种也可以匹配上(r"^a","\nabc\neee",flags=re.MULTILINE)
'$'     匹配字符结尾，或e.search("foo$","bfoo\nsdfsf",flags=re.MULTILINE).group()也可以
'*'     匹配*号前的字符0次或多次，re.findall("ab*","cabb3abcbbac")  结果为['abb', 'ab', 'a']
'+'     匹配前一个字符1次或多次，re.findall("ab+","ab+cd+abb+bba") 结果['ab', 'abb']
'?'     匹配前一个字符1次或0次
'{m}'   匹配前一个字符m次
'{n,m}' 匹配前一个字符n到m次，re.findall("ab{1,3}","abb abc abbcbbb") 结果'abb', 'ab', 'abb']
'|'     匹配|左或|右的字符，re.search("abc|ABC","ABCBabcCD").group() 结果'ABC'
'(...)' 分组匹配，re.search("(abc){2}a(123|456)c", "abcabca456c").group() 结果 abcabca456c， 内部为且关系
 
 
'\A'    只从字符开头匹配，re.search("\Aabc","alexabc") 是匹配不到的
'\Z'    匹配字符结尾，同$
'\d'    匹配数字0-9
'\D'    匹配非数字
'\w'    匹配[A-Za-z0-9]
'\W'    匹配非[A-Za-z0-9]
's'     匹配空白字符、\t、\n、\r , re.search("\s+","ab\tc1\n3").group() 结果 '\t'

```



## 引擎选项

### IgnoreCase 

匹配是忽略大小写

```
re.I
re.IGNORECASE
```



### Singleline

单行模式，可以匹配所有字符，包括\n

```
re.S
re.DOTALL
```



### Multiline

多行模式，^行首， $行尾

```
re.M
re.MULTILINE
```



### IgnorePatternWhitespace

忽略表达式中的空白字符，如果使用空白字符需转译， #可以用来注释

```
re.X
re.VERBOSE
```



## 常用方法

### re.compile 编译

使用 compile 函数将正则表达式的字符串形式编译为一个 Pattern 对象

```
re.compile(pattern, flags=0)
```

设定flags，编译模式，返回正则对象regex

正则被编译后，保存下来，下次使用同样的pattern时候，不需再次编译









### re.match 匹配

返回值为对象， 匹配成功就结束，即匹配一次就结束

```
re.match(pattern, string, flags=0)
```

从头开始检查字符串是否符合正则表达式。必须从字符串的第一个字符开始就相符

```
In [8]: ret = re.match('^Rick', "Rickwithyou")

In [9]: print(ret)
<re.Match object; span=(0, 4), match='Rick'>

In [10]: ret.group()
Out[10]: 'Rick'
```





### re.search 搜索

匹配不到返回None。 匹配成功就结束，即匹配一次就结束

```
re.search(pattern, string, flags=0)
```

从开头所有直到第一个匹配，返回match对象

第一个参数是正则表达式

第二个参数是找到符合要求的字符串

m.group() 方法可以查看搜索到的结果。

```
In [47]: re.search("\D+", '1234$@a')
Out[47]: <_sre.SRE_Match object; span=(4, 7), match='$@a'>

In [48]: re.search('(?P<id>[0-9]+)(?P<name>[a-zA-Z]+)', 'abcd1234EFGh!@#$').group('id')
Out[48]: '1234'
```

```
import re
m = re.search('[0-9]', 'abcde4fg')
print(m.group(0))
>>>
4
```





### re.fullmatch 整个匹配

整个字符串和正则匹配, 返回结果是列表

```
re.fullmatch(pattern, string, flags=0)
```



```
In [5]: print(re.fullmatch('seg_by_app_usage', 'seg_by_app_usage_cross'))
None
```

 





### re.findall 匹配所有，返回列表 （常用）

根据正则表达式所有字符串，将所有符合的子字符串放在一个表list中返回





### re.split 分割

根据正则表达式分割字字符串，将分割后的所有子字符串放在一个表list中返回

```
In [49]: re.split('[0-9]', 'abcd1234EFGH!@#$')
Out[49]: ['abcd', '', '', '', 'EFGH!@#$']
```



### re.finditer 匹配所有，返回迭代器

对整个字符串，匹配所有项目，返回迭代器



### re.sub 匹配替换

在string中利用正则变换pattern进行搜索，对于搜索到的字符串，用另一字符串replacement替换，返回替换后的字符串
str = re.sub(pattern, replacement, string, count=0, flags=0)



```
In [52]: re.sub('[0-9]+', '|', 'abcd1234EFGH!@#$')
Out[52]: 'abcd|EFGH!@#$'

In [55]: re.sub('[0-9]+', '|', 'abcd1234EFGH!@#$1111', count=1)
Out[55]: 'abcd|EFGH!@#$1111'
```

count=0 表示无限次匹配

count=1 表示最大匹配次数为1次





去除标点和换行符

```
re.sub(r'[^\w ]', ' ', text)
```





replacement 可以是一个函数

```
In [43]: language = 'PythonC#\njavaPHPC#'

In [44]: def convert(value):
    ...:     matched = value.group()
    ...:     return '!!' + matched + '!!'
    ...:

In [45]: re.sub('C#', convert, language)
Out[45]: 'Python!!C#!!\njavaPHP!!C#!!'
```



```
In [48]: s = 'A8C3721D86'

In [49]: def convert(value):
    ...:     matched = value.group()
    ...:     if int(matched) >= 6:
    ...:         return '9'
    ...:     else:
    ...:         return '0'
    ...:

In [50]: re.sub('\d', convert, s)
Out[50]: 'A9C0900D99'
```





## 返回控制

### group 分组

获取分组匹配

m.group(number) 的方法来查询群组。

m.group(0) 表示整个表达式的搜索结果

m.group(1) 表示第一个群组信息

m.group(nam) 以命名的方式返回分组

m.groupdict() 返回所有命名的分组

小括号内部是且关系



```
import re
m = re.search("output_(\d{4})", "output_1989.txt")
print(m.group(1))
print(m.group(0))
>>>
1989
output_1989
```



```
In [64]: s = 'Life is short, I use Python'

In [65]: r = re.search('Life(.*)Python', s)

In [66]: r.group(1)
Out[66]: ' is short, I use '
```

```
In [67]: s = 'Life is short, I use Python'

In [68]: re.findall('Life(.*)Python', s)
Out[68]: [' is short, I use ']
```



```
In [69]: s = 'Life is short, I use Python, I love Python'

In [70]: r = re.search('Life(.*)Python(.*)Python', s)

In [71]: r.group(0)
Out[71]: 'Life is short, I use Python, I love Python'

In [72]: r.group(1)
Out[72]: ' is short, I use '

In [73]: r.group(2)
Out[73]: ', I love '

In [74]: r.group(0, 1, 2)
Out[74]:
('Life is short, I use Python, I love Python',
 ' is short, I use ',
 ', I love ')
 
In [75]: r.groups()
Out[75]: (' is short, I use ', ', I love ')
```









## 匹配模式

### re.I(re.IGNORECASE)

忽略大小写（括号内是完整写法，下同）

```
re.findall("c#", 1.txt, re.I)
```





### M(MULTILINE)

多行模式，改变'^'和'$'的行为（参见上图）



### S(DOTALL)

点任意匹配模式，改变'.'的行为, 即可以匹配到\n

```
In [40]: language = 'PythonC#\njavaPHP'

In [41]: import re

In [42]: re.findall('c#.{1}', language, re.I | re.S)  # | 表示且关系，即两个都需要支持
Out[42]: ['C#\n']
```



## 反斜杠

反斜杠的困扰 与大多数编程语言相同，正则表达式里使用"\"作为转义字符，这就可能造成反斜杠困扰。假如你需要匹配文本中的字符"\"，那么使用编程语言表示的正则表达式里将需要4个反斜杠"\\\\"：前两个和后两个分别用于在编程语言里转义成反斜杠，转换成两个反斜杠后再在正则表达式里转义成一个反斜杠。Python里的原生字符串很好地解决了这个问题，这个例子中的正则表达式可以使用r"\\"表示。同样，匹配一个数字的"\\d"可以写成r"\d"。有了原生字符串，你再也不用担心是不是漏写了反斜杠，写出来的表达式也更直观。 

