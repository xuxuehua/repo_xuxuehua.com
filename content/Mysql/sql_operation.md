---
title: "sql_operation"
date: 2018-12-20 21:49
---


[TOC]



# DDL & DML



## DDL 数据定义语言



### CREATE

DB 组件：数据库，表，索引，视图，用户，存储过程，存储函数，触发器，事件调度器等

```
mysql> help CREATE
Many help items for your request exist.
To make a more specific request, please type 'help <item>',
where <item> is one of the following
topics:
   CREATE DATABASE
   CREATE EVENT
   CREATE FUNCTION
   CREATE FUNCTION UDF
   CREATE INDEX
   CREATE PROCEDURE
   CREATE RESOURCE GROUP
   CREATE ROLE
   CREATE SERVER
   CREATE SPATIAL REFERENCE SYSTEM
   CREATE TABLE
   CREATE TABLESPACE
   CREATE TRIGGER
   CREATE USER
   CREATE VIEW
   SHOW
   SHOW CREATE DATABASE
   SHOW CREATE EVENT
   SHOW CREATE FUNCTION
   SHOW CREATE PROCEDURE
   SHOW CREATE TABLE
   SHOW CREATE USER
   SPATIAL
```



### ALTER

组件同CREATE





### DROP

组件同CREATE





## DML 数据操作语言



### INSERT

一次插入一行或多行数据

每插入一次，会触发index更新一次



* 方法1

```
INSERT [LOW_PRIORITY | DELAYED | HIGH_PRIORITY] [IGNORE]
    [INTO] tbl_name
    [PARTITION (partition_name [, partition_name] ...)]
    [(col_name [, col_name] ...)]
    {VALUES | VALUE} (value_list) [, (value_list)] ...
    [ON DUPLICATE KEY UPDATE assignment_list]
```

```
mysql> INSERT INTO students (Name,Age,Gender) VALUES ('Jingjiao King', 100, 'M');
```



* 方法2

```
INSERT [LOW_PRIORITY | DELAYED | HIGH_PRIORITY] [IGNORE]
    [INTO] tbl_name
    [PARTITION (partition_name [, partition_name] ...)]
    SET assignment_list
    [ON DUPLICATE KEY UPDATE assignment_list]
```

```
INSERT INTO students SET Name='Yinjiao King', Age=98, Gender='M';
```



* 方法3

```
INSERT [LOW_PRIORITY | HIGH_PRIORITY] [IGNORE]
    [INTO] tbl_name
    [PARTITION (partition_name [, partition_name] ...)]
    [(col_name [, col_name] ...)]
    SELECT ...
    [ON DUPLICATE KEY UPDATE assignment_list]
```



### DELETE

一定要加上的限制条件，否则将清空表中的所有数据。

如WHERE，LIMIT



```
Single-Table Syntax
DELETE [LOW_PRIORITY] [QUICK] [IGNORE] FROM tbl_name
    [PARTITION (partition_name [, partition_name] ...)]
    [WHERE where_condition]
    [ORDER BY ...]
    [LIMIT row_count]
```







### UPDATE

一定要加上的限制条件，否则将修改表中所有行的字段。

如WHERE，LIMIT

```
Single-table syntax:

UPDATE [LOW_PRIORITY] [IGNORE] table_reference
    SET assignment_list
    [WHERE where_condition]
    [ORDER BY ...]
    [LIMIT row_count]
```





### SELECT

查询执行路径中的组件：查询缓存，解析器，预处理器，优化器，查询执行引擎，存储引擎



SELECT执行流程

```
FROM Clause -> WHERE Clause -> GROUP BY -> HAVING Clause -> ORDER BY -> SELECT -> 
```



#### 单表查询

