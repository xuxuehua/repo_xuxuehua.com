---
title: "cut"
date: 2020-03-22 20:07
---
[toc]



# cut

在文件中负责剪切数据使用



## -f 列号



## -d 分隔符

取出当前系统上所有用户的shell，要求每种shell只显示一次，并且按照顺序进行排列

```
cut -d: -f7 /etc/passwd | sort -u
```



## -c 具体字符



