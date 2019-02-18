---
title: "internal_variables 内部变量"
date: 2018-11-08 13:07
---


[TOC]


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

