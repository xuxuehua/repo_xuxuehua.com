---
title: "basic_knowledge"
date: 2018-08-05 01:29
---

[TOC]

# 基础知识



## RDBMS 关系型数据库管理系统

`关系型数据库`又称`为关系型数据库管理系统`(RDBMS),它是利用数据概念实现对数据处理的算法，达到对数据及其快速的增删改查操作



例子：

`表单A` 中有一个名为user_id的字段

`表单B` 中也有一个名为user_id的字段

现在我把他们建立一种联系，当我去修改`表单A`的user_id的值时，`表单B` 中的user_id的值也会自动进行修改，因为他们建立的一种`关系`，因为这种关系，`使得数据具有一致性`





## 主键

你可以理解为**主要关键字.  主键在当前表单的当前字段是唯一的** 

比如数据库通常都是在第一个字段是 `ID`，这个通常就是一个主键，它默认会自增长。它在名为`ID`的字段下是不会重复的，每行的值与其他行的值不会重复。



## 外键

主要用于两个表直接的关联

比如我现在有一个名为`A` 和 `B` 的表单，在`A` 中有一个名为 **username**的字段，在`B`中有一个名为**user_email**的字段，这时**username去关联user_emai**l的字段，这时的**username**字段就叫做**外键.**



## 索引

把表中的特殊数据提取出来

利用一定的算法方法，对专门的字段进行优化，使其加快查询速率。



### 视图



## 数据库

### DDL 定义语言



### DML 操作语言





# MySQL

与MariaDB的接口基本兼容

使用插件式存储引擎

单进程多线程的模型（连接线程和守护线程）



## 逻辑架构划分

### Server 层

包含连接器，查询缓存，分析器，优化器，执行器等，以及所有的内置函数（时间，日期，数学和加密函数等）

所有跨存储引擎的功能都在这一层体现，比如存储过程，触发器，视图等



#### 连接器

负责和客户端建立连接，获取权限，维持和管理连接，如

```
mysql -h$ip -P$port -u$user -p
```

连接器会到权限表里面查出你拥有的权限。用户一旦成功建立连接，也不会影响已经存在的连接权限

```
MariaDB [(none)]> show processlist\G;
*************************** 1. row ***************************
      Id: 75
    User: root
    Host: localhost
      db: NULL
 Command: Query
    Time: 0
   State: NULL
    Info: show processlist
Progress: 0.000
1 row in set (0.00 sec)
```

> 客户端长时间没有动静，Command会显示Sleep，自动断开连接
>
> 默认8小时，由参数wait_timeout控制



避免长连接期间，执行过程中因临时使用内存导致内存增长过快，因为资源会在连接断开的时候才释放，可以在mysql5.7之后的新版本执行

```
mysql_reset_connection
```

从而初始化连接资源，此过程不需要重连接和重新做权限认证



#### 查询缓存（不建议使用）

MySQL拿到一个查询请求后，会先到查询缓存看看，之前是不是执行过该语句。执行过的语句会以key-value形式存储在内存中

如果语句不在查询缓存中，就会继续后面的执行阶段。执行完成后，执行结果会被存储在查询缓存中



查询缓存的命中率一般非常低，除非是一张静态表，很长时间才会更新一次，比如系统配置表



该设置以按需使用方式

```
query_cache_type=DEMAND
```



或者使用SQL_CACHE显示指定

```
mysql> SELECT SQL_CACHE * from T where ID=10;
```



MySQL 8.0没有查询缓存功能了



#### 分析器 （做什么）

如果没有命中查询缓存，分析器会对语句做词法分析，根据语法规则做判断



#### 优化器 （怎么做）

优化器是在表里面有多个索引的时候，决定使用哪个索引，或者在一个语句有多个关联（join）的时候，决定各个表的连接顺序



#### 执行器 （执行）

执行SQL 语句

慢查询日志中 `rows_examined` 表示语句执行过程中描述了多少行， 这个值在执行器每次调用引擎获取数据行的时候累加





### 存储引擎层

负责数据的存储和读取，其架构模式是插件式的，支持InnoDB，MyISAM，Memory等多个存储引擎



## 配置文件

集中式的配置文件，能够为mysql的各应用程序提供配置信息

```
[mysqld]
[mysql_safe] 线程安全的mysql配置
[mysqld_multi] 多实例模型的mysql，共享的参数在这里
[server]
[mysql]
[mysqldump] 备份
[client]
```



### 查找路径

依次找到，越靠后，越是最终生效的

```
/etc/my.cnf -> /etc/mysql/my.cnf -> $MYSQL_HOME/my.cnf -> --default-extra-file=/path/to/somedir/my.cnf -> ~/.my.cnf
```







