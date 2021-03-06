---
title: "yaml"
date: 2019-03-08 11:59
---

[TOC]

# apiVersion

```
group_name/version
```

> group_name 省略表示 core核心群组

指明资源所在群组以及版本

```
[root@master ~]# kubectl api-versions
admissionregistration.k8s.io/v1beta1
apiextensions.k8s.io/v1beta1
apiregistration.k8s.io/v1
apiregistration.k8s.io/v1beta1
apps/v1
apps/v1beta1
apps/v1beta2
authentication.k8s.io/v1
authentication.k8s.io/v1beta1
authorization.k8s.io/v1
authorization.k8s.io/v1beta1
autoscaling/v1
autoscaling/v2beta1
autoscaling/v2beta2
batch/v1
batch/v1beta1
certificates.k8s.io/v1beta1
coordination.k8s.io/v1
coordination.k8s.io/v1beta1
events.k8s.io/v1beta1
extensions/v1beta1
networking.k8s.io/v1
networking.k8s.io/v1beta1
node.k8s.io/v1beta1
policy/v1beta1
rbac.authorization.k8s.io/v1
rbac.authorization.k8s.io/v1beta1
scheduling.k8s.io/v1
scheduling.k8s.io/v1beta1
storage.k8s.io/v1
storage.k8s.io/v1beta1
v1
```

# kind 资源类别

初始化资源对象时使用

如Pod，Replicas， deployment， StatefulSet

## Pod

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



## Secret

```
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  user: YWRtaW4=
  pass: MWYyZDFlMmU2N2Rm


```

# Metadata

API对象的“标识”，即元数据，也是从Kubernetes里找到这个对象的主要依据，对所有API对象来说，这一部分的格式和字段基本是一致的

其中最主要使用到的字段是Labels

嵌套字段

## name

在同一类别中，name必须是唯一的

实例化对象的名称

## namespace

实例化对象资源的名称空间

name是受限于namespace的

## labels (重要)

key-value 数据

key最多63个字符，只能使用字母，数字，下划线，横线

value 可以为空，只能字母或者数字开头及结尾

资源对象进行管理，可以添加多个标签

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

## annotation

资源注解

## ownerReference

用于保存当前这个API对象的拥有者(Owner) 的信息

每个资源引用的PATH

```
/api/GROUP/VERSION/namespaces/NAMESPACE/RESOURCE_TYPE/NAME
```

## annotations

不能用于挑选资源对象，仅用于对象提供元数据

key value不受限制

支持动态编辑

生成资源注解

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-nodeselector
  annotations:
    xuxuehua.com/created-by: "cluster admin"
spec: 
....
```





## initializers

Kubernetes 还允许你通过配置，来指定要对什么样的资源进行这个 Initialize 操作

```
apiVersion: admissionregistration.k8s.io/v1alpha1 
kind: InitializerConfiguration
metadata:
  name: envoy-config 
  initializers:
  // 这个名字必须至少包括两个 "."
    - name: envoy.initializer.kubernetes.io
      rules:
        - apiGroups:
          - "" // 前面说过， "" 就是 core API Group 的意思 
        apiVersions:
          - v1 
        resources:
          - pods
```

这个配置，就意味着 Kubernetes 要对所有的 Pod 进行这个 Initialize 操作，并且，我们指定了 负责这个操作的 Initializer，名叫:envoy-initializer。

一旦这个 InitializerConfiguration 被创建，Kubernetes 就会把这个 Initializer 的名字，加 在所有新创建的 Pod 的 Metadata 上

```
apiVersion: admissionregistration.k8s.io/v1alpha1 
kind: InitializerConfiguration
metadata:
  name: envoy-config 
  initializers:
    pending:
      - name: envoy.initializer.kubernetes.io
  name: myapp-pod 
  labels: 
    app: myapp
```

可以看到，每一个新创建的 Pod，都会自动携带了 metadata.initializers.pending 的 Metadata 信息。

这个 Metadata，正是接下来 Initializer 的控制器判断这个 Pod 有没有执行过自己所负责的初 始化操作的重要依据(也就是前面伪代码中 isInitialized() 方法的含义)。

这也就意味着，当你在 Initializer 里完成了要做的操作后，一定要记得将这个 metadata.initializers.pending 标志清除掉。这一点，你在编写 Initializer 代码的时候一定要非常注意。



此外，除了上面的配置方法，你还可以在具体的 Pod 的 Annotation 里添加一个如下所示的字 段，从而声明要使用某个 Initializer

```
apiVersion: v1
kind: Pod
metadata
  annotations: 
    "initializer.kubernetes.io/envoy": "true" 
    ...
