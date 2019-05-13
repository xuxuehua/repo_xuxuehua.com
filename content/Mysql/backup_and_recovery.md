---
  title: "backup_and_recovery 备份和恢复"
date: 2019-01-09 21:19
---


[TOC]



# 要点

1. 做还原测试，用于测试备份的可用性
2. 还原演练



# 备份类型



## 完全备份

备份整个数据集



## 增量备份

仅仅备份最近一次的完全备份，或者增量备份以来变化的数据



## 差异备份

仅仅备份最近一次完全备份以来变化的数据

即提供运行完整备份后变更的文件的备份选择式备份





## 热备份（常用）

读写操作均可进行

InnoDB 支持



## 温备份

读操作可以执行，但写操作不行

MyISAM 支持



## 冷备份

读写操作均不可进行



## 物理备份

直接复制数据文件进行备份

需要专用的二进制工具

热备份很难



## 逻辑备份

从数据库中导出数据里另存而进行的备份

需要专用的协议和客户端，与存储引擎无关

恢复起来相对容易

热备份容易



# 备份考虑因素

持锁多久

备份过程的时长

备份负载

恢复过程的时长



## 备份的数据

### 数据

### 二进制日志



### InnoDB的事务日志

### 代码

包括存储过程， 存储函数，触发器，事件调度器

### 服务器的配置文件





# 备份方案

## 数据集

完全+增量

## 备份手段

物理，逻辑



## 常用策略

全量备份+增量备份+binlogs



# 备份工具

## mysqldump

逻辑备份工具，适用于所有存储引擎

支持温备份，完全备份，部分备份

对InnoDB存储引擎支持热备份



## cp, tar 等

物理备份工具，适用于所有存储引擎

只能冷备份，支持完全备份，部分备份



## lvm2

快照功几乎实现热备份， 需要借助于文件系统管理工具进行备份



## mysqlhotcopy

几乎实现冷备份，仅适用MyISAM存储引擎



# 工具选择

## mysqldump+复制binlog

速度比较慢，适用于小范围，对要求不是很严格 

mysqldump 进行完全备份

复制binlog中指定时间范围的event，实现增量备份



## Lvm2 快照+复制binlog

lvm2快照，使用cp或tar等做物理备份，实现完全备份

复制binlog中指定时间范围的event，实现增量备份



## xtrabackup 

由Percona提供支持InnoDB的热备份（物理备份）的工具

实现完全备份，增量备份









# mysqldump 

只是备份程序文件

需要手动操作二进制日志的内容，因为在增加备份时，全库的所有操作都会记录到二进制日志中

## Usage 

```
mysqldump [OPTIONS] database [tables]
```



##-A, --all-databases 全部数据库 

备份全部数据库

会自动创建CREATE DATABASE语句

会导致备份的数据不一致

```
mysqldump -uroot --databases hellodb
```



## -B, --databases 指定数据库





## -x, --lock-all-tables 锁定全局

锁定所有库的所有表，会施加全局读锁，对线上写操作会阻塞，

只能实现温备份



## -l, --lock-tables 锁定表

对于每个单独的数据库，在启动备份之前锁定其所有表

只能实现温备份





## --single-transaction 启动单个事务

启动一个巨大的事务语句，保证事务执行完成期间的时间点是一定的



## -E, --events 事件

备份指定数据库相关的events和scheduler



## -R, --routines 过程和函数

Dump stored routines (functions and procedures).

指定数据库相关的所有存储过程和存储函数



## --triggers 触发器

备份表相关的触发器





## --skip-triggers 跳过触发器

跳过触发器备份



## --master-data[=#]

1：记录为CHANGE MASTER TO 语句，此语句不被注释

2：记录为注释的CHANGE MASTER TO 语句（常用）





### example

在还原操作过程中，需要关闭二进制日志，这样不会产生因还原操作生成的二进制日志文件

```
SET sql_log_bin=OFF;
```



```
mysqldump -uroot --all-databases --lock-all-tables --master-data=2 > all.sql
```



`cat all.sql`

```
--
-- Position to start replication or point-in-time recovery from
--

-- CHANGE MASTER TO MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=15773;

--
-- Current Database: `hellodb`
--
```



做一些操作

