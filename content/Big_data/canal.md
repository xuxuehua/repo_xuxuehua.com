---
title: "canal 实时导入"
date: 2019-07-25 08:46
---
[TOC]





# Canal 

想实时导入关系数据库的数据，可以选择Canal。

Canal是阿里巴巴开源的一个MySQL binlog获取工具，binlog是MySQL的事务日志，可用于MySQL数据库主从复制，Canal将自己伪装成MySQL从库，从MySQL获取binlog。



![img](https://snag.gy/5xPp9E.jpg)



只要开发一个Canal客户端程序就可以解析出来MySQL的写操作数据，将这些数据交给大数据流计算处理引擎，就可以实现对MySQL数据的实时处理了。



