---
title: "basic_knowlege"
date: 2019-08-04 23:04
---
[TOC]





# 安装

## linux

server

```
sudo apt-get install postgresql
```



client

```
sudo apt-get install postgresql-client
```



# 初始化

## 新用户

```
sudo adduser dbuser
sudo su - postgres
```



## 登陆

```
psql
```



使用\password命令，为postgres用户设置一个密码。

```
\password postgres
```



创建数据库用户dbuser（刚才创建的是Linux系统用户），并设置密码。

```
CREATE USER dbuser WITH PASSWORD 'password';
```



创建用户数据库，这里为exampledb，并指定所有者为dbuser。

```
CREATE DATABASE exampledb OWNER dbuser;
```



将exampledb数据库的所有权限都赋予dbuser，否则dbuser只能登录控制台，没有任何数据库操作权限

```
GRANT ALL PRIVILEGES ON DATABASE exampledb to dbuser;
```



最后，使用\q命令退出控制台（也可以直接按ctrl+D）

```
\q
```



# 操作

psql命令存在简写形式。如果当前Linux系统用户，同时也是PostgreSQL用户，则可以省略用户名（-U参数的部分）

```
psql -U dbuser -d exampledb -h 127.0.0.1 -p 5432
```



恢复外部数据，可以使用下面的命令。

```
psql exampledb < exampledb.sql
```



## 控制台命令

```
\h：查看SQL命令的解释，比如\h select。
\?：查看psql命令列表。
\l：列出所有数据库。
\c [database_name]：连接其他数据库。
\d：列出当前数据库的所有表格。
\d [table_name]：列出某一张表格的结构
\dn: list schema
\dt: list tables
\dt SCHEMA.*  列出schema所有的tables
\du：列出所有用户。
\e：打开文本编辑器。
\conninfo：列出当前数据库和连接的信息。
\q	退出
```



## Privilege

```
1、查看某用户的表权限
select * from information_schema.table_privileges where grantee='user_name';

2、查看usage权限表
select * from information_schema.usage_privileges where grantee='user_name';

3、查看存储过程函数相关权限表
select * from information_schema.routine_privileges where grantee='user_name';

4、建用户授权
create user user_name;
alter user user_name with password '';
alter user user_name with CONNECTION LIMIT  20;#连接数限制
```



# 数据库操作

```
# 创建新表
CREATE TABLE user_tbl(name VARCHAR(20), signup_date DATE);

# 插入数据
INSERT INTO user_tbl(name, signup_date) VALUES('张三', '2013-12-22');

# 选择记录
SELECT * FROM user_tbl;

# 更新数据
UPDATE user_tbl set name = '李四' WHERE name = '张三';

# 删除记录
DELETE FROM user_tbl WHERE name = '李四' ;

# 添加栏位
ALTER TABLE user_tbl ADD email VARCHAR(40);

# 更新结构
ALTER TABLE user_tbl ALTER COLUMN signup_date SET NOT NULL;

# 更名栏位
ALTER TABLE user_tbl RENAME COLUMN signup_date TO signup;

# 删除栏位
ALTER TABLE user_tbl DROP COLUMN email;

# 表格更名
ALTER TABLE user_tbl RENAME TO backup_tbl;

# 删除表格
DROP TABLE IF EXISTS backup_tbl;
```





# Concept



## Role

PostgreSQL manages database access permissions using the concept of *roles*. A role can be thought of as either a database user, or a group of database users, depending on how the role is set up. 

After version 8.1 Any role can act as a user, a group, or both.