```
MariaDB [hellodb]> INSERT into students (Name,Age,Gender,ClassID,TeacherID)  values ('Chao Gai', 47, 'M', 3, 7);
MariaDB [hellodb]> DELETE FROM students WHERE StuID=3;
```



恢复操作

```
/usr/local/mysql/bin/mysqlbinlog --start-position=15773 /mydata/data/mysql-bin.000001 > incremental.sql

mysql < incremental.sql
```







##  -F, --flush-logs 日志滚动

二进制日志滚动，锁定表完成后指定flush logs命令

二进制日志不应该与数据文件放在同一个磁盘上





# lvm2 备份

## 1. 请求锁定所有表

```
FLUSH TABLES WITH READ LOCK;
```





## 2. 记录二进制日志文件以及事件位置

```
FLUSH  LOGS;
SHOW MASTER STATUS;
```

```
mysql -e 'SHOW MASTER STATUS' > /PATH/TO/SOMEFILE
```





## 3. 创建快照

```
lvcreate -L # -s -p r -n NAME /DEV/VG_NAME/LV_NAME
```



## 4. 释放锁

```
UNLOCK TABLES；
```



## 5. 挂载快照卷，执行数据备份



## 6. 备份完成后，删除快照卷



## 7. 定制好策略 通过原卷备份二进制日志



# Xtrabackup

## Installation

```
yum -y install https://www.percona.com/downloads/XtraBackup/Percona-XtraBackup-2.4.8/binary/redhat/7/x86_64/percona-xtrabackup-24-2.4.8-1.el7.x86_64.rpm
```



## 完全备份



确保存储引擎为InnoDB

```
MariaDB [hellodb]> show table status\G;
```



确保`innodb_file_per_table` 为ON

```
MariaDB [hellodb]> SHOW GLOBAL VARIABLES LIKE '%innodb_file_per_table%';
+-----------------------+-------+
| Variable_name         | Value |
+-----------------------+-------+
| innodb_file_per_table | ON    |
+-----------------------+-------+
1 row in set (0.00 sec)
```

```
rm -rf /data/mysql/*
rm -rf /data/binlogs/*

vim my.cnf

[mysqld]
innodb_file_per_table=ON
```



查看二进制日志序号

```
mkdir /backups

MariaDB [(none)]> SHOW BINARY LOGS;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 |     16255 |
+------------------+-----------+
1 row in set (0.00 sec)
```



```
innobackupex --user=root /backups/
```





或者（未测试）

```
innobackupex --user=root --password=redhat --apply-log /data/xt_full_backup/20181112
```



## 完全还原

确保`innodb_file_per_table`参数是ON



复制所有文件到backups目录

整理log

```
innobackupex --apply-log /backups/
```



还原操作

```
systemctl stop mariadb

rm -rf /data/mysql/*

innobackupex --copy-back /backups/

chown -R mysql.mysql ./*
```



或者(未测试)

```
innobackupex --user=root --password=redhat --copy-back /data/xt_full_backup/20181112
```

> --copy-back需要再mysqld本地主机执行，mysqld不能启动



## 增量备份

仅能应用于InnoDB 或XtraDB表

需要在每个备份（包括完全备份和各个增量备份）上，将已经提交的事务进行重放，所有备份数据将合并到完全备份上面

基于所有的备份将未提交的事务进行回滚操作



创建新的表，并插入数据

```
MariaDB [(none)]> use hellodb;
Database changed
MariaDB [hellodb]> CREATE TABLE testtb (id int);
Query OK, 0 rows affected (0.01 sec)

MariaDB [hellodb]> INSERT INTO testtb VALUES (1), (10), (99);
Query OK, 3 rows affected (0.00 sec)
Records: 3  Duplicates: 0  Warnings: 0

MariaDB [hellodb]> SELECT * FROM testtb;
```



备份数据

```
rm -rf /backups/*
innobackupex /backups/
```



增量操作

```
MariaDB [hellodb]> DROP TABLE coc;
Query OK, 0 rows affected (0.00 sec)

MariaDB [hellodb]> INSERT INTO testtb VALUES (44),(32);
Query OK, 2 rows affected (0.00 sec)
Records: 2  Duplicates: 0  Warnings: 0
```



```
 innobackupex --incremental /backups/ --incremental-basedir=/backups/2019-01-12_03-44-14/
```



查看备份类型

