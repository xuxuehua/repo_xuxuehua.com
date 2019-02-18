---
title: "date"
date: 2018-09-03 14:49
---

[TOC]

# date 



## -d 

转换timestamp 

```
$ date -d @1267619929
Wed Mar  3 12:38:49 UTC 2010
```



## %N

随机数

```
$ date +%N | cut -c 1-8
10842178
```

