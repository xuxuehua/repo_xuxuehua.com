---
title: "k8s_in_ubuntu"
date: 2019-03-10 21:15
---


[TOC]



# Master 节点

## Ubuntu 

16.04, 2 CPU, 7.5G mem, 30G HDD

### Letting iptables see bridged traffic

```
cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
sysctl -p

```



```
apt -y update && apt -y upgrade 
```



### docker

```

# Install Docker CE
## Set up the repository:
### Install packages to allow apt to use a repository over HTTPS
apt-get update && apt-get install -y \
  apt-transport-https ca-certificates curl software-properties-common gnupg2

### Add Docker’s official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -

### Add Docker apt repository.
add-apt-repository \
  "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) \
  stable"

## Install Docker CE.
apt-get update && apt-get install -y \
  containerd.io=1.2.13-1 \
  docker-ce=5:19.03.8~3-0~ubuntu-$(lsb_release -cs) \
  docker-ce-cli=5:19.03.8~3-0~ubuntu-$(lsb_release -cs)




# Setup daemon.
cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF


mkdir -p /etc/systemd/system/docker.service.d

# Restart docker.
systemctl daemon-reload
systemctl restart docker
docker version


```



```
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update -y
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

```



## kubeadm

### 编写 yaml

kubeadm.yaml

```
apiVersion: kubeadm.k8s.io/v1alpha1
kind: MasterConfiguration
controllerManagerExtraArgs:
  horizontal-pod-autoscaler-use-rest-clients: "true"
  horizontal-pod-autoscaler-sync-period: "10s"
  node-monitor-grace-period: "10s"
apiServerExtraArgs:
  runtime-config: "api/all=true"
kubernetesVersion: "stable-1.11"
```

> 这里的controller-manager 配置了`horizontal-pod-autoscaler-use-rest-clients: "true"`



这样能够使用自定义资源(Custom Metrics) 进行水平扩展



#### v1.13.0

```
apiVersion: kubeadm.k8s.io/v1beta1
kind: InitConfiguration
controllerManager:
  horizontal-pod-autoscaler-use-rest-clients: "true"
  horizontal-pod-autoscaler-sync-period: "10s"
  node-monitor-grace-period: "10s"
apiServer:
  runtime-config: "api/all=true"
kubernetesVersion: "v1.13.0"
```





#### v1.18.1

```
apiVersion: kubeadm.k8s.io/v1beta2
kind: InitConfiguration
controllerManager:
  horizontal-pod-autoscaler-use-rest-clients: "true"
  horizontal-pod-autoscaler-sync-period: "10s"
  node-monitor-grace-period: "10s"
apiServer:
  runtime-config: "api/all=true"
kubernetesVersion: "v1.18.1"
```



### 执行

```
kubeadm init --config kubeadm.yaml
```



部署完成后

```
Your Kubernetes master has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of machines by running the following on each node
as root:

  kubeadm join x.x.x.x:6443 --token h7t5zp.0atym3rlxb7oa9vu --discovery-token-ca-cert-hash sha256:3f9b2fac0cdf760f6b03ccb0934743e97c182e127eca99a516716010275cc046
```



执行部署信息

```
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
```





## 查看部署信息

可以查看到当前唯一的节点

```
root@111:~# kubectl  get nodes
NAME      STATUS     ROLES     AGE       VERSION
111       NotReady   master    3m        v1.11.3
```



Master节点状态是NotReady，因为KubeletNotReady 

```
kubectl describe node 111
```

> 在condition里面会有这个信息 KubeletNotReady



### 查看节点Pod信息

kube-system是Kubernetes项目预留的系统Pod工作空间(Namespace, 只是Kubernetes 划分不同工作空间的单位)

