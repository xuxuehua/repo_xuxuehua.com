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





## NF 最后一列

```
$ echo $a
1, 2, 3, 4

$ echo $a | awk '{print $NF}'
4
```



## OFS 输出字段分隔符

Output Field Operator

默认为空格

```
awk -F' ' 'BEGIN{OFS="--"}{print $1,$2,$3}' num.txt
```

> 使用`--` 进行分割





## ORS 输出记录分隔符

默认为换行符

```
awk -F' ' 'BEGIN{OFS="--";ORS="#"}{print $1,$2,$3}' num.txt
```

> 换行符变为#



