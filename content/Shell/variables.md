---
title: "variables 变量"
date: 2020-03-22 19:48
---
[toc]





# 变量

变量可被设置为当前shell的局部变量，或是环境变量。如果您的shell脚本不需要调用其他脚本，其中的变量通常设置为脚本内的局部变量



变量定义中“=”前后不能有空格，命名规则就和其它语言一样了

对shell而言，默认所有的变量值都是字符串，所以默认时不能进行算数计算



## 环境变量

作用域为当前shell进程及其子进程

```
export VARNAME=VALUE
or
VARNAME=VALUE   export VARNAME
```



查看当前shell中的环境变量

```
printenv
env

\0     means a null character
\n     换行
export
```



## 本地变量

```
VARNAME=VALUE        # 整个bash进程有效
```



## 局部变量

默认变量是全局的，在函数中变量local指定为局部变量，避免污染其他作用域。

```
local   VARNAME＝VALUE         # 只对当前代码段有效
```



## 位置变量

```
$1     第一个位置变量
$2     第二个位置变量
```



```
./filetest.sh  /etc/fstab /etc/inittab

$1     脚本中引用此变量，表示参数 /etc/fstab
$2     对应/etc/inittab
……依次类推
```





## 获取变量

要获取变量的值，在美元符后跟变量名即可。shell会对双引号内的美元符后的变量执行变量扩展，单引号中的美元符则不会被执行变量扩展。

```
name="John Doe" or declare name="John Doe"   # local variable
```



## 全局变量

使用export提升变量为全局



```
export NAME="John Doe"    # global variable
```

```
echo "$name" "$NAME"      # extract the value
```



## 只读变量 readonly

```
$ readonly x=1

$ echo $x
1

$ x=2
-su: x: readonly variable

$ unset x
-su: unset: x: cannot unset: readonly variable
```



## 引用变量

```
${ }   大括号可以省略，但是有时可以，下面的不可以
```



```
ANIMAL＝pig

echo "The are some ${ANIMAL}s" 
```

>  这里使用弱引用双引号，完成变量替换



## 撤销变量

```
unset  VARNAME
```



## 查看变量

```
set
```

查看当前shell中所有变量，包括本地变量

```
-x     turn on the shell tracing and verbosity
-v     turn off the shell tracing and verbosity
```



# internal_variables



## RANDOM 随机数

随机字符串

```
# echo $RANDOM | md5sum | cut -c 1-8
024cbfdc
```

随机数字

```
# echo $RANDOM | cksum | cut -c 1-8
20613431
```





## HISTTIMEFORMAT 历史命令格式

```
$ export HISTTIMEFORMAT='%F %T  '

      1  2013-06-09 10:40:12   cat /etc/issue
      2  2013-06-09 10:40:12   clear
      3  2013-06-09 10:40:12   find /etc -name *.conf
      4  2013-06-09 10:40:12   clear
      5  2013-06-09 10:40:12   history
      6  2013-06-09 10:40:12   PS1='\e[1;35m[\u@\h \w]$ \e[m '
      7  2013-06-09 10:40:12   PS1="\e[0;32m[\u@\h \W]$ \e[m "
      8  2013-06-09 10:40:12   PS1="\u@\h:\w [\j]$ "
      9  2013-06-09 10:40:12   ping google.com
     10  2013-06-09 10:40:12   echo $PS1
```



## HISTCONTROL 控制

### ignoredups 过滤重复

```
$ export HISTCONTROL=ignoredups
```



### unset export 关闭定义

```
$ unset export HISTCONTROL
```





## 系统内置变量

```
$HOME, $PWD, $SHELL, $USER 等
```



```
$ echo $USER
rxu
```





# 特殊变量



## `$n`

n为数字

表示第几个位置参数



## `$#`  参数的个数

获取所有参数个数，常用于循环

类似于

```
public static void main() {
		$1 = args[0]
		$# = args.length
}
```



## `$*` 参数列表

获取所有参数，会把其看成一个整体





## `$@` 参数列表

获取所有参数，但是区分对待每一个参数





## `$?` 状态返回值

上一次命令执行的返回状态

0表示执行正确，非0表示错误

1-255   错误执行     1，2，127 已经被系统预留

通过echo $? 来测试结果的准确性