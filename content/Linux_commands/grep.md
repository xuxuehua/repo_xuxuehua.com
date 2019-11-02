---
title: "grep"
date: 2018-08-28 09:46
---

[TOC]

# grep 

grep命令是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹 配的行打印出来。grep全称是Global Regular Expression Print，表示全局正则表达式版本，它的使用权限是所有用户。

grep的工作方式是这样的，它在一个或多个文件中搜索字符串模板。如果模板包括空格，则必须被引用，模板后的所有字符串被看作文件名。搜索的结果被送到标准输出，不影响原文件内容。

grep可用于shell脚本，因为grep通过返回一个状态值来说明搜索的状态，如果模板搜索成功，则返回0，如果搜索不成功，则返回1，如果搜索的文件不存在，则返回2。我们利用这些返回值就可进行一些自动化的文本处理工作。



## 命令格式

grep [option] pattern file



## -a 不要忽略二进制

-a      This processes binary file as a text file



## -A -B -C 匹配行的信息

-A +num     显示被匹配之后，和后面number 行的信息

-B +num     显示被匹配之后，和前面number 行的信息

-C +num     显示被匹配之后，和前后number行的信息



## -b   显示字符的编号

--byte-offset   #在显示符合样式的那一行之前，标示出该行第一个字符的编号。



## -c 计算匹配行数

-c           This prints the count  of matching lines of each input file

```
[root@ ~]# ps -ef|grep  svn
root      9618  8011  0 18:05 pts/0    00:00:00 grep --color=auto svn
[root@ ~]# ps -ef|grep -c svn
1
```



## -C 显示行数

--context=<显示行数>或-<显示行数>   #除了显示符合样式的那一行之外，并显示该行之前后的内容。  



## -d  查找目录

--directories=<动作>   #当指定要查找的是目录而非文件时，必须使用这项参数，否则grep指令将回报信息并停止动作。



## -e 指定样式

-e PATTERN      This specifies multiple search pattern 

指定字符串做为查找文件内容的样式。

   

## -E 扩展正则表达式

-E     extended regexp

等同于egrep 命令



行首不为#的行作为输出

```
grep -E '^[^#]'  files
```



## -f 文件内容对比

-f FILE       This obtains a patterns from a file, one per line 

输出test.txt文件中含有从test2.txt文件中读取出的关键词的内容行

```
[root@localhost test]# cat test.txt 
hnlinux
peida.cnblogs.com
ubuntu
ubuntu linux
redhat
Redhat
linuxmint

[root@localhost test]# cat test2.txt 
linux
Redhat

[root@localhost test]# cat test.txt | grep -f test2.txt
hnlinux
ubuntu linux
Redhat
linuxmint

```





## -F   固定字符串

--fixed-regexp   #将样式视为固定字符串的列表。 



## -G   普通的表示法

--basic-regexp   #将样式视为普通的表示法来使用。   



## -h   不标示文件名称

--no-filename   #在显示符合样式的那一行之前，不标示该行所属的文件名称。   



## -H   标示文件名称

--with-filename   #在显示符合样式的那一行之前，表示该行所属的文件名称。  





## -i 不敏感大小写

-i     ignore 大小写 The enforces case insensitive match in both pattern and input files

​    

## -l   列出文件内容符合

--file-with-matches   #列出文件内容符合指定的样式的文件名称。   





## -L   列出文件内容不符合

--files-without-match   #列出文件内容不符合指定的样式的文件名称。   



## -n 行号

-n      This prefixes each matched line along with a line number



输出test.txt文件中含有从test2.txt文件中读取出的关键词的内容行，并显示每一行的行号

```
[root@localhost test]# cat test.txt 
hnlinux
peida.cnblogs.com
ubuntu
ubuntu linux
redhat
Redhat
linuxmint

[root@localhost test]# cat test2.txt 
linux
Redhat

[root@localhost test]# cat test.txt | grep -nf test2.txt
1:hnlinux
4:ubuntu linux
6:Redhat
7:linuxmint


```



## -o 显示被匹配

-o     只显示被匹配到的字符串



抓取mac 网卡地址

```
ifconfig en0 | grep -o "inet .*" | cut -d ' ' -f 2
```



抓取Linux 网卡地址

```
ifconfig eth0 | grep -o "inet addr:[0-9.]{1.}"
```







## -q 静默模式

