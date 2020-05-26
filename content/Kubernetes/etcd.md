---
title: "etcd"
date: 2020-05-01 11:04
---
[toc]



# etcd

Master节点使用etcd进行存储, 简单的key-value 存储，即整个集群的持久化数据

整个集群所有的对象状态信息，都存储与etcd中，需要高可用，至少3个

但都是通过kube-apiserver实现，因为需要APIServer进行授权工作

