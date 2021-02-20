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



# 导入

## RDBMS to HDFS

全部导入

```
$ bin/sqoop import \
--connect jdbc:mysql://hadoop102:3306/company \
--username root \
--password 000000 \
--table staff \
--target-dir /user/company \
--delete-target-dir \
--num-mappers 1 \
--fields-terminated-by "\t"
```



查询导入

```
$ bin/sqoop import \
--connect jdbc:mysql://hadoop102:3306/company \
--username root \
--password 000000 \
--target-dir /user/company \
--delete-target-dir \
--num-mappers 1 \
--fields-terminated-by "\t" \
--query 'select name,sex from staff where id <=1 and $CONDITIONS;'
提示：must contain '$CONDITIONS' in WHERE clause.
如果query后使用的是双引号，则$CONDITIONS前必须加转移符，防止shell识别为自己的变量。
```



导入指定列

```
$ bin/sqoop import \
--connect jdbc:mysql://hadoop102:3306/company \
--username root \
--password 000000 \
--target-dir /user/company \
--delete-target-dir \
--num-mappers 1 \
--fields-terminated-by "\t" \
--columns id,sex \
--table staff
提示：columns中如果涉及到多列，用逗号分隔，分隔时不要添加空格
```



使用sqoop关键字筛选查询导入数据

```
$ bin/sqoop import \
--connect jdbc:mysql://hadoop102:3306/company \
--username root \
--password 000000 \
--target-dir /user/company \
--delete-target-dir \
--num-mappers 1 \
--fields-terminated-by "\t" \
--table staff \
--where "id=1"
```



## RDBMS to Hive

```
$ bin/sqoop import \
--connect jdbc:mysql://hadoop102:3306/company \
--username root \
--password 000000 \
--table staff \
--num-mappers 1 \
--hive-import \
--fields-terminated-by "\t" \
--hive-overwrite \
--hive-table staff_hive
提示：该过程分为两步，第一步将数据导入到HDFS，第二步将导入到HDFS的数据迁移到Hive仓库，第一步默认的临时目录是/user/atguigu/表名
```



## RDBMS to HBase

```
$ bin/sqoop import \
--connect jdbc:mysql://hadoop102:3306/company \
--username root \
--password 000000 \
--table staff \
--columns "id,name,sex" \
--column-family "info" \
--hbase-create-table \
--hbase-row-key "id" \
--hbase-table "hbase_company" \
--num-mappers 1 \
--split-by id
提示：sqoop1.4.6只支持HBase1.0.1之前的版本的自动创建HBase表的功能
解决方案：手动创建HBase表

hbase> create 'hbase_company','info'
在HBase中scan这张表得到如下内容
hbase> scan 'hbase_company'
```





# 导出



## HIVE/HDFS to RDBMS

```
$ bin/sqoop export \
--connect jdbc:mysql://hadoop102:3306/company \
--username root \
--password 000000 \
--table staff \
--num-mappers 1 \
--export-dir /user/hive/warehouse/staff_hive \
--input-fields-terminated-by "\t"
提示：Mysql中如果表不存在，不会自动创建
```