-q     quiet, Do not write anything to standard output.





## -r 目录遍历，不含软连接

-r      Reads all the files in a directory recursively, excluding resolving of symbolic links unless explicitly specified as an input file 



## -R  目录遍历，含软连接

-R      This reads all the files in a directory recursively and resolving symbolic if any





## -s 不显示错误信息

-s     Don’t print error messages



## -v 显示没有被匹配

-v     显示没有被模式匹配到的行 

grep不显示本身进程

```
[root@vultr ~]# ps -ef | grep host
root     10017  8011  0 18:20 pts/0    00:00:00 grep --color=auto host
[root@vultr ~]# ps -ef | grep host | grep -v 'grep'
[root@vultr ~]#
```





## -V   显示版本信息

--version   #显示版本信息。  



## -w   只显示全字符合的列

--word-regexp   #只显示全字符合的列。   

## -x    只显示全列符合的列

--line-regexp   #只显示全列符合的列。   

## -y   与“-i”参数相同

此参数的效果和指定“-i”参数相同。

## --colour 加上颜色

 \- - colour 加上颜色





## --exclude-dir 排除目录

```
grep -r --exclude-dir=dev --exclude-dir=sys --exclude-dir=proc PATTERN /path/to/search
```

通过find 实现

```
find . -not -path "*/.svn*" -not -type d -exec grep -ni "myfunc" {} \; -print

find /dir \( -name node_modules -prune \) -o -name "*.sh" -exec grep --color -Hn "your text to find" {} 2>/dev/null \;
```



## 规则表达式

| 定义 | 解释 | 实例 |
| --- | ---- | ---- |
| ^ | #锚定行的开始 | 如：'^grep'匹配所有以grep开头的行。 |
| | | |
| | | |
| | | |
| | | |
| | | |
| | | |
| | | |
| | | |



​    

\$  #锚定行的结束 如：'grep$'匹配所有以grep结尾的行。    

.  #匹配一个非换行符的字符 如：'gr.p'匹配gr后接一个任意字符，然后是p。    

\*  #匹配零个或多个先前字符 如：'*grep'匹配所有一个或多个空格后紧跟grep的行。    

.*   #一起用代表任意字符。   

[]   #匹配一个指定范围内的字符，如'[Gg]rep'匹配Grep和grep。    

[^]  #匹配一个不在指定范围内的字符，如：'[^A-FH-Z]rep'匹配不包含A-R和T-Z的一个字母开头，紧跟rep的行。    

\(..\)  #标记匹配字符，如'\(love\)'，love被标记为1。    

\<      #锚定单词的开始，如:'\<grep'匹配包含以grep开头的单词的行。    

\>      #锚定单词的结束，如'grep\>'匹配包含以grep结尾的单词的行。    

x\{m\}  #重复字符x，m次，如：'0\{5\}'匹配包含5个o的行。    

x\{m,\}  #重复字符x,至少m次，如：'o\{5,\}'匹配至少有5个o的行。    

x\{m,n\}  #重复字符x，至少m次，不多于n次，如：'o\{5,10\}'匹配5--10个o的行。   

\w    #匹配文字和数字字符，也就是[A-Za-z0-9]，如：'G\w*p'匹配以G后跟零个或多个文字或数字字符，然后是p。   

\W    #\w的反置形式，匹配一个或多个非单词字符，如点号句号等。   

\b    #单词锁定符，如: '\bgrep\b'只匹配grep。  



POSIX字符:

为了在不同国家的字符编码中保持一至，POSIX(The Portable Operating System Interface)增加了特殊的字符类，如[:alnum:]是[A-Za-z0-9]的另一个写法。要把它们放到[]号内才能成为正则表达式，如[A- Za-z0-9]或[[:alnum:]]。在linux下的grep除fgrep外，都支持POSIX的字符类。



[:alnum:]    #文字数字字符   

[:alpha:]    #文字字符   

[:digit:]    #数字字符   

[:graph:]    #非空字符（非空格、控制字符）   

[:lower:]    #小写字符   

[:cntrl:]    #控制字符   

[:print:]    #非空字符（包括空格）   

[:punct:]    #标点符号   

[:space:]    #所有空白字符（新行，空格，制表符）   

[:upper:]    #大写字符   

[:xdigit:]   #十六进制数字（0-9，a-f，A-F）  















