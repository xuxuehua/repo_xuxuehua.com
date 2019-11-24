---
title: "pod"
date: 2019-02-27 21:42
---


[TOC]



# Pod



![img](https://snag.gy/fPQNgz.jpg)





标准的Kubernetes API资源，在yaml中使用kind，apiVersion，metadata和spec字段定义

status字段在对象创建后由系统自行维护



通过在spec字段中嵌套containers，将容器对象启动



Pod 里面的容器共享同一个Network Namespace，同一组数据卷，而达到高效率的交换信息，即保证了容器间的紧密协作关系



Pod类似于传统基础设置里面的虚拟机角色，而容器，就是虚拟机里面运行的用户程序



## 实现

Pod其实是一组共享了某些资源的容器



Pod里面所有容器，共享的是同一个Network Namespace，并且可以声明共享同一个Volume



### infra 容器

在Kubernetes 中，Pod的实现需要使用一个中间容器称为Infra容器，在Pod中，与Infra容器关联在一起

而Pod的生命周期和Infra容器是一致的，与容器A和B无关



![img](https://snag.gy/m59fG6.jpg)



Infra 容器使用一个特殊的镜像 (k8s.gcr.io/pause)，占用资源极少， Infra 容器Hold住Network Namespace后，用户容器就可以加入到Infra 容器的Network Namespace中，所以在查看容器在宿主机的Namespace文件的时候，指向的值是完全一样的



这样，容器A和容器B可以直接通过localhost 进行通信，由于一个Pod值可以有一个IP地址，而这个地址就是Network Namespace 对应的IP地址









## 设计

通常应该一个容器中仅运行一个进程，而日志信息通过输出至容器的标准输出，用户通过kubectl log 进行获取

省去了用户手动分拣日志信息



# Pod 生命周期（phase）

pod.status.phase 表示当前Pod的状态

极少使用，如依赖git仓库中的代码，在postStart的时候使用git clone



## postStart   ` <Object>` 启动后

### exec `<Object>` 用户指定命令

根据指令返回码判断

```
apiVersion: v1
kind: Pod
metadata:
  name: poststart-pod
  namespace: default
spec:
  containers:
  - name: busybox-httpd
    image: busybox:latest
    imagePullPolicy: IfNotPresent
    lifecycle:
      postStart:
        exec:
          command: ['mkdir', '-p', '/data/web/html']
    command: ['/bin/sh', '-c', 'sleep 3600']
```





### httpGet      `<Object>`

```

```





### tcpSocket    `<Object>`



## preStop     `<Object>` 终止前

### exec `<Object>` 用户指定命令

根据指令返回码判断

```

```





### httpGet      `<Object>`

```

```





### tcpSocket    `<Object>`





# 容器探测

主容器定时探测容器状态

建立在pod.containers 之上

```
[root@master ~]# kubectl explain pods.spec.containers
KIND:     Pod
VERSION:  v1

RESOURCE: containers <[]Object>
```



## liveness 存活探测

探测容器是否处于存活状态



### exec `<Object>` 用户指定命令

根据指令返回码判断

```
apiVersion: v1
kind: Pod
metadata:
  name: liveness-exec-pod
  namespace: default
spec:
  containers:
  - name: liveness-exec-container
    image: busybox:latest
    imagePullPolicy: IfNotPresent
    command: ["/bin/sh", "-c", "touch /tmp/healthy; sleep 30; rm -f /tmp/healthy; sleep 3600"]
    livenessProbe:
      exec:
        command: ["test", "-e", "/tmp/healthy"]
      initialDelaySeconds: 1
      periodSeconds: 3
```





### httpGet      `<Object>`

```
apiVersion: v1
kind: Pod
metadata:
  name: liveness-httpget-pod
  namespace: default
spec:
  containers:
  - name: liveness-httpget-container
    image: ikubernetes/myapp:v1
    imagePullPolicy: IfNotPresent
    ports:
    - name: http
      containerPort: 80
    livenessProbe:
      httpGet:
      	port: http
      	path: /index.html
      initialDelaySeconds: 1
      periodSeconds: 3
```





### tcpSocket    `<Object>`





## readiness 就绪探测   (重要)

探测容器中的服务和程序是否提供服务, 不设置会让未经探测的pod被selector匹配之后立即提供服务，从而导致错误



### exec `<Object>` 用户指定命令

根据指令返回码判断







### httpGet      `<Object>`

```
apiVersion: v1
kind: Pod
metadata:
  name: readiness-httpget-pod
  namespace: default
spec:
  containers:
  - name: readiness-httpget-container
    image: ikubernetes/myapp:v1
    imagePullPolicy: IfNotPresent
    ports:
    - name: http
      containerPort: 80
    readinessProbe:
      httpGet:
      	port: http
      	path: /index.html
      initialDelaySeconds: 1
      periodSeconds: 3
```



进入容器后创建和删除index文件，会看到以下效果

```
[root@master ~]# kubectl exec -it readiness-httpget-pod -- /bin/sh
```



```
[root@master manifests]# kubectl get pods -w
NAME                           READY   STATUS             RESTARTS   AGE
client                         1/1     Running            0          35h
liveness-exec-pod              0/1     CrashLoopBackOff   43         147m
liveness-httpget-pod           1/1     Running            1          12m
myapp-5bc569c47d-62hwb         1/1     Running            0          34h
myapp-5bc569c47d-kwlcp         1/1     Running            0          34h
myapp-5bc569c47d-tpns4         1/1     Running            0          34h
nginx-deploy-55d8d67cf-7zkxm   1/1     Running            0          44h
pod-demo                       2/2     Running            8          8h
readiness-httpget-pod          1/1     Running            0          67s
readiness-httpget-pod          0/1     Running            0          69s
readiness-httpget-pod          1/1     Running            0          4m48s
```







### tcpSocket    `<Object>`







### 





## Pod 状态





### Pending

Pod的YAML文件已经提交给Kubernetes，API Server 创建了Pod资源对象并已存储etcd中，但未被调度完成，或着仍然处于从仓库下载镜像的过程中

如调度失败的情况



#### Condition: Unschedulable

调度出现问题



### Running

Pod已经被调度至一个具体节点绑定，并且所有容器都已经被kubelet创建完成



#### 异常情况

Pod（即容器）的状态是 Running，但是应用其实已经停止服务的例子

```
1. 程序本身有 bug，本来应该返回 200，但因为代码问题，返回的是500；
2. 程序因为内存问题，已经僵死，但进程还在，但无响应；
3. Dockerfile 写的不规范，应用程序不是主进程，那么主进程出了什么问题都无法发现；
4. 程序出现死循环。
```



### Succeeded

Pod 中的所有容器都已经成功运行完成并终止，且不会被重启

常见于一次性任务



### Failed

所有容器都已经终止，但至少有一个容器终止失败，即容器返回了非0值的退出状态或已经被系统终止

此时需要debug这个容器的应用，如查看Pod的Events和日志



### Unknown

Pod的状态不能持续地被kubelet汇报给kube-apiserver，很可能是主从节点间通信出现问题



### CrashLoopBackOff

Kubernetes. 尝试一次又一次的重启Pod





## restartPolicy 容器重启策略

### Always (默认)

pod对象终止就将其重启，默认设定



### OnFailure

只在pod对象出现错误时方将其重启



### Never

从不重启



## pod 资源需求

自主式Pod 要求stress 容器确保128Mi的内存及五分之一的CPU核心(200m) 资源可用

运行stress-ng 镜像启动一个进程(-m 1) 进行内存性能压力测试，满载测试时也会尽可能多地占用CPU资源

```
apiVersion: v1
kind: Pod
metadata:
  name: stress-pod
spec:
  containers:
    - name: stress
      image: ikubernetes/stress-ng
      command: ["/usr/bin/stress-ng", "-m 1", "-c 1", "-metrics-brief"]
      resources:
        requests:
          memory: "128Mi"
          cpu: "200m"

```





```
kubectl create -f pod-resources-test.yaml
```



top命令观察其CPU及内存资源占用状态

```
kubectl exec stress-pod -- top
```





### limits 资源限制

limits 属性为容器定义资源的最大可用量。

资源分配时，可压缩形资源的CPU的控制阀可以自由调节，容器进程无法获得超出其CPU配额的可用时间

如果超出，会被OOM kill 掉



```
apiVersion: v1
kind: Pod
metadata:
  name: memleak-pod
  labels:
    app: memleak
spec:
  containers:
    - name: simmemleak
      image: saadali/simmemleak
      resources:
        requests:
          memory: "64Mi"
          cpu: "1"
        limits:
          memory: "64Mi"
          cpu: "1"

```





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









# Pod 控制器 Controller

借助Controller 对Pod进行管理，实现一次性的Pod对象管理, 对同一类pod进行管理

包括以下多种调度器



## Replication Controller (淘汰)

定义了一个期望的场景，声明某种pod的副本数量在任意时刻都符合某个预期值

e.g. `apiVersion: extensions/v1beat1 kind: Replication metadata: name: frontend `





## ReplicaSet

确保给一个pod所指定数量的replicas 会一直运行

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp
  namespace: default
spec: #控制器的spec
  replicas: 2
  selector:
    matchLabels:
      app: myapp
      release: canary
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        release: canary
        environment: qa
    spec:  #pod 的spec
      containers:
      - name: myapp-container
        image: ikubernetes/myapp:v1
        ports:
        - name: http
          containerPort: 80
```



## Deployment  无状态（常用）

Deployment 为Pod和ReplicaSet提供一个声明方法，用来替代Replication Controller 来方便管理

可以声明一个yaml文件，确保deployment的状态信息

管理无状态的应用





Deployment可以建立在ReplicasSet之上， 控制多个rs

![img](https://snipboard.io/21EiWY.jpg)



```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deploy
  namespace: default
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myapp
      release: canary
  template:
    metadata:
      labels:
        app: myapp
        release: canary
    spec:
      containers:
      - name: myapp
        image: ikubernetes/myapp:v1
        ports:
        - name: http
          containerPort: 80
```



```
# kubectl get pods
NAME                            READY   STATUS    RESTARTS   AGE
myapp-deploy-7d574d56c7-nphdc   1/1     Running   0          104s
myapp-deploy-7d574d56c7-t989l   1/1     Running   0          104s
```



## HPA

Horizontal Pod Autoscaler

根据资源要求，自动扩展资源



## StatefulSet  有状态

管理有状态应用，每一个pod副本会单独管理



## Operator 有状态

比较好的有状态应用的解决方案，但目前都未成熟



## Job

Pods管理程序，包含一系列job

类似于cronjob，一次性任务



## CronJob

计划的任务





## DaemonSet 无状态

确保所有nodes 只运行一个指定类型的pod副本

常用系统级别的应用，守护进程类的

```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: myapp-ds
  namespace: default
spec:
  selector:
    matchLabels:
      app: filebeat
      release: stable
  template:
    metadata:
      labels:
        app: filebeat
        release: stable
    spec:
      containers:
      - name: filebeat
        image: ikubernetes/filebeat: 5.6.5-alpine
        env:
        - name: REDIS_HOST
          value: redis.default.svc.cluster.local
        - name: REDIS_LOG_LEVEL
```





## Services

允许多个deployments之间通信，从而确保pods之间通信

Services 通过标签选择器来关联后端的pod

其本质就是iptables的DNAT规则， 在1.11版本之后，使用ipvs来实现更好的负载均衡效果



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



### Services 类型

```
Internal:仅用于集群内部通信的ClusterIP类型，即internal IP

External: 接入集群外部请求的NodePort类型， 工作于每个节点的主机IP之上，

LoadBalance: 可以把外部请求负载均衡至多个Node主机IP的NodePort之上
```





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





## Pod Probe 探针

健康检查探针，kubelet会根据Probe返回值来决定这个容器的状态



test-liveness-exec.yaml

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: test-liveness-exec
spec:
  containers:
  - name: liveness
    image: busybox
    args:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 5
      periodSeconds: 5
```

> 与此同时，我们定义了一个这样的 livenessProbe（健康检查）。它的类型是 exec，这意味着，它会在容器启动后，在容器里面执行一句我们指定的命令，比如：“cat /tmp/healthy”。这时，如果这个文件存在，这条命令的返回值就是 0，Pod 就会认为这个容器不仅已经启动，而且是健康的。这个健康检查，在容器启动 5 s 后开始执行（initialDelaySeconds: 5），每 5 s 执行一次（periodSeconds: 5）。



```
$ kubectl create -f test-liveness-exec.yaml
```

```
$ kubectl get pod
NAME                READY     STATUS    RESTARTS   AGE
test-liveness-exec   1/1       Running   0          10s
```



30s 之后查看

```
$ kubectl describe pod test-liveness-exec
```

```
FirstSeen LastSeen    Count   From            SubobjectPath           Type        Reason      Message
--------- --------    -----   ----            -------------           --------    ------      -------
2s        2s      1   {kubelet worker0}   spec.containers{liveness}   Warning     Unhealthy   Liveness probe failed: cat: can't open '/tmp/healthy': No such file or directory
```

> 这里报错，表示文件已经不存在了



然而pod并没有Fail，而是进入了running 状态，是因为Pod的恢复机制，即restartPolicy

Pod的恢复过程永远发生在当前节点(Node), 除非pod.spec.node字段被更改