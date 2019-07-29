---
title: "sqoop 数据导入导出"
date: 2019-07-25 08:45
---
[TOC]



# Sqoop

Sqoop是一个数据库批量导入导出工具，可以将关系数据库的数据批量导入到Hadoop，也可以将Hadoop的数据导出到关系数据库。



## 操作

Sqoop数据导入命令示例如下。

```
$ sqoop import --connect jdbc:mysql://localhost/db --username foo --password --table TEST
```

你需要指定数据库URL、用户名、密码、表名，就可以将数据表的数据导入到Hadoop。