```
root@111:~# kubectl get pods -n kube-system
NAME                          READY     STATUS    RESTARTS   AGE
coredns-78fcdf6894-46p7z      0/1       Pending   0          43m
coredns-78fcdf6894-scs85      0/1       Pending   0          43m
etcd-111                      1/1       Running   0          43m
kube-apiserver-111            1/1       Running   0          42m
kube-controller-manager-111   1/1       Running   0          43m
kube-proxy-stblp              1/1       Running   0          43m
kube-scheduler-111            1/1       Running   0          42m
```



这里可以看到CoreDNS等依赖网络的Pod都是pending



## 部署网络插件

### kubectl apply

部署weave插件

```
kubectl apply -f https://raw.githubusercontent.com/weaveworks/weave/master/prog/weave-kube/weave-daemonset-k8s-1.11.yaml

```



这样所有系统的Pod都启动了

```
root@111:~# kubectl get pods -n kube-system
NAME                          READY     STATUS    RESTARTS   AGE
coredns-78fcdf6894-46p7z      1/1       Running   0          47m
coredns-78fcdf6894-scs85      1/1       Running   0          47m
etcd-111                      1/1       Running   0          46m
kube-apiserver-111            1/1       Running   0          46m
kube-controller-manager-111   1/1       Running   0          46m
kube-proxy-stblp              1/1       Running   0          47m
kube-scheduler-111            1/1       Running   0          46m
weave-net-mkhhs               2/2       Running   0          25s
```











## Dashboard 可视化插件

https://github.com/kubernetes/dashboard

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
```



查看dashboard 对应的pod

```
root@111:~# kubectl get pods -n kube-system
NAME                                    READY     STATUS    RESTARTS   AGE
coredns-78fcdf6894-46p7z                1/1       Running   0          1h
coredns-78fcdf6894-scs85                1/1       Running   0          1h
etcd-111                                1/1       Running   0          1h
kube-apiserver-111                      1/1       Running   0          1h
kube-controller-manager-111             1/1       Running   0          1h
kube-proxy-stblp                        1/1       Running   0          1h
kube-scheduler-111                      1/1       Running   0          1h
kubernetes-dashboard-5dd89b9875-sbct6   1/1       Running   0          22s
weave-net-mkhhs                         2/2       Running   0          44m
```



需要注意的是，由于 Dashboard 是一个 Web Server，很多人经常会在自己的公有云上无意地暴露 Dashboard 的端口，从而造成安全隐患。所以，1.7 版本之后的 Dashboard 项目部署完成后，默认只能通过 Proxy 的方式在本地访问。具体的操作，你可以查看 Dashboard 项目的文档



## 容器持久化插件

### 持久化

很多时候我们需要用数据卷（Volume）把外面宿主机上的目录或者文件挂载进容器的 Mount Namespace 中，从而达到容器和宿主机共享这些目录或者文件的目的。容器里的应用，也就可以在这些数据卷中新建和写入文件。



可是，如果你在某一台机器上启动的一个容器，显然无法看到其他机器上的容器在它们的数据卷里写入的文件。



而容器的持久化存储，就是用来保存容器存储状态的重要手段：存储插件会在容器里挂载一个基于网络或者其他机制的远程数据卷，使得在容器里创建的文件，实际上是保存在远程存储服务器上，或者以分布式的方式保存在多个节点上，而与当前宿主机没有任何绑定关系。这样，无论你在其他哪个宿主机上启动新的容器，都可以请求挂载指定的持久化存储卷，从而访问到数据卷里保存的内容。这就是持久化



由于 Kubernetes 本身的松耦合设计，绝大多数存储项目，比如 Ceph、GlusterFS、NFS 等，都可以为 Kubernetes 提供持久化存储能力。



### Rook （No PROD）

Rook 项目是一个基于 Ceph 的 Kubernetes 存储插件（它后期也在加入对更多存储实现的支持）。不过，不同于对 Ceph 的简单封装，Rook 在自己的实现中加入了水平扩展、迁移、灾难备份、监控等大量的企业级功能，使得这个项目变成了一个完整的、生产级别可用的容器存储插件。



现在rook ceph版本还是beta，不能用于生产环境



得益于容器化技术，用两条指令，Rook 就可以把复杂的 Ceph 存储后端部署起来：

```
kubectl apply -f https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/common.yaml


