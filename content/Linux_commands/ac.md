---
title: "ac linux_debugger"
date: 2018-11-05 21:37
---


[TOC]


# ac

打印用户连接时间

print statistics about users' connect time



## -d, --daily-totals

​              Print totals for each day rather than just one big total at the end.  The output looks like this:
​                      Jul  3  total     1.17
​                      Jul  4  total     2.10
​                      Jul  5  total     8.23
​                      Jul  6  total     2.10
​                      Jul  7  total     0.30



## -p, --individual-totals

​              Print time totals for each user in addition to the usual everything-lumped-into-one value.  It looks like:
​                      bob       8.06
​                      goff      0.60
​                      maley     7.37
​                      root      0.12
​                      total    16.15