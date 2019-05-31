---
title: "uniq"
date: 2019-05-30 01:26
---


[TOC]



# uniq



## 集合操作

假设有a、b两个文本文件，文件本身已经去除了重复内容。下面是效率最高的方法，可以处理任何 体积的文件，甚至几个G的文件。(Sort对内存没有要求，但也许你需要用 -T 参数。)

```
cat a b | sort | uniq > c   # c 是a和b的合集
cat a b | sort | uniq -d > c   # c 是a和b的交集
cat a b b | sort | uniq -u > c   # c 是a和b的不同
```

