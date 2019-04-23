---
title: "find"
date: 2018-10-10 11:44
---


[TOC]


# find

## Usage

```
find [-H] [-L] [-P] [-Olevel] [-D help|tree|search|stat|rates|opt|exec] [path...] [expression]
```



## Options

### 逻辑判断



#### -a 与操作



#### -o 或操作

当前目录及子目录下查找所有以 `.txt` 或 `.pdf` 结尾的文件

```
find . \( -name "*.txt" -o -name "*.pdf" \)

或

find . -name "*.txt" -o -name "*.pdf" 
```



#### ！否定

找出/home下不是以.txt结尾的文件

```
find /home ! -name "*.txt"
```



### -amin

查找在指定时间曾被存取过的文件或目录，单位以分钟计算

搜索访问时间超过10分钟的所有文件

```
find . -type f -amin +10
```



### -anewer<参考文件或目录>

查找其存取时间较指定文件或目录的存取时间更接近现在的文件或目录


### -atime 

查找在指定时间曾被存取过的文件或目录，单位以24小时计算

搜索最近七天内被访问过的所有文件

```
find . -type f -atime -7
```

搜索恰好在七天前被访问过的所有文件

```
find . -type f -atime 7
```

搜索超过七天内被访问过的所有文件

```
find . -type f -atime +7
```



### -cmin<分钟>

查找在指定时间之时被更改过的文件或目录


### -cnewer<参考文件或目录>查找其更改时间较指定文件或目录的更改时间更接近现在的文件或目录；
-ctime<24小时数>

查找在指定时间之时被更改的文件或目录，单位以24小时计算


### -daystart

从本日开始计算时间



### -delete 删除

删除当前目录下所有.txt文件

```
find . -type f -name "*.txt" -delete
```




### -depth

从指定目录下最深层的子目录开始查找


### -empty

寻找文件大小为0 Byte的文件，或目录下没有任何子目录或文件的空目录



要列出所有长度为零的文件

```
find . -empty
```




### -exec 执行指令

假设find指令的回传值为True，就执行该指令



找出当前目录下所有root的文件，并把所有权更改为用户tom

```
find .-type f -user root -exec chown tom {} \;
```

>  上例中，**{}** 用于与**-exec**选项结合使用来匹配所有文件，然后会被替换为相应的文件名。



查找当前目录下所有.txt文件并把他们拼接起来写入到all.txt文件中

```
find . -type f -name "*.txt" -exec cat {} \;> all.txt
```

将30天前的.log文件移动到old目录中

```
find . -type f -mtime +30 -name "*.log" -exec cp {} old \;
```

找出当前目录下所有.txt文件并以“File:文件名”的形式打印出来

```
find . -type f -name "*.txt" -exec printf "File: %s\n" {} \;
```

因为单行命令中-exec参数中无法使用多个命令，以下方法可以实现在-exec之后接受多条命令

```
-exec ./text.sh {} \;
```



### -false

将find指令的回传值皆设为False


### -fls<列表文件>

此参数的效果和指定“-ls”参数类似，但会把结果保存为指定的列表文件


### -follow

排除符号连接


### -fprint<列表文件>

此参数的效果和指定“-print”参数类似，但会把结果保存成指定的列表文件


### -fprint0<列表文件>

此参数的效果和指定“-print0”参数类似，但会把结果保存成指定的列表文件


### -fprintf<列表文件><输出格式>

此参数的效果和指定“-printf”参数类似，但会把结果保存成指定的列表文件


### -fstype<文件系统类型>

只寻找该文件系统类型下的文件或目录



### -prune 跳出指定目录

查找当前目录或者子目录下所有.txt文件，但是跳过子目录sk

```
find . -path "./sk" -prune -o -name "*.txt" -print
```




### -gid<群组识别码>

查找符合指定之群组识别码的文件或目录


### -group<群组名称>

查找符合指定之群组名称的文件或目录


### -help或--help

在线帮助


### -ilname<范本样式>

此参数的效果和指定“-lname”参数类似，但忽略字符大小写的差别


### -iname<范本样式>

此参数的效果和指定“-name”参数类似，但忽略字符大小写的差别


### -inum<inode编号>

查找符合指定的inode编号的文件或目录


### -ipath<范本样式>

此参数的效果和指定“-path”参数类似，但忽略字符大小写的差别

### -iregex

此参数的效果和指定“-regexe”参数类似，但忽略字符大小写的差别



忽略大小写

```
find . -iregex ".*\(\.txt\|\.pdf\)$"
```


### -links<连接数目>

查找符合指定的硬连接数目的文件或目录


### -iname 

指定字符串作为寻找符号连接的范本样式



忽略大小写

```
find /home -iname "*.txt"
```


### -ls

假设find指令的回传值为Ture，就将文件或目录名称列出到标准输出


### -maxdepth 

设置最大目录层级

向下最大深度限制为3

```
find . -maxdepth 3 -type f
```


### -mindepth 

设置最小目录层级

搜索出深度距离当前目录至少2个子目录的所有文件

