---
title: "kubelet"
date: 2019-02-28 19:18
---


[TOC]



# kubelet

运行在每一个node上的Kubernetes node agent



## 实现的角色

和API server 通信，是否pods 已经被分配到nodes里面

通过容器引擎执行pod containers

挂在pod volumns 和secrets

执行健康检查，以确认pod/node 状态





## Podspec

通过yaml文件描述一个pod



