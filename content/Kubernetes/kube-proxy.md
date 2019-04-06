---
title: "kube-proxy"
date: 2019-02-28 19:23
---


[TOC]



# Kube-proxy

需要连接至Master节点的API_Server进行交互，获取service 相关的资源变动状态

service资源发生的变动，kube-proxy都会将其转化为调动service后端pod节点上的iptables或ipvs规则



## 特点

在所有worker nodes上运行

反应每一个node的服务状态，以及简单的round-robin 转发到后端服务





## 模式

### User space (常用)



### iptables



### ipvs



