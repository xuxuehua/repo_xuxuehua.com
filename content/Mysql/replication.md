---
title: "replication 复制"
date: 2019-01-16 12:18
---


[TOC]



# 复制

每个节点都有相同的数据集

实现数据分布，负载均衡读，备份，高可用和故障切换

slave会从master读取binlog来进行数据同步







## 主从复制

1 master将改变记录到二进制日志（binary log）。这些记录过程叫做二进制日志事件，binary log events； 

2 slave将master的binary log events拷贝到它的中继日志（relay log）； 

3 slave重做中继日志中的事件，将改变应用到自己的数据库中。 MySQL复制是异步的且串行化的 



### 主节点

Dump Thread： 为每个Slave的I/O Thread 启动一个dump线程，用于向其发送binary log events





### 从节点

I/O Thread： 从master请求二进制日志事件，并保存于中继日志中

SQL Thread： 从中继日志中读取日志事件，在本地完成重放









## 复制架构

### 一主多从

从服务器还可以再有从服务器

MySQL之间数据复制的基础是二进制日志文件（binary log file）。一台MySQL数据库一旦启用二进制日志后，其作为master，它的数据库中所有操作都会以“事件”的方式记录在二进制日志中，其他数据库作为slave通过一个I/O线程与主服务器保持通信，并监控master的二进制日志文件的变化，如果发现master二进制日志文件发生变化，则会把变化复制到自己的中继日志中，然后slave的一个SQL线程会把相关的“事件”执行到自己的数据库中，以此实现从数据库和主数据库的一致性，也就实现了主从复制。

mysql实现主从复制的方案主要有两种：基于二进制日志的主从复制和基于GTID的主从复制。

传统复制是基于主库的二进制日志进行复制，在主站和从站之间同步二进制日志文件和日志文件中的位置标识。



三个工作线程

- 主服务器提供了一个工作线程I/O dump thread
- 从服务器有两个工作线程，I/O thread和SQL thread



执行过程

- 主库把接收到的SQL请求记录到自己的binlog日志文件
- 从库的I/O thread请求主库I/O dump thread，获得binglog日志，并追加到relay log日志文件
- 从库的SQL thread执行relay log中的SQL语句



#### 复制实现细节分析

MySQL主从复制功能使用**三个线程实现**，**一个在主服务器上**，**两个在从服务器上**

1.Binlog转储线程。

当从服务器与主服务器连接时，主服务器会创建一个线程将二进制日志内容发送到从服务器。
该线程可以使用 语句 `SHOW PROCESSLIST`(下面有示例介绍) 在服务器 sql 控制台输出中标识为Binlog Dump线程。

二进制日志转储线程获取服务器上二进制日志上的锁，用于读取要发送到从服务器的每个事件。一旦事件被读取，即使在将事件发送到从服务器之前，锁会被释放。



2.从服务器I/O线程。

当在从服务器sql 控制台发出 `START SLAVE`语句时，从服务器将创建一个I/O线程，该线程连接到主服务器，并要求它发送记录在主服务器上的二进制更新日志。

从机I/O线程读取主服务器Binlog Dump线程发送的更新 （参考上面 Binlog转储线程 介绍），并将它们复制到自己的本地文件二进制日志中。

该线程的状态显示详情 Slave_IO_running 在输出端 使用 命令`SHOW SLAVE STATUS`

使用`\G`语句终结符,而不是分号,是为了，易读的垂直布局

这个命令在上面 **查看从服务器状态** 用到过

```
mysql> SHOW SLAVE STATUS\G
```



3.从服务器SQL线程。

从服务器创建一条SQL线程来读取由主服务器I/O线程写入的二级制日志，并执行其中包含的事件。

在前面的描述中，每个主/从连接有三个线程。主服务器为每个当前连接的从服务器创建一个二进制日志转储线程，每个从服务器都有自己的I/O和SQL线程。
从服务器使用两个线程将读取更新与主服务器更新事件，并将其执行为独立任务。因此，如果语句执行缓慢，则读取语句的任务不会减慢。

例如，如果从服务器开始几分钟没有运行，或者即使SQL线程远远落后，它的I/O线程也可以从主服务器建立连接时，快速获取所有二进制日志内容。

如果从服务器在SQL线程执行所有获取的语句之前停止，则I/O线程至少获取已经读取到的内容，以便将语句的安全副本存储在自己的二级制日志文件中，准备下次执行主从服务器建立连接，继续同步。

使用命令 `SHOW PROCESSLIST\G` 可以查看有关复制的信息

命令 SHOW FULL PROCESSLISTG

**在 Master 主服务器 执行的数据示例**

```
mysql>  SHOW FULL PROCESSLIST\G
*************************** 1. row ***************************
     Id: 22
   User: repl
   Host: node2:39114
     db: NULL
Command: Binlog Dump
   Time: 4435
  State: Master has sent all binlog to slave; waiting for more updates
   Info: NULL
```

