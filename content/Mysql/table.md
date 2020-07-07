---
title: "table"
date: 2020-06-25 18:10
---
[toc]



# 表

## 字符集

主要是为了保存emoji表情，例如: 微信昵称，就有很多带有emoji表情的，这里我们使用utf8mb4字符集，千万不要使用blob类型来存储



## 主/外键类型

主键的设定是非常重要的，在主键的选择上，应该满足以下几个条件:

```
1. 唯一性 (必要条件)
2. 非空性
3. 有序性
4. 可读性
5. 可扩展性
```

>  有序性就有不少好处。例如: 查询时，为有序IO，就可提高查询效率，存储的顺序也是有序的，往远了看，分库分表也是有好处的。因此，我建议使8字节无符号的bigint(20)作为主键的数据类型  
>
>  主外键的数据类型一定要一致！
>
>  每个表中的主键命名保持一致！

```
create table t_base_user(
id bigint(20) unsigned not null primary key auto_increment;
....
)
```



## 无符号与有符号的区别

有符号允许存储负数，无符号只允许从正数开始，无符号最小值为0，最大值根据类型不同而不同。



## 外键约束

外键约束用来保证数据完整性的。但不建议在数据库表中加外键约束，因为在数据表中添加外键约束，会影响性能，

例如: 每一次修改数据时，都要在另外的一张表中执行查询。应该是：在应用层，也就是代码层面，来维持外键关系。



## comment 添加注释

添加注释，这是非常重要的，其中包括表注释，字段注释。主要是为了后期表结构的维护

```
create table t_base_user(
 id bigint(20) UNSIGNED not null primary key auto_increment comment "主键",
 name varchar(50) character set utf8mb4 comment "",
 created_time datetime null default now() comment "创建时间",
 updated_time datetime null default now() comment "修改时间",
 deleted tinyint not null default 1 comment "逻辑删除 0正常数据 1删除数据"
)engine=InnoDB charset=utf8 comment "用户表";

//添加索引
alter table t_base_user idx_created_time(created_time);
```





# 定义

## 字段

```
字段：字段名， 字段数据类型，修饰符
```



## 索引

应该创建在经常用作查询条件的字段上

实现级别在存储引擎



### 稠密索引

B+索引， hash索引， R树索引， FULLTEXT索引



### 稀疏索引

聚合索引， 非聚合索引



简单索引，组合索引



# 表操作



## 创建表

直接创建

```
mysql> help create table
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    (create_definition,...)
    [table_options]
    [partition_options]
```



通过现存的表创建，新表会被直接插入查询而来的数据

```
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    [(create_definition,...)]
    [table_options]
    [partition_options]
    [IGNORE | REPLACE]
    [AS] query_expression
```



通过复制现存表结构创建，只复制表不复制数据

```
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    { LIKE old_tbl_name | (LIKE old_tbl_name) }
```



## 表类型

Storage Engine 是指表类型，也就是在表创建时指明其使用的存储引擎

同一个库中表要使用同一种存储引擎类型

```
mysql> SHOW GLOBAL VARIABLES LIKE '%default%engine%';
+----------------------------+--------+
| Variable_name              | Value  |
+----------------------------+--------+
| default_storage_engine     | InnoDB |
| default_tmp_storage_engine | InnoDB |
+----------------------------+--------+
2 rows in set (0.00 sec)
```



## 表结构

```
DESCRIBE tbl_name;
```



## 表状态信息

```
SHOW [FULL] TABLES [{FROM | IN } db_name] [LIKE 'pattern' | WHERE expr]
```



```
mysql> SHOW TABLE STATUS LIKE 't1'\G;
*************************** 1. row ***************************
           Name: t1
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 0
 Avg_row_length: 0
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: 5
    Create_time: 2018-12-20 13:43:39
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
1 row in set (0.00 sec)
```



## 修改表

```
ALTER TABLE
```



```
mysql> ALTER TABLE movie CHANGE `start` star smallint(6);
```



## 删除表

```
DROP TABLE
```





