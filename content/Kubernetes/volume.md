---
title: "volume"
date: 2019-03-14 16:12
---


[TOC]

# Volume



## Projected Volume

特殊的volume

为容器提供预先定义好的数据，即volume里面的信息仿佛是被Kubernetes 投射进入容器当中



### Secret

帮助Pod访问加密的数据，存放到Etcd中，然后可以通过Pod的容器里面挂载Volume的方式，访问懂啊这些Secret里面的保存的信息



#### 存放Credential

存放数据库的 Credential 信息场景

```
apiVersion: v1
kind: Pod
metadata:
  name: test-projected-volume 
spec:
  containers:
  - name: test-secret-volume
    image: busybox
    args:
    - sleep
    - "86400"
    volumeMounts:
    - name: mysql-cred
      mountPath: "/projected-volume"
      readOnly: true
  volumes:
  - name: mysql-cred
    projected:
      sources:
      - secret:
          name: user
      - secret:
          name: pass
```

> 这里的Volume类型为projected， 而source是user和pass



用到的数据库的用户名、密码，正是以 Secret 对象的方式交给 Kubernetes 保存的。完成这个操作的指令

```
$ cat ./username.txt
admin
$ cat ./password.txt
c1oudc0w!

$ kubectl create secret generic user --from-file=./username.txt
$ kubectl create secret generic pass --from-file=./password.txt
```



#### 查看Credential

```
$ kubectl get secrets
NAME           TYPE                                DATA      AGE
user          Opaque                                1         51s
pass          Opaque                                1         51s
```



#### 创建Secret by yaml

Secret对象要求数据必须经过Base64转码操作

```
$ echo -n 'admin' | base64
YWRtaW4=
$ echo -n '1f2d1e2e67df' | base64
MWYyZDFlMmU2N2Rm
```

> 像这样创建的 Secret 对象，它里面的内容仅仅是经过了转码，而并没有被加 密。在真正的生产环境中，你需要在 Kubernetes 中开启 Secret 的加密插件，增强数据的安全性。



创建这个yaml 文件

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



```
$ kubectl create -f test-projected-volume.yaml
$ kubectl exec -it test-projected-volume -- /bin/sh
$ ls /projected-volume/
user
pass
$ cat /projected-volume/user
root
$ cat /projected-volume/pass
1f2d1e2e67df

```



更重要的是，像这样通过挂载方式进入到容器里的 Secret，一旦其对应的 Etcd 里的数据被更新，这些 Volume 里的文件内容，同样也会被更新。这是 kubelet 组件在定时维护这些 Volume。

需要注意的是，这个更新可能会有一定的延时。所以在编写应用程序时，在发起数据库连接的代码处写好重试和超时的逻辑，绝对是个好习惯。













### ConfigMap

与 Secret 类似, 但保存的是不需要加密的信息，如配置信息



```
# .properties 文件的内容
$ cat example/ui.properties
color.good=purple
color.bad=yellow
allow.textmode=true
how.nice.to.look=fairlyNice

# 从.properties 文件创建 ConfigMap
$ kubectl create configmap ui-config --from-file=example/ui.properties

# 查看这个 ConfigMap 里保存的信息 (data)
$ kubectl get configmaps ui-config -o yaml
apiVersion: v1
data:
  ui.properties: |
    color.good=purple
    color.bad=yellow
    allow.textmode=true
    how.nice.to.look=fairlyNice
kind: ConfigMap
metadata:
  name: ui-config
  ...
```

> kubectl get -o yaml 这样的参数，会将指定Pod API对象以YAML的方式显示出来





### Downward API

让Pod里面的容器能够直接获取到这个Pod API对象本身

而这些信息，一定是Pod里面的容器进程启动之前就能确定下来的信息

若要获取Pod运行后的信息，如进程PID，需要定义sidecar容器



```
apiVersion: v1
kind: Pod
metadata:
  name: test-downwardapi-volume
  labels:
    zone: us-est-coast
    cluster: test-cluster1
    rack: rack-22
spec:
  containers:
    - name: client-container
      image: k8s.gcr.io/busybox
      command: ["sh", "-c"]
      args:
      - while true; do
          if [[ -e /etc/podinfo/labels ]]; then
            echo -en '\n\n'; cat /etc/podinfo/labels; fi;
          sleep 5;
        done;
      volumeMounts:
        - name: podinfo
          mountPath: /etc/podinfo
          readOnly: false
  volumes:
    - name: podinfo
      projected:
        sources:
        - downwardAPI:
            items:
              - path: "labels"
                fieldRef:
                  fieldPath: metadata.labels
```

> 声明要暴露Pod的metadata.labels信息给容器
>
> 这样当前Pod的labels字段值，就会被Kubernetes 自动挂载称为容器里的/etc/podinfo/labels



上面的操作，会不断打印出labels里面的内容

```
$ kubectl create -f dapi-volume.yaml
$ kubectl logs test-downwardapi-volume
cluster="test-cluster1"
rack="rack-22"
zone="us-est-coast"

```





#### 支持字段



```
1. 使用 fieldRef 可以声明使用:
spec.nodeName - 宿主机名字
status.hostIP - 宿主机 IP
metadata.name - Pod 的名字
metadata.namespace - Pod 的 Namespace
status.podIP - Pod 的 IP
spec.serviceAccountName - Pod 的 Service Account 的名字
metadata.uid - Pod 的 UID
metadata.labels['<KEY>'] - 指定 <KEY> 的 Label 值
metadata.annotations['<KEY>'] - 指定 <KEY> 的 Annotation 值
metadata.labels - Pod 的所有 Label
metadata.annotations - Pod 的所有 Annotation

2. 使用 resourceFieldRef 可以声明使用:
容器的 CPU limit
容器的 CPU request
容器的 memory limit
容器的 memory request
```