Id: 22是Binlog Dump服务连接的从站的复制线程
Host: node2:39114 是从服务，主机名 级及端口
State: 信息表示所有更新都已同步发送到从服务器，并且主服务器正在等待更多更新发生。
如果Binlog Dump在主服务器上看不到 线程，意味着主从复制没有配置成功; 也就是说，没有从服务器连接主服务器。

命令 SHOW PROCESSLISTG

**在 Slave 从服务器 ，查看两个线程的更新状态**

```
mysql> SHOW PROCESSLIST\G
*************************** 1. row ***************************
     Id: 6
   User: system user
   Host: 
     db: NULL
Command: Connect
   Time: 6810
  State: Waiting for master to send event
   Info: NULL
*************************** 2. row ***************************
     Id: 7
   User: system user
   Host: 
     db: NULL
Command: Connect
   Time: 3069
  State: Slave has read all relay log; waiting for more updates
   Info: NULL
```

**Id: 6**是与主服务器通信的I/O线程
**Id: 7**是正在处理存储在中继日志中的更新的SQL线程

在 运行 `SHOW PROCESSLIST` 命令时，两个线程都空闲，等待进一步更新

如果在主服务器上在设置的超时，时间内 Binlog Dump线程没有活动，则主服务器会和从服务器断开连接。超时取决于的 **服务器系统变量** 值 net_write_timeout(在中止写入之前等待块写入连接的秒数，默认10秒)和 net_retry_count;(如果通信端口上的读取或写入中断，请在重试次数，默认10次) 设置 [服务器系统变量](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)

该SHOW SLAVE STATUS语句提供了有关从服务器上复制处理的附加信息。请参见 第16.1.7.1节“检查复制状态”。





#### GTID

基于GTID（全局事务标识符）复制是事务性的，因此不需要指定二进制日志文件和文件中的位置，这大大简化了许多常见的复制任务。只要在主站上提交的所有事务也都应用于从站，使用gtids进行复制可确保主站和从站之间的数据一致性。







### 一从多主



### 半同步复制

master及slave都需要安装插件支持 

MySQL默认的复制即是异步的，主库在执行完客户端提交的事务后会立即将结果返给给客户端，并不关心从库是否已经接收并处理，这样就会有一个问题，主如果crash掉了，此时主上已经提交的事务可能并没有传到从上，如果此时，强行将从提升为主，可能导致新主上的数据不完整。半同步复制情况下，主库在执行完客户端提交的事务后不是立刻返回给客户端，而是等待至少一个从库接收到并写到relay log中才返回给客户端。相对于异步复制，半同步复制提高了数据的安全性，同时它也造成了一定程度的延迟，这个延迟最少是一个TCP/IP往返的时间。所以，半同步复制最好在低延时的网络中使用。



确保slave库I/O thread接收完master库的binlog，已经写入到relay log，才通知master库的等待线程该操作已正常结束；

等待超时，会关闭半同步复制，自动切换至异步复制，直到至少有一台slave库通知master库已经接收到binlog信息再恢复     提升了主从之间的数据一致性



### 配置注意点



#### 限制只读方式

在从服务器上设置`read_only=ON`， 此限制对拥有Super权限的用户均无效



#### 阻止所有用户

```
mysql> FLUSH TABLES WITH RAED LOCK;
```



#### 保证复制的事务安全

在master 节点启用参数`sync_binlog=ON`

针对InnoDB存储引擎， 开启

```
innodb_flush_logs_at_trx_commit=ON
innodb_support_xa=ON
```



Slave 节点

```
skip_slave_start=ON
```



master 节点

```
sync_master_info
```



slave 节点

```
sync_relay_log
sync_relay_log_info
```



### MMM 

Multi   Master MySQL





### MHA

Master HA

对主节点进行监控，可实现自动转移至其他从节点

通过提升某一从节点为新的主节点



#### MHA Manager 

通常部署在一台独立机器上管理多个master/slave集群，每个集群成为一个application



#### MHA Node

运行在每台MySQL服务器上，通过监控具备解析和清理logs功能的脚本来加快故障转移



### Galera Cluster

实现多主节点模型的机制

通过wresp协议协议在全局事件复制

任何一结点都可读写

工作较为底层，能为服务提供数据复制的组件



#### Percona-Cluster

即整合过Galera Cluster的Percona MySQL 分支版本

至少需要三个结点



#### MariaDB-Cluster









## 二进制日志事件记录格式

### STATEMENT



### ROW



### MIXED



## 复制相关的文件

### `master.info`

用于保存slave 连接至master 时想你管的信息，如账号，密码，服务器地址等



### `reply-log.info`

保存在当前slave结点上已经复制的当前二进制日志和本地replay log 日志的对应关系



## 常用操作

### 清理日志

```
PURGE
```



### 复制监控

```
show master status;
show binlog events;
show binary logs;

show slave status;
show processlist;
```



### 从服务器是否落后于主服务器

```
show slave status\G;
Seconds_Behind_Master: 0
```

### 

### 判断主从结点数据是否一致

