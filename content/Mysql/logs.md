---
title: "logs 日志"
date: 2019-01-07 12:21
---


[TOC]



# 日志



## Write-Ahead-Logging (WAL) 机制

即先写日志，再写入磁盘



### 重做日志 redo log

InnoDB 会先把记录写入redo log 中，并更新内存，此时更新就算完成。

同时，InnoDB引擎会在适当的时候，将操作记录更新到磁盘里面，而这个更新往往是系统比较空闲的时候做。



InnoDB 中的redo log是固定大小

redo log是物理日志，记录的是在某个数据页上做了什么修改

redo log 是循环写入，空间由于固定会用完



```
innodb_flush_log_at_trx_commit=1
```

> 表示每次事务的redo log都是直接持久化到磁盘，即MySQL异常重启之后不会丢失数据





### 归档日志 bin log

MySQL在Server层面实现的，所有引擎都可以使用

binlog 是逻辑日志，记录的是这个语句的原始逻辑，即所有的逻辑操作

binlog 是可以追加写入，即文件大到一定程度会切换到下一个，并不会覆盖之前的日志



```
sync_binlog=1
```

> 表示每次事务的binlog都持久化到磁盘，保证MySQL异常重启后binlog不丢失



## 查询日志 query log

查询操作的日志，默认是关闭

可以记录在文件中，或者表中

### 配置管理

```
MariaDB [(none)]> SHOW GLOBAL VARIABLES LIKE "%log%";

general_log=ON|OFF
general_log_file=HOSTNAME.log 
log_output=TABLE|FILE|NONE
```







## 慢查询日志 slow query log

用来记录MySQL中响应时间超过阈值的语句，即运行时间超过long_query_time的SQL会被记录

查询操作超出指定时常的查询操作。一般开启，查看查询语句过慢的原因，如果不是调优需要，不建议开启



### 配置管理

```
MariaDB [(none)]> SHOW GLOBAL VARIABLES LIKE 'long_query_time';
+-----------------+-----------+
| Variable_name   | Value     |
+-----------------+-----------+
| long_query_time | 10.000000 |
+-----------------+-----------+
1 row in set (0.01 sec)

MariaDB [(none)]> SELECT @@GLOBAL.long_query_time;
+--------------------------+
| @@GLOBAL.long_query_time |
+--------------------------+
|                10.000000 |
+--------------------------+
1 row in set (0.00 sec)
```





### 设置时常

```
SET GLOBAL long_query_time=
```



```
MariaDB [(none)]> SHOW GLOBAL VARIABLES LIKE "%slow_query%";
+---------------------+--------------------+
| Variable_name       | Value              |
+---------------------+--------------------+
| slow_query_log      | OFF                |
| slow_query_log_file | localhost-slow.log |
+---------------------+--------------------+
2 rows in set (0.00 sec)

slow_query_log=ON|OFF
slow_query_log_file=HOSTNAME-slow.log
```



```
MariaDB [(none)]> SHOW GLOBAL VARIABLES LIKE '%log_slow_%';
+---------------------+--------------------------------------------------------------------------------------------------------------+
| Variable_name       | Value                                                                                                        |
+---------------------+--------------------------------------------------------------------------------------------------------------+
| log_slow_filter     | admin,filesort,filesort_on_disk,full_join,full_scan,query_cache,query_cache_miss,tmp_table,tmp_table_on_disk |
| log_slow_queries    | OFF                                                                                                          |
| log_slow_rate_limit | 1                                                                                                            |
| log_slow_verbosity  |                                                                                                              |
+---------------------+--------------------------------------------------------------------------------------------------------------+
4 rows in set (0.00 sec)



```



### mysqldumpslow

日志分析工具

-s: 是表示按照何种方式排序

c: 访问次数

l: 锁定时间

r: 返回记录

t: 查询时间

al:平均锁定时间

ar:平均返回记录数

at:平均查询时间

-t:即为返回前面多少条的数据；

-g:后边搭配一个正则匹配模式，大小写不敏感的；

```
得到返回记录集最多的10个SQL 
mysqldumpslow -s r -t 10 /var/lib/mysql/atguigu-slow.log 
 
得到访问次数最多的10个SQL 
mysqldumpslow -s c -t 10 /var/lib/mysql/atguigu-slow.log 
 
得到按照时间排序的前10条里面含有左连接的查询语句 
mysqldumpslow -s t -t 10 -g "left join" /var/lib/mysql/atguigu-slow.log 
 
另外建议在使用这些命令时结合 | 和more 使用 ，否则有可能出现爆屏情况 
mysqldumpslow -s r -t 10 /var/lib/mysql/atguigu-slow.log | more 
```





## 错误日志 error log

也包括服务启动关闭的正常日志， 默认是关闭的

mysqld启动和关闭过程中输出的事件信息

mysqld运行中产生的错误信息

event scheduler 运行一个event时产生的日志信息

在主从复置架构中的从服务器



### 配置管理

```
MariaDB [(none)]> SHOW GLOBAL VARIABLES LIKE '%log_e%';
+---------------+-------------------------------+
| Variable_name | Value                         |
+---------------+-------------------------------+
| log_error     | /data/mysql/centos7.is.cc.err |
+---------------+-------------------------------+
1 row in set (0.00 sec)

log_error=/PATH/TO/LOG_ERROR_FILE
```



