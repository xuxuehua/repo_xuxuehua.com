---
title: "awk"
date: 2019-01-20 17:43
---


[TOC]



# awk

```
Usage: awk [POSIX or GNU style options] -f progfile [--] file ...
Usage: awk [POSIX or GNU style options] [--] 'program' file ...
```



## -F 分隔符

```
# cat /etc/passwd | awk -F : '/^ubuntu/{print $1}'
ubuntu
```



## -v 变量

赋值一个用户定义的变量



## NF 浏览域个数

浏览记录的域的个数，即切割后列的个数

```
$ echo $a
1, 2, 3, 4

$ echo $a | awk '{print $NF}'
4
```







## NR 已读的记录数

NF = Number of Fields

```
awk 'NF==1' file_name
```



取反

```
awk 'NR!=1' file_name
```



## BEGIN 开头行





## END 最后一行

```
awk "END{print $0}" file_name
```







## OFS 输出字段分隔符

Output Field Operator

默认为空格

```
awk -F' ' 'BEGIN{OFS="--"}{print $1,$2,$3}' num.txt
```

> 使用`--` 进行分割





## ORS 输出记录分隔符

默认为换行符

```
awk -F' ' 'BEGIN{OFS="--";ORS="#"}{print $1,$2,$3}' num.txt
```

> 换行符变为#



