---
title: "resource_management 资源管理"
date: 2019-02-23 23:05
---


[TOC]







# Pod控制器 (工作负载 Workload)

Pod 为此基础资源，负责运行容器，控制器负责Pod监控和管理

当Pod非正常中止，重建工作由此控制器完成



## ReplicationController 无状态 （废弃）

负责无状态应用，上一代应用控制器

保证每个容器或者容器组运行并且可以被访问



## ReplicaSet 无状态

负责无状态应用，新一代ReplicationController

比上一代支持基于集合的选择器

代用户创建对pod副本，并保证其运行数量的状态



### 结构

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



### 水平扩展

```
$ kubectl scale deployment nginx-deployment --replicas=4
deployment.apps/nginx-deployment scaled
```



### 滚动扩展

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



## Deployment  无状态 (常用)

负责无状态应用, 一个deployment 可以管理多个ReplicaSet

用于管理无状态持久化的应用，如HTTP

构建在ReplicaSet之上，通过控制ReplicaSet来控制副本

负责在Pod定义发生变化时，对每个副本进行跟懂更新

实现了Pod的水平扩展和收缩(horizontal scaling out/in)



Deployment 所管理的Pod，他的ownerReference 时ReplicaSet

相对而言，Deployment 只是在ReplicaSet 的基础上，添加了`UP-TO-DATE` 这个跟版本有关的字段



Deployment 实际上并不足以覆盖所有的应用编排问题。即所有的Pod都是一样的，相互之间没有顺序，也无宿主机要求。

但分布式应用的多个实例之间是相互有依赖关系的



```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
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



### HPA 水平Pod伸缩

Horizontal Pod Autoscaler









## StatefulSet 有状态

负责有状态应用

用于管理有状态的持久化应用，如database

要稳定且拥有唯一的网络标识符

而且需要有序，平滑的部署，扩展，滚动更新，删除，终止

运维操作极其复杂，需要将脚本写入到statefulset里面完成其对应的操作

比Deployment会为每个Pod创建一个独有的持久性标识符，并确保Pod之间的顺序性，即管理的是不同的Pod 实例，而不是ReplicaSet中完全一样的Pod

创建statefulset必须要先创建一个headless的service，分为两个步骤， 而且必须是Headless Service





### 设计

```
拓扑状态
应用的多个实例之间不是完全对等的关系。
这些应用实例，必须按照某些顺序启动，比如应用的主节点 A 要先于从节点 B 启动。而如果你把 A 和 B 两个 Pod 删除掉，它们再次被创建出来时也必须严格按照这个顺序才行。并且，新创建出来的 Pod，必须和原来 Pod 的网络标识一样，这样原先的访问者才能使用同样的方法，访问到这个新 Pod。
```



```
存储状态
应用的多个实例分别绑定了不同的存储数据。
对于这些应用实例来说，Pod A 第一次读取到的数据，和隔了十分钟之后再次读取到的数据，应该是同一份，哪怕在此期间 Pod A 被重新创建过。这种情况最典型的例子，就是一个数据库应用的多个存储实例。
```



### Headless Service

即一个标准Service YAML文件

```
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx
```

> 因为clusterIP是None，所以创建之后不会分配VIP地址。所以将采用DNS记录的方式暴露出所在的代理Pod



当按照上面的方式创建了一个 Headless Service 之后，它所代理的所有 Pod 的 IP 地址，都会被绑定一个这样格式的 DNS 记录

```
<pod-name>.<svc-name>.<namespace>.svc.cluster.local
```





statefuleSet.yaml

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
```

>  serviceName=nginx 字段, 就是告诉 StatefulSet 控制器，在执行控制循环（Control Loop）的时候，请使用 nginx 这个 Headless Service 来保证 Pod 的“可解析身份”。



```
$ kubectl create -f svc.yaml
$ kubectl get service nginx
NAME      TYPE         CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
nginx     ClusterIP    None         <none>        80/TCP    10s

$ kubectl create -f statefulset.yaml
$ kubectl get statefulset web
NAME      DESIRED   CURRENT   AGE
web       2         1         19s
```



```
$ kubectl get pods -w -l app=nginx
NAME      READY     STATUS    RESTARTS   AGE
web-0     0/1       Pending   0          0s
web-0     0/1       Pending   0         0s
web-0     0/1       ContainerCreating   0         0s
web-0     1/1       Running   0         19s
web-1     0/1       Pending   0         0s
web-1     0/1       Pending   0         0s
web-1     0/1       ContainerCreating   0         0s
web-1     1/1       Running   0         20s
```

> StatefulSet 给所有被管理的Pod编号，通过-分隔



