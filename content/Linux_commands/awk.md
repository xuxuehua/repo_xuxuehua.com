---
title: "awk"
date: 2019-01-20 17:43
---


[TOC]



# awk



## \$NF 最后一列

```
$ echo $a
1, 2, 3, 4

$ echo $a | awk '{print $NF}'
4
```



