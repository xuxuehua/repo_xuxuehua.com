---
title: "basic_knowlege"
date: 2019-08-04 23:04
---
[TOC]









# Ecosystem

![image-20210623154304166](/Users/rxu/coding/github/repo_xuxuehua.com/content/Postgresql/basic_knowlege.assets/image-20210623154304166.png)





# Installation

## linux

server

```
sudo apt-get install postgresql
```



client

```
sudo apt-get install postgresql-client
```



### amazonlinux Postgresql 12

Next we need to add the PostgreSQL repository to our Amazon Linux 2 machine for us to be able to install packages. Also note that PostgreSQL 11 and PostgreSQL 10 packages are available in Amazon Linux extras repository.

```
$ sudo  amazon-linux-extras | grep postgre
  5  postgresql9.6            available    \
  6  postgresql10             available    [ =10  =stable ]
 41  postgresql11             available    [ =11  =stable ]
 58  postgresql12             available    [ =stable ]
 59  postgresql13             available    [ =stable ]
```

To add official PostgreSQL repository to Amazon Linux 2 server, run the following commands as root or user account with sudo privileges.

```
sudo tee /etc/yum.repos.d/pgdg.repo<<EOF
[pgdg12]
name=PostgreSQL 12 for RHEL/CentOS 7 - x86_64
baseurl=https://download.postgresql.org/pub/repos/yum/12/redhat/rhel-7-x86_64
enabled=1
gpgcheck=0
EOF
```

Update your packages index file.

```
sudo yum makecache

yum -y install postgresql12.x86_64 postgresql12-server.x86_64 
```



Preparing the environment

```
sudo su - postgres
echo "export PATH=/usr/pgsql-12/bin:$PATH PAGER=less" >> ~/.pgsql_profile
source ~/.pgsql_profile
exit
```





We need to initialize the database server for configuration files to be generated. This is done by calling the setup script.

```
$ sudo /usr/pgsql-12/bin/postgresql-12-setup initdb
Initializing database ... OK
```



PostgreSQL 12 server uses configuration file in */var/lib/pgsql/12/data/postgresql.conf* . You can review all default values and tune to your liking before using the database server for your Production workloads.

To start and enable the service to start at OS boot, run the following command:

```
systemctl enable --now postgresql-12
systemctl status postgresql-12
```



create soft link

```
ln -s /usr/pgsql-12/bin/* /usr/bin/
```



Set PostgreSQL admin user password that you’ll use to escalate privilege for DB operations.

```
$ sudo su - postgres 
-bash-4.2$ /usr/pgsql-12/bin/psql 
psql (12.5)
Type "help" for help.

postgres=# \password
Enter new password: 
Enter it again: 

# OR reset to specific password
postgres=# alter user postgres password 'postgres';
ALTER ROLE
postgres=# \q
```



```
vim /var/lib/pgsql/12/data/pg_hba.conf
# "local" is for Unix domain socket connections only
local all all peer(change to trust)
# IPv4 local connections:
host all all 127.0.0.1/32 ident(change to md5)
# IPv6 local connections:
host all all ::1/128 ident (change to md5)
# Allow replication connections from localhost, by a user with the
# replication privilege.

systemctl restart postgresql-12
```





create new user if you need

```
-bash-4.2$ createuser --interactive rick
Shall the new role be a superuser? (y/n) y
```





Modify the parameter listen_addresses to allow a specific IP interface or all (using *). Modifying this parameter requires a restart of the PostgreSQL instance to get the change into effect.

```
# as postgres user
$ psql -c "ALTER SYSTEM SET listen_addresses TO '*'";
ALTER SYSTEM
```



allow remote access

```
vim /var/lib/pgsql/12/data/postgresql.conf
listen_addresses = '*'   

OR
postgres=# ALTER SYSTEM SET listen_addresses TO '*';

postgres=# show listen_addresses;
 listen_addresses 
------------------
 *
(1 row)
```



check version

```
[root@ip-172-31-9-108 ~]# sudo su - postgres
Last login: Thu Jul  8 07:10:41 UTC 2021 on pts/0
-bash-4.2$ psql
shpsql (9.2.24, server 12.7)
WARNING: psql version 9.2, server version 12.0.
         Some psql features might not work.
Type "help" for help.

postgres=# show server_version;
 server_version 
----------------
 12.7
(1 row)
```





### psql

```
cd /tmp
wget https://ftp.postgresql.org/pub/source/v11.5/postgresql-11.5.tar.gz
tar zxvf postgresql-11.5.tar.gz
cd postgresql-11.5
./configure --without-readline && make && make install
```

> By default, it will install pg_dump into `/usr/local/pgsql/bin/pg_dump`



## docker

```
docker run -d --name postgresql2 --restart always -e POSTGRES_USER='postgres' -e POSTGRES_PASSWORD='abc123' -e ALLOW_IP_RANGE=0.0.0.0/0 -v /home/postgres/data:/var/lib/postgresql -v /4T/tmp:/tmp/tmp -p 55433:5432 -t postges
```

```
psql -h 127.0.0.1 -p 55433 -U postgres 
```





## macos

```
brew search postgresql
```

It will list all the available postgresql packages.

```
brew install postgresql@12
```



### psql



```sh
brew install libpq
```

update PATH

if use zsh:

