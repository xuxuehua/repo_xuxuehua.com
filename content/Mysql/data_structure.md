---
title: "data_structure"
date: 2018-12-20 20:39
---


[TOC]



# 数据类型



## 字符型

定长字段，建议使用 CHAR 类型，不定长字段尽量使用 VARCHAR，且仅仅设定适当的最大长度，而不是非常随意的给一个很大的最大长度限定，因为不同的长度范围，MySQL也会有不一样的存储处理。

```
字符串类型	字节数	取值范围
char(m)	m	m为0 ~ 255之间的整数
varchar(m)	值长度+1	m为0~65535之间的整数
tinytext	值长度+2	允许长度0~255字节
text	值长度+2	允许长度0~65535字节
mediumtext	值长度+3	允许长度0~167772150字节
longtext	值长度+3	允许长度0~4294967295字节
binary(m)	m	允许0~m个字节定长的字符串
varbinary(m)	值长度+1	允许0~m个字节变长的字符串
tinyblob	值长度+1	允许长度0~255字节
blob	值长度+2	允许长度0~65535字节
mediumblob	值长度+3	允许长度0~167772150字节
longblob	值长度+4	允许长度0~4294967295字节
enum	1或2	1255个成员需要1个字节存；25565535个成员，2个字节存
set	1/2/3/4/8	类似enum,set一次可以选取多个成员，而enum只能一个
```



### CHAR / BINARY  定长



### VARCHAR / VARBINARY 变长 

需要结束符



### TEXT / TINYTEXT / MEDIUMTEXT / LONGTEXT

非万不得已不要使用 TEXT 数据类型，其处理方式决定了他的性能要低于char或者是varchar类型的处理。





### BLOB / TINYBLOB / MEDIUMBLOB / LONGBLOB

强烈反对在数据库中存放 LOB 类型数据



## 字符类型修饰符

### NOT NULL

非空约束

NULL 类型比较特殊，SQL 难优化。虽然 MySQL NULL类型和 Oracle 的NULL 有差异，会进入索引中，但如果是一个组合索引，那么这个NULL 类型的字段会极大影响整个索引的效率。此外，NULL 在索引中的处理也是特殊的，也会占用额外的存放空间。



### NULL



### DEFAULT ‘STRING’ 

指明默认类型



### CHARACTER SET

使用的字符集



### COLLATION

使用排序的规则

```
mysql> SHOW CHARACTER SET;
mysql> SHOW COLLATION;
```









## 数值型



### 精确数值



#### 整型

##### TINYINT / SMALLINT / MEDIUMINT / INT / BIGINT

在数据量较大的情况下，建议区分开 TINYINT / INT / BIGINT 的选择，因为三者所占用的存储空间也有很大的差别，能确定不会使用负数的字段，建议添加unsigned定义

```
整数类型	字节数	最小值 ~ 最大值
tinyint	1	-128~127 或 0-255
smallint	2	-32768~32767 或 0~65535
mediumint	3	-8388608~8388607 或 0~1677215
int	4	-2147483648~2147483647 或 0~4294967295
bigint	8	-9223372036854775808~9223372036854775807 或 0~18446744073709551615
```



#### 整型数据修饰型

NOT NULL

NULL

DEFAULT NUMBER



##### AUTO_INCREMENT

UNSIGNED 不允许有负数

PRIMARY KEY | UNIQUE KEY

NOT NULL

```
mysql> SELECT LAST_INSERT_ID();
```





#### 十进制型

##### DECIMAL

对货币等对精度敏感的数据，应该用定点数表示或存储

固定精度的小数，也不建议使用DECIMAL，建议乘以固定倍数转换成整数存储，可以大大节省存储空间，且不会带来任何附加维护成本

```
定点数类型	字节数	最小值 ~ 最大值
dec(m,d)	m+2	最大取值范围与double相同，给定decimal的有效值取值范围由m和d决定
```



### 近似数值

#### 浮点型

编程中，如果用到浮点数，要特别注意误差问题，并尽量避免做浮点数比较

##### FLOAT



##### DOUBLE