kubectl apply -f https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/operator.yaml

kubectl apply -f https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/cluster.yaml

```



```
root@master1:~# kubectl get pods -n rook-ceph
NAME                                           READY   STATUS    RESTARTS   AGE
csi-cephfsplugin-jlvfr                         3/3     Running   0          2m30s
csi-cephfsplugin-provisioner-fd87698db-gfz2d   5/5     Running   0          2m30s
csi-cephfsplugin-provisioner-fd87698db-wg625   5/5     Running   0          2m30s
csi-cephfsplugin-wlm8j                         3/3     Running   0          2m30s
csi-rbdplugin-cxl6z                            3/3     Running   0          2m30s
csi-rbdplugin-provisioner-7c9c6578b-v4bjp      6/6     Running   0          2m30s
csi-rbdplugin-provisioner-7c9c6578b-zsknz      6/6     Running   0          2m30s
csi-rbdplugin-w4lct                            3/3     Running   0          2m30s
rook-ceph-mon-a-canary-54b8fbdfcb-l8s2d        1/1     Running   0          2m12s
rook-ceph-mon-b-canary-667f566d99-mf4gw        1/1     Running   0          2m11s
rook-ceph-mon-c-canary-67c948794-2pd4w         0/1     Pending   0          2m11s
rook-ceph-operator-567d7945d6-2rzq9            1/1     Running   0          3m44s
rook-discover-7l4cf                            1/1     Running   0          3m3s
rook-discover-srr4r                            1/1     Running   0          3m3s
```



这样，一个基于 Rook 持久化存储集群就以容器的方式运行起来了，而接下来在 Kubernetes 项目上创建的所有 Pod 就能够通过 Persistent Volume（PV）和 Persistent Volume Claim（PVC）的方式，在容器里挂载由 Ceph 提供的数据卷了。







## nginx 部署

nginx-deployment.yaml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```



```
kubectl create -f nginx-deployment.yaml
```



```
root@111:~# kubectl get pods -l app=nginx
NAME                                READY     STATUS    RESTARTS   AGE
nginx-deployment-67594d6bf6-hjgf2   1/1       Running   0          18s
nginx-deployment-67594d6bf6-xjtlr   1/1       Running   0          18s
```



```
root@111:~# kubectl describe pod nginx-deployment-67594d6bf6-hjgf2
Name:               nginx-deployment-67594d6bf6-hjgf2
Namespace:          default
Priority:           0
PriorityClassName:  <none>
Node:               111/178.128.110.131
Start Time:         Wed, 13 Mar 2019 06:29:14 +0000
Labels:             app=nginx
                    pod-template-hash=2315082692
Annotations:        <none>
Status:             Running
IP:                 10.32.0.13
Controlled By:      ReplicaSet/nginx-deployment-67594d6bf6
Containers:
  nginx:
    Container ID:   docker://fab3aadd7695c7505d463decce821d5e40e7cc15a1760375e63b16ef13d5ae77
    Image:          nginx:1.7.9
    Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Wed, 13 Mar 2019 06:29:23 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-bzv7k (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-bzv7k:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-bzv7k
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  38s   default-scheduler  Successfully assigned default/nginx-deployment-67594d6bf6-hjgf2 to 111
  Normal  Pulling    37s   kubelet, 111       pulling image "nginx:1.7.9"
  Normal  Pulled     29s   kubelet, 111       Successfully pulled image "nginx:1.7.9"
  Normal  Created    29s   kubelet, 111       Created container
  Normal  Started    29s   kubelet, 111       Started container
```

> 这里的Events信息表示对API对象所有重要操作，都会被记录在这个对象的Events里面



### 升级 nginx 1.8

