---
title: "calculation 运算"
date: 2018-11-10 21:18
---


[TOC]


# 数字运算



## 四则运算 + - * /

expr 运算符之间要有空格

```
#!/bin/bash

a=3
b=5

val=`expr $a + $b`
echo "a+b=$val"

val=`expr $a - $b`
echo "a-b=$val"

val=`expr $a \* $b`
echo "a*b=$val"

val=`expr $a / $b`
echo "a/b=$val"

>>>
a+b=8
a-b=-2
a*b=15
a/b=0
```





## 其它运算

### % 求余

```
#!/bin/bash

a=3
b=5

val=`expr $a % $b`
echo "a%b=$val"

>>>
a%b=3
```



### == 相等

```
#!/bin/bash

a=5
b=5

if [ $a == $b ]; then
    echo "$a is equal to $b"
fi

>>>
5 is equal to 5
```



### != 不相等

```
#!/bin/bash

a=3
b=5

if [ $a != $b ]; then
    echo "$a is not equal to $b"
fi

>>>
3 is not equal to 5
```





## 整数运算符

### -eq 

两数相等返回true

```
$ if [ 10 -eq 10 ]; then echo "yes" ; fi
yes
```



* 条件测试的表达式

```
INT1=63
INT2=77
     1. [ expression ] 一定要有空格
         [ $INT1 -eq $INT2 ]
     2. [ [ expression ] ]
          [ [ $INT1 -eq $INT2 ] ]
     3. test expression
          test $INT1 -eq $INT2条件测试的表达式
```



### -ne 

两数不等返回true

```
if [ $UID -ne 0 ]  ; then
	echo Not root user, Please run as root.
else
	echo Root user.
fi
```



### -gt

左侧大于右侧返回true



### -lt

左侧小雨右侧返回true



### -ge

左侧大于等于右侧返回true



### -le

左侧小于等于右侧返回true





# 字符串运算

## = 

两个字符串相等返回true



## !=

两个字符串不相等返回true



## -z

字符串长度为0返回true， 测试指定字符串是否不空

```
[ -z `hostname` ] || [ `hostname` == ‘local’ -o  `hostname` == ‘(none)’  ] && hostname $HOSTNAME
```



## -n

字符串长度不为0返回true



# 文件运算

## -e 存在

filename or file path 测试文件是否存在，包括目录

```
#!/bin/bash
if [ ! -e $FILE ] ;     then
    echo “No $FILE file.”
    exit 8    /只要不是0就可以
fi
```

```
[ -e /etc/inittab ]
```



## -f 文件

filename or file path 测试是否为普通文件



## -s

file existence along with file size greater than 0



## -d 目录

filename or file path 测试指定路径是否／为目录



## -r   

测试指定文件对当前用户而言，是否可读



## -w  

测试指定文件对当前用户而言，是否可写



## -x   

测试指定文件对当前用户而言，是否可执行

```
[ -x /etc/rc.d/rc.sysint ]
```





# 组合条件运算

## -a 与关系

```
if [ $# -gt 1 -a $# -le 3 ]
if [ $# -gt 1 ] && [ $# -le 3 ]
```



## -o 或关系



## ! 非关系

