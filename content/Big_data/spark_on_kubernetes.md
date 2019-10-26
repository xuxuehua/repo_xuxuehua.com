---
title: "spark_on_kubernetes"
date: 2019-10-11 13:45
---
[TOC]



## Spark on K8s

利用原生的Kubernetes调度器进行集群资源的分配与管理Spark应用。在此模式下，Spark Driver和Executor均使用Pod来运行，进一步可通过指定Node Selector功能将应用运行于特定节点之上（如：带有GPU的节点实例）



