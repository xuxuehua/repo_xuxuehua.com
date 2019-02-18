---
title: "Basic_Knowledge"
layout: page
date: 2018-06-02 19:03
---

[TOC]

# 基础知识

![alt](https://cdn.pbrd.co/images/HocvM1f.png)


## Kubernetes 特性



###自动的调度 Auto Scaling

对资源默认的调度



### 服务的自动发现 Service Discovery



### 灰度发布 App Deployment

灰度发布（又名金丝雀发布）是指在黑与白之间，能够平滑过渡的一种发布方式。在其上可以进行A/B testing，即让一部分用户继续用产品特性A，一部分用户开始用产品特性B，如果用户对B没有什么反对意见，那么逐步扩大范围，把所有用户都迁移到B上面来。灰度发布可以保证整体系统的稳定，在初始灰度的时候就可以发现、调整问题，以保证其影响度。

一旦有故障发生，会立即自动执行回滚操作。





## Kubernetes 架构



![alt](https://cdn.pbrd.co/images/HocwGKR.png)



### Master node

集群的网关和中枢，负责为用户和客户端暴露API，跟踪其他服务器的健康状态，以最优的方式调度工作负载，已经编排其他组件之间的通信等任务。



包含3个进程

API Server：提供资源统一的入口

Controller manager：资源的管理和调度（pod调度）

Scheduler：调度的队列，node的列表



使用etcd进行存储





### Worker node

包含3个进程

Kubelet：负责pod对应的容器创建，启动停止等任务， 与master 节点协作，实现集群管理， 与Master node协同同坐

Kube proxy：实现kubernetes service 的通信和负载均衡机制的重要组件，缓存代理，通过iptables的nat表实现

Docker Engine：Docker 引擎，负责本机的容器创建和管理工作



## Kubernetes 功能

### Pod 容器集

Pod 是Kubernetes中可以创建的最小部署单元， Pod是一组容器的集合

为分组的容器增加了一个抽象层，帮助用户调度工作负载，并为这些容器提供所需的联网和存储服务。

每个Pod里面都会有一个pause，大概20k大小。这个pause管理网络，以及容器的状态



#### Pod template

* (Resource)[https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/]

```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']
```



### Label 

> label是一个key=value的键值对，可以附加到各种资源对象上， 列入Node，Pod， Service， Replication Controller等



#### Label Selector 

> 很多object可能有相同的label通过label selector， 客户端可以指定object集合，通过label selector 对object的集合进行操作

* Equality-based: 可以使用=， ==， !=操作， 逗号分隔多个表达式

* Et-based:  可以使用in， notin， ! 操作符

  e.g.`kubectl get pods -l 'environment=production,tier=frontend' $ kubectl get pods -l 'environment in (production), tier in (frontend)'`



### Replication Controller

> 定义了一个期望的场景，声明某种pod的副本数量在任意时刻都符合某个预期值

e.g. `apiVersion: extensions/v1beat1 kind: Replication metadata: name: frontend `



### Deployment

> Deployment 为Pod和ReplicaSet提供一个声明方法，用来替代Replication Controller 来方便管理



### Horizontal Pod Autoscaler

> 对资源实现削峰填谷， 提高集群的整体资源利用率

![alt](https://cdn.pbrd.co/images/HocsZti.png)

#### Metrics 支持

* autoscaling/v1

  * CPU
* autoscaling/v2
  * Memory 

  * 自定义

    * Kubernetes1.6起支持，必须在kube-controller-manager中配制下列两项

      > --horizontal-pod-autoscaler-use-rest-clients=true
      >
      > --api-server 指向kube-aggregator, 或--api-server=true

      



### Service

> 一个pod的逻辑分组，一种可以访问它们的策略成为微服务
>
> 一组pod能够被Service 访问到，通常通过Label Selector 实现



* Pod, RC, Service 关系



![alt](https://cdn.pbrd.co/images/Hocxo86.png)



 * service 事例

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

   



#### Endpoint

> Endpoint: Pod IP + Container Port 

​    

## Kubernetes 服务发现机制

> 支持两种服务发现模式 - 环境变量和DNS



### 环境变量

> 当Pod运行在Node上，Kubelet会为每个活跃的Service添加一组环境变量

### DNS

> Kubernetes 通过Add on 方式引入DNS，把服务名称作为DNS域名，这样，程序就可以直接使用服务名来建立通信连接，目前大部分应用都采用DNS这种机制



### 外部系统访问service问题

* Node IP： node物理节点的IP
* Pod IP：Pod的IP
* Cluster IP：Service 的IP （不可以ping测试）



### 服务类型

* Cluster IP:  通过集群内部IP暴露服务，服务之能够在集群内部访问，默认是ServiceType
* Node Pord：每个
* 







​    

​    

​    



