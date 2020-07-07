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

mysql实现主从复制的方案主要有两种：基于二进制日志的主从复制和基于GTID的主从复制。传统复制是基于主库的二进制日志进行复制，在主站和从站之间同步二进制日志文件和日志文件中的位置标识。



三个工作线程

- 主服务器提供了一个工作线程I/O dump thread
- 从服务器有两个工作线程，I/O thread和SQL thread



执行过程

- 主库把接收到的SQL请求记录到自己的binlog日志文件
- 从库的I/O thread请求主库I/O dump thread，获得binglog日志，并追加到relay log日志文件
- 从库的SQL thread执行relay log中的SQL语句







#### binlog



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





# 基于二进制日志的主从复制

**2.1 配置要求**

1）主库开启二进制日志

2）主库和各个从库配置唯一的server_id

**2.2 主库创建复制用户**

从库需要使用一个用户来连接主库，可以使用添加复制权限的生产应用用户。为了减少对应用用户的影响，建议单独创建一个仅有复制权限的用户。

CREATE USER 'repl'@'%' IDENTIFIED BY 'password';

GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';

**2.3 获取主库的二进制日志位置**

1）在主库打开一个连接，执行下列命令清除所有表并阻止写入语句：

mysql> FLUSH TABLES WITH READ LOCK;

2）重新打开一个连接，执行下列命令获取二进制日志的位置：

mysql > SHOW MASTER STATUS;

+--------------------+----------+--------------+------------------+-------------------+|

File | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |

+--------------------+----------+--------------+------------------+-------------------+|

mysql_bin.000003 | 154 | | | |

+--------------------+----------+--------------+------------------+-------------------+

3）对于配置一个新的主从复制环境，主库没有初始数据需要同步到从库，则在第一个连接中释放锁：

mysql> UNLOCK TABLES;

**2.4 配置从库**

配置前先需要确认是否为从库设置了唯一的server_id。然后在从库上执行下面语句，请将参数值替换为实际值：

mysql> CHANGE MASTER TO MASTER_HOST='master_host_name', MASTER_USER='replication_user_name', MASTER_PASSWORD='replication_password', MASTER_LOG_FILE='recorded_log_file_name', MASTER_LOG_POS=recorded_log_position;

如果需要配置多源的复制，需要为每一个主库指定一个通道:

mysql> CHANGE MASTER TO MASTER_HOST='master_host_name', MASTER_USER='replication_user_name', MASTER_PASSWORD='replication_password', MASTER_LOG_FILE='recorded_log_file_name', MASTER_LOG_POS=recorded_log_position FOR CHANNEL 'master-1';

**2.5 启动主从复制**

在从库上执行：

mysql> START SLAVE;

**2.6 查看复制状态**

在从库上执行：

mysql> SHOW SLAVE STATUSG;

当Slave_IO_Running和Slave_SQL_Running都为YES的时候就表示主从同步设置成功了。接下来就可以进行一些验证了，比如在主master数据库的test数据库的一张表中插入一条数据，在slave的test库的相同数据表中查看是否有新增的数据即可验证主从复制功能是否有效，还可以关闭slave（mysql>stop slave;）,然后再修改master，看slave是否也相应修改（停止slave后，master的修改不会同步到slave），就可以完成主从复制功能的验证了。







**2.基于二进制日志的主从复制**

**2.1 配置要求**

1）主库开启二进制日志

2）主库和各个从库配置唯一的server_id

**2.2 主库创建复制用户**

从库需要使用一个用户来连接主库，可以使用添加复制权限的生产应用用户。为了减少对应用用户的影响，建议单独创建一个仅有复制权限的用户。

CREATE USER 'repl'@'%' IDENTIFIED BY 'password';

GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';

**2.3 获取主库的二进制日志位置**

1）在主库打开一个连接，执行下列命令清除所有表并阻止写入语句：

mysql> FLUSH TABLES WITH READ LOCK;

2）重新打开一个连接，执行下列命令获取二进制日志的位置：

