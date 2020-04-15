---
title: "storage_engine"
date: 2018-12-30 11:31
---


[TOC]





# 查看引擎

查看支持的引擎

```
show engines;
```



查看当前的引擎

```
show variables like '%storage_engine%';
```



# INNODB

用于处理大量的短期事务，数据存储于表空间table space中

MyISAM 不能保证系统奔溃后可以恢复



## 使用方式

所有InnoDB表的数据和索引放置于同一个表空间中，不方便操作及其备份

```
datadir定义的目录下
ibddata1,ibddata2, ...
```

数据文件（存储数据和索引）命名

```
tbl_name.ibd 
```





每个表单单独使用一个表空间存储表的数据和引擎

```
mysql> SHOW GLOBAL VARIABLES LIKE "innodb_file%";
+--------------------------+-----------+
| Variable_name            | Value     |
+--------------------------+-----------+
| innodb_file_format       | Barracuda |
| innodb_file_format_check | ON        |
| innodb_file_format_max   | Barracuda |
| innodb_file_per_table    | ON        |
+--------------------------+-----------+
4 rows in set (0.01 sec)

innodb_file_per_table=ON
```

表格式定义

```
tbl_name.frm
```



## 特点

基于MVCC来支持高并发，支持所有4个隔离级别，默认级别为REPEATABLE READ；间隙锁防止幻读

使用聚集索引方式

性能通过，支持自适应hash索引，预计操作哦，插入缓存区等方法

锁力度：行级别锁





# MyISAM

支持全文索引（FULLTEXT index），压缩，空间函数（GIS），但不支持事务，且为表级别锁

崩溃后无法安全恢复（MariaDB 使用Aria 存储引擎， crash-safe）



## 文件

```
tbl_name.frm 	表格式定义
tbl_name.MYD 	数据文件
tbl_name.MYI	索引文件
```



## 特性

加锁和并发，表级锁

修复：手工或自动修复，但可能丢失数据

索引：非聚集索引

延迟更新索引键

压缩表



## 行格式

```
dynamic, fixed, compressed, compact, redundent
```





# 其他存储引擎

## CSV

将普通的CSV(字段通过逗号分隔)作为MySQL表使用



## MRG_MyISAM

将多个MyISAM表合并成为一个虚拟表



## BLACKHOLE

类似于/dev/null， 不真正存储任何数据



## MEMORY

所有数据都保存于内存中，

内存表：支持hash索引，表级锁

可以作为临时表



## PREFORMANCE_SCHEMA

伪存储引擎



## ARCHIVE

只支持SELECT和INSERT操作

支持行级锁和专用缓存区





## FEDERATED

用于访问其他远程MySQL服务器的一个代理，通过创建一个到远程MySQL服务器的客户端连接，并将查询传输到远程服务器执行，而后完成数据存储

在MariaDB上实现的是FederatedX



## MariaDB还支持的

### OQGraph



### SphinxSE

可以全文搜索，常用于企业内部的搜索引擎



### TokuDB

可以存储大量数据



### Cassandra

NoSQL的一种



### CONNECT



### SQUENCE