可以查看到容器内部的hostname是不一样的

```
$ kubectl exec web-0 -- sh -c 'hostname'
web-0
$ kubectl exec web-1 -- sh -c 'hostname'
web-1
```



```
$ kubectl run -i --tty --image busybox dns-test --restart=Never --rm /bin/sh
$ nslookup web-0.nginx
Server:    10.0.0.10
Address 1: 10.0.0.10 kube-dns.kube-system.svc.cluster.local

Name:      web-0.nginx
Address 1: 10.244.1.7

$ nslookup web-1.nginx
Server:    10.0.0.10
Address 1: 10.0.0.10 kube-dns.kube-system.svc.cluster.local

Name:      web-1.nginx
Address 1: 10.244.2.7
```

> 从 nslookup 命令的输出结果中，我们可以看到，在访问 web-0.nginx 的时候，最后解析到的，正是 web-0 这个 Pod 的 IP 地址；而当访问 web-1.nginx 的时候，解析到的则是 web-1 的 IP 地址。



```
$ kubectl delete pod -l app=nginx
pod "web-0" deleted
pod "web-1" deleted

$ kubectl get pod -w -l app=nginx
NAME      READY     STATUS              RESTARTS   AGE
web-0     0/1       ContainerCreating   0          0s
NAME      READY     STATUS    RESTARTS   AGE
web-0     1/1       Running   0          2s
web-1     0/1       Pending   0         0s
web-1     0/1       ContainerCreating   0         0s
web-1     1/1       Running   0         32s
```

> 可以看到，当把这两个 Pod 删除之后，Kubernetes 会按照原先编号的顺序，创建出了两个新的 Pod。并且，Kubernetes 依然为它们分配了与原来相同的“网络身份”：web-0.nginx 和 web-1.nginx。



尽管 web-0.nginx 这条记录本身不会变，但它解析到的 Pod 的 IP 地址，并不是固定的。这就意味着，对于“有状态应用”实例的访问，必须使用 DNS 记录或者 hostname 的方式，而绝不应该直接访问这些 Pod 的 IP 地址。





### volumeClaimTemplate

不同过pod模版生产，即生成每一个Pod时，会对每一个pod自动创建volume， 而且对每一个volume生成对应的PVC，从而绑定预设好的PV



#### example

生成PV

```
[root@master volumes]# cat pv-demo.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv001
  labels:
    name: pv001
spec:
  nfs:
    path: /data/volumes/v1
    server: 202.182.104.162
  accessModes: ["ReadWriteMany", "ReadWriteOnce"]
  capacity:
    storage: 5Gi
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv002
  labels:
    name: pv002
spec:
  nfs:
    path: /data/volumes/v2
    server: 202.182.104.162
  accessModes: ["ReadWriteOnce"]
  capacity:
    storage: 5Gi
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv003
  labels:
    name: pv003
spec:
  nfs:
    path: /data/volumes/v3
    server: 202.182.104.162
  accessModes: ["ReadWriteMany", "ReadWriteOnce"]
  capacity:
    storage: 5Gi
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv004
  labels:
    name: pv004
spec:
  nfs:
    path: /data/volumes/v4
    server: 202.182.104.162
  accessModes: ["ReadWriteMany", "ReadWriteOnce"]
  capacity:
    storage: 5Gi
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv005
  labels:
    name: pv005
spec:
  nfs:
    path: /data/volumes/v5
    server: 202.182.104.162
  accessModes: ["ReadWriteMany", "ReadWriteOnce"]
  capacity:
    storage: 5Gi
---

[root@master volumes]# kubectl  get pv
NAME    CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
pv001   5Gi        RWO,RWX        Retain           Available                                   11m
pv002   5Gi        RWO            Retain           Available                                   11m
pv003   5Gi        RWO,RWX        Retain           Available                                   11m
pv004   5Gi        RWO,RWX        Retain           Available                                   11m
pv005   5Gi        RWO,RWX        Retain           Available                                   11m
```





生成StatefulSet

```
[root@master manifests]# cat stateful-demo.yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp
  labels:
    app: myapp
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: myapp-pod
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: myapp
spec:
  serviceName: myapp
  replicas: 3
  selector:
    matchLabels:
      app: myapp-pod
  template:
    metadata:
      labels:
        app: myapp-pod
    spec:
      containers:
      - name: myapp
        image: ikubernetes/myapp:v1
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: myappdata
          mountPath: /usr/share/nginx/html/
  volumeClaimTemplates:
  - metadata:
      name: myappdata
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 5Gi                   
```

> 要在每个worker node提前挂载nfs目录




