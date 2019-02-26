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



# Installation 安装



```
rpm -Uvh https://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm
yum clean all
yum -y install zabbix-server-mysql zabbix-web-mysql zabbix-agent
zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzbxuser -p zabbix
```



```
MariaDB [(none)]> CREATE database zabbix character set utf8 collate utf8_bin;
Query OK, 1 row affected (0.00 sec)

MariaDB [(none)]> grant all on zabbix.* TO 'zbxuser'@'localhost' identified by 'zbxpass';
Query OK, 0 rows affected (0.01 sec)
```



```
Edit file /etc/zabbix/zabbix_server.conf

DBPassword=password
```

```
Edit file /etc/httpd/conf.d/zabbix.conf, uncomment and set the right timezone for you.
# php_value date.timezone Europe/Riga
```



```
Start Zabbix server and agent processes
Start Zabbix server and agent processes and make it start at system boot:

# systemctl restart zabbix-server zabbix-agent httpd
# systemctl enable zabbix-server zabbix-agent httpd
```



配置时区

```
vim /etc/php.ini

date.timezone = Asia/Shanghai
```





# 术语

## template

通常包含item， trigger， graph， screen

模版可以直接链接至单个主机



## application

一组item的集合

