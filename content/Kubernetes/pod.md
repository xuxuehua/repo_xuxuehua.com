---
title: "pod"
date: 2019-02-27 21:42
---


[TOC]



# Pod

标准的Kubernetes API资源，在yaml中使用kind，apiVersion，metadata和spec字段定义

status字段在对象创建后由系统自行维护



通过在spec字段中嵌套containers，将容器对象启动



## 设计

通常应该一个容器中仅运行一个进程，而日志信息通过输出至容器的标准输出，用户通过kubectl log 进行获取

省去了用户手动分拣日志信息



## pod 生命周期

### Pending

API Server 创建了Pod资源对象并已存储etcd中，但未被调度完成，或着仍然处于从仓库下载镜像的过程中



### Running

Pod已经被调度至某节点，并且所有容器都已经被kubelet创建完成



### Succeeded

Pod 中的所有容器都已经成功终止并且不会被重启



### Failed

所有容器都已经终止，但至少有一个容器终止失败，即容器返回了非0值的退出状态或已经被系统终止



### Unknown

API Server 无法正常获取到Pod对象的状态信息，通常是由于无法与所在工作节点的kubelet通信





## 分布式模型

### sidercar pattern 

边车模型或跨斗模型

即pod的主应用容器提供协同的辅助应用容器，每个应用独立运行

如主应用容器中的日志使用agent收集到日志服务器中，可以将agent运行为辅助应用容器，即sidecar

还如主应用容器中启动database 缓存，sidecar启动Redis Cache



### Ambassador pattern 

大使模型

即远程服务器创建本地代理，主容器应用通过代理容器访问远程服务





### Adapter pattern

适配器模式

将主应用容器中的内容进行标准化输出

如日志数据或者指标数据的输出，







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







## 环境变量

通过环境变量在容器启动时传递配置信息



### env

在容器配置段中嵌套env字段，值是环境变量构成的列表

```
name <string>  环境变量名称，必须字段
value <string>  传递值，通过$(VAR_NAME) 引用
```



#### example

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-env
spec: 
  containers:
  - name: filebeat
    image: ikubernetes/filebeat:5.6.5-alpine
    env: 
    - name: REDIS_HOST
      value: db.xurick.com:6379
    - name: LOG_LEVEL
      value: info
```



### envFrom



# yaml 定义

## apiVersion





## command

指定不同于镜像默认运行的应用程序，可以同时使用args字段进行参数传递，将覆盖镜像中的默认定义

自定义args 是向容器中的应用程序传递配置信息的常用方式



### example

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-custom-command
spec:
  containers:
    - name: myapp
      image: alpine:latest
      command: ["/bin/sh"]
      args: ["-c", "while true; do sleep 30; done"]
```





## ports

显示指定容器端口，为其赋予一个名称方便调用

其值为一个列表，有一个到多个端口对象组成，且嵌套以下字段

```
containerPort <integer> 必选字段，指定Pod的IP地址暴露的容器端口 0-65536
name <string> 当前容器端口名称，在当前pod内需要唯一，此端口名可以被Service资源调用
protocol 可以为TCP或UDP，默认为TCP
```

### example

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-example
spec:
  containers:
    - name: myapp
      image: ikubernetes/myapp:v1
      ports:
        - name: http
          containerPort: 80
          protocol: TCP
```







## imagePullPolicy

```
Always 镜像标签为latest，或镜像不存在时总是从指定的仓库中获取镜像
IfNotPresent  仅当本地镜像缺失时方才从目标仓库中下载镜像
Never 禁止从仓库中下载镜像，仅仅使用本地镜像
```



### example

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
    - name: nginx
      image: nginx:latest
        imagePullPolicy: Always
```

> 总是从镜像仓库中获取最新的nginx 镜像







## labels

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-labels
  labels:
    env: qa
    tier: frontend
spec: 
  containers:
  - name: myapp
    image: ikubernetes/myapp:v1
```



## selector

### matchLabels

通过直接给定键值来指定标签选择器

```
selector:
  matchLabels:
    component: redis
```





### matchExpressions

基于表达式指定的标签选择器列表，每个选择器都形如

```
{key: KEY_NAME, operator: OPERATOR, values: [VALUE1, VALUE2, ...]}
```



```
selector:
  matchExpressions:
    - {key: tier, operator: In, values: [cache]}
    - {key: environment, operator: Exists, values:}
```



## nodeSelector

调度某些资源至指定设备节点，使用nodeSelector选择器

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-nodeselector
  labels:
    env: testing
spec: 
  containers:
  - name: myapp
    image: ikubernetes/myapp:v1
  nodeSelector:
    disktype: ssd
```



## annotations

生成资源注解

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-nodeselector
  annotations:
    ilinux.io/created-by: cluster admin
spec: 
....
```

