---
title: "resource_management 资源管理"
date: 2019-02-23 23:05
---


[TOC]



# 资源管理

## 功能分类

更好的运行和丰富Pod资源，从而为容器化应用提供更灵活，更完善的操作与管理组件为核心设计



### 工作负载 Workload （也称Pod控制器）

Pod 为此基础资源，负责运行容器

当Pod非正常中止，重建工作由此控制器完成



#### ReplicationController 无状态 （废弃）

负责无状态应用，上一代应用控制器

保证每个容器或者容器组运行并且可以被访问



#### ReplicaSet 无状态

负责无状态应用，新一代ReplicationController

比上一代支持基于集合的选择器



#### Deployment  无状态

负责无状态应用

用于管理无状态持久化的应用，如HTTP

构建在ReplicaSet之上



#### StatefulSet 有状态

负责有状态应用

用于管理有状态的持久化应用，如database

比Deployment会为每个Pod创建一个独有的持久性标识符，并确保Pod之间的顺序性



#### DaemonSet 

常用于运行集群存储的守护进程，如glusterd，ceph，

日志收集进程如fluentd，logstash，

监控进程，如prometheus的Node Explorter，collected，datadog agent， Ganglia的gmond

确保每个节点都运行Pod的副本



#### Job

用于管理运行完成后可以终止的应用，如批处理任务







### 发现和负载均衡 Discovery & LB

确保Pod资源被外部及集群内部访问，要为同一种工作负载的访问流量进行负载均衡



### 配置和存储 Config & Storage

支持多种存储确保存储资源持久化，如GlusterFS，ceph RBD， Flocker

使用ConfigMap资源能够以环境变量或存储卷的方式接入到Pod中，还可以被同类Pod共享，不适合存储密钥等敏感数据





### 集群级资源 Cluster

#### Namespace 

资源对象名称的作用范围，默认隶属default



#### Node

Kubernetes集群工作节点，其标识符在当前集群唯一



#### Role 

名称空间级别有规则组成的权限集合

被RoleBinding 引用



#### ClusterRole

Cluster 级别的 

规则组成的权限集合

被RoleBinding，ClusterRole Binding 引用



#### RoleBinding

将Role权限绑定在一个或一组用户上，

可以引用同一名称空间的Role，或全局名称的ClusterRole



#### ClusterRoleBinding

将ClusterRole中定义的许可权限绑定在一个或一组用户上，引用ClusterRole





### 元数据 Metadata

用于为集群内部的其他资源配置其行为或特征，如HorizontalPodAutoscaler用于自动伸缩工作负载类型的资源对象的规模



