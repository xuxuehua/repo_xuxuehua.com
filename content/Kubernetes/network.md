---
title: "network"
date: 2019-03-29 14:02
---


[TOC]



# Kubernetes 网络

外部请求，顺序为node代理至service， service代理至Pod



![img](https://snag.gy/H8Oely.jpg)





## Node 节点网络 (网络独立)

在构建之前，配置完成节点间的网络通信



## Service/cluster 集群网络 (网络独立)

虚拟地址， 仅存在于iptables 和ipvs中，即iptables规则中的地址



通过kube-proxy在每个节点上创建规则，实现管控

每次网络变更的实现，都是有kube-proxy在每一个节点上创建规则

每一个service的变动，也需要kube-proxy反应的规则上





## Pod 网络 (网络独立)

Pod内部可相互访问



### 容器间通信

通过lo接口通信



### 各Pod之间通信

各Pod通过自己的地址直接通信，也就是每个Pod的地址是独立的

通过Overlay network的隧道方式完成Pod之间的通信，可以转发二层报文，也可以转发三层报文



### Pod与service通信

通过kube-proxy实现





## CNI

Container Network Interface 

需要第三方工具提供网络规则的方式，以隔绝网络中非同一业务的Pod之间的网络通信



### flannel 

仅支持网络配置，不支持网络策略

叠加网络实现



### calico 

支持网络配置，网络策略

支持BGP协议，实现之间通信





### canel

结合calico和flannel













