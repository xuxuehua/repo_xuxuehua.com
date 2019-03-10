---
title: "yaml"
date: 2019-03-08 11:59
---


[TOC]





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

