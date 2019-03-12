---
title: "k8s_in_digital_ocean"
date: 2019-03-10 21:15
---


[TOC]



# Master 节点

## Ubuntu 

16.04, 2 CPU, 7.5G mem, 30G HDD

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



## kubeadm

### 编写 yaml

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
root@111:~# kubectl apply -f https://git.io/weave-kube-1.6
serviceaccount/weave-net created
clusterrole.rbac.authorization.k8s.io/weave-net created
clusterrolebinding.rbac.authorization.k8s.io/weave-net created
role.rbac.authorization.k8s.io/weave-net created
rolebinding.rbac.authorization.k8s.io/weave-net created
daemonset.extensions/weave-net created
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



## Dashboard 可视化插件

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



由于 Kubernetes 本身的松耦合设计，绝大多数存储项目，比如 Ceph、GlusterFS、NFS 等，都可以为 Kubernetes 提供持久化存储能力。在这次选择部署一个很重要的 Kubernetes 存储插件项目：Rook。



### Rook （No PROD）

Rook 项目是一个基于 Ceph 的 Kubernetes 存储插件（它后期也在加入对更多存储实现的支持）。不过，不同于对 Ceph 的简单封装，Rook 在自己的实现中加入了水平扩展、迁移、灾难备份、监控等大量的企业级功能，使得这个项目变成了一个完整的、生产级别可用的容器存储插件。



现在rook ceph版本还是beta，不能用于生产环境



得益于容器化技术，用两条指令，Rook 就可以把复杂的 Ceph 存储后端部署起来：

```
kubectl apply -f https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/operator.yaml

kubectl apply -f https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/cluster.yaml

```



```
root@111:~# kubectl  get pods -n rook-ceph
NAME                               READY     STATUS    RESTARTS   AGE
rook-ceph-mon-a-6c66975-5kgxt      1/1       Running   0          22s
rook-ceph-mon-b-b955c6b59-9r9kk    1/1       Running   0          16s
rook-ceph-mon-c-585b9dbff9-h92wq   1/1       Running   0          6s
root@111:~# kubectl  get pods -n rook-ceph-system
NAME                                  READY     STATUS    RESTARTS   AGE
rook-ceph-agent-njf9n                 1/1       Running   0          1m
rook-ceph-operator-5496d44d7c-mgplk   1/1       Running   0          1m
rook-discover-66m7x                   1/1       Running   0          1m
```



这样，一个基于 Rook 持久化存储集群就以容器的方式运行起来了，而接下来在 Kubernetes 项目上创建的所有 Pod 就能够通过 Persistent Volume（PV）和 Persistent Volume Claim（PVC）的方式，在容器里挂载由 Ceph 提供的数据卷了。





# 部署应用

## nginx

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