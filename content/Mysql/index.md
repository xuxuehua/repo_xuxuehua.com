---
title: "index 索引"
date: 2018-12-29 12:47
---


[TOC]



# 索引

## 构建法则

索引应该构建在被用作查询条件的字段上



## 索引类型

### B+ Tree 索引

顺序存储

每一个叶子结点到根节点的距离是相同的，类型是左前缀索引，适合查询范围类的数据

可以使用B+Tree索引的查询类型全键值，键值范围或者键前缀查询



所以精确列放在左侧，范围列放在右侧



#### 查询类型

* 全值匹配

精确某个值

```
"Jinjiao King"
```



* 匹配最左前缀

只精确匹配起头部分

```
"Jin%"
```



* 匹配范围值



* 精确匹配某一列并范围匹配另一列



* 只访问索引的查询





#### 不适合使用场景

* 如果不从最左列开始，索引无效

```
Age, Name
```



* 不能跳过索引中的列

查询StuID且Age< 20, 跳过Name

```
StuID, Name, Age 
```



* 如果查询中某个列是为范围查询，那么其右侧的列都无法再使用索引优化查询





### HASH 索引

基于哈希表的键值

不适合值顺序查询，适合精确匹配索引中的所有列

MySQL中只有memory 存储引擎支持显式hash索引



#### 适合使用场景

只支持等值比较查询，包括=, IN(), <=>;



#### 不适合使用场景

存储为非为值的顺序，因此，不适合顺序查询

不支持模糊匹配



### 空间索引 （R-Tree）

只有MyISAM 支持空间索引



### 全文索引（FULL TEXT）

只有MyISAM 支持

在大量文本中查找关键词



## 索引优点

索引可以降低服务器需要扫描的数据量，减少了I/O次数

索引可以帮助服务器避免排序和使用临时表

索引可以帮助将随机I/O转为聚集I/O



## 高性能索引策略

### 使用独立的列

即不要让字段参与运算

```
SELECT * FROM students WHERE Age+20>50;
```



### 使用左前缀索引

索引构建于字段的左侧的多少个字符，要通过索引选择性评估

索引选择性指，不重复的索引值和数据表的记录总数的比值



### 多列索引



### 选择合适的索引列次序

即将选择性最高的放在左侧，精确匹配放在左侧，范围匹配放右侧



## 冗余和重复索引

不是一种良好的使用策略

避免使用类似这样的查询

```
%abc%
```



## EXPLAIN 索引有效性

获取查询执行计划信息，用来查看查询优化器是如何执行查询

```
EXPLAIN SELECT clause
```





```
mysql> EXPLAIN SELECT Name FROM students WHERE StuID>10\G;
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: students
   partitions: NULL
         type: ALL
possible_keys: PRIMARY
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 27
     filtered: 62.96
        Extra: Using where
1 row in set, 1 warning (0.02 sec)
```



### id 

指当前语句中，每个SELECT语句的编号

#### 复杂类型的查询

* 简单子查询

* 用于FROM中的子查询

* 联合查询

  > UNION 查询的分析结果会出现一额外的匿名临时表



### select_type

简单查询为SIMPLE

```
mysql> EXPLAIN SELECT Name FROM students WHERE StuID>10\G;
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: students
   partitions: NULL
         type: ALL
possible_keys: PRIMARY
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 27
     filtered: 62.96
        Extra: Using where
1 row in set, 1 warning (0.02 sec)
```



复杂查询为

SUBQUERY, 简答子查询	

```
mysql> EXPLAIN SELECT Name, Age FROM students WHERE Age > (SELECT avg(Age) FROM students)\G;
*************************** 1. row ***************************
           id: 1
  select_type: PRIMARY
        table: students
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 27
     filtered: 33.33
        Extra: Using where
*************************** 2. row ***************************
           id: 2
  select_type: SUBQUERY
        table: students
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 27
     filtered: 100.00
        Extra: NULL
2 rows in set, 1 warning (0.00 sec)

ERROR:
No query specified
```





DERIVED, 用于FROM中的子查询

```

```

UNION， UNION 语句的第一个之后的SELECT语句

UNION RESULT， 匿名临时表

```
mysql> EXPLAIN SELECT Name FROM students UNION SELECT Name FROM teachers\G;
*************************** 1. row ***************************
           id: 1
  select_type: PRIMARY
        table: students
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 27
     filtered: 100.00
        Extra: NULL
*************************** 2. row ***************************
           id: 2
  select_type: UNION
        table: teachers
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 4
     filtered: 100.00
        Extra: NULL
*************************** 3. row ***************************
           id: NULL
  select_type: UNION RESULT
        table: <union1,2>
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: NULL
     filtered: NULL
        Extra: Using temporary
3 rows in set, 1 warning (0.02 sec)

ERROR:
No query specified
```



### table

SELECT语句关联到的表



### type （性能逐个上升）

关联类型，或访问类型

即MySQL决定的如何去查询表中行的行为



#### ALL

全表扫描

```
mysql> EXPLAIN SELECT Name, Age FROM students WHERE Age > (SELECT avg(Age) FROM students)\G;
*************************** 1. row ***************************
           id: 1
  select_type: PRIMARY
        table: students
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 27
     filtered: 33.33
        Extra: Using where
*************************** 2. row ***************************
           id: 2
  select_type: SUBQUERY
        table: students
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 27
     filtered: 100.00
        Extra: NULL
2 rows in set, 1 warning (0.00 sec)
```



#### index

根据索引的次序进行全表扫描

如果在Extra列出现`Using index` 表示了使用覆盖索引，而非全表扫描

```
mysql> EXPLAIN SELECT * FROM (SELECT * FROM students WHERE Gender='M') AS a WHERE a.Age > 25\G;
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: students
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 27
     filtered: 16.66
        Extra: Using where
1 row in set, 1 warning (0.00 sec)
```



#### range

 有范围限制的根据索引实现范围扫描，扫描位置始于索引中的某一点，结束于某一点

```

```



#### ref

根据索引返回表中匹配某单个值的所有行

```
mysql> EXPLAIN SELECT Name FROM students WHERE Age=100\G;
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: students
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 27
     filtered: 10.00
        Extra: Using where
1 row in set, 1 warning (0.00 sec)

ERROR:
No query specified

mysql> CREATE INDEX age ON students(Age);
Query OK, 27 rows affected (0.26 sec)
Records: 27  Duplicates: 0  Warnings: 0

mysql> EXPLAIN SELECT Name FROM students WHERE Age=100\G;
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: students
   partitions: NULL
         type: ref
possible_keys: age
          key: age
      key_len: 1
          ref: const
         rows: 2
     filtered: 100.00
        Extra: NULL
1 row in set, 1 warning (0.00 sec)

ERROR:
No query specified
```



#### eq_ref

等同于ref，仅仅返回一个行，但需要与某个参考值做比较

```

```



#### const/system （最佳查询）

只返回单个行，视为

```
mysql> EXPLAIN SELECT Name FROM students WHERE StuID=14\G;
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: students
   partitions: NULL
         type: const
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
1 row in set, 1 warning (0.00 sec)
```



### possible_keys

查询可能会用到的



### key

查询中使用了的索引



### key_len

在索引使用的字节数



### ref

在利用key字段所表示的索引完成查询时所有的列货某常量值



### rows

MySQL估计为找所有的目标行而需要读取的行数



### Extra

```
Using index		mysql将会使用覆盖索引，以避免访问表
Using where		mysql服务器将在存储引擎检索后，再进行一次过滤
Using temporary	mysql对结果排序会使用临时表
Using filsort	对结果使用一个外部索引排序
```