```
...    
    spec:
      containers:
      - name: nginx
        image: nginx:1.8 # 这里被从 1.7.9 修改为 1.8
        ports:
      - containerPort: 80

```



更新

```
$ kubectl apply -f nginx-deployment.yaml

# 修改 nginx-deployment.yaml 的内容

$ kubectl apply -f nginx-deployment.yaml

```



## 声明Volume

仅需要修改yaml文件即可

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8
        ports:
        - containerPort: 80
        volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: nginx-vol
      volumes:
      - name: nginx-vol
        emptyDir: {}
```

> emptyDir 就是Docker的隐式Volume 参数，即不显式声明宿主机目录的Volume



Kubernetes会为此在宿主就创建一个临时目录，这个目录将来会被绑定挂载到容器所声明的Volume目录上





若是显式的Volume定义，叫做hostPath

```
volumes:
     - name: nginx-vol
       hostPath:
         path: "/home/vagrant/mykube/firstapp/html"
```





更新yaml

```
root@111:~# kubectl apply -f nginx-deployment.yaml
deployment.apps/nginx-deployment configured
```



可以看到Volumes 的信息

```
root@111:~# kubectl get pods
NAME                                READY     STATUS    RESTARTS   AGE
nginx-deployment-5c678cfb6d-jzknn   1/1       Running   0          5m
nginx-deployment-5c678cfb6d-t785g   1/1       Running   0          5m