```
percona-tools
```





# 半同步复制

**安装插件**

在主库上执行：

INSTALL PLUGIN rpl_semi_sync_master SONAME 'semisync_master.so';

在从库上执行：

INSTALL PLUGIN rpl_semi_sync_slave SONAME 'semisync_slave.so';

查看插件状态：

SELECT PLUGIN_NAME,PLUGIN_STATUS FROM INFORMATION_SCHEMA.PLUGINS WHERE PLUGIN_NAME LIKE '%semi%';

**4.2 配置参数**

主库参数：

rpl_semi_sync_master_enabled=1 #主库启用半同步复制

rpl_semi_sync_master_timeout=1000 #主库等待从库的超时时间，超时切换回异步复制，默认值10000（10s）

rpl_semi_sync_master_wait_point=AFTER_SYNC #等待点模式（AFTER_SYNC表示主库等待从库接收到binlog写到本地的relay-log里，才提交到存储引擎层，然后把请求返回给客户端；

AFTER_COMMIT表示主库写入binlog，边提交，边等待从库收到binlog写到本地的relay-log）

rpl_semi_sync_master_wait_for_slave_count=1 #主库等待从库的个数

从库参数：

rpl_semi_sync_slave_enabled=1 #从库启用半同步复制

**4.3 半同步复制状态监控**

Rpl_semi_sync_master_status #主库是否运行在半同步模式

Rpl_semi_sync_master_clients #半同步从库的个数

Rpl_semi_sync_master_yes_tx #从库确认成功的提交事务数

Rpl_semi_sync_master_no_tx #从库未确认成功的提交事务数

Rpl_semi_sync_slave_status #从库是否可运行半同步复制





# 主从复制案例



```
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm

yum -y install mysql57-community-release-el7-10.noarch.rpm

yum -y install mysql-community-server 

systemctl start mysqld

grep "password" /var/log/mysqld.log

```



开启mysql的远程访问

执行以下命令开启远程访问限制（注意：下面命令开启的IP是 192.168.0.1，如要开启所有的，用%代替IP）：

```
grant all privileges on *.* to 'root'@'192.168.0.1' identified by 'password' with grant option;
```

然后再输入下面两行命令

```
flush privileges; 
exit;
```



为firewalld添加开放端口

添加mysql端口3306

```
firewall-cmd --zone=public --add-port=3306/tcp --permanent
```

然后再重新载入

```
firewall-cmd --reload
```



修改mysql的字符编码（不修改会产生中文乱码问题）

显示原来编码：

```
show variables like '%character%';
```

修改/etc/my.cnf

```
[mysqld]
character-set-server=utf8mb4
[mysql]
default-character-set=utf8mb4
```



## 主节点

启动二进制日志

为当前节点设置一个全局的唯一ID号

```
vim /etc/my.cnf

[mysqld]
log-bin=master-bin
server-id=1
innodb_file_per_table=ON
character-set-server=utf8mb4
skip_name_resolve=ON
```

> /var/lib/mysql/master-bin.index                                                
> /var/lib/mysql/master-bin.000001                                               
> /var/lib/mysql/master-bin.000002                                              
> /var/lib/mysql/master-bin.000003  
>
> 可以指定绝对路径 `log-bin=/var/log/mysql/mysql-bin`



```
MariaDB [(none)]> show global variables like '%log_bin%';
+---------------------------------+-------+
| Variable_name                   | Value |
+---------------------------------+-------+
| log_bin                         | ON    |
| log_bin_trust_function_creators | OFF   |
| sql_log_bin                     | ON    |
+---------------------------------+-------+
3 rows in set (0.00 sec)


MariaDB [(none)]> SHOW MASTER LOGS;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 |     28415 |
| mysql-bin.000002 |   1038814 |
| mysql-bin.000003 |       264 |
| mysql-bin.000004 |       245 |
+------------------+-----------+
4 rows in set (0.00 sec)

MariaDB [(none)]> SHOW GLOBAL VARIABLES LIKE '%server%';
+----------------------+-----------------+
| Variable_name        | Value           |
+----------------------+-----------------+
| character_set_server | utf8            |
| collation_server     | utf8_general_ci |
| server_id            | 1               |
+----------------------+-----------------+
3 rows in set (0.00 sec)
```



创建有复制权限的用户账号

```
mysql> create user  'replica_user'@'%'  identified by 'replica_passwd';
```



```
MariaDB [(none)]> GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'repluser'@'208.73.201.%' IDENTIFIED BY 'replpass';
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.01 sec)
```

> 需要指定为 `*.*`   而不能单独对某个库、表授权



为了主从数据库一致，将进行先锁表，并且导出数据

```undefined
mysql> FLUSH TABLES WITH READ LOCK
```





```
mysqldump -u root -p --databases DB_master > DB_masterSlave.sql
```



登陆数据库查看Pos值并且解锁数据表

```
mysql> show master status;
mysql> unlock tables;
```



将备份的sql导入到slave节点