```
[root@master manifests]# kubectl  apply -f stateful-demo.yaml
service/myapp unchanged
statefulset.apps/myapp created

[root@master ~]# kubectl  get pods
NAME          READY   STATUS    RESTARTS   AGE
myapp-0       1/1     Running   0          59m
myapp-1       1/1     Running   0          58m
myapp-2       1/1     Running   0          52m
pod-vol-nfs   1/1     Running   0          5d23h
[root@master ~]# kubectl  get pods
NAME          READY   STATUS    RESTARTS   AGE
myapp-0       1/1     Running   0          59m
myapp-1       1/1     Running   0          59m
myapp-2       1/1     Running   0          52m
pod-vol-nfs   1/1     Running   0          5d23h
[root@master ~]# kubectl  get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   7d20h
myapp        ClusterIP   None         <none>        80/TCP    59m
[root@master ~]# kubectl  get sts
NAME    READY   AGE
myapp   3/3     59m

[root@master ~]# kubectl  get pv
NAME    CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                       STORAGECLASS   REASON   AGE
pv001   5Gi        RWO,RWX        Retain           Bound       default/myappdata-myapp-1                           59m
pv002   5Gi        RWO            Retain           Bound       default/myappdata-myapp-0                           59m
pv003   5Gi        RWO,RWX        Retain           Bound       default/myappdata-myapp-2                           59m
pv004   5Gi        RWO,RWX        Retain           Available                                                       59m
pv005   5Gi        RWO,RWX        Retain           Available                                                       59m


[root@master ~]# kubectl  get pvc
NAME                STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
myappdata-myapp-0   Bound    pv002    5Gi        RWO                           59m
myappdata-myapp-1   Bound    pv001    5Gi        RWO,RWX                       59m
myappdata-myapp-2   Bound    pv003    5Gi        RWO,RWX                       53m
```







## DaemonSet 守护进程 无状态

这个Pod会运行在Kubernetes集群里面的有限节点(Node)上面， 而且只会有一个这样的pod 实例



常用于运行集群存储的守护进程，如glusterd，ceph，

日志收集进程如fluentd，logstash，

监控进程，如prometheus的Node Explorter，collected，datadog agent， Ganglia的gmond

当有新的节点加入到Kubernetes集群中，该Pod会自动在新节点上创建完成，旧节点删除后，Pod也会被回收

相对而言，DaemonSet开始运行的时间，会比整个Kubernetes集群要早

DaemonSet没有replicas字段

创建每个Pod的时候，DaemonSet会自动给这个Pod加上一个nodeAffinity，从而保证Pod只会在这个节点上启动，同时还会自动加上一个Toleration，从而忽略节点的unschedulable污点



```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-elasticsearch
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd-elasticsearch
  template:
    metadata:
      labels:
        name: fluentd-elasticsearch
    spec:
      tolerations:
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: fluentd-elasticsearch
        image: k8s.gcr.io/fluentd-elasticsearch:1.20
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
```

> 管理的是一个fluentd-elasticsearch镜像的Pod，即通过fluentd将Docker容器里面的日志转发到ElasticSearch中
>
> 两个hostPath分别对应/var/log目录和/var/lib/docker/containers目录





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
       image: ikubernetes/filebeat:5.6.5-alpine
       env:
       - name: REDIS_HOST
         value: redis.default.svc.cluster.local
       - name: REDIS_LOG_LEVEL
         value: info
```





### 滚动更新方式

```
root@master ~]# kubectl set image daemonsets filebeat-ds filebeat=ikubernetes/filebeat:5.6.6-alpine
daemonset.extensions/filebeat-ds image updated
[root@master ~]# kubectl  get pods -w
NAME                               READY   STATUS              RESTARTS   AGE
client                             1/1     Running             0          3d10h
filebeat-ds-9tgw8                  1/1     Running             1          11m
filebeat-ds-x8zpd                  0/1     ContainerCreating   0          3s
liveness-exec-pod                  0/1     CrashLoopBackOff    806        2d1h
liveness-httpget-pod               1/1     Running             1          47h
myapp-deployment-67b6dfcd8-59wxt   1/1     Running             0          50m
myapp-deployment-67b6dfcd8-g2cnc   1/1     Running             0          50m
myapp-deployment-67b6dfcd8-jjfml   1/1     Running             0          50m
myapp-deployment-67b6dfcd8-rc7t8   1/1     Running             0          50m
myapp-deployment-67b6dfcd8-s6g9m   1/1     Running             0          50m
poststart-pod                      0/1     CrashLoopBackOff    319        26h
readiness-httpget-pod              1/1     Running             0          46h
redis-58b9f5776-bdp8d              1/1     Running             0          11m
filebeat-ds-x8zpd                  1/1     Running             0          6s
filebeat-ds-9tgw8                  1/1     Terminating         1          11m
filebeat-ds-9tgw8                  0/1     Terminating         1          11m
filebeat-ds-9tgw8                  0/1     Terminating         1          11m
filebeat-ds-9tgw8                  0/1     Terminating         1          11m
filebeat-ds-k926z                  0/1     Pending             0          0s
filebeat-ds-k926z                  0/1     Pending             0          0s
filebeat-ds-k926z                  0/1     ContainerCreating   0          0s
filebeat-ds-k926z                  1/1     Running             0          5s
```



### Master 节点 toleration 

添加Toleration，在Master节点上部署Pod

默认Kubernetes集群不允许用户在Master节点上部署Pod，因为Master节点携带了一个`node-role.kubernetes.io/master` 污点，所以要容忍这个污点

```
tolerations:
- key: node-role.kubernetes.io/master
  effect: NoSchedule
