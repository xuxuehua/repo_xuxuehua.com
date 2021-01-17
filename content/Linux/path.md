---
title: "path 环境变量"
date: 2018-11-25 19:32
---


[TOC]

# PATH 环境变量

PATH，使用冒号分割的，执行命令时，shell会按照次序搜索命令是否已经在PATH环境变量中的某一个位置

 通常$PATH is defined in /etc/environment or /etc/profile or ~/.bashrc

 添加新的环境变量

```
export PATH=“$PATH:/home/user/bin”

or

PATH=“$PATH:/home/user/bin”
export PATH
```



## HISTFILE 历史

历史文件

```
# echo $HISTFILE
/root/.bash_history
```

关闭历史

```
unset HISTFILE
```



## HISTSIZE 历史大小

默认为1000

```
# echo $HISTSIZE
1000
```



```
HISTSIZE=0
```



## HISTTIMEFORMAT 历史格式

```
HISTTIMEFORMAT="%F %T `whoami` "
export HISTTIMEFORMAT
```