```
mysql -uroot -p < DB_masterSlave.sql
```





### 查看binlog 

查看主服务器的运行状态

```
mysql> show master status;
+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000001 |     1190 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
```



查看从服务器主机列表

```
mysql> show slave hosts;
+-----------+------+------+-----------+--------------------------------------+
| Server_id | Host | Port | Master_id | Slave_UUID                           |
+-----------+------+------+-----------+--------------------------------------+
|         2 |      | 3306 |         1 | 6b831bf2-8ae7-11e7-a178-000c29cb5cbc |
+-----------+------+------+-----------+--------------------------------------+
```



获取binlog文件列表

```
mysql> show binary logs;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 |      1190 |
+------------------+-----------+
```



只查看第一个binlog文件的内容

```
mysql> show binlog events;
+------------------+-----+----------------+-----------+-------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Log_name         | Pos | Event_type     | Server_id | End_log_pos | Info                                                                                                                                                                                                  |
+------------------+-----+----------------+-----------+-------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| mysql-bin.000001 |   4 | Format_desc    |         1 |         123 | Server ver: 5.7.19-log, Binlog ver: 4                                                                                                                                                                 |
| mysql-bin.000001 | 123 | Previous_gtids |         1 |         154 |                                                                                                                                                                                                       |
| mysql-bin.000001 | 420 | Anonymous_Gtid |         1 |         485 | SET @@SESSION.GTID_NEXT= 'ANONYMOUS'                                                                                                                                                                  |
| mysql-bin.000001 | 485 | Query          |         1 |         629 | GRANT REPLICATION SLAVE ON *.* TO 'replication'@'192.168.252.124'                                                                                                                                     |
| mysql-bin.000001 | 629 | Anonymous_Gtid |         1 |         694 | SET @@SESSION.GTID_NEXT= 'ANONYMOUS'                                                                                                                                                                  |
| mysql-bin.000001 | 694 | Query          |         1 |         847 | CREATE DATABASE `replication_wwww.ymq.io`                                                                                                                                                             |
| mysql-bin.000001 | 847 | Anonymous_Gtid |         1 |         912 | SET @@SESSION.GTID_NEXT= 'ANONYMOUS'                                                                                                                                                                  |
| mysql-bin.000001 | 912 | Query          |         1 |        1190 | use `replication_wwww.ymq.io`; CREATE TABLE `sync_test` (`id` int(11) NOT NULL AUTO_INCREMENT, `name` varchar(255) NOT NULL, PRIMARY KEY (`id`) ) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8 |
+------------------+-----+----------------+-----------+-------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

查看指定binlog文件的内容

```
mysql> mysql> show binlog events in 'mysql-bin.000001';
+------------------+-----+----------------+-----------+-------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Log_name         | Pos | Event_type     | Server_id | End_log_pos | Info                                                                                                                                                                                                  |
+------------------+-----+----------------+-----------+-------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| mysql-bin.000001 |   4 | Format_desc    |         1 |         123 | Server ver: 5.7.19-log, Binlog ver: 4                                                                                                                                                                 |
| mysql-bin.000001 | 123 | Previous_gtids |         1 |         154 |                                                                                                                                                                                                       |
| mysql-bin.000001 | 420 | Anonymous_Gtid |         1 |         485 | SET @@SESSION.GTID_NEXT= 'ANONYMOUS'                                                                                                                                                                  |
| mysql-bin.000001 | 485 | Query          |         1 |         629 | GRANT REPLICATION SLAVE ON *.* TO 'replication'@'192.168.252.124'                                                                                                                                     |
| mysql-bin.000001 | 629 | Anonymous_Gtid |         1 |         694 | SET @@SESSION.GTID_NEXT= 'ANONYMOUS'                                                                                                                                                                  |
| mysql-bin.000001 | 694 | Query          |         1 |         847 | CREATE DATABASE `replication_wwww.ymq.io`                                                                                                                                                             |
| mysql-bin.000001 | 847 | Anonymous_Gtid |         1 |         912 | SET @@SESSION.GTID_NEXT= 'ANONYMOUS'                                                                                                                                                                  |
| mysql-bin.000001 | 912 | Query          |         1 |        1190 | use `replication_wwww.ymq.io`; CREATE TABLE `sync_test` (`id` int(11) NOT NULL AUTO_INCREMENT, `name` varchar(255) NOT NULL, PRIMARY KEY (`id`) ) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8 |
+------------------+-----+----------------+-----------+-------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```





## 从节点

如果是PXB备份的话，会在xtrabackup_binlog_info文件中记录备份完成时的binlog文件和pos点的；

如果是mysqldump备份，若未在master执行锁表导出操作，需要带上--master-data=2这个参数才会记录备份开始时的binlog文件和pos点。

```
mysqldump -uroot -p --master-data=2 --single-transaction  --all-databases > master_backup.sql

mysqldump: [Warning] Using a password on the command line interface can be insecure.

