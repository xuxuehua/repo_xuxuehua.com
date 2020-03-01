---
title: "basic_knowledge"
date: 2020-02-29 21:24
---
[toc]



# Redis

开源的kv 存储系统 

原子性操作如push/pop add/remove 

数据存于内存中，但是不同于memcache，会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，在此基础上实现主从同步

配合关系型数据库做高速缓存



## 安装



