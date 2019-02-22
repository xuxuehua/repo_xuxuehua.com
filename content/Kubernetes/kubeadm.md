---
title: "kubeadm"
date: 2019-02-22 22:20
---


[TOC]



# kubeadm

Kubernetes 项目自带的集群构建工具

负责构建一个最小化的可用集群



## 特点

仅仅关心如何初始化并启动集群



## 集成

### kubeadm init

集群快速初始化，部署master 节点组件



### kubeadm join

将节点快速加入到指定集群中



### kubeadm token

集群构建后管理用于加入集群时使用的认证令牌



### kubeadm reset

删除集群构建过程中生成的文件，回到初始状态



