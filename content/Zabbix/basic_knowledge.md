---
title: "basic_knowledge"
date: 2019-02-15 17:22
---


[TOC]



# 组成

## zabbix-server

C语言编写



### 进程

watchdog

housekeeper

alerter

poller

httppoller

discoverer

pinger

db_config_syncer

db_data_syncer

nodewatcher

timer

escalater





### zabbix_server.conf

配置文件

### zabbix_server.log 

日志

### zabbix_get

收集数据

和zabbix_agentd 通信



## zabbix-agent

C语言编写



### zabbix_agentd

收集本地数据



### zabbix_agentd.conf



### zabbix_agentd.log





## zabbix-database

支持MySQL，PostgreSQL， Oracle，DB2， 



## zabbix-web

GUI，用于实现zabbix设定和展示



## zabbix-proxy

分布式监控环境中的专用组件



### zabbix_proxy.conf



### zabbix_proxy.log



# 术语

## template

通常包含item， trigger， graph， screen

模版可以直接链接至单个主机



## application

一组item的集合