```
SELECT
    [ALL | DISTINCT | DISTINCTROW ]
      [HIGH_PRIORITY]
      [STRAIGHT_JOIN]
      [SQL_SMALL_RESULT] [SQL_BIG_RESULT] [SQL_BUFFER_RESULT]
      [SQL_CACHE | SQL_NO_CACHE] [SQL_CALC_FOUND_ROWS]
    select_expr [, select_expr ...]
    [FROM table_references
      [PARTITION partition_list]
    [WHERE where_condition]
    [GROUP BY {col_name | expr | position}, ... [WITH ROLLUP]]
    [HAVING where_condition]
    [WINDOW window_name AS (window_spec)
        [, window_name AS (window_spec)] ...]
    [ORDER BY {col_name | expr | position}
      [ASC | DESC], ... [WITH ROLLUP]]
    [LIMIT {[offset,] row_count | row_count OFFSET offset}]
    [INTO OUTFILE 'file_name'
        [CHARACTER SET charset_name]
        export_options
      | INTO DUMPFILE 'file_name'
      | INTO var_name [, var_name]]
    [FOR {UPDATE | SHARE} [OF tbl_name [, tbl_name] ...] [NOWAIT | SKIP LOCKED]
      | LOCK IN SHARE MODE]]
```



DISTINCT 数据去重

```
mysql> SELECT DISTINCT Gender FROM students;
+--------+
| Gender |
+--------+
| M      |
| F      |
+--------+
```



SQL_CACHE 显式指定存储查询结果于缓存之中

SQL_NO_CACHE 显式查询结果不予缓存



##### query_cache_type

```
query_cache_type 的值为ON时，查询缓存功能打开
SELECT的结果符合缓存条件即会缓存，否则不予缓存
显式指定SQL_NO_CACHE，不予缓存
```

```
query_cache_type 的值为DEMAND时，查询缓存功能按需进行，
显式指定SQL_CACHE的SELECT语句才会缓存，其他不予缓存
```



##### 别名使用

```
col1 AS alias1, col2 AS alias2, ....
```



##### WHERE 子句

指明过滤条件以实现“选择” 功能



###### 过滤条件

布尔表达式



###### 算数操作符

```
+， -， *， /， %
```



###### 比较操作符

```
=, !=, <>, <=>, >, >=, <, <=
```

```
BETWEEN min_num AND max_num
IN (element1, element2, ...)
IS NULL
IS NOT NULL
LIKE:
	% 任意长度的任意字符
	_ 任意单个字符
RLIKE
REGEXP 匹配字符串可以用正则表达式书写模式
```



```
mysql> SELECT Name, ClassID FROM students WHERE ClassID IS NULL;
+---------------+---------+
| Name          | ClassID |
+---------------+---------+
| Xu Xian       |    NULL |
| Sun Dasheng   |    NULL |
| Jingjiao King |    NULL |
| Yinjiao King  |    NULL |
+---------------+---------+
```



###### 逻辑运算符

```
NOT, AND, OR, XOR（异或）
```



##### GROUP 子句

根据指定的条件把查询结果进行分组，以用于做聚合运算

```
avg(), max(), min(), count(), sum()
```

```
mysql> SELECT avg(Age),Gender FROM students GROUP BY Gender;
+----------+--------+
| avg(Age) | Gender |
+----------+--------+
|  40.7647 | M      |
|  19.0000 | F      |
+----------+--------+
2 rows in set (0.00 sec)
```



###### HAVING

对分组聚合运算后的结果指定过滤条件

```
mysql> SELECT avg(Age) as AAge,Gender FROM students GROUP BY Gender HAVING AAge >20;
+---------+--------+
| AAge    | Gender |
+---------+--------+
| 40.7647 | M      |
+---------+--------+
1 row in set (0.00 sec)
```

```
mysql> SELECT count(StuID) AS NumOfStu, ClassID FROM students GROUP BY ClassID HAVING NumOfStu >2;
+----------+---------+
| NumOfStu | ClassID |
+----------+---------+
|        3 |       2 |
|        4 |       1 |
|        4 |       4 |
|        4 |       3 |
|        3 |       7 |
|        4 |       6 |
|        4 |    NULL |
+----------+---------+
7 rows in set (0.00 sec)
```



##### ORDER BY

根据指定字段对查询结果进行排序

升序 ASC

降序 DESC

