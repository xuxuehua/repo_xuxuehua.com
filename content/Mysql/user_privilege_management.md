---
title: "user_privilege_management"
date: 2018-12-27 11:48
---


[TOC]

# 用户和权限管理



## 权限类别

### 管理类

```
CREATE TEMPORARY TABLES
CREATE USER
FILE
SUPER
SHOW DATABASES
RELOAD
SHUTDOWN
REPLICATION SLAVE
REPLICATION CLIENT
LOCK TABLES
PROCESS
```



### 程序类

均可用CREATE，ALTER，DROP，EXCUTE

```
FUNCTION
PROCEDURE
TRIGGER
```





### 库和表级别

```
ALTER 仅对TABLE和DATABASE有效
CREATE VIEW
DROP TABLE or DATABASE
INDEX TABLE or DATABASE
SHOW VIEW
GRANT OPTION: 能够把自己获得的权限赠给其他用户一个副本
```



### 数据操作

```
SELECT
INSERT
DELETE
UPDATE
```



### 字段级别

```
SELECT(col1, col2...)
UPDATE(col1, col2...)
INSERT(col1, col2...)
```





### 所有权限

```
ALL PRIVILEGES 或者简写为ALL
```





## 元数据数据库 mysql

### 授权表

```
mysql> use mysql;
mysql> SHOW TABLES;
```

```
db, host, user
columns_priv, table_priv, procs_priv, proxies_priv
```



# 用户账号

组成格式

```
'USERNAME'@'HOST'
```



## @'HOST'

使用方式

```
主机名
IP地址或者Network
同配置分
```



## 创建用户

```
CREATE USER 'USERNAME'@'HOST' [IDENTIFIED BY 'password']
```



### 查看授权

```
SHOW GRANTS FOR 'USERNAME'@'HOST'
```



### 重命名

```
RENAME USER old_user_new TO new_user_name
```





## 删除用户

```
DROP USER 'USERNAME'@'HOST'
```



## 修改密码

```
SET PASSWORD FOR
```

or

```
UPDATE mysql.user SET password=PASSWORD('your_password') WHERE clause;
```

or

```
mysqladmin [OPTIONS] command, command ...
$ mysqladmin password
```



### 解决忘记管理员密码

启动mysqld进程时，为其使用 (在启动配置文件里面添加)

```
--skip-grant-tables --skip-networking
```

```
$bindir/mysqld_safe --skip-grant-tables --skip-networking --datadir="$datadir" --pid-file="$mysqld_pid_file_path" $other_args > /dev/null 2>&1 & wait_for_ready return_value=$?
```

使用update命令修改管理员密码

```
UPDATE mysql.user SET password=PASSWORD("mynewpassword") WHERE user='root';
flush privileges;
```

关闭mysqld进程，移除上面两个选项，重启mysqld







# 授权 与 收回授权

## 授权 GRANT

```
GRANT priv_type[,...] ON [{table|function|procedure}] db.{tables|routine} TO 'USERNAME'@'HOST' [IDENTIFIED BY 'password'] [REQUIRE SSL] [WITH with_option]
```

```
with_option
GRANT OPTION
|MAX_QUERIES_PER_HOUR count
|MAX_UPDATES_PER_HOUR count
|MAX_CONNECTIONS_PER_HOUR count
|MAX_USER_CONNECTIONS count
```





## 收回授权 REVOKE

```
REVOKE priv_type [(column_list)] [, priv_type [(column_list)]] ... ON [object_type] priv_level FROM user [, user] ...
```