```

在这个 Pod 里，我们添加了一个 Annotation，写明: initializer.kubernetes.io/envoy=true。这样，就会使用到我们前面所定义的 envoy-initializer 了

# Spec 对象独有定义

specification 规格

存放属于这个对象独有的定义，用来描述它要表达的功能

即用户来定义资源所期望的目标状态

## 仔细阅读

仔细阅读 $GOPATH/src/k8s.io/kubernetes/vendor/k8s.io/api/core/v1/types.go 里，type Pod struct ，尤其是 PodSpec 部分的内容。争取做到下次看到一个 Pod 的 YAML 文件时，不再需要查阅文档，就能做到把常用字段及其作用信手拈来。

## Kubectl explain spec.[Object]

返回为对象，可以一直向下嵌套



## initContainers

所有 Init Container 定义的容器，都会比 spec.containers 定义的用户容器先启动。并 且，Init Container 容器会按顺序逐一启动，而直到它们都启动并且退出了，用户容器才会启动。

可以解决在一个pod中，多个容器间的启动顺序

实际上，这个所谓的“组合”操作，正是容器设计模式里最常用的一种模式，它的名字叫: sidecar。

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



## containers 容器列表

内容为列表，开头需要添加 `-`

### name `<string>`

pod 内嵌的容器名称

### image `<string>`

pod 容器使用的镜像

若为自定义仓库，需要指明仓库路径以及名称

### imagePullPolicy `<string>`

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

### ports `<[]Object>`

暴露一个端口，仅仅是提供额外信息

显示指定容器端口，为其赋予一个名称方便调用

其值为一个对象列表，有一个到多个端口对象组成，且嵌套以下字段

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

### command `<[]string>`

指定不同于镜像默认运行的应用程序，可以同时使用args字段进行参数传递，将覆盖镜像中的默认定义

自定义args 将传递args内容作为参数，而镜像中的CMD参数将会被忽略

其内部的变量引用格式为`$(VAR_NAME)`, 逃逸方式为`$$(VAR_NAME)`

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

This table summarizes the field names used by Docker and Kubernetes.

| Description                         | Docker field name | Kubernetes field name |
|:----------------------------------- |:----------------- |:--------------------- |
| The command run by the container    | Entrypoint        | command               |
| The arguments passed to the command | Cmd               | args                  |

When you override the default Entrypoint and Cmd, these rules apply:

- If you do not supply `command` or `args` for a Container, the defaults defined in the Docker image are used.
- If you supply a `command` but no `args` for a Container, only the supplied `command` is used. The default EntryPoint and the default Cmd defined in the Docker image are ignored.
- If you supply only `args` for a Container, the default Entrypoint defined in the Docker image is run with the `args` that you supplied.
- If you supply a `command` and `args`, the default Entrypoint and the default Cmd defined in the Docker image are ignored. Your `command` is run with your `args`.

Here are some examples:

| Image Entrypoint | Image Cmd   | Container command | Container args | Command run      |
|:---------------- |:----------- |:----------------- |:-------------- |:---------------- |
| `[/ep-1]`        | `[foo bar]` | <not set>         | <not set>      | `[ep-1 foo bar]` |
| `[/ep-1]`        | `[foo bar]` | `[/ep-2]`         | <not set>      | `[ep-2]`         |
| `[/ep-1]`        | `[foo bar]` | <not set>         | `[zoo boo]`    | `[ep-1 zoo boo]` |
| `[/ep-1]`        | `[foo bar]` | `[/ep-2]`         | `[zoo boo]`    | `[ep-2 zoo boo]` |



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





#### postStart   ` <Object>` 

指在容器启动后，立刻执行一个指定的操作

若postStart执行超时或者错误，Kubernetes会在该Pod的Events中报出该容器启动失败的错误信息，导致Pod也处于失败的状态



##### exec `<Object>` 用户指定命令

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





##### httpGet      `<Object>`

Pod 其实可以暴露一个健康检查 URL(比如 /healthz)，或者直接让健康检查去检测 应用的监听端口

```