grep -i "CHANGE MASTER" master_backup.sql
4-- CHANGE MASTER TO MASTER_LOG_FILE='mysql5729-bin.000001', MASTER_LOG_POS=1405;

```



```
mysql -uroot < master_backup.sql -p
```



启动中继日志

为当前节点设置一个全局唯一的ID号

```
vim /etc/my.cnf

[mysqld]
relay-log=relay-log
relay-log-index=relay-log.index
server-id=7
innodb_file_per_table=ON
skip_name_resolve=ON
```



Master log info

```
MariaDB [(none)]> SHOW MASTER LOGS;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 |     28415 |
| mysql-bin.000002 |   1038814 |
| mysql-bin.000003 |       264 |
| mysql-bin.000004 |       499 |
+------------------+-----------+
4 rows in set (0.00 sec)
```



使用有复制权限的用户账号连接至主服务器，并启动复制线程

```
MariaDB [(none)]> CHANGE MASTER TO MASTER_HOST='208.73.201.156', MASTER_USER='repluser', MASTER_PASSWORD='replpass', MASTER_LOG_FILE='mysql-bin.000004', MASTER_LOG_POS=499;
Query OK, 0 rows affected (0.01 sec)

MariaDB [(none)]> SHOW SLAVE STATUS \G;
*************************** 1. row ***************************
               Slave_IO_State:
                  Master_Host: 208.73.201.156
                  Master_User: repluser
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000004
          Read_Master_Log_Pos: 499
               Relay_Log_File: localhost-relay-bin.000001
                Relay_Log_Pos: 4
        Relay_Master_Log_File: mysql-bin.000004
             Slave_IO_Running: No
            Slave_SQL_Running: No
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 499
              Relay_Log_Space: 245
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 0
1 row in set (0.00 sec)


MariaDB [(none)]> START SLAVE;   #不指定参数默认启动IO_THREAD|SQL_THREAD
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> SHOW SLAVE STATUS \G;
*************************** 1. row ***************************
               Slave_IO_State: Connecting to master
                  Master_Host: 208.73.201.156
                  Master_User: repluser
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000004
          Read_Master_Log_Pos: 499
               Relay_Log_File: localhost-relay-bin.000001
                Relay_Log_Pos: 4
        Relay_Master_Log_File: mysql-bin.000004
             Slave_IO_Running: Connecting
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 499
              Relay_Log_Space: 245
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 1130
                Last_IO_Error: error connecting to master 'repluser@208.73.201.156:3306' - retry-time: 60  retries: 86400  message: Host '185.202.172.152' is not allowed to connect to this MariaDB server
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 0
1 row in set (0.00 sec)
```

> `Slave_IO_State` #从站的当前状态
> `Slave_IO_Running： Yes` #读取主程序二进制日志的I/O线程是否正在运行
> `Slave_SQL_Running： Yes` #执行读取主服务器中二进制日志事件的SQL线程是否正在运行。与I/O线程一样
> `Seconds_Behind_Master `#是否为0，0就是已经同步了

**必须都是 Yes**

如果不是原因主要有以下 4 个方面：

1、网络不通
2、密码不对
3、MASTER_LOG_POS 不对 ps
4、mysql 的 `auto.cnf` server-uuid 一样（可能你是复制的mysql）

```
$ find / -name 'auto.cnf'
$ cat /var/lib/mysql/auto.cnf
[auto]
server-uuid=6b831bf3-8ae7-11e7-a178-000c29cb5cbc # 按照这个16进制格式，修改server-uuid，重启mysql即可
```



可能需要操作

```
MariaDB [(none)]> change master to master_password ='replpass';
Query OK, 0 rows affected (0.00 sec)
```

清理之前的配置

```
mysql> reset slave all; 
```



## 测试

主节点

```
MariaDB [mysql]> show master status;
+------------------+----------+--------------+------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000004 |     1259 |              |                  |
+------------------+----------+--------------+------------------+
1 row in set (0.00 sec)

MariaDB [mysql]> create database mydb;
Query OK, 1 row affected (0.00 sec)

MariaDB [mysql]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mydb               |
| mysql              |
| performance_schema |
| test               |
+--------------------+
5 rows in set (0.00 sec)

MariaDB [mysql]> show master status;
+------------------+----------+--------------+------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000004 |     1342 |              |                  |
+------------------+----------+--------------+------------------+
1 row in set (0.00 sec)
```



从节点

```
MariaDB [(none)]> show slave status \G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 208.73.201.156
                  Master_User: repluser
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000004
          Read_Master_Log_Pos: 1259
               Relay_Log_File: localhost-relay-bin.000003
                Relay_Log_Pos: 1289
        Relay_Master_Log_File: mysql-bin.000004
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 1259
              Relay_Log_Space: 1587
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 1
1 row in set (0.00 sec)

ERROR: No query specified