```sh
echo 'export PATH="/usr/local/opt/libpq/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

if use bash:

```sh
echo 'export PATH="/usr/local/opt/libpq/bin:$PATH"' >> ~/.bash_profile
source ~/.bash_profile
```





# 初始化

## 创建用户

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
\d [table_name]：列出某一张表格的结构  或者 select * from information_schema.columns where table_name='app';
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




pg=> CREATE SCHEMA anomaly_detection;
CREATE SCHEMA
pg=>
pg=> GRANT USAGE ON SCHEMA anomaly_detection TO pg_user_rxu;
GRANT
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





## 导出

```
pg_dump  -U  postgres  -f  c:\db.sql postgis
```

或者

    pg_dump  -U postgres  postgis > c:\db.sql



```
pg_dump -Upostgres -t mytable -f  dump.sql  postgres

>>>
pg_dump -U USER -d DATABASE_NAME -t SCHEMA.TABLE_NAME -f LOCALFILE_NAME.sql -h DB_INSTANCE -p 5432
```







## 导入

```
psql  -d  postgis  -f  c:\db.sql  postgres
```



```
psql  -d  postgis  -f  c:\ dump.sql postgres
```





## pg_stat_activity

```
select * from pg_stat_activity;
```



### idle transaction 

```
SELECT usename,state,count(1)
FROM pg_stat_activity
where xact_start is not null
group by usename,state;
```





```
SELECT datname, pid, state, query, age(clock_timestamp(), query_start) AS age 
FROM pg_stat_activity
WHERE state <> 'idle' 
    AND query NOT LIKE '% FROM pg_stat_activity %' 
ORDER BY age;
```





## pg_cancel_backend() 

取消后台操作，回滚未提交事物

```
user_data=> select pg_cancel_backend(1264);
 pg_terminate_backend
----------------------
 t
(1 row)

user_data=>  select * from pg_stat_activity where pid=1264;
(No rows)
```



## pg_terminate_backend() 

中断session，回滚未提交事物

```
user_data=> select pg_terminate_backend(1264);
 pg_terminate_backend
----------------------
 t
(1 row)

user_data=>  select * from pg_stat_activity where pid=1264;
(No rows)
```







# Type

## json vs jsonb

The key difference between them is that `JSON` stores data in a raw format and `JSONB` stores data in a custom binary format. Our focus here is going to be on the `JSONB` data type because it allows the contents to be indexed and queried with ease.

The JSON data type is basically a blob that stores JSON data in raw format, preserving even insignificant things such as whitespace, the order of keys in objects, or even duplicate keys in objects. It offers limited querying capabilities, and it's slow because it needs to load and parse the entire JSON blob each time.

JSONB on the other hand stores JSON data in a custom format that is optimized for querying and will not reparse the JSON blob each time.

If you know before hand that you will not be performing JSON querying operations, then use the `JSON`data type. For all other cases, use `JSONB`



The following example demonstrates the difference:

```
select '{"user_id":1,    "paying":true}'::json, '{"user_id":1, "paying":true}'::jsonb;

            json                |             jsonb              
--------------------------------+--------------------------------
{"user_id":1,    "paying":true} | {"paying": true, "user_id": 1}
(1 row)
```

(the whitespace and the order of the keys are preserved in the JSONB column.)





```
create table amsterdam
(
   id       integer primary key, 
   payload  jsonb not null default '{}'::jsonb
);
```



If you are altering an already existing table, then the syntax is as follows:

```sql
alter table TABLE add column COLUMN jsonb not null default '{}'::jsonb;
```



# Concept



## Role

PostgreSQL manages database access permissions using the concept of *roles*. A role can be thought of as either a database user, or a group of database users, depending on how the role is set up. 

After version 8.1 Any role can act as a user, a group, or both.







# login without input password

```
# chmod 600 ~/.pgpass^C
# cat ~/.pgpass
#hostname:port:database:username:password
192.168.1.1:5432:meta:readOnly:xx
192.168.1.2:5432:admin:readWrite:xx

# psql -U readWrite -d admin -h 192.168.1.2 -p 5432
psql (9.2.24, server 11.5)
WARNING: psql version 9.2, server version 11.0.
         Some psql features might not work.
SSL connection (cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256)
Type "help" for help.
admin=> \q
```







# example

## select count

```
SELECT task_id, attempt, COUNT(attempt) FROM  schema_name.task_attempts GROUP BY task_id, attempt HAVING COUNT(attempt) > 1;
 task_id | attempt | count
---------+---------+-------
      15 |       0 |     2
     619 |       0 |     2
     621 |       0 |     2
    1113 |       0 |     2
     727 |       0 |     2
```



# FAQ

## cannot use column reference in DEFAULT expression 

注意默认值如何用双引号和单引号引起来







# Postgres protocol

pg 消息流:
http://www.postgres.cn/docs/13/protocol-flow.html#id-1.10.5.7.3
pg 消息格式
http://www.postgres.cn/docs/13/protocol-message-formats.html
https://developer.aliyun.com/article/751984
https://zhuanlan.zhihu.com/p/24661559
https://developer.aliyun.com/article/405149
https://segmentfault.com/a/1190000017136059



go版pg协议实现: https://github.com/jackc/pgproto3
pg mock proxy: https://github.com/jackc/pgmock#pgmockproxy