```
mysql> SELECT Name, Age FROM students ORDER BY Age DESC
    -> ;
+---------------+-----+
| Name          | Age |
+---------------+-----+
| Jingjiao King | 100 |
| Sun Dasheng   | 100 |
| Yinjiao King  |  98 |
| Xie Yanke     |  53 |
| Shi Qing      |  46 |
| Tian Boguang  |  33 |
| Ding Dian     |  32 |
| Xu Xian       |  27 |
| Yu Yutong     |  26 |
| Lin Chong     |  25 |
| Ma Chao       |  23 |
| Yuan Chengzhi |  23 |
| Hua Rong      |  23 |
| Shi Potian    |  22 |
| Huang Yueying |  22 |
| Shi Zhongyu   |  22 |
| Xu Zhu        |  21 |
| Xiao Qiao     |  20 |
| Ren Yingying  |  20 |
| Duan Yu       |  19 |
| Diao Chan     |  19 |
| Wen Qingqing  |  19 |
| Yue Lingshan  |  19 |
| Xi Ren        |  19 |
| Xue Baochai   |  18 |
| Lu Wushuang   |  17 |
| Lin Daiyu     |  17 |
+---------------+-----+
27 rows in set (0.00 sec)
```



##### LIMIT 

```
LIMIT [[offset,] row_count] 对查询
```



```
mysql> SELECT Name, Age FROM students ORDER BY Age DESC;
+---------------+-----+
| Name          | Age |
+---------------+-----+
| Jingjiao King | 100 |
| Sun Dasheng   | 100 |
| Yinjiao King  |  98 |
| Xie Yanke     |  53 |
| Shi Qing      |  46 |
| Tian Boguang  |  33 |
| Ding Dian     |  32 |
| Xu Xian       |  27 |
| Yu Yutong     |  26 |
| Lin Chong     |  25 |
| Ma Chao       |  23 |
| Yuan Chengzhi |  23 |
| Hua Rong      |  23 |
| Shi Potian    |  22 |
| Huang Yueying |  22 |
| Shi Zhongyu   |  22 |
| Xu Zhu        |  21 |
| Xiao Qiao     |  20 |
| Ren Yingying  |  20 |
| Duan Yu       |  19 |
| Diao Chan     |  19 |
| Wen Qingqing  |  19 |
| Yue Lingshan  |  19 |
| Xi Ren        |  19 |
| Xue Baochai   |  18 |
| Lu Wushuang   |  17 |
| Lin Daiyu     |  17 |
+---------------+-----+
27 rows in set (0.00 sec)
```



```
mysql> SELECT Name, Age FROM students ORDER BY Age DESC LIMIT 10;
+---------------+-----+
| Name          | Age |
+---------------+-----+
| Sun Dasheng   | 100 |
| Jingjiao King | 100 |
| Yinjiao King  |  98 |
| Xie Yanke     |  53 |
| Shi Qing      |  46 |
| Tian Boguang  |  33 |
| Ding Dian     |  32 |
| Xu Xian       |  27 |
| Yu Yutong     |  26 |
| Lin Chong     |  25 |
+---------------+-----+
10 rows in set (0.00 sec)

mysql> SELECT Name, Age FROM students ORDER BY Age DESC LIMIT 10, 10;
+---------------+-----+
| Name          | Age |
+---------------+-----+
| Ma Chao       |  23 |
| Yuan Chengzhi |  23 |
| Hua Rong      |  23 |
| Shi Potian    |  22 |
| Huang Yueying |  22 |
| Shi Zhongyu   |  22 |
| Xu Zhu        |  21 |
| Xiao Qiao     |  20 |
| Ren Yingying  |  20 |
| Duan Yu       |  19 |
+---------------+-----+
10 rows in set (0.00 sec)
```



##### 查询结果加锁

```
FROM UPDATE 写锁，排他锁
LOCK IN SHARE MODE 读锁，共享锁
```



## 多表查询

### 交叉连接

即笛卡尔积



### 内连接

#### 等值连接

让表之间的字段以等值建立连接关系

