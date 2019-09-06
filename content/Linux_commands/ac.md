---
title: "ac linux_debugger"
date: 2018-11-05 21:37
---


[TOC]


# ac

打印用户连接时间

print statistics about users' connect time

显示所有用户连接总时间


## -d, --daily-totals

显示每天所有用户连接总时间

Print totals for each day rather than just one big total at the end.  The output looks like this:
Jul  3  total     1.17
Jul  4  total     2.10
Jul  5  total     8.23
Jul  6  total     2.10
Jul  7  total     0.30



## silence      

显示指定用户连接时间

```
ac -d silence   #显示指定用户每天连接时间
```



## -p, --individual-totals

显示每个用户连接时间

Print time totals for each user in addition to the usual everything-lumped-into-one value.  It looks like:
bob       8.06
goff      0.60
maley     7.37
root      0.12
total    16.15