## 安装后设定

```
mysql_secure_installation
```



设置root密码

```
mysql> SET PASSWORD;
or
mysql> update mysql.user SET password=PASSWORD('your pass') WHERE cluase;
mysql> flush privileges;
```



删除匿名用户

```
mysql> DROP USER ''@'localhost';
```



关闭主机名反解

```
skip name resolve
```



## 客户端程序

### mysql

命令行工具

#### 可用选项

```
-u, --user=
-h, --host=
-p, --password=
-P, --port=
--protocol={tcp|sock}
-S, --socket=
-D, --database=  #指定数据库
-C, --compress=  #压缩
```

```
mysql -e "SQL" #在非交互模式中，指定SQL指令并返回
```



#### 脚本模式操作

```
$ mysql -uUSERNAME -hHOST -pPASSWORD < /path/from/somefile.sql
or 
mysql> source /path/from/somefile.sql
```



### mysqldump 

备份工具，基于mysql协议想mysqld发起查询请求，并将查到的所有数据转换成insert等写操作语句保存到文本文件中



### mysqladmin

基于mysql协议管理mysqld



### mysqlimport

数据导入工具





## 程序默认使用的配置

```
mysql --print-defaults
or
mysqld --print-defaults
```





## 服务器端 mysqld 



### 获取参数列表

```
mysql --help --verbose
```



### 服务器参数及其值

有些参数支持运行时修改，会立即生效；

有些参数不支持，且只能通过修改配置文件，并重启服务器程序生效

```
mysql> SHOW GLOBAL VARIABLES:
mysql> SHOW SESSION VARIABLES;
```

> 有些参数作用域是全局的，且不可改变
>
> 有些可以为每个用户提供单独的配置



### 修改服务器变量的值

```
mysql> help SET
```



* 全局

```
mysql> SET GLOBAL system_var_name=value;
mysql> SET @@global.system_var_name=value;
```



* 会话

```
mysql> SET [SESSION] system_var_name=value;
mysql> SET @@[session.]system_var_name=value;
```



### 状态变量

用于保存mysqld运行中统计数据的变量

```
mysql> SHOW GLOBAL STATUS;
mysql> SHOW [SESSION] STATUS;
```



### 





## 基本授权

### 授权用户

```
grant all on test.* to 'rick'@'localhost' identified by "rickpass";
```

> `grant`是Mysql一个专门控制权限的命令
> `all` 指的是所有权限
> `test.*` test是数据库名字，然后后边的 `.*`是指当前所有表
> `'rick'@'localhost'` 其中前面的rick指的是用户名，而**localhost**指的是这个用户名能在哪里进行登录，这里的localhost是本地。
> `identified by "rickpass"` 指的是设置密码









## UTF-8 模式

### 设置utf-8



MySQL的配置文件默认存放在`/etc/my.cnf`或者`/etc/mysql/my.cnf`：

```
[client]
default-character-set = utf8

[mysqld]
default-storage-engine = INNODB
character-set-server = utf8
collation-server = utf8_general_ci
```



### 显示utf-8

```
mysql> show variables like '%char%';
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | utf8                       |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | utf8                       |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.00 sec)
```







## 安装



### docker 方法

#### 启动

```
docker run --name some-mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD='123456' -d mysql:latest
```



#### 访问

```
mysql -uroot -p123456 -h 127.0.0.1 -P3306
```









# 并发控制

## 锁分类

### 隐式锁 （ 推荐）

有存储引擎自动施加的锁， 推荐使用

### 显式锁

手动添加的锁，如LOCK TABLES



## 锁控制

### 读锁

共享锁



### 写锁

独占锁



## 锁粒度





### 表级锁

用户可以显示请求， 但不建议手动显示请求施加写操作



### 行级锁



### 锁策略

在锁粒度及数据安全性寻求的平衡机制

每种存储引擎都可以自行实现其锁策略和锁粒度

MySQL在服务器级也实现了锁



#### 方法一

语法

```
LOCK TABLES tbl_name [[AS] alias] lock_type [, tbl_name [[AS] alias] lock_type]
```

```
UNLOCK TABLES
```



* lock_type

```
READ [LOCAL] | [LOW_PRIORITY] WRITE
```



```
LOCK TABLES students READ;
UNLOCK TABLES;
LOCK TABLES students WRITE;
```



#### 方法二

```
FLUSH TABLES tb_name[,...] [WITH READ LOCK]
```



#### 方法三

```
SELECT class [FOR UPDATE] [WITH READ LOCK]
```