mysql > SHOW MASTER STATUS;

+--------------------+----------+--------------+------------------+-------------------+|

File | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |

+--------------------+----------+--------------+------------------+-------------------+|

mysql_bin.000003 | 154 | | | |

+--------------------+----------+--------------+------------------+-------------------+

3）对于配置一个新的主从复制环境，主库没有初始数据需要同步到从库，则在第一个连接中释放锁：

mysql> UNLOCK TABLES;

**2.4 配置从库**

配置前先需要确认是否为从库设置了唯一的server_id。然后在从库上执行下面语句，请将参数值替换为实际值：

mysql> CHANGE MASTER TO MASTER_HOST='master_host_name', MASTER_USER='replication_user_name', MASTER_PASSWORD='replication_password', MASTER_LOG_FILE='recorded_log_file_name', MASTER_LOG_POS=recorded_log_position;

如果需要配置多源的复制，需要为每一个主库指定一个通道:

mysql> CHANGE MASTER TO MASTER_HOST='master_host_name', MASTER_USER='replication_user_name', MASTER_PASSWORD='replication_password', MASTER_LOG_FILE='recorded_log_file_name', MASTER_LOG_POS=recorded_log_position FOR CHANNEL 'master-1';

**2.5 启动主从复制**

在从库上执行：

mysql> START SLAVE;

**2.6 查看复制状态**

在从库上执行：

mysql> SHOW SLAVE STATUSG;

当Slave_IO_Running和Slave_SQL_Running都为YES的时候就表示主从同步设置成功了。接下来就可以进行一些验证了，比如在主master数据库的test数据库的一张表中插入一条数据，在slave的test库的相同数据表中查看是否有新增的数据即可验证主从复制功能是否有效，还可以关闭slave（mysql>stop slave;）,然后再修改master，看slave是否也相应修改（停止slave后，master的修改不会同步到slave），就可以完成主从复制功能的验证了。





# 基于GTID的主从复制

**3.1 配置要求**

\1. 主库开启二进制日志

\2. 主库和各个从库配置唯一的server_id

**3.2 主库创建复制用户**

从库需要使用一个用户来连接主库，可以使用添加复制权限的生产应用用户。为了减少对应用用户的影响，建议单独创建一个仅有复制权限的用户。

CREATE USER 'repl'@'%' IDENTIFIED BY 'password';

GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';

**3.3 在主库和从库的/etc/my.cnf文件中[mysqld]添加启用GTID的变量：**

gtid_mode=ON

enforce-gtid-consistency=true

配置完成后重启mysql。

systemctl restart mysqld

**3.4 配置从库**

配置前先需要确认是否为从库设置了唯一的server_id。然后在从库上执行下面语句，请将参数值替换为实际值：

mysql>CHANGE MASTER TO MASTER_HOST = '128.*.*.*',MASTER_PORT = 3306,MASTER_USER = 'repl',MASTER_PASSWORD = 'password',MASTER_AUTO_POSITION = 1;

如果需要配置多源的复制，需要为每一个主库指定一个通道:

mysql>CHANGE MASTER TO MASTER_HOST = '128.*.*.*',MASTER_PORT = 3306,MASTER_USER = 'repl',MASTER_PASSWORD = 'password',MASTER_AUTO_POSITION = 1 FOR CHANNEL 'master-1';

**3.5 启动主从复制**

在从库上执行：

mysql> START SLAVE;





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



## 主节点

启动二进制日志

为当前节点设置一个全局的唯一ID号

```
vim /etc/my.cnf

[mysqld]
log-bin=master-bin
server-id=1
innodb_file_per_table=ON
skip_name_resolve=ON
```



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
MariaDB [(none)]> GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'repluser'@'208.73.201.%' IDENTIFIED BY 'replpass';
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.01 sec)
```





## 从节点

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

可能需要操作

```
MariaDB [(none)]> change master to master_password ='replpass';
Query OK, 0 rows affected (0.00 sec)
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



