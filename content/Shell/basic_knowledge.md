---
title: "basic_knowledge"
date: 2018-10-18 10:11
---


[TOC]


# basic_knowledge



## 开头解释器

开头加解释器：#!/bin/bash



## 语法缩进

使用四个空格；多加注释说明



## 命名建议规则

变量名大写、局部变量小写，函数名小写，名字体现出实际作用



## 变量

变量可被设置为当前shell的局部变量，或是环境变量。如果您的shell脚本不需要调用其他脚本，其中的变量通常设置为脚本内的局部变量



变量定义中“=”前后不能有空格，命名规则就和其它语言一样了



### 局部变量

默认变量是全局的，在函数中变量local指定为局部变量，避免污染其他作用域。



### 获取变量

要获取变量的值，在美元符后跟变量名即可。shell会对双引号内的美元符后的变量执行变量扩展，单引号中的美元符则不会被执行变量扩展。

```
name="John Doe" or declare name="John Doe"   # local variable
```



```
export NAME="John Doe"    # global variable
```

```
echo "$name" "$NAME"      # extract the value
```





### 系统内置变量

```
$HOME, $PWD, $SHELL, $USER 等
```



```
$ echo $USER
rxu
```



### 只读变量 readonly

```

```



## 脚本调试

set -e 遇到执行非0时退出脚本，set-x 打印执行过程。





