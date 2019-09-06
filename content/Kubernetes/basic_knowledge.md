---
title: "Basic_Knowledge"
layout: page
date: 2018-06-02 19:03
---

[TOC]

# 基础知识

![alt](https://cdn.pbrd.co/images/HocvM1f.png)





## 容器本质

对与容器而已，一个容器永远只能管理一个进程，

而一个容器就是一个进程




## Kubernetes 特性

How do you manage all these containers running on a single host, and across your whol infrastructure?

Kubernetes is an open source project that enables software teams of all sizes, from a small startup to a Fortune 100 company, to automate deploying, scaling, and manging applications on a group of cluster of server machines. 



Google 每周约部署2billion containers.





###自动的调度 Auto Scaling

对资源默认的调度

Pods can be horizontally scaled via API



### 服务的自动发现 Service Discovery





### 金丝雀发布/灰度发布 Canary Deployment

灰度发布（又名金丝雀发布）是指在黑与白之间，能够平滑过渡的一种发布方式。在其上可以进行A/B testing，即让一部分用户继续用产品特性A，一部分用户开始用产品特性B，如果用户对B没有什么反对意见，那么逐步扩大范围，把所有用户都迁移到B上面来。灰度发布可以保证整体系统的稳定，在初始灰度的时候就可以发现、调整问题，以保证其影响度。

一旦有故障发生，会立即自动执行回滚操作。

优先发布一台或少量机器升级，等验证无误后再更新其他机器。优点是用户影响范围小，不足之处是要额外控制如何做自动更新。





### 蓝绿部署 Blue-Green Deployment

2组机器，蓝代表当前的V1版本，绿代表已经升级完成的V2版本。通过LB将流量全部导入V2完成升级部署。优点是切换快速，缺点是影响全部用户。







## Kubernetes 架构



![alt](https://cdn.pbrd.co/images/HocwGKR.png)

![alt](https://snag.gy/xikmph.jpg)



![img](https://snag.gy/XTBSJr.jpg)



![img](https://snag.gy/FPMuN5.jpg)





![img](https://snag.gy/Ht860F.jpg)





### Master node

集群的网关和中枢，负责为用户和客户端暴露API，跟踪其他服务器的健康状态，以最优的方式调度工作负载，已经编排其他组件之间的通信等任务。



包含3个进程

#### API Server

提供资源统一的入口

负责API服务



#### Controller manager

资源的管理和调度（pod调度），即容器编排

确保创建的容器始终处于健康状态， 确保控制器健康



#### Scheduler

调度的队列，观察node的列表

调度容器创建的请求

根据用户需要的资源量，进行分配





### Worker node

#### Kubelet

与Master node通信的集群代理

负责pod对应的容器创建，启动停止等任务，  与Master node协同工作, 实现集群管理，实现容器运行时的交互，称CRI (Container  Runtime Interface)



还通过gRPC协议，同Device Plugin的插件进行交互，这个插件时kubernetes用来管理GPU等宿主机物理设备的主要组件

也是基于Kubernetes项目进行机器学习训练，高性能作业支持等工作必须关注的功能



另是调用网络插件和存储插件为容器配置网络和数据持久化，即CNI (Container Networking Interface), CSI (Container Storage Interface)





#### Kube proxy

实现kubernetes service 的通信和负载均衡机制的重要组件，缓存代理，通过iptables的nat表实现



#### Docker Engine

Docker 引擎，负责本机的容器创建和管理工作



### etcd

Master节点使用etcd进行存储, 简单的key-value 存储，即整个集群的持久化数据

整个集群所有的对象状态信息，都存储与etcd中，需要高可用，至少3个

但都是通过kube-apiserver实现，因为需要APIServer进行授权工作





### kubectl

与master node交互

#### kubeconfig

包含服务器信息，接入API Server的认证信息





## Kubernetes 功能

### Pod 容器集

Pod 是Kubernetes中可以创建的最小部署单元， Pod是一组容器的集合

同一Pod中的容器共享网络名称空间和存储资源，这些容器可以通过loopback直接通信，同时又在Mount，User， PID等名称空间保持了隔离

Pod 代表着Kubernetes的部署单元以及原子运行单元，即一个应用程序的单一运行实例，通常由共享资源且关系紧密的一个或者多个应用容器组成

集群中Pod 对象的IP地址需在同一平面（网段）内进行通信

Supports 5k node clusters

150k total pods, 



#### Pod IP

Pod 对象被分配的一个集群内专用的IP，Pod内部所有容器共享Pod对象的Network和UTS名称空间，容器间通过回环地址通信

Pod外的其他组件通信需要使用Service资源对象的Cluster IP



#### 存储卷 

Pod对象可以配置一组存储卷，给其内部所有容器使用，完成容器间数据共享

同时也保证容器重启后，数据不丢失





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







### Volume 存储卷

独立于容器文件系统之外的存储空间，常用于扩展容器的存储空间并为其提供存储能力。

临时卷和本地卷位于Node本地，一旦Pod被调度到其他Node，这类存储卷将无法访问





### name 

name 是kubernetes集群中资源对象的标识符，作用域为namespace





### namespace

namespace用于实现项目资源隔离，形成逻辑分组(如多个客户服务之间的区分)

启动kubernetes，默认为default namespace， 开始时objects都在default namespace中

```
[root@localhost ~]# kubectl get namespace
NAME              STATUS   AGE
default           Active   16m
kube-node-lease   Active   16m
kube-public       Active   16m
kube-system       Active   16m
```





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

Pod的抽象层，

到达Service IP的请求将被负载均衡至其后的端点，各个Pod对象之上，因此Service 本质上来讲是一个四层代理服务

一组pod能够被Service 访问到，通常通过Label Selector 做关联的



* Pod, RC, Service 关系



![alt](https://cdn.pbrd.co/images/Hocxo86.png)








#### Service IP (Cluster IP)

一种虚拟IP，也称Cluster IP， 专用于集群内通信



#### Service 类型

```

```

> 每一种都以前一种为基础才能实现



#### Endpoint

Endpoint: Pod IP + Container Port 





### Kubernetes 证书

除非开启不安全模式，否则默认都是通过https访问kube-apiserver



kubeadm 为Kubernetes 项目生成的证书文件都放在master节点的`/etc/kubernetes/pki` 下面，最主要的是`ca.crt` 和私钥`ca.key`



kubectl获取容器日志等streaming操作时，需要通过kube-apiserver向kubelet发起请求，这个连接也必须是安全的，kubeadm为这一步生成的是`apiserver-kubelet-client.crt`文件，对应私钥为`apiserver-kubelet-client.key`





## Kubernetes 集群组件



### Master 

集群管理，集群接口管理，监控，编排



#### API server  集群的网关

负责输出RESTful API输出

接受响应所有REST请求，结果状态被持久存储于etcd中

即集群的网关



#### controller-manager





#### scheduler



#### etcd 集群状态存储系统

独立的服务组件，不隶属于Kubernetes集群自身





### Node 

负责提供容器的各种依赖环境，并接受master管理



#### kubelet 代理组件

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



## Kubernetes API 对象

一个API对象在Etcd里面的完整路径由: GROUP(API组)，Version(API 版本)， Resource(API 资源类型) 三部分组成



API 对象的组织方式，是层层传递的

![img](https://snag.gy/KV5e6q.jpg)



```
apiVersion: batch/v2alpha1
kind: CronJob
...
```

> CronJob 就是API对象的Resource， batch是Group， v2alpha1是Version











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

支持两种服务发现模式 - 环境变量和DNS



### 环境变量

当Pod运行在Node上，Kubelet会为每个活跃的Service添加一组环境变量

### DNS

Kubernetes 通过Add on 方式引入DNS，把服务名称作为DNS域名，这样，程序就可以直接使用服务名来建立通信连接，目前大部分应用都采用DNS这种机制



### 外部系统访问service问题

* Node IP： node物理节点的IP
* Pod IP：Pod的IP
* Cluster IP：Service 的IP （不可以ping测试）





## Kubernetes 日志

### Logs 日志

#### 典型架构

![img](https://snag.gy/fTuwEB.jpg)



## Kubernetes 监控



### 监控指标

Node health

Health of Kubernetes

Application health (and metrics)



### 常用工具

#### cAdvisor

专用于containers的开源资源收集器，自动发现node并收集信息，以及主机全局的使用及分析



#### Heapster

以pod形式运行在cluster

![img](https://snag.gy/nvUobY.jpg)



#### Prometheus

时间序列数据库，通过query 语句发送应用以及metrics data



#### Grafana

将上述三种系统数据组合成显示图表





# 术语

## HPA

Horizontal Pod Autoscaler    

​    



## CNCF

Cloud Native Computing Foundation