MariaDB [(none)]> show slave status \G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 208.73.201.156
                  Master_User: repluser
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000004
          Read_Master_Log_Pos: 1342
               Relay_Log_File: localhost-relay-bin.000003
                Relay_Log_Pos: 1372
        Relay_Master_Log_File: mysql-bin.000004
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 1342
              Relay_Log_Space: 1670
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 1
1 row in set (0.00 sec)
```





# 主主复制案例 （慎用）

## 自动增长ID

在一个节点使用奇数id

```
auto_increment_offset=1
auto_increment_increment=2
```

另一个节点使用偶数id

```
auto_increment_offset=2
auto_increment_increment=2
```



## 配置步骤

各个节点使用一个唯一的server_id

### 节点1

```
vim /etc/my.cnf

log-bin=master-bin
relay_log=relay-log
server-id=1
innodb_file_per_table=ON
skip_name_resolve=ON
auto_increment_offset=1
auto_increment_increment=2
```



查看状态

```
MariaDB [(none)]> show global variables like '%log_bin%';
+---------------------------------+-------+
| Variable_name                   | Value |
+---------------------------------+-------+
| log_bin                         | ON    |
| log_bin_trust_function_creators | OFF   |
| sql_log_bin                     | ON    |
+---------------------------------+-------+
3 rows in set (0.00 sec)

MariaDB [(none)]> show global variables like '%relay_log%';
+----------------------------------+----------------+
| Variable_name                    | Value          |
+----------------------------------+----------------+
| innodb_recovery_update_relay_log | OFF            |
| max_relay_log_size               | 0              |
| relay_log                        | relay-log      |
| relay_log_index                  |                |
| relay_log_info_file              | relay-log.info |
| relay_log_purge                  | ON             |
| relay_log_recovery               | OFF            |
| relay_log_space_limit            | 0              |
| sync_relay_log                   | 0              |
| sync_relay_log_info              | 0              |
+----------------------------------+----------------+
10 rows in set (0.00 sec)
```



授权账户

```
grant replication slave, replication client on *.* to 'repluser'@'185.202.172.152' identified by 'replpass';

flush privileges;
```

```
MariaDB [(none)]> change master to master_host='185.202.172.152', master_user='repluser', master_password='replpass', master_log_file='master-bin.000003', master_log_pos=511;
Query OK, 0 rows affected (0.14 sec)
```

> log_file 及其pos 需去主机器上`show master status`查看



### 节点2

```
vim /etc/my.cnf

log-bin=master-bin
relay_log=relay-log
server-id=5
innodb_file_per_table=ON
skip_name_resolve=ON
auto_increment_offset=2
auto_increment_increment=2
```



查看状态

```
MariaDB [(none)]> show global variables like '%log_bin%';
+---------------------------------+-------+
| Variable_name                   | Value |
+---------------------------------+-------+
| log_bin                         | ON    |
| log_bin_trust_function_creators | OFF   |
| sql_log_bin                     | ON    |
+---------------------------------+-------+
3 rows in set (0.01 sec)

MariaDB [(none)]> show global variables like '%relay_log%';
+----------------------------------+----------------+
| Variable_name                    | Value          |
+----------------------------------+----------------+
| innodb_recovery_update_relay_log | OFF            |
| max_relay_log_size               | 0              |
| relay_log                        | relay-log      |
| relay_log_index                  |                |
| relay_log_info_file              | relay-log.info |
| relay_log_purge                  | ON             |
| relay_log_recovery               | OFF            |
| relay_log_space_limit            | 0              |
| sync_relay_log                   | 0              |
| sync_relay_log_info              | 0              |
+----------------------------------+----------------+
10 rows in set (0.01 sec)
```



授权账户

```
grant replication slave, replication client on *.* to 'repluser'@'185.202.172.152' identified by 'replpass';

flush privileges;
```

```
MariaDB [(none)]> change master to master_host='208.73.201.156', master_user='repluser', master_password='replpass', master_log_file='master-bin.000004', master_log_pos=512;
Query OK, 0 rows affected (0.01 sec)
```

> log_file 及其pos 需去主机器上`show master status`查看



```
mysql> set global validate_password_policy='low';
```





# 半同步复制案例



## Master 

```
vim /etc/my.cnf
```

```
datadir = /data/mysql
log-bin=master-bin
server-id=1
innodb_file_per_table=ON
skip_name_resolve=ON
```



创建有复制权限的用户账号

```
MariaDB [(none)]> GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'repluser'@'185.202.172.152' IDENTIFIED BY 'replpass';  # 对端IP
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> SHOW MASTER STATUS;
+------------------+----------+--------------+------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000004 |      500 |              |                  |
+------------------+----------+--------------+------------------+
1 row in set (0.00 sec)
```



开始配置半同步, 安装插件

```
MariaDB [(none)]> install plugin rpl_semi_sync_master SONAME 'semisync_master.so';
Query OK, 0 rows affected (0.01 sec)

MariaDB [(none)]> show plugins;
+--------------------------------+--------+--------------------+--------------------+---------+
| Name                           | Status | Type               | Library            | License |
+--------------------------------+--------+--------------------+--------------------+---------+
| binlog                         | ACTIVE | STORAGE ENGINE     | NULL               | GPL       |
| rpl_semi_sync_master           | ACTIVE | REPLICATION        | semisync_master.so | GPL     |
+--------------------------------+--------+--------------------+--------------------+---------+
41 rows in set (0.00 sec)