```
mysql> SELECT s.Name , c.Class  FROM students as s, classes as c WHERE s.ClassID=c.ClassID;
+---------------+----------------+
| Name          | Class          |
+---------------+----------------+
| Shi Zhongyu   | Emei Pai       |
| Shi Potian    | Shaolin Pai    |
| Xie Yanke     | Emei Pai       |
| Ding Dian     | Wudang Pai     |
| Yu Yutong     | QingCheng Pai  |
| Shi Qing      | Riyue Shenjiao |
| Xi Ren        | QingCheng Pai  |
| Lin Daiyu     | Ming Jiao      |
| Ren Yingying  | Lianshan Pai   |
| Yue Lingshan  | QingCheng Pai  |
| Yuan Chengzhi | Lianshan Pai   |
| Wen Qingqing  | Shaolin Pai    |
| Tian Boguang  | Emei Pai       |
| Lu Wushuang   | QingCheng Pai  |
| Duan Yu       | Wudang Pai     |
| Xu Zhu        | Shaolin Pai    |
| Lin Chong     | Wudang Pai     |
| Hua Rong      | Ming Jiao      |
| Xue Baochai   | Lianshan Pai   |
| Diao Chan     | Ming Jiao      |
| Huang Yueying | Lianshan Pai   |
| Xiao Qiao     | Shaolin Pai    |
| Ma Chao       | Wudang Pai     |
+---------------+----------------+
23 rows in set (0.00 sec)
```



#### 不等值连接

#### 自然连接



#### 自连接

```
mysql> SELECT s.Name , t.Name FROM students as s, students as t WHERE s.TeacherID = t.StuID;
+---------------+-------------+
| Name          | Name        |
+---------------+-------------+
| Shi Zhongyu   | Xie Yanke   |
| Shi Potian    | Xi Ren      |
| Xie Yanke     | Xu Zhu      |
| Ding Dian     | Ding Dian   |
| Yu Yutong     | Shi Zhongyu |
| Jingjiao King | Shi Zhongyu |
| Yinjiao King  | Shi Potian  |
+---------------+-------------+
7 rows in set (0.00 sec)
```





### 外连接

#### 左外连接

```
FROM tb1 LEFT JOIN tb2 ON tb1.col=tb2.col;
```

```
mysql> SELECT s.Name , c.Class FROM students as s LEFT JOIN classes AS c ON s.ClassID = c.ClassID;
+---------------+----------------+
| Name          | Class          |
+---------------+----------------+
| Shi Zhongyu   | Emei Pai       |
| Shi Potian    | Shaolin Pai    |
| Xie Yanke     | Emei Pai       |
| Ding Dian     | Wudang Pai     |
| Yu Yutong     | QingCheng Pai  |
| Shi Qing      | Riyue Shenjiao |
| Xi Ren        | QingCheng Pai  |
| Lin Daiyu     | Ming Jiao      |
| Ren Yingying  | Lianshan Pai   |
| Yue Lingshan  | QingCheng Pai  |
| Yuan Chengzhi | Lianshan Pai   |
| Wen Qingqing  | Shaolin Pai    |
| Tian Boguang  | Emei Pai       |
| Lu Wushuang   | QingCheng Pai  |
| Duan Yu       | Wudang Pai     |
| Xu Zhu        | Shaolin Pai    |
| Lin Chong     | Wudang Pai     |
| Hua Rong      | Ming Jiao      |
| Xue Baochai   | Lianshan Pai   |
| Diao Chan     | Ming Jiao      |
| Huang Yueying | Lianshan Pai   |
| Xiao Qiao     | Shaolin Pai    |
| Ma Chao       | Wudang Pai     |
| Xu Xian       | NULL           |
| Sun Dasheng   | NULL           |
| Jingjiao King | NULL           |
| Yinjiao King  | NULL           |
+---------------+----------------+
27 rows in set (0.00 sec)
```



#### 右外连接

```
FROM tb1 RIGHT JOIN tb2 ON tb1.col=tb2.col;
```



