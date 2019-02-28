---
title: "pod"
date: 2019-02-27 21:42
---


[TOC]



# Pod

## 状态信息

### Pending



### Running



### Succeeded



### Failed



### CrashLoopBackOff

Kubernetes. 尝试一次又一次的重启Pod





## Pod 控制器 Controller

借助Controller 对Pod进行管理，实现一次性的Pod对象管理

包括以下多种调度器



### Replication Controller (淘汰)

定义了一个期望的场景，声明某种pod的副本数量在任意时刻都符合某个预期值

e.g. `apiVersion: extensions/v1beat1 kind: Replication metadata: name: frontend `



### ReplicaSet

确保给一个pod所指定数量的replicas 会一直运行





### Deployment （常用）

Deployment 为Pod和ReplicaSet提供一个声明方法，用来替代Replication Controller 来方便管理

可以声明一个yaml文件，确保deployment的状态信息



### Services

允许多个deployments之间通信，从而确保pods之间通信



实例

```
kind: Service
opiVersion: v1
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```



#### Services 类型

```
Internal:仅用于集群内部通信的ClusterIP类型，即internal IP

External: 接入集群外部请求的NodePort类型， 工作于每个节点的主机IP之上，

LoadBalance: 可以把外部请求负载均衡至多个Node主机IP的NodePort之上
```



### Job

Pods管理程序，包含一系列job

类似于cronjob



### DaemonSet

确保所有nodes 运行同一个指定类型的pod