```





## Ingress Controller (七层调度)

一种特殊的Pod，直接监听在宿主机的网络上接入外部请求

独立运行的一组Pod资源，通常为应用程序，即拥有七层调度的代理能力



![img](https://snag.gy/HhNRni.jpg)

> 这里通过一个service，帮助来对Pod进行分组，本身并不接受外部请求，然后通过ingress资源，实时反应Pod节点的状态，将其信息动态注入到ingress controller，并生成对应的配置文件，哪些pod可以被使用



通常有3种选择，Nginx，Traefik， Envoy(微服务)







### example



对应的repo

https://github.com/kubernetes/ingress-nginx.git



创建名称空间

```
[root@master ~]# kubectl  create namespace ingress-nginx
namespace/ingress-nginx created
```



激活所有yaml

cat namespace.yaml

```
---

apiVersion: v1
kind: Namespace
metadata:
  name: ingress-nginx
```



```

[root@master deploy]# kubectl apply -f namespace.yaml
Warning: kubectl apply should be used on resource created by either kubectl create --save-config or kubectl apply
namespace/ingress-nginx configured
[root@master deploy]# kubectl apply -f ./
configmap/nginx-configuration created
configmap/tcp-services created
configmap/udp-services created
namespace/ingress-nginx unchanged
configmap/nginx-configuration unchanged
configmap/tcp-services unchanged
configmap/udp-services unchanged
serviceaccount/nginx-ingress-serviceaccount created
clusterrole.rbac.authorization.k8s.io/nginx-ingress-clusterrole created
role.rbac.authorization.k8s.io/nginx-ingress-role created
rolebinding.rbac.authorization.k8s.io/nginx-ingress-role-nisa-binding created
clusterrolebinding.rbac.authorization.k8s.io/nginx-ingress-clusterrole-nisa-binding created
deployment.apps/nginx-ingress-controller created
namespace/ingress-nginx unchanged
serviceaccount/nginx-ingress-serviceaccount unchanged
clusterrole.rbac.authorization.k8s.io/nginx-ingress-clusterrole unchanged
role.rbac.authorization.k8s.io/nginx-ingress-role unchanged
rolebinding.rbac.authorization.k8s.io/nginx-ingress-role-nisa-binding unchanged
clusterrolebinding.rbac.authorization.k8s.io/nginx-ingress-clusterrole-nisa-binding unchanged
deployment.apps/nginx-ingress-controller unchanged
```



激活service和pod

```
[root@master ingress]# cat deploy-demo.yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp
  namespace: default
spec:
  selector:
    app: myapp
    release: canary
  ports:
  - name: http
    targetPort: 80
    port: 80
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deploy
  namespace: default
spec:
  replicas: 3
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
       image: ikubernetes/myapp:v2
       ports:
       - name: http
         containerPort: 80
[root@master ingress]# kubectl  apply -f deploy-demo.yaml
deployment.apps/myapp-deploy created

[root@master ingress]# kubectl get pods
NAME                                READY   STATUS             RESTARTS   AGE
client                              1/1     Running            0          5d12h
filebeat-ds-8kc4g                   1/1     Running            0          5h19m
filebeat-ds-8vwkt                   1/1     Running            0          5h19m
liveness-exec-pod                   0/1     CrashLoopBackOff   1630       4d4h
liveness-httpget-pod                1/1     Running            1          4d1h
myapp-deploy-675558bfc5-6mtj8       1/1     Running            0          78s
myapp-deploy-675558bfc5-9c52x       1/1     Running            0          78s
myapp-deploy-675558bfc5-n2949       1/1     Running            0          78s
myapp-deployment-675558bfc5-q8gxv   1/1     Running            0          5h19m
myapp-deployment-675558bfc5-tdfk7   1/1     Running            0          5h19m
myapp-deployment-675558bfc5-zhn8b   1/1     Running            0          5h19m
poststart-pod                       0/1     CrashLoopBackOff   915        3d5h
readiness-httpget-pod               1/1     Running            0          4d1h
redis-58b9f5776-bdp8d               1/1     Running            0          2d3h
```





修改ingress controller，使其能够接入集群外部的流量

```
[root@master ingress-nginx]# vim deploy/provider/baremetal/service-nodeport.yaml

