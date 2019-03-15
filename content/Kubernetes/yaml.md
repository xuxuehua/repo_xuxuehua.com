---
title: "yaml"
date: 2019-03-08 11:59
---


[TOC]





# yaml 定义

## apiVersion





## kind

定义了这个API对象的类型



## Metadata

API对象的“标识”，即元数据，也是从Kubernetes里找到这个对象的主要依据，对所有API对象来说，这一部分的格式和字段基本是一致的

其中最主要使用到的字段是Labels





## Spec

存放属于这个对象独有的定义，用来描述它要表达的功能



### 仔细阅读

仔细阅读 $GOPATH/src/k8s.io/kubernetes/vendor/k8s.io/api/core/v1/types.go 里，type Pod struct ，尤其是 PodSpec 部分的内容。争取做到下次看到一个 Pod 的 YAML 文件时，不再需要查阅文档，就能做到把常用字段及其作用信手拈来。



## command

指定不同于镜像默认运行的应用程序，可以同时使用args字段进行参数传递，将覆盖镜像中的默认定义

自定义args 是向容器中的应用程序传递配置信息的常用方式





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





## imagePullPolicy

定义了拉取镜像的策略

默认策略为Always，即每次创建Pod的时候会重新拉取一次镜像

IfNotPresent  仅当本地镜像缺失时方才从目标仓库中下载镜像
Never 禁止从仓库中下载镜像，仅仅使用本地镜像





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







## ports

显示指定容器端口，为其赋予一个名称方便调用

其值为一个列表，有一个到多个端口对象组成，且嵌套以下字段

```
containerPort <integer> 必选字段，指定Pod的IP地址暴露的容器端口 0-65536
name <string> 当前容器端口名称，在当前pod内需要唯一，此端口名可以被Service资源调用
protocol 可以为TCP或UDP，默认为TCP
```



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



## containers



### Lifecycle

定义了Container Lifecycle Hooks，即容器状态发生变化时触发的一系列钩子

```
apiVersion: v1
kind: Pod
metadata:
  name: lifecycle-demo
spec:
  containers:
  - name: lifecycle-demo-container
    image: nginx
    lifecycle:
      postStart:
        exec:
          command: ["/bin/sh", "-c", "echo Hello from the postStart handler > /usr/share/message"]
      preStop:
        exec:
          command: ["/usr/sbin/nginx","-s","quit"]
```

> postStart 指在容器启动后，立刻执行一个指定的操作
>
> 若postStart执行超时或者错误，Kubernetes会在该Pod的Events中报出该容器启动失败的错误信息，导致Pod也处于失败的状态
>
> postStop 指容器被杀死之前，执行的操作
>
> 由于是同步的，会阻塞之前的容器杀死流程，直到这个Hook定义的操作完成之后，才允许容器被杀死







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

供用户将Pod与Node进行绑定的字段



```
apiVersion: v1
kind: Pod
...
spec:
 nodeSelector:
   disktype: ssd
```

> 这样意味着Pod只能运行在携带disktype： ssd标签的节点上，否则将调度失败



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



### NodeName

一旦 Pod 的这个字段被赋值，Kubernetes 项目就会被认为这个 Pod 已经经过了调度，调度的结果就是赋值的节点名字。所以，这个字段一般由调度器负责设置，但用户也可以设置它来“骗过”调度器，当然这个做法一般是在测试或者调试的时候才会用到。



## hostAliases

定义了 Pod 的 hosts 文件（比如 /etc/hosts）里的内容



```
apiVersion: v1
kind: Pod
...
spec:
  hostAliases:
  - ip: "10.1.2.3"
    hostnames:
    - "foo.remote"
    - "bar.remote"
...
```

以上面的配置，Pod启动后，/etc/hosts文件会如下

```
cat /etc/hosts
# Kubernetes-managed hosts file.
127.0.0.1 localhost
...
10.244.135.10 hostaliases-pod
10.1.2.3 foo.remote
10.1.2.3 bar.remote
```



在 Kubernetes 项目中，如果要设置 hosts 文件里的内容，一定要通过这种方法。否则，如果直接修改了 hosts 文件的话，在 Pod 被删除重建之后，kubelet 会自动覆盖掉被修改的内容。



### hostNetwork/hostIPC/hostPID

在Pod中的容器要共享宿主机的Namespace，也一定是pod级别定义的

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  hostNetwork: true
  hostIPC: true
  hostPID: true
  containers:
  - name: nginx
    image: nginx
  - name: shell
    image: busybox
    stdin: true
    tty: true
```

> 这个Pod里面的所有容器，都会直接使用宿主机的网络，直接与IPC进行通信，以及看到宿主机正在运行的所有进程



## shareProcessNamespace

Pod 里面的容器要共享PID Namespace

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  shareProcessNamespace: true
  containers:
  - name: nginx
    image: nginx
  - name: shell
    image: busybox
    stdin: true
    tty: true
```



这个 Pod 被创建后，可以使用 shell 容器的 tty 跟这个容器进行交互了。





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



## initContainers

以列表的形式定义可用的初始容器

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
    image: ikubernetes/myapp:v1
  initContainers:
  - name: init-something
    image: busybox
    command: ['sh', '-c', 'sleep 10']
```





## lifecycle

### postStart 

于容器创建完成之后立即运行钩子处理器handler

```
apiVersion: v1
kind: Pod
metadata:
  name: lifecycle-demo
spec: 
  containers:
  - name: lifecycle-demo-containers
    image: ikubernetes/myapp:v1
    lifecycle:
      postStart:
        exec:
          command: ["/bin/sh", "-c", "echo 'lifecycle hooks handler' > /usr/share/nginx/html/test.html"]
```



### preStop 

于容器终止操作之前立即运行的钩子处理器，以同步的方式调用

在其完成之前会阻塞删除容器的操作的调用





## livenessProbe



### exec

exec类型探针通过在目标容器中执行由用户自定义的命令来判断容器的健康状态

返回0表示成功，其他均为失败

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness-exec
  name: liveness-exec
spec: 
  containers:
  - name: liveness-exec-demo
    image: busybox
    args: ["/bin/sh", "-c", "touch /tmp/healthy; sleep 60; rm -rf /tmp/healthy; sleep 600;"]
    livenessProbe:
      exec:
        command: ["test", "-e", "/tmp/healthy"]
```



### httpGet

向目标容器发起一个http请求，根据响应状态码进行结果盘点

2xx 或3xx表示通过

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-http
spec:
  containers:
    - name: liveness-http-demo
      image: nginx:1.12-alpine
      ports:
      - name: http
        containersPort: 80
      lifecycle:
        portStart:
          exec:
            command: ["/bin/sh", "-c", "echo Healthy > /usr/share/nginx/html/healthz"]
      livenessProbe:
        httpGet:
          path: /healthz
          port: http
          scheme: HTTP
```





### tcpSocket

基于TCP的存活性探测(TCPSocketAction) 向容器的特定端口发起TCP请求并尝试建立连接进行判定

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-tcp
spec:
  containers:
    - name: liveness-tcp-demo
      image: nginx:1.12-alpine
      ports:
      - name: http
        containersPort: 80
      livenessProbe:
        tcpSocket:
          port: http
```