root@111:~# kubectl describe pod nginx-deployment-5c678cfb6d-jzknn
....
    Mounts:
      /usr/share/nginx/html from nginx-vol (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-bzv7k (ro)

Volumes:
  nginx-vol:
    Type:    EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:

```





可以切进去查看目录信息

```
root@111:~# kubectl exec -it nginx-deployment-5c678cfb6d-jzknn -- /bin/bash

root@nginx-deployment-5c678cfb6d-jzknn:/# ls /usr/share/nginx/html/
```







### 共享Volume

nginx-container 读取 debian-container中的index.html

```
apiVersion: v1
kind: Pod
metadata:
  name: two-containers
spec:
  restartPolicy: Never
  volumes:
  - name: shared-data
    hostPath:      
      path: /data
  containers:
  - name: nginx-container
    image: nginx
    volumeMounts:
    - name: shared-data
      mountPath: /usr/share/nginx/html
  - name: debian-container
    image: debian
    volumeMounts:
    - name: shared-data
      mountPath: /pod-data
    command: ["/bin/sh"]
    args: ["-c", "echo Hello from the debian container > /pod-data/index.html"]
```

> shared-data 是hostPath类型，而对应在宿主机上的目录就是/data ，而这个目录，其实同时绑定挂载进上述两个容器中





#### Sidecar 组合

指在一个Pod中，启动一个辅助容器，完成一些独立与主进程(主容器)之外的工作



这里，Tomcat就是主容器，而initContainer优先运行WAR包容器，扮演了sidecar 角色

```
apiVersion: v1
kind: Pod
metadata:
  name: javaweb-2
spec:
  initContainers:
  - image: geektime/sample:v2
    name: war
    command: ["cp", "/sample.war", "/app"]
    volumeMounts:
    - mountPath: /app
      name: app-volume
  containers:
  - image: geektime/tomcat:7.0
    name: tomcat
    command: ["sh","-c","/root/apache-tomcat-7.0.42-v2/bin/start.sh"]
    volumeMounts:
    - mountPath: /root/apache-tomcat-7.0.42-v2/webapps
      name: app-volume
    ports:
    - containerPort: 8080
      hostPort: 8001 
  volumes:
  - name: app-volume
    emptyDir: {}
```

> WAR 包容器是一个initContainers，会比所有spec.containers 优先启动，initContainer 容器也会按照顺序启动
>
> 通过命令将WAR包复制到/app目录下然后退出
>
> /app 目录挂载了名叫app-volume 的Volume
>
> Tomcat也同样声明挂载了app-volume到自己的/webapps下面





#### 容器的日志收集

将实现不断把日志文件输出到容器的/var/log目录中



可以将一个Pod里面的Volume挂载到应用容器的/var/log目录上，然后pod里面运行sidecar容器声明挂载同一个Volume到自己的/var/log目录上

sidecar不断的从自己的/var/log目录中读取日志文件，转发到mongoDB或者ElasticSearch中





# Worker 节点

Worker 节点和Master节点几乎是相同的， 运行着都是一个kubelet 组件



唯一区别在kubeadm init的过程中，kubelet 启动后，Master节点上还会自动运行kube-apiserver，kube-scheduler， kube-controller-manager 着三个系统Pod



## Ubuntu

```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -

cat <<EOF > /etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF

apt-get -y update

apt -y install kubelet=1.11.3-00
apt -y install kubectl=1.11.3-00
apt -y install kubeadm=1.11.3-00
```



## Kubeadm join

运行master node init之后生成的token指令即可





# Worker 节点（与Master在一台主机）

默认Master节点是不允许运行用户的Pod，但可以Kubernetes的Taint/Toleration机制就可以



即某节点被加上一个Taint，那么所有的Pod就不能在这个节点上运行

除非有个别的Pod声明可以容忍这个污点，显式声明Toleration

```
kubectl taint nodes node1 foo=bar:NoSchedule
```

> NoScheduler 意味着Taint只会在调度新Pod时产生作用，不会影响已经在node1上运行的Pod



## 声明Pod

为了实现Toleration，需要在Pod的yaml文件中 spec加入tolerations字段即可

```
apiVersion: v1
kind: Pod
...
spec:
  tolerations:
  - key: "foo"
    operator: "Equal"
    value: "bar"
    effect: "NoSchedule"
```

> 这个 Toleration 的含义是，这个 Pod 能“容忍”所有键值对为 foo=bar 的 Taint（ operator: “Equal”，“等于”操作）。





## master节点的Taint

```
root@111:~# kubectl  describe node 111 | grep Taints
Taints:             node-role.kubernetes.io/master:NoSchedule
```

> 可以看到，Master 节点默认被加上了node-role.kubernetes.io/master:NoSchedule
>
> 这样一个“污点”，其中“键”是node-role.kubernetes.io/master，而没有提供“值”。



若想要一个单节点的Kubernetes，删除这个Taint即可

```
root@111:~# kubectl taint nodes --all node-role.kubernetes.io/master-
node/111 untainted
```

> 结尾的短线dash表示，移除所有以node-role.kubernetes.io/master为键的taint





# example



## MySQL 主从同步

Master 节点和 Slave 节点需要有不同的配置文件

使用ConfigMap保存不同的配置文件信息

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql
  labels:
    app: mysql
data:
  master.cnf: |
    # 主节点 MySQL 的配置文件
    [mysqld]
    log-bin
  slave.cnf: |
    # 从节点 MySQL 的配置文件
    [mysqld]
    super-read-only
```





创建两个 Service 来供 StatefulSet 以及用户使用

```
apiVersion: v1
kind: Service
metadata:
  name: mysql
  labels:
    app: mysql
spec:
  ports:
  - name: mysql
    port: 3306
  clusterIP: None
  selector:
    app: mysql
```

> 这里clusterIP 是None， 即为一个Headless Service




```
apiVersion: v1
kind: Service
metadata:
  name: mysql-read
  labels:
    app: mysql
spec:
  ports:
  - name: mysql
    port: 3306
  selector:
    app: mysql
```

> 常规的Service





InitContainer

```
      ...
      # template.spec
      initContainers:
      - name: init-mysql
        image: mysql:5.7
        command:
        - bash
        - "-c"
        - |
          set -ex
          # 从 Pod 的序号，生成 server-id
          [[ `hostname` =~ -([0-9]+)$ ]] || exit 1
          ordinal=${BASH_REMATCH[1]}
          echo [mysqld] > /mnt/conf.d/server-id.cnf
          # 由于 server-id=0 有特殊含义，我们给 ID 加一个 100 来避开它
          echo server-id=$((100 + $ordinal)) >> /mnt/conf.d/server-id.cnf
          # 如果 Pod 序号是 0，说明它是 Master 节点，从 ConfigMap 里把 Master 的配置文件拷贝到 /mnt/conf.d/ 目录；
          # 否则，拷贝 Slave 的配置文件
          if [[ $ordinal -eq 0 ]]; then
            cp /mnt/config-map/master.cnf /mnt/conf.d/
          else
            cp /mnt/config-map/slave.cnf /mnt/conf.d/
          fi
        volumeMounts:
        - name: conf
          mountPath: /mnt/conf.d
        - name: config-map
          mountPath: /mnt/config-map
```



第二个InitContainer

```
      ...
      # template.spec.initContainers
      - name: clone-mysql
        image: gcr.io/google-samples/xtrabackup:1.0
        command:
        - bash
        - "-c"
        - |
          set -ex
          # 拷贝操作只需要在第一次启动时进行，所以如果数据已经存在，跳过
          [[ -d /var/lib/mysql/mysql ]] && exit 0
          # Master 节点 (序号为 0) 不需要做这个操作
          [[ `hostname` =~ -([0-9]+)$ ]] || exit 1
          ordinal=${BASH_REMATCH[1]}
          [[ $ordinal -eq 0 ]] && exit 0
          # 使用 ncat 指令，远程地从前一个节点拷贝数据到本地
          ncat --recv-only mysql-$(($ordinal-1)).mysql 3307 | xbstream -x -C /var/lib/mysql
          # 执行 --prepare，这样拷贝来的数据就可以用作恢复了
          xtrabackup --prepare --target-dir=/var/lib/mysql
        volumeMounts:
        - name: data
          mountPath: /var/lib/mysql
          subPath: mysql
        - name: conf
          mountPath: /etc/mysql/conf.d
```



MySQL 容器

```
      ...
      # template.spec
      containers:
      - name: mysql
        image: mysql:5.7
        env:
        - name: MYSQL_ALLOW_EMPTY_PASSWORD
          value: "1"
        ports:
        - name: mysql
          containerPort: 3306
        volumeMounts:
        - name: data
          mountPath: /var/lib/mysql
          subPath: mysql
        - name: conf
          mountPath: /etc/mysql/conf.d
        resources:
          requests:
            cpu: 500m
            memory: 1Gi
        livenessProbe:
          exec:
            command: ["mysqladmin", "ping"]
          initialDelaySeconds: 30
          periodSeconds: 10
          timeoutSeconds: 5
        readinessProbe:
          exec:
            # 通过 TCP 连接的方式进行健康检查
            command: ["mysql", "-h", "127.0.0.1", "-e", "SELECT 1"]
          initialDelaySeconds: 5
          periodSeconds: 2
          timeoutSeconds: 1
```





# FAQ

## token 过期

Kubeadm的token 23小时后过期，所以如果你worker端和master 端不是在同一天做的，有可能会出现如下错误:
[kubelet] Downloading configuration for the kubelet from the "kubelet-config-1.11" ConfigMap in the kube-system namespace
Unauthorized

Stackoverflow上有这个解:
https://stackoverflow.com/questions/52823871/unable-to-join-the-worker-node-to-k8-master-node

简单来说，在master 上重新kubeadm token create一下，替换原来的token 就好了

另外，如果出现
[preflight] Some fatal errors occurred:
        [ERROR FileAvailable--etc-kubernetes-pki-ca.crt]: /etc/kubernetes/pki/ca.crt already exists
        [ERROR FileAvailable--etc-kubernetes-bootstrap-kubelet.conf]: /etc/kubernetes/bootstrap-kubelet.conf already exists
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`

把那2个文件mv挪走就好了



如果token过期了，不建议设置ttl=0，使用`kubeadm upgrade`