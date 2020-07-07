---
title: "sql_operation"
date: 2018-12-20 21:49
---


[TOC]





# DDL 数据定义语言



## CREATE

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



### copy table 

```
CREATE TABLE new_table_name ( like old_table_name including all)
```





## ALTER

组件同CREATE





## DROP

组件同CREATE





# DML 数据操作语言



## INSERT

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



### insert from select response

```
insert into NEW_TABLE (select * from OLD_TABLE where id=1);
```



## DELETE

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







## UPDATE

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





## SELECT

查询执行路径中的组件：查询缓存，解析器，预处理器，优化器，查询执行引擎，存储引擎



关键字的顺序是不能颠倒

```
SELECT ... FROM ... WHERE ... GROUP BY ... HAVING ... ORDER BY ...
```

SELECT执行流程

```
FROM Clause -> WHERE Clause -> GROUP BY -> HAVING Clause -> SELECT的字段 -> DISTINCT -> ORDER BY -> LIMIT -> 
```



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



### query_cache_type

```
query_cache_type 的值为ON时，查询缓存功能打开
SELECT的结果符合缓存条件即会缓存，否则不予缓存
显式指定SQL_NO_CACHE，不予缓存
```

```
query_cache_type 的值为DEMAND时，查询缓存功能按需进行，
显式指定SQL_CACHE的SELECT语句才会缓存，其他不予缓存
```



### AS 别名

一般来说起别名的作用是对原有名称进行简化，从而让 SQL 语句看起来更精简

```
col1 AS alias1, col2 AS alias2, ....
```

```
SELECT name AS n, hp_max AS hm, mp_max AS mm, attack_max AS am, defense_max AS dm FROM heros;
```



### WHERE 子句

指明过滤条件以实现“选择” 功能



#### 过滤条件

布尔表达式



#### 算数操作符

```
+， -， *， /， %
```



#### 比较操作符

```
=, !=, <>, <=>, >, >=, <, <=
```

```
BETWEEN min_num AND max_num
IN (element1, element2, ...)

IS NOT NULL

RLIKE
REGEXP 匹配字符串可以用正则表达式书写模式
```



#### LIKE 

```
LIKE:
	% 任意长度的任意字符
	_ 任意单个字符
```



```
SELECT name FROM heros WHERE name LIKE '%太%'
```



实际操作尽量少用通配符，因为它需要消耗数据库更长的时间来进行匹配。即使你对 LIKE 检索的字段进行了索引，索引的价值也可能会失效。如果要让索引生效，那么 LIKE 后面就不能以（%）开头，比如使用LIKE '%太%'或LIKE '%太'的时候就会对全表进行扫描。



#### IS NULL

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



#### 逻辑运算符

```
NOT, AND, OR, XOR（异或）
```



```
SELECT name, hp_max, mp_max FROM heros WHERE (hp_max+mp_max) > 8000 OR hp_max > 6000 AND mp_max > 1700 ORDER BY (hp_max+mp_max) DESC
```



### GROUP BY 子句

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



### HAVING

对分组聚合运算后的结果指定过滤条件

HAVING 的作用和 WHERE 一样，都是起到过滤的作用，只不过 WHERE 是用于数据行，而 HAVING 则作用于分组

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



### ORDER BY

ORDER BY 后面可以有一个或多个列名，如果是多个列名进行排序，会按照后面第一个列先进行排序，当第一列的值相同的时候，再按照第二列进行排序，以此类推

ORDER BY 可以使用非选择列进行排序，所以即使在 SELECT 后面没有这个列名，你同样可以放到 ORDER BY 后面进行排序

ORDER BY 通常位于 SELECT 语句的最后一条子句，否则会报错

ASC 升序， DESC 降序 

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



### LIMIT 

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



#### 查询结果加锁

```
FROM UPDATE 写锁，排他锁
LOCK IN SHARE MODE 读锁，共享锁
```



### DISTINCT 去重

从结果中去掉重复的行

```
SELECT DISTINCT attack_range FROM heros
```

> 只对attack_range去重



# 多表查询

## 交叉连接 （笛卡尔积）CROSS JOIN

笛卡尔乘积是一个数学运算。假设我有两个集合 X 和 Y，那么 X 和 Y 的笛卡尔积就是 X 和 Y 的所有可能组合，也就是第一个对象来自于 X，第二个对象来自于 Y 的所有可能。

作用就是可以把任意表进行连接，即使这两张表不相关。但我们通常进行连接还是需要筛选的，因此你需要在连接后面加上 WHERE 子句，也就是作为过滤条件对连接数据进行筛选。



```
 SELECT * FROM player CROSS JOIN team;
```



## 等值连接

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





## 非等值连接

```
SELECT p.player_name, p.height, h.height_level
FROM player AS p, height_grades AS h
WHERE p.height BETWEEN h.height_lowest AND h.height_highest
```





## 外连接

除了查询满足条件的记录以外，外连接还可以查询某一方不满足条件的记录。两张表的外连接，会有一张是主表，另一张是从表。如果是多张表的外连接，那么第一张表是主表，即显示全部的行，而第剩下的表则显示对应连接的信息。



### 左外连接

指左边的表是主表，需要显示左边表的全部行，而右侧的表是从表

```
FROM tb1 LEFT JOIN tb2 ON tb1.col=tb2.col;
```



```
SELECT * FROM player LEFT JOIN team on player.team_id = team.team_id;
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



### 右外连接

右边的表是主表，需要显示右边表的全部行，而左侧的表是从表。

```
FROM tb1 RIGHT JOIN tb2 ON tb1.col=tb2.col;
```



```
SELECT * FROM player RIGHT JOIN team on player.team_id = team.team_id;
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





## 自连接

可以对多个表进行操作，也可以对同一个表进行操作。也就是说查询条件使用了当前表的字段

```
SELECT b.player_name, b.height FROM player as a, player as b WHERE a.player_name = '布雷克-格里芬' and a.height < b.height;
```









# 子查询

在查询语句嵌套查询语句

基于某语句的查询结果再次进行的查询



## WHERE 

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





## FROM 

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



## EXISTS 

EXISTS 子查询用来判断条件是否满足，满足的话为 True，不满足为 False。

```
SELECT player_id, team_id, player_name FROM player WHERE EXISTS (SELECT player_id FROM player_score WHERE player.player_id = player_score.player_id)
```



## IN 

IN表是外边和内表进行hash连接，是先执行子查询。
EXISTS是对外表进行循环，然后在内表进行查询。
因此如果外表数据量大，则用IN，如果外表数据量小，也用EXISTS。
IN有一个缺陷是不能判断NULL，因此如果字段存在NULL值，则会出现返回，因为最好使用NOT EXISTS

```
SELECT * FROM A WHERE cc IN (SELECT cc FROM B)
```





## UNION 联合查询 

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







# example



## 重复记录

查找全部重复记录

```sql
Select * From 表 Where 重复字段 In (Select 重复字段 From 表 Group By 重复字段 Having Count(*)>1)
```





过滤重复记录(只显示一条)

```sql
Select * From HZT Where ID In (Select Max(ID) From HZT Group By Title)
```





删除全部重复记录（慎用）

```sql
Delete 表 Where 重复字段 In (Select 重复字段 From 表 Group By 重复字段 Having Count(*)>1)
```







保留一条（这个应该是大多数人所需要的）

```sql
Delete HZT Where ID Not In (Select Max(ID) From HZT Group By Title)
```







