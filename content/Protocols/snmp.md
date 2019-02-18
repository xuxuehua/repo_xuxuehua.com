---
title: "snmp"
date: 2019-02-13 23:00
---


[TOC]



# SNMP

Simple Network Management Protocols



## 程序包

Linux: net-snmp



## 工作模式

NMS向Agent采集数据

agent 向NMS报告数据

NMS请求agent修改配置



## 组件

### MIB 

Management Information Base

被管理对象的集合，即每个agent都有本地的MIB 库



#### OID

Object ID

即每一组树状对象通过OID表示





### SMI

MIB表示符号



### SNMP协议



## NMS



### NMS操作

Get， GetNext， Set，Trap



#### agent

Response



### 协议

UDP

NMS： 161

Agent： 162





## SNMP版本

### V1

无验证

### V2C

基于社区的管理， 通过验证社区密钥验证，即双方社区密钥需要相同

密钥通信未加密



### V3

认证，加密，解密





