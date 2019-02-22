---
title: "http_process"
date: 2019-02-19 14:55
---


[TOC]



# http 事件模块

## 流程

![image-20190219145636828](/Users/xhxu/Library/Application Support/typora-user-images/image-20190219145636828.png)



![image-20190219145704712](/Users/xhxu/Library/Application Support/typora-user-images/image-20190219145704712.png)



connection_pool_size 512 字节是 nginx 框架和客户端建立了连接时就产生的。
当客户端有数据请求发来时，这时候是在 request_pool_size 4k 的空间里，开始分配出具体的 client_header_buffer_size 1k 
然后，如果客户端的请求头部太长、超过了 4k ，这时候 large_client_header_buffers 4 8k 就会起作用

如果请求的URI不超过1k，client_header_buffer_size够用的话，后面的8k的large_client_header_buffers就不用分配了

而4k的请求内存池是一定会分配的

large_client_header_buffers是从请求内存池分配的



client_header_buffer_size定义的这段内存，是从连接的内存池中分配出的（即connection_pool_size），如果连接被复用的话，虽然请求内存池会被释放，但连接内存池照旧使用。所以，我们需要先分清连接与请求，并清楚他们的内存池。

pool_size只是内存池的初始分配大小，当然实际使用中可以超出此大小。而client_header_buffer_size则具体指明某一用途下的内存大小，这里就是最开始接收HTTP请求的内存大小。





## 处理http请求 的11个阶段

![img](https://img2018.cnblogs.com/blog/311549/201901/311549-20190112172113223-1317047452.png)



阶段间是固定的，同一阶段内的各模块是倒序的

但不一定某个阶段的模块执行顺序是一定的

![img](https://img2018.cnblogs.com/blog/311549/201901/311549-20190112172703202-1828176178.png)





所有请求都是依据http 11个阶段顺序执行



| 序号 | 阶段           | 模块                             | 备注                         |
| ---- | -------------- | -------------------------------- | ---------------------------- |
| 1    | POST_READ      | realip                           | 获取客户端真实IP             |
| 2    | SERVER_REWRITE | rewrite                          |                              |
| 3    | FIND_CONFIG    |                                  | nginx 框架会做，location匹配 |
| 4    | REWRITE        | rewrite                          |                              |
| 5    | POST_REWRITE   |                                  |                              |
| 6    | PRE_ACCESS     | limit_conn, limit_req            | 并发连接数，每秒请求数       |
| 7    | ACCESS         | auth_basic, access, auth_request | auth_basic可以做访问限制     |
| 8    | POST_ACCESS    |                                  |                              |
| 9    | PRE_CONTENT    | try_files                        |                              |
| 10   | CONTENT        | index, autoindex, concat         |                              |
| 11   | LOG            | access_log                       | access_log记录请求日志       |



## 模块

### realip

需要基于变量来使用

如binary_remote_addr, remote_addr这样的变量，其值为真实IP，这样做连接限制(limit_conn模块)才有意义



#### 编译安装

默认不会编译进nginx, 需下载源代码添加以下编译参数

```
--with-http_realip_module
```



#### 模块指令

##### set_real_ip_from 

Address | CIDR | unix

Context: http, server, location



##### real_ip_header

field | X-Real-IP | X-Forwarded-For | proxy_protocol

context: http, server, location



##### real_ip_recursive

on | off

Default: off

context: http, server, location 



#### example

```
vim realip.conf

server {
    server_name realip.xurick.com;
    error_log logs/myerror.log debug;
    set_real_ip_from 119.119.119.119
    #real_ip_header X-Real-IP;
    real_ip_recursive off; #这里关闭了
    #real_ip_recursive on;
    real_ip_header	X-Forwarded-For;
    
    location / {
        return 200 "Client real ip: $remote_addr\n";
    }
}


# 若访问 curl -H 'X-Forwarded-For: 1.1.1.1,119.119.119.119' realip.xurick.com
Client real ip: 119.119.119.119
```

```
vim realip.conf

server {
    server_name realip.xurick.com;
    error_log logs/myerror.log debug;
    set_real_ip_from 119.119.119.119
    #real_ip_header X-Real-IP;
    #real_ip_recursive off; 
    real_ip_recursive on;	#这里打开了
    real_ip_header	X-Forwarded-For;
    
    location / {
        return 200 "Client real ip: $remote_addr\n";
    }
}


# 若访问 curl -H 'X-Forwarded-For: 1.1.1.1,119.119.119.119' realip.xurick.com
Client real ip: 1.1.1.1
```

