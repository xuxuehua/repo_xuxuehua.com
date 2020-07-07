---
title: "index 索引"
date: 2018-12-29 12:47
---


[TOC]



# 索引

## 构建原则

索引应该构建在被用作查询条件的字段上

实际工作中，我们也需要注意平衡，如果索引太多了，在更新数据的时候，如果涉及到索引更新，就会造成负担。



**字段的数值有唯一性的限制**

比如用户名索引本身可以起到约束的作用，比如唯一索引、主键索引都是可以起到唯一性约束的，因此在我们的数据表中，如果某个字段是唯一性的，就可以直接创建唯一性索引，或者主键索引。



**频繁作为 WHERE 查询条件的字段，尤其在数据表大的情况下**

在数据量大的情况下，某个字段在 SQL 查询的 WHERE 条件中经常被使用到，那么就需要给这个字段创建索引了。创建普通索引就可以大幅提升数据查询的效率



**需要经常 GROUP BY 和 ORDER BY 的列**

当我们使用 GROUP BY 对数据进行分组查询，或者使用 ORDER BY 对数据进行排序的时候，就需要对分组或者排序的字段进行索引。



**UPDATE、DELETE 的 WHERE 条件列，一般也需要创建索引**

对数据按照某个条件进行查询后再进行 UPDATE 或 DELETE 的操作，如果对 WHERE 字段创建了索引，就能大幅提升效率



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









# 索引管理

索引是按照特定数据结构存储的数据

索引并不会是我们需要的数据本身，而是类似指针指向所需要的数据



## 索引类型

### 聚集索引，非聚集索引

数据是否与索引存储在一起





### 主键索引，辅助索引



### 稠密索引，稀疏索引

是否索引了每一个数据项



### B+ Tree (MySQL 索引)

B+树的查询效率更加稳定：由于非终结点并不是最终指向文件内容的结点，而只是叶子结点中关键字的索引。所以任何关键字的查找必须走一条从根结点到叶子结点的路。所有关键字查询的路径长度相同，导致每一个数据的查询效率相当。

B+树的磁盘读写代价更低：B+树的内部节点并没有指向关键字具体信息的指针，因此其内部节点相对B树更小，如果把所有同一内部节点的关键字存放在同一盘块中，那么盘块所能容纳的关键字数量也越多，一次性读入内存的需要查找的关键字也就越多，相对IO读写次数就降低了。

由于B+树的数据都存储在叶子结点中，分支结点均为索引，方便扫库，只需要扫一遍叶子结点即可，但是B树因为其分支结点同样存储着数据，我们要找到具体的数据，需要进行一次中序遍历按序来扫，所以B+树更加适合在区间查询的情况，所以通常B+树用于数据库索引。



### HASH索引（键值索引）

Hash 本身是一个函数，又被称为散列函数，它可以帮助我们大幅提升检索数据的效率

Hash索引有很大的限制，如联合索引、模糊查询、范围查询，以及列里有重复值多。
需要遍历链表中所有行指针，逐一进行比较，直到找到所有符合条件的

mysql查询中存在着很多范围查询、order by的场景，在这些场景下，B+树的性能好于Hash索引；关键字出现相同Hash码时，会出现hash冲突

对于一般需求来说，B+树在数据库应用的场景更多，Hash适用一些特殊的需求，比如文件校验，密码学等



### R Tree 



### 简单索引，组合索引



### 左前缀索引

```
like ‘abc%’
```



### 覆盖索引





## 管理索引方法

### 创建索引

创建表时指定 CREATE INDEX



### 创建或删除索引

修改表的命令



### 删除索引

DROP INDEX



### 查看表索引

```
Syntax:
SHOW [EXTENDED] {INDEX | INDEXES | KEYS}
    {FROM | IN} tbl_name
    [{FROM | IN} db_name]
    [WHERE expr]
```



```
mysql> SHOW INDEXES FROM students;
+----------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| Table    | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment | Visible | Expression |
+----------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| students |          0 | PRIMARY  |            1 | StuID       | A         |          25 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
+----------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
1 row in set (0.12 sec)
```





### EXPLAIN 表索引对比操作 

```
mysql> explain select * from students WHERE StuID=3;
+----+-------------+----------+------------+-------+---------------+---------+---------+-------+------+----------+-------+
| id | select_type | table    | partitions | type  | possible_keys | key     | key_len | ref   | rows | filtered | Extra |
+----+-------------+----------+------------+-------+---------------+---------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | students | NULL       | const | PRIMARY       | PRIMARY | 4       | const |    1 |   100.00 | NULL  |
+----+-------------+----------+------------+-------+---------------+---------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

mysql> explain select * from students WHERE Age=53;;
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table    | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | students | NULL       | ALL  | NULL          | NULL | NULL    | NULL |   25 |    10.00 | Using where |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.01 sec)
```

> Type 类型显示拥有索引的查询只会查询一条rows记录





