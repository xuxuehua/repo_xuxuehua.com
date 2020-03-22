---
title: "date"
date: 2018-09-03 14:49
---

[TOC]

# date 



## -d  转换timestamp 

转换timestamp 

```
$ date -d @1267619929
Wed Mar  3 12:38:49 UTC 2010
```



## %N 随机数

```
$ date +%N | cut -c 1-8
10842178
```



##-Iseconds

It works for amazon linux1 

```
# date -Isecond
2020-03-17T14:09:46+0000
```

## 

## 显示当前时间

```
$ date "+%Y-%m-%d %H:%M:%S"
2020-02-29 17:39:30
```