```
find . -mindepth 2 -type f
```




### -mmin<分钟>

查找在指定时间曾被更改过的文件或目录，单位以分钟计算


### -mount

此参数的效果和指定“-xdev”相同


### -mtime<24小时数>

查找在指定时间曾被更改过的文件或目录，单位以24小时计算

To find all files modified in the last 24 hours (last full day) in a particular specific directory and its sub-directories:

```sh
find /directory_path -mtime -1 -ls
```

>  The `-` before `1` is important - it means anything changed one day or less ago. A `+` before `1`would instead mean anything changed at least one day ago, while having nothing before the `1`would have meant it was changed exacted one day ago, no more, no less.




### -name 指定字符串

指定字符串作为寻找文件或目录的范本样式



在`/home`目录下查找以.txt结尾的文件名

```
find /home -name "*.txt"
```




### -newer 

查找其更改时间较指定文件或目录的更改时间更接近现在的文件或目录



找出比[file](http://man.linuxde.net/file).log修改时间更长的所有文件

```
find . -type f -newer file.log
```




### -nogroup

找出不属于本地主机群组识别码的文件或目录


### -noleaf

不去考虑目录至少需拥有两个硬连接存在


### -nouser

找出不属于本地主机用户识别码的文件或目录




### -ok 执行指令

此参数的效果和指定“-exec”类似，但在执行指令之前会先询问用户，若回答“y”或“Y”，则放弃执行命令



找出自己家目录下所有的.txt文件并删除

```
find $HOME/. -name "*.txt" -ok rm {} \;
```

>  上例中，**-ok**和**-exec**行为一样，不过它会给出提示，是否执行相应的操作。




### -path 路径

匹配文件路径或者文件

```
find /usr/ -path "*local*"
```




### -perm 权限

查找符合指定的权限数值的文件或目录

当前目录下搜索出权限为777的文件

```
find . -type f -perm 777
```

找出当前目录下权限不是644的[php](http://man.linuxde.net/php)文件

```
find . -type f -name "*.php" ! -perm 644
```



### -print

假设find指令的回传值为Ture，就将文件或目录名称列出到标准输出。格式为每列一个名称，每个名称前皆有“./”字符串


### -print0

假设find指令的回传值为Ture，就将文件或目录名称列出到标准输出。格式为全部的名称皆在同一行


### -printf<输出格式>

假设find指令的回传值为Ture，就将文件或目录名称列出到标准输出。格式可以自行指定


### -prune 排除

不寻找字符串作为寻找文件或目录的范本样式



if you want to exclude the `misc` directory just add a `-path ./misc -prune -o` to your find command:

```sh
find . -path ./misc -prune -o -name '*.txt' -print
```

Here is an example with multiple directories:

```sh
find . -type d \( -path dir1 -o -path dir2 -o -path dir3 \) -prune -o -print
```

Here we exclude dir1, dir2 and dir3, since in `find` expressions it is an action, that acts on the criteria `-path dir1 -o -path dir2 -o -path dir3` (if dir1 or dir2 or dir3), ANDed with `type -d`. Further action is `-o print`, just print.

[](https://nagios.stg.fwmrm.net/thruk/#cgi-bin/status.cgi?hidesearch=0&hidetop=&style=detail&dfl_s0_type=host&dfl_s0_op=%3D&dfl_s0_value=stgcore-new.stg&dfl_s1_type=host&dfl_s1_op=%3D&dfl_s1_value=STGcore.stg)

### -regex 正则表达式

基于正则表达式匹配文件路径

```
find . -regex ".*\(\.txt\|\.pdf\)$"
```


### -size 文件大小

查找符合指定的文件大小的文件



文件大小单元：

- **b** —— 块（512字节）
- **c** —— 字节
- **w** —— 字（2字节）
- **k** —— 千字节
- **M** —— 兆字节
- **G** —— 吉字节

搜索大于10KB的文件

```
find . -type f -size +10k
```

搜索小于10KB的文件

```
find . -type f -size -10k
```

搜索等于10KB的文件

```
find . -type f -size 10k
```




### -true

将find指令的回传值皆设为True


### -type 文件类型

只寻找符合指定的文件类型的文件

类型参数列表：

- **f** 普通文件
- **l** 符号连接
- **d** 目录
- **c** 字符设备
- **b** 块设备
- **s** 套接字
- **p** Fifo






### -uid<用户识别码>

查找符合指定的用户识别码的文件或目录


### -used<日数>

查找文件或目录被更改之后在指定时间曾被存取过的文件或目录，单位以日计算


### -user 拥有者名称

查找符和指定的拥有者名称的文件或目录

找出当前目录用户tom拥有的所有文件

```
find . -type f -user tom
```

找出当前目录用户组sunk拥有的所有文件

```
find . -type f -group sunk
```


### -version或——version

显示版本信息


### -xdev

将范围局限在先行的文件系统中


### -xtype<文件类型>

此参数的效果和指定“-type”参数类似，差别在于它针对符号连接检查



