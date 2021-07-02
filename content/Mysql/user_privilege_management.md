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



## 查看用户

```
SELECT user FROM mysql.user;
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







### 重制root密码



```
vim /etc/my.cnf

# 追加到[mysqld]
skip-grant-tables
```



```
systemctl restart mysqld
```



```
$ mysql

mysql> use mysql;
mysql> update user set password=PASSWORD("myPassword") where user="root";
mysql> flush privileges;
```

after accessing then execute below

```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass';
```



### 查看修改密码规则



if there is an error of "Your password does not satisfy the current policy requirements."

```sql
SHOW VARIABLES LIKE 'validate_password%';
```

The output should be something like that :

```sql
+--------------------------------------+-------+
| Variable_name                        | Value |
+--------------------------------------+-------+
| validate_password.check_user_name    | ON    |
| validate_password.dictionary_file    |       |
| validate_password.length             | 6     |
| validate_password.mixed_case_count   | 1     |
| validate_password.number_count       | 1     |
| validate_password.policy             | LOW   |
| validate_password.special_char_count | 1     |
+--------------------------------------+-------+
```

then you can set the password policy level lower, for example:

```sql
SET GLOBAL validate_password.length = 6;
SET GLOBAL validate_password.number_count = 0;
SET GLOBAL validate_password.policy = 'LOW';
```



# 授权 与 收回授权

* 查看当前用户权限

```
show grants;
```



* 查看某个用户的全局权限

```
select * from user;
```



* 查看某个用户的某张表权限

```
select * from table_priv;
```





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



```
mysql> update user set host='%' where user='root';

mysql> grant all privileges on  *.*  to root@'%'  identified  by 'xxxx';
```

> 第一个`*`表示数据库名；第二个`*`表示该数据库的表名
>
> `*.*`的话表示所有到数据库下到所有表都允许访问
>
> `%`表示允许访问到mysql的ip地址,%表示所有ip均可以访问



## 

```
grant all on test.* to 'rick'@'localhost' identified by "rickpass";
```

> `grant`是Mysql一个专门控制权限的命令
> `all` 指的是所有权限
> `test.*` test是数据库名字，然后后边的 `.*`是指当前所有表
> `'rick'@'localhost'` 其中前面的rick指的是用户名，而**localhost**指的是这个用户名能在哪里进行登录，这里的localhost是本地。
> `identified by "rickpass"` 指的是设置密码

```
grant select,insert,delete,drop on testdb.* to rick@localhost;

grant all privileges on *.* to rick@'%' identified by '123456';
```







## 收回授权 REVOKE

```
REVOKE priv_type [(column_list)] [, priv_type [(column_list)]] ... ON [object_type] priv_level FROM user [, user] ...
```



* 回收全库全表的所有权限

```
revoke all privileges on mysql.* from rick@localhost;
```



```
revoke select,insert,update,delete on mysql.* from rick@localhost;
```



# 

