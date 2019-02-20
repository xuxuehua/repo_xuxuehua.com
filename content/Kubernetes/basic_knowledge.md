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

同一Pod中的容器共享网络名称空间和存储资源，这些容器可以通过loopback直接通信，同时又在Mount，User， PID等名称空间保持了隔离





#### Pod template

(Resource)[https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/]

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

将资源进行分类的标识符。

label是一个key=value的键值对，可以附加到各种资源对象上， 列入Node，Pod， Service， Replication Controller等



#### Label Selector 

很多object可能有相同的label通过label selector， 客户端可以指定object集合，通过label selector 对object的集合进行操作

* Equality-based: 可以使用=， ==， !=操作， 逗号分隔多个表达式

* Et-based:  可以使用in， notin， ! 操作符

  e.g.`kubectl get pods -l 'environment=production,tier=frontend' $ kubectl get pods -l 'environment in (production), tier in (frontend)'`



### Pod 控制器

借助Controller 对Pod进行管理

包括以下多种调度器



#### Replication Controller

定义了一个期望的场景，声明某种pod的副本数量在任意时刻都符合某个预期值

e.g. `apiVersion: extensions/v1beat1 kind: Replication metadata: name: frontend `



#### ReplicaSet



#### Deployment

Deployment 为Pod和ReplicaSet提供一个声明方法，用来替代Replication Controller 来方便管理



#### StatefulSet



#### Job





### Volume 存储卷

独立于容器文件系统之外的存储空间，常用于扩展容器的存储空间并为其提供存储能力。

临时卷和本地卷位于Node本地，一旦Pod被调度到其他Node，这类存储卷将无法访问





### name 和 namespace

name 是kubernetes集群中资源对象的标识符，作用域为namespace

namespace用于实现项目资源隔离，形成逻辑分组





### Annotation 注解

附加在对象之上的键值类型的数据，用于将各种非标识型元数据附加到对象之上，但不能用于标识和选择对象

方便用户和工具阅读查找



### Ingress 网络入口

Pod和Service对象间的通信使用其内部专用地址进行通信

Ingress可以开放某些Pod对象给外部用户访问









### Horizontal Pod Autoscaler

对资源实现削峰填谷， 提高集群的整体资源利用率

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

      



### Service 服务资源

建立在一组pod之上，并为这组Pod对象定义一个统一的固定访问入口（通常是一个IP地址）

到达Service IP的请求将被负载均衡至其后的端点，各个Pod对象之上，因此Service 本质上来讲是一个四层代理服务

一组pod能够被Service 访问到，通常通过Label Selector 实现



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

Endpoint: Pod IP + Container Port 





## Kubernetes 集群组件



### Master 

集群管理，集群接口管理，监控，编排



#### API server 

负责输出RESTful API输出

接受响应所有REST请求，结果状态被持久存储于etcd中

即集群的网关



#### controller-manager





#### scheduler



### etcd 集群状态存储系统

独立的服务组件，不隶属于Kubernetes集群自身





### Node 

负责提供容器的各种依赖环境，并接受master管理



#### kubelet

代理程序

在API Server上注册当前工作节点，定期向Master汇报节点资源使用情况

通过cAdvistor 监控容器和节点的资源占用情况



#### Container Runtime

负责下载镜像并运行容器

支持Docker，RKT，cri-o和Fraki



#### kube-proxy

每个工作节点都需要运行该守护进程，能够按需为Service资源对象生成iptables 和ipvs规则





### Add-ons 附件

#### KubeDNS

集群中调度运行提供DNS服务的Pod

同一集群中的其他Pod可以使用此DNS服务解决主机名解析问题



#### Kubernetes Dashboard 

管理集群应用



#### Heapster 

容器和节点的性能监控与分析系统

逐渐有Prometheus结合其他组件替代



#### Ingress Controller

实现应用层HTTP(s)的负载均衡





## Kubernetes 网络模型

主要的通信

```
同一Pod内
Pod之间
Pod 与Service之间
集群外与 Service
```



### 实现策略

所有Pod间均可不经过NAT而直接通信

所有节点均可不经过NAT而直接与所有容器通信

容器自己使用的IP也是其他容器或节点直接看到的地址，即Pod自身的地址直接通信



Pod IP可以存在于某个网卡，或者虚拟设备

Service IP是一个虚拟IP地址，没有任何设备配置此地址，由iptables或ipvs重新定向到本地端口，再调度到后端Pod 对象，即Cluster IP





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



