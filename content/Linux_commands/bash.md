---
title: "bash"
date: 2018-09-05 15:31
---

[TOC]

# bash

Bash(GNU Bourne-Again Shell)是许多Linux平台的内定Shell，事实上，还有许多传统UNIX上用的Shell，像tcsh、csh、ash、bsh、ksh等等



## -c

c字符串：若用-c参数，则bash从字符串中读入命令，如果字符串后还有变量就被设定为从$0开始的位置参数。

## -i 

若用-i参数，则bash是交互的。



## -n 语法检查

可以对脚本进行检查



## -s

若用-s参数，则bash从标准输入中读入命令（在执行完-c带的命令之后。）直到输入exit。



## -v 跟踪命令

可以用-v跟踪脚本里的每个命令的执行



## -x 跟踪命令并扩展信息

可以用-x跟踪脚本里的每个命令的执行，并附加扩充信息





## - 

单一的`-`符号表明参数执行完毕，并且屏蔽此后所跟参数，后面的所有变量都被看作是文件名。

## norc

如果bash是交互的，则不执行个人初始化文件：-/.bashrc，如果bash作为sh来运行，这个参数缺省是关闭的。

## noprofile

不执行系统范围的启动文件/etc/profile也不执行个人的启动文件-/.bash_profile，-/.bash_login或-/.profile，缺省情况下，bash作为登录的shell时以这些文件作为启动文件。

## -refile

如果bash是交互的，则以此文件作为bash的启动文件。替代-/.bashrc。

## version

在bash开始时显示此bash的版本号。

## quiet

不显示版本号和其他信息，这是缺省值。

## login

激活bash，伪装为登录shell。

## nobraceexpansion

不执行大括号扩展。

## nolineediting

在交互状态下不使用GNU的readline库去读取命令。即取消了命令行编辑功能。

## posix

改变bash的行为，使其符合Posix 1003.2规定的标准。

