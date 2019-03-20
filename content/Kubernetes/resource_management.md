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



##### 结构

Nginx-deployment.yaml

```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-set
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
```

> replicas 是3，对应关系如下



![img](https://snag.gy/RwPJvc.jpg)



##### 水平扩展

```
$ kubectl scale deployment nginx-deployment --replicas=4
deployment.apps/nginx-deployment scaled
```



##### 滚动扩展

```
$ kubectl create -f nginx-deployment.yaml --record
```

> --record  可以记录每次操作所执行的命令



```
$ kubectl get deployments
NAME               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3         0         0            0           1s
```

> AVAILABLE 字段描述了用户所期望的最终状态



```
$ kubectl rollout status deployment/nginx-deployment
Waiting for rollout to finish: 2 out of 3 new replicas have been updated...
deployment.apps/nginx-deployment successfully rolled out
```

> 在这个返回结果中，“2 out of 3 new replicas have been updated”意味着已经有 2 个 Pod 进入了 UP-TO-DATE 状态。



```
$ kubectl get rs
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-3167673210   3         3         3       20s
```

> 在用户提交了一个 Deployment 对象后，Deployment Controller 就会立即创建一个 Pod 副本个数为 3 的 ReplicaSet。这个 ReplicaSet 的名字，则是由 Deployment 的名字和一个随机字符串共同组成。



修改了 Deployment 的 Pod 模板，“滚动更新”就会被自动触发。

```
$ kubectl edit deployment/nginx-deployment
... 
    spec:
      containers:
      - name: nginx
        image: nginx:1.9.1 # 1.7.9 -> 1.9.1
        ports:
        - containerPort: 80
...
deployment.extensions/nginx-deployment edited
```

> 编辑 Etcd 里的 API 对象



查看滚动更细腻状态变化

```
$ kubectl rollout status deployment/nginx-deployment
Waiting for rollout to finish: 2 out of 3 new replicas have been updated...
deployment.extensions/nginx-deployment successfully rolled out


$ kubectl describe deployment nginx-deployment
...
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
...
  Normal  ScalingReplicaSet  24s   deployment-controller  Scaled up replica set nginx-deployment-1764197365 to 1
  Normal  ScalingReplicaSet  22s   deployment-controller  Scaled down replica set nginx-deployment-3167673210 to 2
  Normal  ScalingReplicaSet  22s   deployment-controller  Scaled up replica set nginx-deployment-1764197365 to 2
  Normal  ScalingReplicaSet  19s   deployment-controller  Scaled down replica set nginx-deployment-3167673210 to 1
  Normal  ScalingReplicaSet  19s   deployment-controller  Scaled up replica set nginx-deployment-1764197365 to 3
  Normal  ScalingReplicaSet  14s   deployment-controller  Scaled down replica set nginx-deployment-3167673210 to 0
```



新旧状态对比

```
$ kubectl get rs
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-1764197365   3         3         3       6s
nginx-deployment-3167673210   0         0         0       30s
```



#### Deployment  无状态

负责无状态应用

用于管理无状态持久化的应用，如HTTP

构建在ReplicaSet之上

负责在Pod定义发生变化时，对每个副本进行跟懂更新

实现了Pod的水平扩展和收缩(horizontal scaling out/in)



Deployment 所管理的Pod，他的ownerReference 时ReplicaSet

相对而言，Deployment 只是在ReplicaSet 的基础上，添加了`UP-TO-DATE` 这个跟版本有关的字段







#### StatefulSet 有状态

负责有状态应用

用于管理有状态的持久化应用，如database

比Deployment会为每个Pod创建一个独有的持久性标识符，并确保Pod之间的顺序性



#### DaemonSet 守护进程

常用于运行集群存储的守护进程，如glusterd，ceph，

日志收集进程如fluentd，logstash，

监控进程，如prometheus的Node Explorter，collected，datadog agent， Ganglia的gmond

确保每个节点都运行Pod的副本



#### Job 完成后终止

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





## API 群组



### 核心群组 core group

REST 路径为`/api/v1`，在资源配置信息apiVersion 字段中引用时可以不指定路径，而仅给出版本

如 `apiVersion:v1`



### 命名群组 named group

REST 路径为`/apis/$GROUP_NAME/$VERSION` 

如 `/apis/apps/v1`  在apiVersion字段中引用的格式为`apiVersion:$GROUP_NAME/$VERSION` 

如`apiVersion:apps/v1`