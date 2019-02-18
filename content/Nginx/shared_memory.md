---
title: "shared_memory 共享内存"
date: 2019-01-03 15:57
---


[TOC]

# 共享内存

跨worker进程进行通信



## Nginx官方模块

### ngx_http_lua_api

openresty 的核心模块





### rbtree

插入删除非常快

#### ngx_stream_limit_conn_module



#### ngx_http_limit_conn_module



#### ngx_stream_limit_req_module



#### http cache

##### ngx_http_file_cache

##### ngx_http_proxy_module

##### ngx_http_scgi_module

##### ngx_http_uwsgi_module

##### ngx_http_fastcgi_module



#### ssl

##### ngx_http_ssl_module

##### ngx_mail_ssl_module

##### ngx_stream_ssl_module





### 单链表

将需要共享的元素串起来

#### ngx_http_upstream_zone_module



#### ngx_stream_upstream_zone_module





## 使用的场景

所有需要多worker进程协同配合的场景。例如upstream负载均衡算法，或者limit_conn、limit_req等。一般指令中有zone关键字都是共享内存。





## slab

## ngx_slab_stat

下载ngx_slab_stat

```
wget http://tengine.taobao.org/download/tengine-2.2.2.tar.gz
tar -xf tengine-2.2.2.tar.gz
cd tengine-2.2.2/

~/tengine-2.2.2/modules/ngx_slab_stat# ls -la ngx_http_slab_stat_module.c
-rw-r--r-- 1 128966 users 6627 Jan 28  2018 ngx_http_slab_stat_module.c
```

重新编译openresty

```
cd openresty-1.13.6.2/
./configure --add-module=/root/tengine-2.2.2/modules/ngx_slab_stat/
make
```

配置openresty

```
http {
    lua_shared_dict dogs 10m;
    server {
location = /slab_stat {
	slab_stat;
}

location /set {
	content_by_lua_block {
		local dogs = ngx.shared.dogs
		dogs:set("Jim", 8)
		ngx.say("STORED")
	}
}

location /get {
	content_by_lua_block {
		local dogs = ngx.shared.dogs
		ngx.say(dogs:get("Jim"))
	}
}
    }
}
```



统计slab使用状态

```
# curl localhost:80/set
STORED

# curl localhost:80/get
8

# curl localhost:80/slab_stat
* shared memory: dogs
total:       10240(KB) free:       10168(KB) size:           4(KB)
pages:       10168(KB) start:00007F680E0A7000 end:00007F680EA97000
slot:           8(Bytes) total:           0 used:           0 reqs:           0 fails:           0
slot:          16(Bytes) total:           0 used:           0 reqs:           0 fails:           0
slot:          32(Bytes) total:         127 used:           1 reqs:           1 fails:           0
slot:          64(Bytes) total:           0 used:           0 reqs:           0 fails:           0
slot:         128(Bytes) total:          32 used:           2 reqs:           2 fails:           0
slot:         256(Bytes) total:           0 used:           0 reqs:           0 fails:           0
slot:         512(Bytes) total:           0 used:           0 reqs:           0 fails:           0
slot:        1024(Bytes) total:           0 used:           0 reqs:           0 fails:           0
slot:        2048(Bytes) total:           0 used:           0 reqs:           0 fails:           0
```