apiVersion: v1
kind: Service
metadata:
  name: ingress-nginx
  namespace: ingress-nginx
  labels:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
spec:
  type: NodePort
  ports:
    - name: http
      port: 80
      targetPort: 80
      protocol: TCP
      nodePort: 30080
    - name: https
      port: 443
      targetPort: 443
      protocol: TCP
      nodePort: 30443
  selector:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
```

> 添加特殊的节点端口30080，30443



```
[root@master deploy]# kubectl apply -f provider/baremetal/service-nodeport.yaml
service/ingress-nginx created
[root@master deploy]# kubectl  get svc -n ingress-nginx
NAME            TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx   NodePort   10.98.203.113   <none>        80:30080/TCP,443:30443/TCP   10s
```





将myapp的代码通过ingress发布出去， 使用虚拟主机来实现

```
[root@master ingress]# cat ingress-myapp.yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-myapp
  namespace: default
  annotations:
    kubenetes.io/ingress.class: "nginx"
spec:
  rules:
  - host: myapp.xurick.com
    http:
      paths:
      - path:
        backend:
          serviceName: myapp
          servicePort: 80
```



查看部署结果

```
[root@master ingress]# kubectl apply -f ingress-myapp.yaml
ingress.extensions/ingress-myapp created
[root@master ingress]# kubectl get ingress
NAME            HOSTS              ADDRESS   PORTS   AGE
ingress-myapp   myapp.xurick.com             80      6s
[root@master ingress]# kubectl  describe ingress ingress-myapp
Name:             ingress-myapp
Namespace:        default
Address:
Default backend:  default-http-backend:80 (<none>)
Rules:
  Host              Path  Backends
  ----              ----  --------
  myapp.xurick.com
                       myapp:80 (10.244.1.40:80,10.244.1.41:80,10.244.1.43:80 + 3 more...)
Annotations:
  kubenetes.io/ingress.class:                        nginx
  kubectl.kubernetes.io/last-applied-configuration:  {"apiVersion":"extensions/v1beta1","kind":"Ingress","metadata":{"annotations":{"kubenetes.io/ingress.class":"nginx"},"name":"ingress-myapp","namespace":"default"},"spec":{"rules":[{"host":"myapp.xurick.com","http":{"paths":[{"backend":{"serviceName":"myapp","servicePort":80},"path":null}]}}]}}

Events:
  Type    Reason  Age   From                      Message
  ----    ------  ----  ----                      -------
  Normal  CREATE  46s   nginx-ingress-controller  Ingress default/ingress-myapp
```



进入容器可以查看到nginx的配置

```
[root@master ingress]# kubectl  get pods -n ingress-nginx
NAME                                        READY   STATUS    RESTARTS   AGE
nginx-ingress-controller-66f94c89cb-thqfv   1/1     Running   0          25h
[root@master ingress]# kubectl  exec -n ingress-nginx -it nginx-ingress-controller-66f94c89cb-thqfv -- /bin/sh
$ cat nginx.conf
...

