---
title: "dirname"
date: 2018-09-12 13:17
---


[TOC]


# dirname

用于取得脚本文件所在目录，然后将当前目录切换过去。



## 脚本参数 \$0



使用 \$0 可以获取到路径，但不一定是绝对路径，实际上， \$0 是代表传递给 bash 这些的第一个参数。

```
$ bash ./mytest.sh              # $0= ./mytest.sh
$ bash ./shell/mytest.sh            # $0= ./shell/mytest.sh
$ bash /home/lcd/shell/mytest.sh    # $0= /home/lcd/shell/mytest.sh
```