```

```
MariaDB [(none)]> show global variables like '%semi%';
+------------------------------------+-------+
| Variable_name                      | Value |
+------------------------------------+-------+
| rpl_semi_sync_master_enabled       | OFF   |
| rpl_semi_sync_master_timeout       | 10000 |
| rpl_semi_sync_master_trace_level   | 32    |
| rpl_semi_sync_master_wait_no_slave | ON    |
+------------------------------------+-------+
4 rows in set (0.01 sec)

MariaDB [(none)]> set global rpl_semi_sync_master_enabled=ON;
Query OK, 0 rows affected (0.01 sec)

MariaDB [(none)]> show global variables like '%semi%';
+------------------------------------+-------+
| Variable_name                      | Value |
+------------------------------------+-------+
| rpl_semi_sync_master_enabled       | ON    |
| rpl_semi_sync_master_timeout       | 10000 |
| rpl_semi_sync_master_trace_level   | 32    |
| rpl_semi_sync_master_wait_no_slave | ON    |
+------------------------------------+-------+
4 rows in set (0.00 sec)
```



slave 启动之后

```
MariaDB [(none)]> show global status like '%semi%';
+--------------------------------------------+-------+
| Variable_name                              | Value |
+--------------------------------------------+-------+
| Rpl_semi_sync_master_clients               | 1     |
| Rpl_semi_sync_master_net_avg_wait_time     | 0     |
| Rpl_semi_sync_master_net_wait_time         | 0     |
| Rpl_semi_sync_master_net_waits             | 0     |
| Rpl_semi_sync_master_no_times              | 0     |
| Rpl_semi_sync_master_no_tx                 | 0     |
| Rpl_semi_sync_master_status                | ON    |
| Rpl_semi_sync_master_timefunc_failures     | 0     |
| Rpl_semi_sync_master_tx_avg_wait_time      | 0     |
| Rpl_semi_sync_master_tx_wait_time          | 0     |
| Rpl_semi_sync_master_tx_waits              | 0     |
| Rpl_semi_sync_master_wait_pos_backtraverse | 0     |
| Rpl_semi_sync_master_wait_sessions         | 0     |
| Rpl_semi_sync_master_yes_tx                | 0     |
+--------------------------------------------+-------+
14 rows in set (0.00 sec)
```



开始测试

```
MariaDB [(none)]> create database mydb;
Query OK, 1 row affected (0.05 sec)

MariaDB [(none)]> use mydb
Database changed
MariaDB [mydb]> create table tb1 (id int, name char(30));
Query OK, 0 rows affected (0.06 sec)

MariaDB [mydb]> show global status like '%semi%';
+--------------------------------------------+--------+
| Variable_name                              | Value  |
+--------------------------------------------+--------+
| Rpl_semi_sync_master_clients               | 1      |
| Rpl_semi_sync_master_net_avg_wait_time     | 56081  |
| Rpl_semi_sync_master_net_wait_time         | 112162 |
| Rpl_semi_sync_master_net_waits             | 2      |
| Rpl_semi_sync_master_no_times              | 0      |
| Rpl_semi_sync_master_no_tx                 | 0      |
| Rpl_semi_sync_master_status                | ON     |
| Rpl_semi_sync_master_timefunc_failures     | 0      |
| Rpl_semi_sync_master_tx_avg_wait_time      | 56189  |
| Rpl_semi_sync_master_tx_wait_time          | 112378 |
| Rpl_semi_sync_master_tx_waits              | 2      |
| Rpl_semi_sync_master_wait_pos_backtraverse | 0      |
| Rpl_semi_sync_master_wait_sessions         | 0      |
| Rpl_semi_sync_master_yes_tx                | 2      |
+--------------------------------------------+--------+
14 rows in set (0.00 sec)
```





## Slave

```
vim /etc/my.cnf
```

```
datadir = /data/mysql
relay_log=relay-log
server-id=5
innodb_file_per_table=ON
skip_name_resolve=ON
```



```
change master to master_host='45.77.120.103', master_user='repluser', master_password='replpass', master_log_file='mysql-bin.000004', master_log_pos=757;
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> start slave;
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> show slave status \G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 45.77.120.103
                  Master_User: repluser
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000004
          Read_Master_Log_Pos: 757
               Relay_Log_File: relay-log.000002
                Relay_Log_Pos: 529
        Relay_Master_Log_File: mysql-bin.000004
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 757
              Relay_Log_Space: 817
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 1
1 row in set (0.00 sec)

ERROR: No query specified
```



开始配置半同步

```
MariaDB [(none)]> stop slave;
Query OK, 0 rows affected (0.67 sec)

MariaDB [(none)]> install plugin rpl_semi_sync_slave SONAME 'semisync_slave.so';
Query OK, 0 rows affected (0.02 sec)

