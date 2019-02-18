---
title: "upgrade"
date: 2019-01-01 14:59
---


[TOC]



# 热升级



## 流程

```
1. 将就的Nginx文件换成新的Nginx文件（注意备份）
2. 向master 进程发送USR2信号
3. master进程修改pid文件名，加上后缀.oldbin
4. master进程用新Nginx文件启动新master进程
5. 向老master进程发送WINCH信号，关闭老worker
6. 回滚，向老master发送HUP信号，向新master 发送QUIT信号
```



## 特点

当新老master同时存在的时候，他们都可以接受请求，老master会把listen的句柄通过epoll_ctl从epoll中移除，不再监听80或者443端口



## 优雅关闭worker进程



```
1. 设置定时器 worker_shutdown_timeout
2. 关闭监听句柄
3. 关闭空闲连接
4. 在循环中等待全部连接关闭 （时间可能很长）
5. 退出进程
```