```
backup_type = incremental
from_lsn = 1703056
to_lsn = 1705718
last_lsn = 1705718
compact = 0
recover_binlog_info = 0
/backups/2019-01-12_03-49-28/xtrabackup_checkpoints (END)
```





## 增量还原

```
/etc/init.d/mysqld stop
```



整理完全备份

```
innobackupex --apply-log --redo-only /backups/2019-01-12_03-44-14/
```



整理增量备份, 合并到完全备份

```
innobackupex --apply-log --redo-only /backups/2019-01-12_03-44-14/ --incremental-dir=/backups/2019-01-12_03-49-28/
```



查看增量合并状态

```
backup_type = log-applied
from_lsn = 0
to_lsn = 1705718
last_lsn = 1705718
compact = 0
recover_binlog_info = 0
/backups/2019-01-12_03-44-14/xtrabackup_checkpoints (END)
```

```
backup_type = incremental
from_lsn = 1703056
to_lsn = 1705718
last_lsn = 1705718
compact = 0
recover_binlog_info = 0
/backups/2019-01-12_03-49-28/xtrabackup_checkpoints (END)
```

> 如果在操作期间还有数据产生，需要使用二进制日志进行时间点还原



通过完全备份恢复

```
rm -rf /mydata/data
innobackupex --copy-back /backups/2019-01-12_03-44-14/
```

```
chown -R mysql.mysql ./*
/etc/init.d/mysqld start

MariaDB [hellodb]> SELECT * FROM testtb;
+------+
| id   |
+------+
|    1 |
|   10 |
|   99 |
|   44 |
|   32 |
+------+
5 rows in set (0.01 sec)
```



### 单张表操作

先进行完全备份

```
innobackupex  /backups/
```



单张表

```
innobackupex --apply-log --export /backups/2019-01-12_04-04-19/
```

会为每张表生产exp文件

```
[root@localhost data]# ls -la /backups/2019-01-12_04-04-19/hellodb/
total 908
drwxr-x---. 2 root root  4096 Jan 12 04:05 .
drwxr-x---. 6 root root  4096 Jan 12 04:05 ..
-rw-r--r--. 1 root root   473 Jan 12 04:05 classes.cfg
-rw-r-----. 1 root root 16384 Jan 12 04:05 classes.exp
-rw-r-----. 1 root root  8636 Jan 12 04:04 classes.frm
-rw-r-----. 1 root root 98304 Jan 12 04:04 classes.ibd
-rw-r--r--. 1 root root   415 Jan 12 04:05 courses.cfg
-rw-r-----. 1 root root 16384 Jan 12 04:05 courses.exp
-rw-r-----. 1 root root  8602 Jan 12 04:04 courses.frm
-rw-r-----. 1 root root 98304 Jan 12 04:04 courses.ibd
-rw-r-----. 1 root root    61 Jan 12 04:04 db.opt
-rw-r--r--. 1 root root   518 Jan 12 04:05 scores.cfg
-rw-r-----. 1 root root 16384 Jan 12 04:05 scores.exp
-rw-r-----. 1 root root  8658 Jan 12 04:04 scores.frm
-rw-r-----. 1 root root 98304 Jan 12 04:04 scores.ibd
-rw-r--r--. 1 root root   640 Jan 12 04:05 students.cfg
-rw-r-----. 1 root root 16384 Jan 12 04:05 students.exp
-rw-r-----. 1 root root  8736 Jan 12 04:04 students.frm
-rw-r-----. 1 root root 98304 Jan 12 04:04 students.ibd
-rw-r--r--. 1 root root   512 Jan 12 04:05 teachers.cfg
-rw-r-----. 1 root root 16384 Jan 12 04:05 teachers.exp
-rw-r-----. 1 root root  8656 Jan 12 04:04 teachers.frm
-rw-r-----. 1 root root 98304 Jan 12 04:04 teachers.ibd
-rw-r--r--. 1 root root   374 Jan 12 04:05 testtb.cfg
-rw-r-----. 1 root root 16384 Jan 12 04:05 testtb.exp
-rw-r-----. 1 root root  8556 Jan 12 04:04 testtb.frm
-rw-r-----. 1 root root 98304 Jan 12 04:04 testtb.ibd
-rw-r--r--. 1 root root   455 Jan 12 04:05 toc.cfg
-rw-r-----. 1 root root 16384 Jan 12 04:05 toc.exp
-rw-r-----. 1 root root  8622 Jan 12 04:04 toc.frm
-rw-r-----. 1 root root 98304 Jan 12 04:04 toc.ibd
```



