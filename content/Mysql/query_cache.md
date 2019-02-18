---
title: "query_cache 查询缓存"
date: 2018-12-28 13:51
---


[TOC]



# MySQL查询缓存

不建议设置过大，默认为16MB

如果查询缓存是有意义的，那就需要打开，除非使用memcache等其他缓存机制。



## 判断命中

通过查询语句的hash值判断，hash时，考虑的因素包括

```
查询本身，要查询的数据库，客户端使用协议版本, ...
```



查询语句任何字符上的不同，都会导致缓存不能命中



## 不会被缓存

查询中包含自定义函数UDF，存储函数，用户自定义变量，临时表，mysql库中系统表，或者包含列级别的表，有着不确定值的函数( Now() );



## 变量

### 查询缓存的相关服务器变量

查询缓存中内存块的最小分配单位

```
query_cache_min_res_unit
```

> 较小值会减少浪费，但会导致更频繁的内存分配操作
>
> 较大值会带来浪费，会导致碎片过多



能够缓存的最大查询结果

```
query_cache_limit
```

> 对于较大结果的查询语句，建议在SELECT中使用SQL_NO_CACHE



查询缓存总共可用的内存空间，单位为字节，必须为1024整数倍

```
query_cache_size
```



指定value为ON，OFF，DEMAND

```
query_cache_type
```



如果某表被其他的连接锁定，是否仍然可以充查询缓存中返回结果

默认值为OFF， 表示可以表被其他连接锁定的场景中继续从缓存返回数据，ON表示不允许

```
query_cache_wlock_invalidate
```



### 查看相关的状态变量

```
mysql> SHOW GLOBAL STATUS LIKE '%Qcache%';
```



### 缓存命中率评估

```
Qcache_hits/(Qcache_hits+Com_select)
```























