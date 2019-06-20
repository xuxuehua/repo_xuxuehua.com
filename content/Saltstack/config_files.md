---
title: "config_files 配置文件"
date: 2019-06-19 14:54
---
[TOC]



# master 配置文件

## max_open_files

可以根据Master将Minion数量进行适当的调整。

## timeout

可以根据Master和Minion的网络状况适当调整。



## auto_accept/autosign_file

在大规模部署Minion的时候可以设置自动签证。



## master_tops和所有以external**开头的参数

这些参数是SaltStack与外部系统进行整合的相关配置参数，扩展SaltStack用得到。



# minion 配置文件