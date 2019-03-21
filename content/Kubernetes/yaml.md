---
title: "yaml"
date: 2019-03-08 11:59
---


[TOC]





# yaml 定义

## apiVersion





## kind

定义了这个API对象的类型



### PodPreset

Pod预先设置



pod.yaml

```
apiVersion: v1
kind: Pod
metadata:
  name: website
  labels:
    app: website
    role: frontend
spec:
  containers:
    - name: website
      image: nginx
      ports:
        - containerPort: 80
```



preset.yaml

```
apiVersion: settings.k8s.io/v1alpha1
kind: PodPreset
metadata:
  name: allow-database
spec:
  selector:
    matchLabels:
      role: frontend
  env:
    - name: DB_PORT
      value: "6379"
  volumeMounts:
    - mountPath: /cache
      name: cache-volume
  volumes:
    - name: cache-volume
      emptyDir: {}
```

> 在这个 PodPreset 的定义中，首先是一个 selector。这就意味着后面这些追加的定义，只会作用于 selector 所定义的、带有“role: frontend”标签的 Pod 对象，这就可以防止“误伤”。
>
> 然后，我们定义了一组 Pod 的 Spec 里的标准字段，以及对应的值。比如，env 里定义了 DB_PORT 这个环境变量，volumeMounts 定义了容器 Volume 的挂载目录，volumes 定义了一个 emptyDir 的 Volume。



现运行preset，然后在运行pod

```
$ kubectl create -f preset.yaml
$ kubectl create -f pod.yaml
```



```
$ kubectl get pod website -o yaml
apiVersion: v1
kind: Pod
metadata:
  name: website
  labels:
    app: website
    role: frontend
  annotations:
    podpreset.admission.kubernetes.io/podpreset-allow-database: "resource version"
spec:
  containers:
    - name: website
      image: nginx
      volumeMounts:
        - mountPath: /cache
          name: cache-volume
      ports:
        - containerPort: 80
      env:
        - name: DB_PORT
          value: "6379"
  volumes:
    - name: cache-volume
      emptyDir: {}
```

> 这个时候，我们就可以清楚地看到，这个 Pod 里多了新添加的 labels、env、volumes 和 volumeMount 的定义，它们的配置跟 PodPreset 的内容一样。此外，这个 Pod 还被自动加上了一个 annotation 表示这个 Pod 对象被 PodPreset 改动过。









## Metadata

API对象的“标识”，即元数据，也是从Kubernetes里找到这个对象的主要依据，对所有API对象来说，这一部分的格式和字段基本是一致的

其中最主要使用到的字段是Labels



### ownerReference

用于保存当前这个API对象的拥有者(Owner) 的信息







## Spec

存放属于这个对象独有的定义，用来描述它要表达的功能



### 仔细阅读

仔细阅读 $GOPATH/src/k8s.io/kubernetes/vendor/k8s.io/api/core/v1/types.go 里，type Pod struct ，尤其是 PodSpec 部分的内容。争取做到下次看到一个 Pod 的 YAML 文件时，不再需要查阅文档，就能做到把常用字段及其作用信手拈来。



### containers



#### command

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







#### Lifecycle

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







#### livenessProbe



##### exec

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



##### httpGet

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





##### tcpSocket

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







#### node

指明Pod与节点Node 的绑定字段



#### readinessProbe

检查结果的成功与否，决定这个Pod是不是能被通过Service的方式访问到，而不影响Pod的声明周期





#### restartPolicy 

pod的恢复机制，默认为Always，即任何时候容器发生已成，会被重建

```
Always:	在任何情况下，只要容器不在运行状态，就需要重启容器
OnFailure: 只在容器，异常时才自动重启容器
Never: 从来不重启容器
```





#### selector

##### matchLabels

通过直接给定键值来指定标签选择器

```
selector:
  matchLabels:
    component: redis
```





##### matchExpressions

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





## RollingUpdateStrategy

Deployment 对象的一个字段

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
...
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
```

> maxSurge 指除了DESIRED数量之外，在一次滚动中，Deployment控制器还可以创建多少个新的Pod
>
> maxUnavailable 指在一次滚动更细腻中，Deployment 控制器可以删除多少个旧Pod



## revisionHistoryLimit

为Deployment保留的历史版本个数

设置为0，表示再也不能做滚动更新操作了





## volumeClaimTemplates

被StatefulSet管理的Pod，都会声明一个对应的PVC

PVC的定义，源自于volumeClaimTemplates的模板字段

```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "nginx"
  replicas: 2
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
        image: nginx:1.9.1
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 1Gi
```





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









## nodeSelector (Deprecated by nodeAffinity)

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



## nodeAffinity

spec.affinity字段，是Pod里跟调度相关的一个字段



```
apiVersion: v1
kind: Pod
metadata:
  name: with-node-affinity
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: metadata.name
            operator: In
            values:
            - node-geektime
```

> requiredDuringSchedulingIgnoredDuringExecution	指这个nodeAffinity必须在每次调度的时候予以考虑，也表示可以在设置某些情况下不考虑这个nodeAffinity
>
> 当前Pod只会运行在metadata.name 是 node-geektime上面节点运行



```
apiVersion: v1
kind: Pod
metadata:
  name: with-toleration
spec:
  tolerations:
  - key: node.kubernetes.io/unschedulable
    operator: Exists
    effect: NoSchedule
```

> Toleration容忍”所有被标记为 unschedulable“污点”的 Node；“容忍”的效果是允许调度。









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