非万不得已不要使用DOUBLE，不仅仅只是存储长度的问题，同时还会存在精确性的问题

```
浮点数类型	字节数	最小值 ~ 最大值
double	4	±1.175494351E-38 ~ ± 3.402823466E+38
double	8	±2.2250738585072014E-308 ~ ±1.7976931348623157E+308
```



#### BIT

```
位类型	字节数	最小值 ~ 最大值
bit(m)	1~8	bit(1) ~ bit(64)
```



## 日期时间型

尽量使用TIMESTAMP类型，因为其存储空间只需要 DATETIME 类型的一半

对于只需要精确到某一天的数据类型，建议使用DATE类型，因为他的存储空间只需要3个字节

```
时间日期类型	字节数	最小值 ~ 最大值
date	4	1000-01-01 ~ 9999-12-31
datetime	8	1000-01-01 00:00:00 ~ 9999-12-31 23:59:59
timestamp	4	19700101080001 ~ 2038年某个时刻
time	3	-838:59:59 ~ 838:59:59
year	1	1901 ~ 2155
```



### DATE

```
类型可用于需要一个日期值而不需要时间部分时。MySQL 以

‘YYYY-MM-DD’

格式检索与显示DATE值。支持的范围则是

‘1000-01-01’ 
到 
‘9999-12-31’。
```



### TIME



### DATETIME

```
类型可用于需要同时包含日期和时间信息的值。MySQL 以：

‘YYYY-MM-DD HH:MM:SS’

格式检索与显示 DATETIME 类型。支持的范围是：

‘1000-01-01 00:00:00’ 
到 
‘9999-12-31 23:59:59’。

(“支持”的含义是，尽管更早的值可能工作，但不能保证他们均可以。)
```





### TIMESTAMP



### YEAR(2)  YEAR(4)





## 内建类型

对于状态字段，可以尝试使用 ENUM 来存放，因为可以极大的降低存储空间，而且即使需要增加新的类型，只要增加于末尾，修改结构也不需要重建表数据。如果是存放可预先定义的属性数据呢?可以尝试使用SET类型，即使存在多种属性，同样可以游刃有余，同时还可以节省不小的存储空间。







# sql_mode 配置

定义mysqld对约束等的响应行为

sql_mode这个变量默认是空值，在这种模式下是可以允许一些非法操作，如非法数据插入

在生产环境需要设置为严格模式

```
show variables like 'sql_mode';

set sql_mode='ONLY_FULL_GROUP_BY';
```



## 修改方式

需要修改权限，仅对修改后新建会话有效，对已经建立的会话无效

```
mysql> SET GLOBAL sql_mode='MODE';
mysql> SET @@global.sql_mode='MODE';
```



## ONLY_FULL_GROUP_BY

对于group by 聚合操作，如果select中的列，没有在group by中出现，sql就是不合法的



## STRICT_TRANS_TABLES

如果一个值不能插入到一个事务表中，则中断当前的操作，对非事务表不做限制



## NO_ZERO_IN_DATE

在严格模式下，不允许日期和月份为0



## NO_ZERO_DATE

数据库中不允许插入零日期，插入零日期会抛出错误而不是警告



## ERROR_FOR_DIVISION_BY_ZERO

在insert和update过程中，如果数据被零除，则产生错误而非警报

若不设定会返回NULL



## NO_AUTO_CREATE_USER

禁止GRANT 空密码用户



## NO_ENGINE_SUBSTITION





## TRADITIONAL

```
mysql> set sql_mode='TRADITIONAL';
```

> 只对当前会话有效



```
mysql> SHOW VARIABLES LIKE 'sql_mo%';
+---------------+----------------------------------------------------------------------------------------------------------------------------------+
| Variable_name | Value                                                                                                                            |
+---------------+----------------------------------------------------------------------------------------------------------------------------------+
| sql_mode      | STRICT_TRANS_TABLES,STRICT_ALL_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,TRADITIONAL,NO_ENGINE_SUBSTITUTION |
+---------------+----------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```







# Mysql 数据文件类型

数据文件

索引文件

重做日志

撤销日志

二进制日志

错误日志

查询日志

慢查询日志

中继日志


