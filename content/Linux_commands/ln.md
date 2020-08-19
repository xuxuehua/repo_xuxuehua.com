---
title: "ln"
date: 2020-08-15 22:01
---
[toc]





# ln

链接生成命令

默认创建硬链接，但不可将硬连接指向目录

硬连接除了文件名和源文件不一样，其余所有信息都是一样的



## -s 软链接

软链接owner和group具有全部的操作权限

```
ln -s source_file target_file
```