```





##### tcpSocket    `<Object>`

Pod 其实可以暴露一个健康检查 URL(比如 /healthz)，或者直接让健康检查去检测 应用的监听端口





#### preStop     `<Object>` 终止前

指容器被杀死之前，执行的操作

由于是同步的，会阻塞之前的容器杀死流程，直到这个Hook定义的操作完成之后，才允许容器被杀死

##### exec `<Object>` 用户指定命令

根据指令返回码判断







##### httpGet      `<Object>`

Pod 其实可以暴露一个健康检查 URL(比如 /healthz)，或者直接让健康检查去检测 应用的监听端口







##### tcpSocket    `<Object>`

Pod 其实可以暴露一个健康检查 URL(比如 /healthz)，或者直接让健康检查去检测 应用的监听端口







### livenessProbe

#### exec

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



#### httpGet

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



#### tcpSocket

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



### readinessProbe

虽然它的用法与 livenessProbe 类似，但作用却大不一样。readinessProbe 检查结果的成功与否，决定的这个 Pod 是不是能被通 过 Service 的方式访问到，而并不影响 Pod 的生命周期



## selector

### matchLabels （Label Selector）

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

## nodeSelector (Deprecated by nodeAffinity)

nodeSelector 其实已经是一个将要被废弃的字段了

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

## nodeName

一旦 Pod 的这个字段被赋值，Kubernetes 项目就会被认为这个 Pod 已经经过了调度，调度的结果就是赋值的节点名字。所以，这个字段一般由调度器负责设置，但用户也可以设置它来“骗过”调度器，当然这个做法一般是在测试或者调试的时候才会用到。

即直接运行在指定节点上



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

> requiredDuringSchedulingIgnoredDuringExecution    指这个nodeAffinity必须在每次调度的时候予以考虑，也表示可以在设置某些情况下不考虑这个nodeAffinity
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

## sessionAffinity

None表示不启用，随机请求后端Pod

ClientIP表示锁定一个后端请求,即访问同一个Pod资源

```
# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: v1
kind: Service
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"name":"myapp","namespace":"default"},"spec":{"clusterIP":"10.99.99.99","ports":[{"nodePort":30080,"port":80,"targetPort":80}],"selector":{"app":"myapp","release":"canary"},"type":"NodePort"}}
  creationTimestamp: "2019-04-07T05:54:38Z"
  name: myapp
  namespace: default
  resourceVersion: "739917"
  selfLink: /api/v1/namespaces/default/services/myapp
  uid: a1636d2f-58f9-11e9-8178-560001fa0d25
spec:
  clusterIP: 10.99.99.99
  externalTrafficPolicy: Cluster
  ports:
  - nodePort: 30080
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: myapp
    release: canary
  sessionAffinity: None
  type: NodePort
status:
  loadBalancer: {}
```



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



## hostNetwork/hostIPC/hostPID

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

> 这个 Pod 被创建后，可以使用 shell 容器的 tty 跟这个容器进行交互了

可以直接认为 tty 就是 Linux 给用户提供的一个常驻小程 序，用于接收用户的标准输入，返回操作系统的标准输出。当然，为了能够在 tty 中输入信息, 还需要同时开启 stdin(标准输入流)。

```
$ kubectl create -f nginx.yaml
$ kubectl attach -it nginx -c shell
/ # ps ef
PID   USER     TIME  COMMAND
    1 root      0:00 /pause
    6 root      0:00 nginx: master process nginx -g daemon off;
   12 101       0:00 nginx: worker process
   13 root      0:00 sh
   18 root      0:00 ps ef
```



## restartPolicy

Kubernetes 里的Pod 恢复机制，默认值是 Always，即:任何时候这个容器发生了异常，它 一定会被重新创建。

Pod 的恢复过程，永远都是发生在当前节点上，而不会跑到别的节点上去。事 实上，一旦一个 Pod 与一个节点(Node)绑定，除非这个绑定发生了变化(pod.spec.node 字段 被修改)，否则它永远都不会离开这个节点。



### 恢复策略

Always:在任何情况下，只要容器不在运行状态，就自动重启容器

OnFailure: 只在容器 异常时才自动重启容器

Never: 从来不重启容器



如果你要关心这个容器退出后的上下文环境，比如容器退出后的日志、文件和目录，就需要将 restartPolicy 设置为 Never。因为一旦容器被自动重新创建，这些内容就有可能丢失掉了(被垃圾 回收了)。



### 恢复原理

1. 只要 Pod 的 restartPolicy 指定的策略允许重启异常的容器(比如:Always)，那么这个 Pod 就会保持 Running 状态，并进行容器重启。否则，Pod 就会进入 Failed 状态 。
2. 对于包含多个容器的 Pod，只有它里面所有的容器都进入异常状态后，Pod 才会进入 Failed 状 态。在此之前，Pod 都是 Running 状态。此时，Pod 的 READY 字段会显示正常容器的个数

# status 当前状态 (read-only)

显示目标资源的当前状态

由kubernetes集群维护，用户不能自定义

即status状态尽最大向spec状态转移

## activeDeadlineSeconds

设置最长运行时间

```
spec:
 backoffLimit: 5
 activeDeadlineSeconds: 100
```

> 这是Job对象的例子

## parallelism

它定义的是一个 Job 在任意时间最多可以启动多少个 Pod 同时运行

## completions

它定义的是 Job 至少要完成的 Pod 数目，即 Job 的最小完成数

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