```
mysql> SELECT s.Name , c.Class FROM students as s RIGHT JOIN classes AS c ON s.ClassID = c.ClassID;
+---------------+----------------+
| Name          | Class          |
+---------------+----------------+
| Shi Zhongyu   | Emei Pai       |
| Shi Potian    | Shaolin Pai    |
| Xie Yanke     | Emei Pai       |
| Ding Dian     | Wudang Pai     |
| Yu Yutong     | QingCheng Pai  |
| Shi Qing      | Riyue Shenjiao |
| Xi Ren        | QingCheng Pai  |
| Lin Daiyu     | Ming Jiao      |
| Ren Yingying  | Lianshan Pai   |
| Yue Lingshan  | QingCheng Pai  |
| Yuan Chengzhi | Lianshan Pai   |
| Wen Qingqing  | Shaolin Pai    |
| Tian Boguang  | Emei Pai       |
| Lu Wushuang   | QingCheng Pai  |
| Duan Yu       | Wudang Pai     |
| Xu Zhu        | Shaolin Pai    |
| Lin Chong     | Wudang Pai     |
| Hua Rong      | Ming Jiao      |
| Xue Baochai   | Lianshan Pai   |
| Diao Chan     | Ming Jiao      |
| Huang Yueying | Lianshan Pai   |
| Xiao Qiao     | Shaolin Pai    |
| Ma Chao       | Wudang Pai     |
| NULL          | Xiaoyao Pai    |
+---------------+----------------+
24 rows in set (0.00 sec)
```



## 子查询

在查询语句嵌套查询语句

基于某语句的查询结果再次进行的查询



### WHERE 子句子查询

* 基于比较表达式中的子查询，子查询仅能返回单个值

```
mysql> SElect Name, Age from students where Age> (Select avg(Age) from students);
+---------------+-----+
| Name          | Age |
+---------------+-----+
| Xie Yanke     |  53 |
| Shi Qing      |  46 |
| Tian Boguang  |  33 |
| Sun Dasheng   | 100 |
| Jingjiao King | 100 |
| Yinjiao King  |  98 |
+---------------+-----+
6 rows in set (0.00 sec)

mysql> EXPLAIN SElect Name, Age from students where Age> (Select avg(Age) from students)\G;
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



* 基于IN中的子查询，子查询应该单键查询并返回一个或者多个值从构成列表

```
mysql> SELECT Name, Age FROM students WHERE Age IN (SELECT Age FROM teachers);
Empty set (0.01 sec)
```





### FROM 子句子查询

```
SELECT tb_alias.col1,... FROM (SELECT clause) AS tb_alias WHERE Clause;
```



```
mysql> SELECT aage,ClassID FROM ( SELECT avg(Age) AS aage, ClassID FROM students WHERE ClassID IS NOT NULL GROUP BY ClassID) AS s WHERE s.aage>30;
+---------+---------+
| aage    | ClassID |
+---------+---------+
| 36.0000 |       2 |
| 46.0000 |       5 |
+---------+---------+
2 rows in set (0.00 sec)
```



## 联合查询 UNION

```
mysql> SELECT Name,Age FROM students UNION SELECT Name,Age FROM teachers;
+---------------+-----+
| Name          | Age |
+---------------+-----+
| Shi Zhongyu   |  22 |
| Shi Potian    |  22 |
| Xie Yanke     |  53 |
| Ding Dian     |  32 |
| Yu Yutong     |  26 |
| Shi Qing      |  46 |
| Xi Ren        |  19 |
| Lin Daiyu     |  17 |
| Ren Yingying  |  20 |
| Yue Lingshan  |  19 |
| Yuan Chengzhi |  23 |
| Wen Qingqing  |  19 |
| Tian Boguang  |  33 |
| Lu Wushuang   |  17 |
| Duan Yu       |  19 |
| Xu Zhu        |  21 |
| Lin Chong     |  25 |
| Hua Rong      |  23 |
| Xue Baochai   |  18 |
| Diao Chan     |  19 |
| Huang Yueying |  22 |
| Xiao Qiao     |  20 |
| Ma Chao       |  23 |
| Xu Xian       |  27 |
| Sun Dasheng   | 100 |
| Jingjiao King | 100 |
| Yinjiao King  |  98 |
| Song Jiang    |  45 |
| Zhang Sanfeng |  94 |
| Miejue Shitai |  77 |
| Lin Chaoying  |  93 |
+---------------+-----+
31 rows in set (0.00 sec)