### ServiceAccountToken

Service Account 对象的作用，就是 Kubernetes 系统内置的一种“服务账户”，它是 Kubernetes 进行权限分配的对象。比如，Service Account A，可以只被允许对 Kubernetes API 进行 GET 操作，而 Service Account B，则可以有 Kubernetes API 的所有操作的权限。

像这样的 Service Account 的授权信息和文件，实际上保存在它所绑定的一个特殊的 Secret 对象里的。这个特殊的 Secret 对象，就叫作ServiceAccountToken

任何Kubernetes上的应用，必须使用这个ServiceAccountToken里面保存的授权token，才能合法的访问API Server



另外，为了方便使用，Kubernetes 已经为你提供了一个的默认“服务账户”（default Service Account）。并且，任何一个运行在 Kubernetes 里的 Pod，都可以直接使用这个默认的 Service Account，而无需显示地声明挂载它。



#### 实现原理

利用Projected Volume 机制

如果你查看一下任意一个运行在 Kubernetes 集群里的 Pod，就会发现，每一个 Pod，都已经自动声明一个类型是 Secret、名为 default-token-xxxx 的 Volume，然后 自动挂载在每个容器的一个固定目录上

```
$ kubectl describe pod nginx-deployment-5c678cfb6d-lg9lw
Containers:
...
  Mounts:
    /var/run/secrets/kubernetes.io/serviceaccount from default-token-s8rbq (ro)
Volumes:
  default-token-s8rbq:
  Type:       Secret (a volume populated by a Secret)
  SecretName:  default-token-s8rbq
  Optional:    false
```



这个 Secret 类型的 Volume，正是默认 Service Account 对应的 ServiceAccountToken。所以说，Kubernetes 其实在每个 Pod 创建的时候，自动在它的 spec.volumes 部分添加上了默认 ServiceAccountToken 的定义，然后自动给每个容器加上了对应的 volumeMounts 字段。这个过程对于用户来说是完全透明的。



这样，一旦 Pod 创建完成，容器里的应用就可以直接从这个默认 ServiceAccountToken 的挂载目录里访问到授权信息和文件。这个容器内的路径在 Kubernetes 里是固定的，即：/var/run/secrets/kubernetes.io/serviceaccount ，而这个 Secret 类型的 Volume 里面的内容如下所示：

```
$ ls /var/run/secrets/kubernetes.io/serviceaccount 
ca.crt namespace  token
```



#### InClusterConfig (推荐)

这种把 Kubernetes 客户端以容器的方式运行在集群里，然后使用 default Service Account 自动授权的方式，被称作“InClusterConfig”，也是我最推荐的进行 Kubernetes API 编程的授权方式。

考虑到自动挂载默认 ServiceAccountToken 的潜在风险，Kubernetes 允许你设置默认不为 Pod 里的容器自动挂载这个 Volume。



# Persistent Volume Claim (PVC) (常用)

降低了用户声明和使用持久化 Volume 的门槛。

PVC是一种特殊的Volume，但PVC所指定的volume类型，是和PV绑定之后才知道

Kubernetes 中 PVC 和 PV 的设计，实际上类似于“接口”和“实现”的思想。开发者 只要知道并会使用“接口”，即:PVC;而运维人员则负责给“接口”绑定具体的实现，即: PV。



## 原理

有了 PVC 之后，一个开发人员想要使用一个 Volume，只需要简单的两步即可

第一步:定义一个 PVC，声明想要的 Volume 的属性

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
	name: pv-claim spec:
accessModes:
- ReadWriteOnce resources:
    requests:
      storage: 1Gi
```

> 可以看到，在这个 PVC 对象里，不需要任何关于 Volume 细节的字段，只有描述性的属性和定 义。比如，storage: 1Gi，表示我想要的 Volume 大小至少是 1 GB;accessModes: ReadWriteOnce，表示这个 Volume 的挂载方式是可读写，并且只能被挂载在一个节点上而非 被多个节点共享



第二步:在应用的 Pod 中，声明使用这个 PVC

```
apiVersion: v1
kind: Pod
metadata:
	name: pv-pod 
spec:
	containers:
	- name: pv-container
    image: nginx
    ports:
		- containerPort: 80 
			name: "http-server"
		volumeMounts:
			- mountPath: "/usr/share/nginx/html"
				name: pv-storage 
	volumes:
		- name: pv-storage 
			persistentVolumeClaim:
				claimName: pv-claim
```





## volumeClaimTemplates

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

> volumeClaimTemplates和Deployment 里 Pod 模板(PodTemplate)的作用类似。也就是说，凡是被这 个 StatefulSet 管理的 Pod，都会声明一个对应的 PVC;而这个 PVC 的定义，就来自于 volumeClaimTemplates 这个模板字段。更重要的是，这个 PVC 的名字，会被分配一个与这个 Pod 完全一致的编号。



# Persistent Volume (PV)

创建PVC之后，Kubernetes会自动为其绑定一个符合条件的Volume

这个符合条件的Volume是来自于PV对象



创建PV对象

```
kind: PersistentVolume
apiVersion: v1
metadata:
  name: pv-volume
  labels:
    type: local
spec:
  capacity:
    storage: 10Gi
  rbd:
    monitors:
    - '10.16.154.78:6789'
    - '10.16.154.82:6789'
    - '10.16.154.83:6789'
    pool: kube
    image: foo
    fsType: ext4
    readOnly: true
    user: admin
    keyring: /etc/ceph/keyring
    imageformat: "2"
    imagefeatures: "layering"
```

> rbd 指 Ceph RBD Volume的详细定义
>
> storage 声明PV容量为10GiB