## start server myapp.xurick.com
        server {
                server_name myapp.xurick.com ;

                listen 80;

                set $proxy_upstream_name "-";

                location / {

                        set $namespace      "default";
                        set $ingress_name   "ingress-myapp";
                        set $service_name   "myapp";
                        set $service_port   "80";
                        set $location_path  "/";

                        rewrite_by_lua_block {
                                balancer.rewrite()
                        }

                        header_filter_by_lua_block {

                        }
                        body_filter_by_lua_block {
                        
                 
```










## Job 完成后终止

只能执行一次性的作业

用来描述离线业务的API对象



restartPolicy 在 Job 对象里只允许被设置为 Never 和 OnFailure

Job对象并不要一定要定一个spec.selector 来描述控制哪些Pod

```
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: resouer/ubuntu-bc 
        command: ["sh", "-c", "echo 'scale=10000; 4*a(1)' | bc -l "]
      restartPolicy: Never
  backoffLimit: 4
```

> 4*a(1) 结果为pi 
>
> restartPolicy: Never  离线计算的Pod不应该被重启



```
$ kubectl create -f job.yaml

$ kubectl describe jobs/pi
Name:             pi
Namespace:        default
Selector:         controller-uid=c2db599a-2c9d-11e6-b324-0209dc45a495
Labels:           controller-uid=c2db599a-2c9d-11e6-b324-0209dc45a495
                  job-name=pi
Annotations:      <none>
Parallelism:      1
Completions:      1
..
Pods Statuses:    0 Running / 1 Succeeded / 0 Failed
Pod Template:
  Labels:       controller-uid=c2db599a-2c9d-11e6-b324-0209dc45a495
                job-name=pi
  Containers:
   ...
  Volumes:              <none>
Events:
  FirstSeen    LastSeen    Count    From            SubobjectPath    Type        Reason            Message
  ---------    --------    -----    ----            -------------    --------    ------            -------
  1m           1m          1        {job-controller }                Normal      SuccessfulCreate  Created pod: pi-rq5rl
```

> 创建成功之后，被自动加载了新的label，controller-uid=< 一个随机字符串 >， 这样保证了Job与其他Pod之间的匹配关系





查看Pod状态

```
$ kubectl get pods
NAME                                READY     STATUS    RESTARTS   AGE
pi-rq5rl                            1/1       Running   0          10s
```



稍后就completed了

```
$ kubectl get pods
NAME                                READY     STATUS      RESTARTS   AGE
pi-rq5rl                            0/1       Completed   0          4m
```



可以查看到结果

```
$ kubectl logs pi-rq5rl
3.141592653589793238462643383279...
```





### 并行控制

spec.parallelism，它定义的是一个 Job 在任意时间最多可以启动多少个 Pod 同时运行

spec.completions，它定义的是 Job 至少要完成的 Pod 数目，即 Job 的最小完成数





根据 Job 控制器的工作原理，如果你定义的 parallelism 比 completions 还大的话，

```
 parallelism: 4
 completions: 2
```

需要创建的 Pod 数目 = 最终需要的 Pod 数目 - 实际在 Running 状态 Pod 数目 - 已经成功退出的 Pod 数目 = 2 - 0 - 0= 2。而parallelism数量为4，2小于4，所以应该会创建2个。





### 使用方法



#### 外部管理器 +Job 模板 

```
apiVersion: batch/v1
kind: Job
metadata:
  name: process-item-$ITEM
  labels:
    jobgroup: jobexample
spec:
  template:
    metadata:
      name: jobexample
      labels:
        jobgroup: jobexample
    spec:
      containers:
      - name: c
        image: busybox
        command: ["sh", "-c", "echo Processing item $ITEM && sleep 5"]
      restartPolicy: Never
```

> $ITEM 用于创建Job时，替换此变量的值
>
> 所有来自于同一个模板的 Job，都有一个 jobgroup: jobexample 标签，也就是说这一组 Job 使用这样一个相同的标识。



#### 替换$ITEM操作

```
$ mkdir ./jobs
$ for i in apple banana cherry
do
  cat job-tmpl.yaml | sed "s/\$ITEM/$i/" > ./jobs/job-$i.yaml
done
```

```
$ kubectl create -f ./jobs
$ kubectl get pods -l jobgroup=jobexample
NAME                        READY     STATUS      RESTARTS   AGE
process-item-apple-kixwv    0/1       Completed   0          4m
process-item-banana-wrsf7   0/1       Completed   0          4m
process-item-cherry-dnfu9   0/1       Completed   0          4m
```



#### 固定数目并行

```
apiVersion: batch/v1
kind: Job
metadata:
  name: job-wq-1
spec:
  completions: 8
  parallelism: 2
  template:
    metadata:
      name: job-wq-1
    spec:
      containers:
      - name: c
        image: myrepo/job-wq-1
        env:
        - name: BROKER_URL
          value: amqp://guest:guest@rabbitmq-service:5672
        - name: QUEUE
          value: job1
      restartPolicy: OnFailure
```

> completions 的值是：8, 即总共要处理的任务数目是 8 个



在这个实例中，选择充当工作队列的是一个运行在 Kubernetes 里的 RabbitMQ。所以，需要在 Pod 模板里定义 BROKER_URL，来作为消费者。

所以，一旦用 kubectl create 创建了这个 Job，它就会以并发度为 2 的方式，每两个 Pod 一组，创建出 8 个 Pod。每个 Pod 都会去连接 BROKER_URL，从 RabbitMQ 里读取任务，然后各自进行处理。这个 Pod 里的执行逻辑，可以用这样一段伪代码来表示：

```
/* job-wq-1 的伪代码 */
queue := newQueue($BROKER_URL, $QUEUE)
task := queue.Pop()
process(task)
exit
```

可以看到，每个 Pod 只需要将任务信息读取出来，处理完成，然后退出即可。而作为用户，我只关心最终一共有 8 个计算任务启动并且退出，只要这个目标达到，我就认为整个 Job 处理完成了。所以说，这种用法，对应的就是“任务总数固定”的场景。



#### 指定并行度（parallelism）

但不设置固定的 completions 的值 



此时，必须自己想办法，来决定什么时候启动新 Pod，什么时候 Job 才算执行完成。在这种情况下，任务的总数是未知的，所以不仅需要一个工作队列来负责任务分发，还需要能够判断工作队列已经为空（即：所有的工作已经结束了）

```
apiVersion: batch/v1
kind: Job
metadata:
  name: job-wq-2
spec:
  parallelism: 2
  template:
    metadata:
      name: job-wq-2
    spec:
      containers:
      - name: c
        image: gcr.io/myproject/job-wq-2
        env:
        - name: BROKER_URL
          value: amqp://guest:guest@rabbitmq-service:5672
        - name: QUEUE
          value: job2
      restartPolicy: OnFailure
```



类似伪代码体现

```
/* job-wq-2 的伪代码 */
for !queue.IsEmpty($BROKER_URL, $QUEUE) {
  task := queue.Pop()
  process(task)
}
print("Queue empty, exiting")
exit
```

由于任务数目的总数不固定，所以每一个 Pod 必须能够知道，自己什么时候可以退出。比如，在这个例子中，简单地以“队列为空”，作为任务全部完成的标志。所以说，这种用法，对应的是“任务总数不固定”的场景。





## CronJob 

定时任务，周期性运行

还要处理的问题，如前一个任务未完成，下一个任务时间点已到，将要触发

CronJob是一个Job对象的控制器Controller

CronJob 是一个专门用来管理 Job 对象的控制器。它创建和删除 Job 的依据，是 schedule 字段定义的、一个标准的Unix Cron格式的表达式



```
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            args:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```



这个 CronJob 对象在创建 1 分钟后，就会有一个 Job 产生了

```
$ kubectl create -f ./cronjob.yaml
cronjob "hello" created

# 一分钟后
$ kubectl get jobs
NAME               DESIRED   SUCCESSFUL   AGE
hello-4111706356   1         1         2s
```



```
$ kubectl get cronjob hello
NAME      SCHEDULE      SUSPEND   ACTIVE    LAST-SCHEDULE
hello     */1 * * * *   False     0         Thu, 6 Sep 2018 14:34:00 -070
```





需要注意的是，由于定时任务的特殊性，很可能某个 Job 还没有执行完，另外一个新 Job 就产生了。这时候，你可以通过 spec.concurrencyPolicy 字段来定义具体的处理策略。

```
concurrencyPolicy=Allow，这也是默认情况，这意味着这些 Job 可以同时存在；
concurrencyPolicy=Forbid，这意味着不会创建新的 Pod，该创建周期被跳过；
concurrencyPolicy=Replace，这意味着新产生的 Job 会替换旧的、没有执行完的 Job。
```



而如果某一次 Job 创建失败，这次创建就会被标记为“miss”。当在指定的时间窗口内，miss 的数目达到 100 时，那么 CronJob 会停止再创建这个 Job。











# 集群级资源 Cluster

## Namespace 

资源对象名称的作用范围，默认隶属default

即管理空间



## Node

Kubernetes集群工作节点，其标识符在当前集群唯一



## Role 

名称空间级别有规则组成的权限集合

被RoleBinding 引用



## ClusterRole

Cluster 级别的 

规则组成的权限集合

被RoleBinding，ClusterRole Binding 引用



## RoleBinding

将Role权限绑定在一个或一组用户上，

可以引用同一名称空间的Role，或全局名称的ClusterRole



## ClusterRoleBinding

将ClusterRole中定义的许可权限绑定在一个或一组用户上，引用ClusterRole





# 元数据资源 Metadata

用于为集群内部的其他资源配置其行为或特征，如HorizontalPodAutoscaler用于自动伸缩工作负载类型的资源对象的规模



## HPA

自动调整元数据的相关信息



## PodTemplate

用于让控制器创建pod的模板



## LimitRange

定义资源限制



### 



# API 群组



## 核心群组 core group

REST 路径为`/api/v1`，在资源配置信息apiVersion 字段中引用时可以不指定路径，而仅给出版本

如 `apiVersion:v1`



## 命名群组 named group

REST 路径为`/apis/$GROUP_NAME/$VERSION` 

如 `/apis/apps/v1`  在apiVersion字段中引用的格式为`apiVersion:$GROUP_NAME/$VERSION` 

如`apiVersion:apps/v1`







# 用户管理



## ServiceAccount

Kubernetes 负责管理的内置用户

如果一个 Pod 没有声明 serviceAccountName，Kubernetes 会自动在它的 Namespace 下创建一个名叫 default 的默认 ServiceAccount，然后分配给这个 Pod。



生产环境，建议所有Namespace下默认ServiceAccount 绑定只读权限的Role







```
apiVersion: v1
kind: ServiceAccount
metadata:
  namespace: mynamespace
  name: example-sa
```





然后，我们通过编写 RoleBinding 的 YAML 文件，来为这个 ServiceAccount 分配权限：

```
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: example-rolebinding
  namespace: mynamespace
subjects:
- kind: ServiceAccount
  name: example-sa
  namespace: mynamespace
roleRef:
  kind: Role
  name: example-role
  apiGroup: rbac.authorization.k8s.io
```

>  RoleBinding 对象里，subjects 字段的类型（kind），不再是一个 User，而是一个名叫 example-sa 的 ServiceAccount。而 roleRef 引用的 Role 对象，依然名叫 example-role，也就是我在这篇文章一开始定义的 Role 对象。





```
$ kubectl create -f svc-account.yaml
$ kubectl create -f role-binding.yaml
$ kubectl create -f role.yaml

$ kubectl get sa -n mynamespace -o yaml
- apiVersion: v1
  kind: ServiceAccount
  metadata:
    creationTimestamp: 2018-09-08T12:59:17Z
    name: example-sa
    namespace: mynamespace
    resourceVersion: "409327"
    ...
  secrets:
  - name: example-sa-token-vmfg6
```



声明使用ServiceAccount

```
apiVersion: v1
kind: Pod
metadata:
  namespace: mynamespace
  name: sa-token-test
spec:
  containers:
  - name: nginx
    image: nginx:1.7.9
  serviceAccountName: example-sa
```



```
$ kubectl describe pod sa-token-test -n mynamespace
Name:               sa-token-test
Namespace:          mynamespace
...
Containers:
  nginx:
    ...
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from example-sa-token-vmfg6 (ro)
```



查看到目录的文件

```
$ kubectl exec -it sa-token-test -n mynamespace -- /bin/bash
root@sa-token-test:/# ls /var/run/secrets/kubernetes.io/serviceaccount
ca.crt namespace  token
```

> 容器里的应用，就可以使用这个 ca.crt 来访问 APIServer 了。更重要的是，此时它只能够做 GET、WATCH 和 LIST 操作。因为 example-sa 这个 ServiceAccount 的权限，已经被我们绑定了 Role 做了限制。







但在这种情况下，这个默认 ServiceAccount 并没有关联任何 Role。也就是说，此时它有访问 APIServer 的绝大多数权限。当然，这个访问所需要的 Token，还是默认 ServiceAccount 对应的 Secret 对象为它提供的，如下所示。

```
$kubectl describe sa default
Name:                default
Namespace:           default
Labels:              <none>
Annotations:         <none>
Image pull secrets:  <none>
Mountable secrets:   default-token-s8rbq
Tokens:              default-token-s8rbq
Events:              <none>

$ kubectl get secret
NAME                  TYPE                                  DATA      AGE
kubernetes.io/service-account-token   3         82d

$ kubectl describe secret default-token-s8rbq
Name:         default-token-s8rbq
Namespace:    default
Labels:       <none>
Annotations:  kubernetes.io/service-account.name=default
              kubernetes.io/service-account.uid=ffcb12b2-917f-11e8-abde-42010aa80002

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1025 bytes
namespace:  7 bytes
token:      <TOKEN 数据 >
```



## Group

一个ServiceAccount在Kubernetes对应的用户为

```
system:serviceaccount:<ServiceAccount 名字 >
```



对应的用户组名

```
system:serviceaccounts:<Namespace 名字 >
```



比如，现在我们可以在 RoleBinding 里定义如下的 subjects：

```
subjects:
- kind: Group
  name: system:serviceaccounts:mynamespace
  apiGroup: rbac.authorization.k8s.io
```

> 这就意味着这个 Role 的权限规则，作用于 mynamespace 里的所有 ServiceAccount。这就用到了“用户组”的概念。









```
subjects:
- kind: Group
  name: system:serviceaccounts
  apiGroup: rbac.authorization.k8s.io
```

> 就意味着这个 Role 的权限规则，作用于整个系统里的所有 ServiceAccount。