```
MariaDB [(none)]> SHOW GLOBAL VARIABLES LIKE '%log_w%';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| log_warnings  | 1     |
+---------------+-------+
1 row in set (0.00 sec)

log_warnings=1|0  # 是否记录警报信息至错误日志中
```



## 二进制日志 binary log (重要)

非常重要，以二进制格式存储，不能使用cat等方式查看日志

记录导致数据改变或者潜在导致数据改变的SQL语句



### 功能

用于通过重放日志文件中的事件来生成服务器副本



### 配置管理

```
MariaDB [(none)]> SHOW BINARY LOGS;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 |     28415 |
| mysql-bin.000002 |   1038814 |
| mysql-bin.000003 |       245 |
+------------------+-----------+
3 rows in set (0.00 sec)

MariaDB [(none)]> SHOW MASTER LOGS;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 |     28415 |
| mysql-bin.000002 |   1038814 |
| mysql-bin.000003 |       245 |
+------------------+-----------+
3 rows in set (0.00 sec)
```



```
MariaDB [(none)]> SHOW BINLOG EVENTS IN 'mysql-bin.000003';
+------------------+-----+-------------+-----------+-------------+-------------------------------------------+
| Log_name         | Pos | Event_type  | Server_id | End_log_pos | Info                                      |
+------------------+-----+-------------+-----------+-------------+-------------------------------------------+
| mysql-bin.000003 |   4 | Format_desc |         1 |         245 | Server ver: 5.5.62-MariaDB, Binlog ver: 4 |
+------------------+-----+-------------+-----------+-------------+-------------------------------------------+
1 row in set (0.00 sec)
```

> Pos 日志位置
>
> End_log_pos 下一个事件的开始位置
>
> Server_id 需要全局唯一
>
> Info 记录事件本身是什么



查看正在使用的二进制文件

```
MariaDB [(none)]> SHOW MASTER STATUS;
+------------------+----------+--------------+------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000003 |      245 |              |                  |
+------------------+----------+--------------+------------------+
1 row in set (0.00 sec)
```



```
SHOW BINLOG EVENTS [IN 'log_name'] [FROM pos] [LIMIT [offset,] row_count]
```



### 日志记录格式

#### statement

基于语句记录



#### row

基于行记录，需要精确记录



#### mixed

混合模式，让系统自行判断其基于哪种方式进行



### 日志构成

#### mysql-bin.文件名后缀

日志文件，二进制格式



#### mysql-bin.index

索引文件，文本格式



### 服务器变量

```
sql_log_bin=ON|OFF
```

> 是否记录二进制日志



```
log_bin=/PATH/TO/BIN_LOG_FILE
```

> 记录的文件位置，通常为ON



```
binlog_format=STATEMENT|ROW|MIXED
```

> 二进制日志的记录格式



```
max_binlog_size=1073741824
```

> 单个二进制文件的最大体积，默认为1G
>
> 到达最大值会自动滚动
>
> 文件到达上限时的大小未必为指定的精确值



```
expire_logs_days=0
```

> log过期时间
>
> 0 表示不清理log



```
sync_binlog=0|1
```

> 设定是否启动二进制日志同步功能
>
> 0 会影响数据
>
> 1 会影响性能



### 命令操作

#### mysqlbinlog

```
mysqlbinlog [OPTIONS] log_file
	--start-position
	--stop-position
	--start-datetime=
	--stop-datetime=
		YYYY-MM-DD hh:mm:ss

```

客户端命令工具

```
[root@centos7 ~]# /usr/local/mysql/bin/mysqlbinlog /data/mysql/mysql-bin.000002
# at 245
#190107 23:50:04 server id 1  end_log_pos 314 	Query	thread_id=1	exec_time=0	error_code=0
SET TIMESTAMP=1546923004/*!*/;
SET @@session.pseudo_thread_id=1/*!*/;
SET @@session.foreign_key_checks=1, @@session.sql_auto_is_null=0, @@session.unique_checks=1, @@session.autocommit=1/*!*/;
SET @@session.sql_mode=0/*!*/;
SET @@session.auto_increment_increment=1, @@session.auto_increment_offset=1/*!*/;
/*!\C utf8 *//*!*/;
SET @@session.character_set_client=33,@@session.collation_connection=33,@@session.collation_server=33/*!*/;
SET @@session.lc_time_names=0/*!*/;
SET @@session.collation_database=DEFAULT/*!*/;
BEGIN
/*!*/;
```

> 190107 23:50:04: 事件发生的日期和时间
>
>  server id 1： 时间发生的服务器标识
>
> end_log_pos 314： 事件的结束位置
>
> Query：事件类型
>
> thread_id=1： 事件发生时所在服务器执行此事件的线程ID
>
> exec_time=0： 语句的时间戳与将其写入二进制文件中的时间差
>
> error_code=0： 错误代码



## 中继日志 relay log

复制架构中，从服务器用于保存来自主服务器的二进制日志读取到的事件



## 事务日志 transaction log

ACID测试是依赖事务日志完成其特性，事务型存储引擎自行管理和使用







