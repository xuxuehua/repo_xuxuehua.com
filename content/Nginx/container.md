---
title: "container"
date: 2019-02-19 00:14
---


[TOC]



# Ngnix 容器



## 数组

多块连续内存



## 链接



## 队列



## 哈希表 （常用）

仅仅用于静态不变内容

bucket size 需要考虑CPU对齐问题

O1的复杂度，非常快





## 红黑树（常用）

遍历复杂度为O(n)

左子节点小于右子节点



### 常用模块

```
ngx_conf_module
ngx_event_timer_rbtree
ngx_http_file_cache
ngx_http_geo_module
ngx_http_limit_conn_module
ngx_http_limit_req_module
ngx_http_lua_shdict:ngx.shared.DICT
resolver  ngx_resolver_t
ngx_stream_geo_module
ngx_stream_limit_conn_module
```



## 基数树