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