MariaDB [(none)]> show plugins;
+--------------------------------+--------+--------------------+-------------------+---------+
| Name                           | Status | Type               | Library           | License |
+--------------------------------+--------+--------------------+-------------------+---------+
| binlog                         | ACTIVE | STORAGE ENGINE     | NULL              | GPL     |
| rpl_semi_sync_slave            | ACTIVE | REPLICATION        | semisync_slave.so | GPL     |
+--------------------------------+--------+--------------------+-------------------+---------+
41 rows in set (0.00 sec)
```

```
MariaDB [(none)]> show global variables like '%semi%';
+---------------------------------+-------+
| Variable_name                   | Value |
+---------------------------------+-------+
| rpl_semi_sync_slave_enabled     | OFF   |
| rpl_semi_sync_slave_trace_level | 32    |
+---------------------------------+-------+
2 rows in set (0.00 sec)

MariaDB [(none)]> set global rpl_semi_sync_slave_enabled=1;
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> show global variables like '%semi%';
+---------------------------------+-------+
| Variable_name                   | Value |
+---------------------------------+-------+
| rpl_semi_sync_slave_enabled     | ON    |
| rpl_semi_sync_slave_trace_level | 32    |
+---------------------------------+-------+
2 rows in set (0.00 sec)

MariaDB [(none)]> start slave;
Query OK, 0 rows affected (0.00 sec)

```



# 复制过滤器

让从节点复制指定的数据库，或指定数据库的指定表



## 二进制日志方式

主服务器仅向二进制日志中记录与特定数据库(特定表)相关的事件

时间还是无法实现，不建议使用

```
binlog_do_db=		#数据库白名单列表
binlog_ignore_db=	#数据库黑名单列表
```





## 中继日志方式

从服务器SQL_THREAD在replay中继日志中的事件时，仅读取与特定数据库(特定表)相关的时间并应用于本地

但会造成网络及磁盘IO浪费

```
replicate_do_db
replicate_ignore_db
replicate_do_table=
replicate_ignore_table=
replicate_wild_do_table=
replicate_wild_ignore_table=
```



### example

从服务器仅复制mydb

```
MariaDB [(none)]> show global variables like '%replicate%';
+----------------------------------+-----------+
| Variable_name                    | Value     |
+----------------------------------+-----------+
| replicate_annotate_row_events    | OFF       |
| replicate_do_db                  |           |
| replicate_do_table               |           |
| replicate_events_marked_for_skip | replicate |
| replicate_ignore_db              |           |
| replicate_ignore_table           |           |
| replicate_wild_do_table          |           |
| replicate_wild_ignore_table      |           |
+----------------------------------+-----------+
8 rows in set (0.00 sec)

MariaDB [(none)]> stop slave;
Query OK, 0 rows affected (0.01 sec)

MariaDB [(none)]> set global replicate_do_db=mydb;
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> start slave;
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> show slave status \G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 45.77.120.103
                  Master_User: repluser
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000004
          Read_Master_Log_Pos: 943
               Relay_Log_File: relay-log.000005
                Relay_Log_Pos: 529
        Relay_Master_Log_File: mysql-bin.000004
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB: mydb
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 943
              Relay_Log_Space: 1287
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 1
1 row in set (0.00 sec)

ERROR: No query specified
```



主节点创建数据库

```
MariaDB [mydb]> create database testdb;
Query OK, 1 row affected (0.06 sec)

MariaDB [mydb]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mydb               |
| mysql              |
| performance_schema |
| test               |
| testdb             |
+--------------------+
6 rows in set (0.00 sec)

MariaDB [mydb]> insert into tb1 values(1, 'a');
Query OK, 1 row affected (0.06 sec)

MariaDB [mydb]> select * from tb1;
+------+------+
| id   | name |
+------+------+
|    1 | a    |
+------+------+
1 row in set (0.00 sec)
```



从节点测试

```
MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mydb               |
| mysql              |
| performance_schema |
| test               |
+--------------------+
5 rows in set (0.01 sec)

MariaDB [(none)]> use mydb
Database changed
MariaDB [mydb]> select * from tb1;
+------+------+
| id   | name |
+------+------+
|    1 | a    |
+------+------+
1 row in set (0.01 sec)

```





# 基于SSL复制

前提，支持SSL

```
MariaDB [mydb]> show global variables like '%ssl%';
+---------------+----------+
| Variable_name | Value    |
+---------------+----------+
| have_openssl  | DISABLED |
| have_ssl      | DISABLED |
| ssl_ca        |          |
| ssl_capath    |          |
| ssl_cert      |          |
| ssl_cipher    |          |
| ssl_key       |          |
+---------------+----------+
7 rows in set (0.00 sec)
```

> 这里需要重新编译mariadb



1. master 配置证书和私钥：并且创建一个要求必须使用SS了连接的复制账号
2. slave 端使用`CHANGE MASTER TO` 命令时指明ssl相关选项



