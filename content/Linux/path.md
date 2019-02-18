---
title: "path 环境变量"
date: 2018-11-25 19:32
---


[TOC]

# PATH 环境变量





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