恢复指定表在新的数据库中

```
MariaDB [(none)]> CREATE DATABASE mydb;
Query OK, 1 row affected (0.00 sec)

MariaDB [(none)]> use mydb;
Database changed
MariaDB [mydb]> SHOW TABLES;
Empty set (0.00 sec)

MariaDB [mydb]>
MariaDB [mydb]>  CREATE TABLE `students` (
    ->   `StuID` int(10) unsigned NOT NULL AUTO_INCREMENT,
    ->   `Name` varchar(50) NOT NULL,
    ->   `Age` tinyint(3) unsigned NOT NULL,
    ->   `Gender` enum('F','M') NOT NULL,
    ->   `ClassID` tinyint(3) unsigned DEFAULT NULL,
    ->   `TeacherID` int(10) unsigned DEFAULT NULL,
    ->   PRIMARY KEY (`StuID`)
    -> ) ENGINE=InnoDB AUTO_INCREMENT=27 DEFAULT CHARSET=utf8
    -> ;
Query OK, 0 rows affected (0.01 sec)

MariaDB [mydb]> desc students;
+-----------+---------------------+------+-----+---------+----------------+
| Field     | Type                | Null | Key | Default | Extra          |
+-----------+---------------------+------+-----+---------+----------------+
| StuID     | int(10) unsigned    | NO   | PRI | NULL    | auto_increment |
| Name      | varchar(50)         | NO   |     | NULL    |                |
| Age       | tinyint(3) unsigned | NO   |     | NULL    |                |
| Gender    | enum('F','M')       | NO   |     | NULL    |                |
| ClassID   | tinyint(3) unsigned | YES  |     | NULL    |                |
| TeacherID | int(10) unsigned    | YES  |     | NULL    |                |
+-----------+---------------------+------+-----+---------+----------------+
6 rows in set (0.01 sec)

MariaDB [mydb]> ALTER TABLE students DISCARD TABLESPACE; #清空表空间
Query OK, 0 rows affected (0.00 sec)
```

```
[root@centos7 mysql]# ls -la /data/mysql/mydb/
total 16
drwx------. 2 mysql mysql 1024 Jan 12 04:12 .
drwxr-xr-x. 7 mysql mysql 1024 Jan 12 04:09 ..
-rw-rw----. 1 mysql mysql   61 Jan 12 04:09 db.opt
-rw-rw----. 1 mysql mysql 8736 Jan 12 04:10 students.frm
```



复制指定文件exp 和idb 文件到指定目录下

```
[root@centos7 mysql]# ls -la /data/mysql/mydb/
total 130
drwx------. 2 mysql mysql  1024 Jan 12 04:16 .
drwxr-xr-x. 7 mysql mysql  1024 Jan 12 04:16 ..
-rw-rw----. 1 mysql mysql    61 Jan 12 04:09 db.opt
-rw-r-----. 1 man   man   16384 Jan 12 04:15 students.exp
-rw-rw----. 1 mysql mysql  8736 Jan 12 04:10 students.frm
-rw-r-----. 1 man   man   98304 Jan 12 04:15 students.ibd
[root@centos7 mysql]# chown -R mysql.mysql *
[root@centos7 mydb]# chmod 660 students.exp students.ibd
[root@centos7 mydb]# chown -R mysql.mysql students.cfg
[root@centos7 mydb]# ls -la
total 132
drwx------. 2 mysql mysql  1024 Jan 12 04:56 .
drwxr-xr-x. 7 mysql mysql  1024 Jan 12 04:47 ..
-rw-rw----. 1 mysql mysql    61 Jan 12 04:47 db.opt
-rw-r--r--. 1 mysql mysql   640 Jan 12 04:56 students.cfg
-rw-rw----. 1 mysql mysql 16384 Jan 12 04:49 students.exp
-rw-rw----. 1 mysql mysql  8736 Jan 12 04:47 students.frm
-rw-rw----. 1 mysql mysql 98304 Jan 12 04:49 students.ibd
```



重新读入新的表空间

```
ALTER TABLE students IMPORT TABLESPACE
```

> 实际操作未成功



![]()