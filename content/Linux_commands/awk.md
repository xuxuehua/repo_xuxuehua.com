---
title: "awk"
date: 2019-01-20 17:43
---


[TOC]



# awk





## NR==1 第一行

NF = Number of Fields

```
awk 'NF==1' file_name
```



取反

```
awk 'NR!=1' file_name
```





## END 最后一行

```
awk "END{print $0}" file_name
```





## \$NF 最后一列

```
$ echo $a
1, 2, 3, 4

$ echo $a | awk '{print $NF}'
4
```



