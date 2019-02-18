---
title: "sequelize"
date: 2018-08-05 01:39
---

[TOC]



# sequelize 框架

ORM技术：Object-Relational Mapping，把关系数据库的表结构映射到对象上。

Node的ORM框架Sequelize来操作数据库



## 使用



### 建立测试数据

```
grant all privileges on test.* to 'www'@'%' identified by 'www';

use test;

create table pets (
    id varchar(50) not null,
    name varchar(100) not null,
    gender bool not null,
    birth varchar(10) not null,
    createdAt bigint not null,
    updatedAt bigint not null,
    version bigint not null,
    primary key (id)
) engine=innodb;
```

> `grant`命令是创建MySQL的用户名和口令，均为`www`，并赋予操作`test`数据库的所有权限。



Package.json

```
{
  "name": "hello-sequelize",
  "version": "0.0.1",
  "dependencies": {
    "sequelize": "^4.38.0",
    "mysql": "^2.16.0"
  }
}
```



config.js

```
var config = {
    database: 'test', // 使用哪个数据库
    username: 'www', // 用户名
    password: 'www', // 口令
    host: 'localhost', // 主机名
    port: 3306 // 端口号，MySQL默认3306
};

module.exports = config;
```



app.js

```

```