mysql> EXPLAIN SELECT Name,Age FROM students UNION SELECT Name,Age FROM teachers\G;
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
3 rows in set, 1 warning (0.00 sec)
```







# 数据库操作



## CREATE	ALTER	DROP

[IF EXISTS]

[IF NOT EXISTS]





# 表



## 设计表

### 字符集

主要是为了保存emoji表情，例如: 微信昵称，就有很多带有emoji表情的，这里我们使用utf8mb4字符集，千万不要使用blob类型来存储



### 主/外键类型

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
> 主外键的数据类型一定要一致！
>
> 每个表中的主键命名保持一致！

```
create table t_base_user(
id bigint(20) unsigned not null primary key auto_increment;
....
)
```



无符号与有符号的区别

```
有符号允许存储负数，无符号只允许从正数开始，无符号最小值为0，最大值根据类型不同而不同。
```



外键约束用来保证数据完整性的。但不建议在数据库表中加外键约束，因为在数据表中添加外键约束，会影响性能，例如: 每一次修改数据时，都要在另外的一张表中执行查询。应该是：在应用层，也就是代码层面，来维持外键关系。



### 添加注释

添加注释，这是非常重要的，其中包括表注释，字段注释。主要是为了后期表结构的维护，我相信你对着数据表中那么多字段，如果没有注释的话，你是很难一下子明白是什么意思的，即使你是该表结构的设计者，十天半个月过去后，你还记得吗？

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





## 定义

### 字段

```
字段：字段名， 字段数据类型，修饰符
```



### 索引

应该创建在经常用作查询条件的字段上

实现级别在存储引擎



#### 稠密索引

#### B+索引， hash索引， R树索引， FULLTEXT索引



#### 稀疏索引

#### 聚合索引， 非聚合索引



#### 简单索引，组合索引



## 创建表

* 直接创建

```
mysql> help create table
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    (create_definition,...)
    [table_options]
    [partition_options]
```



* 通过现存的表创建，新表会被直接插入查询而来的数据

```
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    [(create_definition,...)]
    [table_options]
    [partition_options]
    [IGNORE | REPLACE]
    [AS] query_expression
```



* 通过复制现存表结构创建，只复制表不复制数据

```
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    { LIKE old_tbl_name | (LIKE old_tbl_name) }
```



### 表类型

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



### 表结构

```
DESCRIBE tbl_name;
```



### 表状态信息

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



### 删除表

```
DROP TABLE
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



### B+ Tree，HASH索引（键值索引），R Tree



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





# 视图 VIEW

视图是一张虚表，是存储下来的select 语句



视图中的数据事实上存储于“基表”中，其修改操作也会针对基表实现，其修改操作受基表限制



## 创建view

```
CREATE
    [OR REPLACE]
    [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
    [DEFINER = { user | CURRENT_USER }]
    [SQL SECURITY { DEFINER | INVOKER }]
    VIEW view_name [(column_list)]
    AS select_statement
    [WITH [CASCADED | LOCAL] CHECK OPTION]
```



```
mysql> CREATE VIEW test AS SELECT StuID,Name,Age FROM students;
Query OK, 0 rows affected (0.02 sec)

mysql> SHOW TABLES;
+-------------------+
| Tables_in_hellodb |
+-------------------+
| classes           |
| coc               |
| courses           |
| scores            |
| students          |
| teachers          |
| test              |
| toc               |
+-------------------+
8 rows in set (0.01 sec)

```



```
mysql> SHOW TABLE STATUS LIKE 'test'\G;
*************************** 1. row ***************************
           Name: test
         Engine: NULL
        Version: NULL
     Row_format: NULL
           Rows: 0
 Avg_row_length: 0
    Data_length: 0
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2018-12-21 12:44:43
    Update_time: NULL
     Check_time: NULL
      Collation: NULL
       Checksum: NULL
 Create_options: NULL
        Comment: VIEW
1 row in set (0.01 sec)
```



### 删除view

```
DROP VIEW [IF EXISTS]
    view_name [, view_name] ...
    [RESTRICT | CASCADE]
```





