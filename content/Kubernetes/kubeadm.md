---
title: "kubeadm"
date: 2019-02-22 22:20
---


[TOC]



# kubeadm （不可上PROD）

Kubernetes 项目自带的集群构建工具

负责构建一个最小化的可用集群



使用kubeadm 第一步，是要在机器上手动安装好kubeadm，kubelet和kubectl 这三个二进制文件



kubeadm不可以上生产环境，无法一键部署Kubernetes集群，因为Etcd和Master等组件都应该是多节点集群，而不是单点



## 特点

仅仅关心如何初始化并启动集群



## Installation



### Ubuntu 

```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -

cat <<EOF > /etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF

apt-get update

apt-get install -y docker.io kubeadm
```



## kubeadm init

集群快速初始化，部署master 节点组件

提供join token



### Preflight Checks

执行kubeadm init之后，需要来检查当前主机是否可以部署kubernetes

```
Linux 内核是否必须是3.10以上
Linux Cgroups 模块是否可用
机器的hostname是否标准，在Kubernetes里面，机器的名字以及一切存储在Etcd中的API对象，都必须使用标准的DNS命名
kubeadm和kubelet版本是否匹配
机器上是否已经安装了kubernetes二进制文件
kubernetes的工作端口10250/10251/20252是否已经被占用
ip，mount等Linux指令是否存在
Docker 是否已经安装
……
```



Preflight Checks之后，kubeadm会生成Kubernetes对外提供服务所需的各种证书和对应的目录



### 配置文件

证书生成之后，kubeadm会为其他组件生成访问kube-apiserver所需的配置文件 `/etc/kubernetes/xxx.conf`

这些配置文件记录的是，当前这个Master节点的服务器地址，监听端口，证书目录等信息，对应的客户端如scheduler，kubelet可以直接加载相应文件，使用厘米的信息于kube-apiserver建立安全连接



之后，kubeadm会为Master组件生成Pod 配置文件，这样kube-apiserver，kube-controller-manager，kube-scheduler都会被使用Pod的方式部署起来



在kubeadm中，Master组件的YAML文件会被生成在`/etc/kubernetes/manifests`下面，如kube-apiserver.yaml

```
apiVersion: v1
kind: Pod
metadata:
  annotations:
    scheduler.alpha.kubernetes.io/critical-pod: ""
  creationTimestamp: null
  labels:
    component: kube-apiserver
    tier: control-plane
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-apiserver
    - --authorization-mode=Node,RBAC
    - --runtime-config=api/all=true
    - --advertise-address=10.168.0.2
    ...
    - --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
    - --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
    image: k8s.gcr.io/kube-apiserver-amd64:v1.11.1
    imagePullPolicy: IfNotPresent
    livenessProbe:
      ...
    name: kube-apiserver
    resources:
      requests:
        cpu: 250m
    volumeMounts:
    - mountPath: /usr/share/ca-certificates
      name: usr-share-ca-certificates
      readOnly: true
    ...
  hostNetwork: true
  priorityClassName: system-cluster-critical
  volumes:
  - hostPath:
      path: /etc/ca-certificates
      type: DirectoryOrCreate
    name: etc-ca-certificates
  ...
```





kubeadm还会再生成一个Etcd的Pod YAML文件，用来通过同样的Static Pod的方式启动Etcd

```
$ ls /etc/kubernetes/manifests/
etcd.yaml  kube-apiserver.yaml  kube-controller-manager.yaml  kube-scheduler.yaml
```



### kubeadm 监听

在`/etc/kubernetes/manifests` 目录下，kubelet会自动创建这些YAML文件定义的Pod，即Master组件容器



### Master 容器启动

启动之后，kubeadm会检查`localhost:6443/healthz` ， 这个Master组件的健康检查URL，等Master组件完全运行起来



### 生成token

kubeadm会为整个集群生成一个bootstrap token，通过这个token，任何一个安装了kubelet和kubeadm的节点都可以通过kubeadm join 加入到集群中



在 token 生成之后，kubeadm 会将 ca.crt 等 Master 节点的重要信息，通过 ConfigMap 的方式保存在 Etcd 当中，供后续部署 Node 节点使用。这个 ConfigMap 的名字是 cluster-info。



### 安装默认插件

kube-proxy 和 DNS 这两个插件是必须安装的。

它们分别用来提供整个集群的服务发现和 DNS 功能。其实，这两个插件也只是两个容器镜像而已，所以 kubeadm 只要用 Kubernetes 客户端创建两个 Pod 就可以了。



### --config kubeadm.yaml

指定kube-apiserver的启动参数，如

```
apiVersion: kubeadm.k8s.io/v1alpha2
kind: MasterConfiguration
kubernetesVersion: v1.11.0
api:
  advertiseAddress: 192.168.0.102
  bindPort: 6443
  ...
etcd:
  local:
    dataDir: /var/lib/etcd
    image: ""
imageRepository: k8s.gcr.io
kubeProxy:
  config:
    bindAddress: 0.0.0.0
    ...
kubeletConfiguration:
  baseConfig:
    address: 0.0.0.0
    ...
networking:
  dnsDomain: cluster.local
  podSubnet: ""
  serviceSubnet: 10.96.0.0/12
nodeRegistration:
  criSocket: /var/run/dockershim.sock
  ...

```



## kubeadm join

使用join token将节点快速加入到指定集群中，即work node中，随后就会加入到集群中



kubeadm发起一次非https的访问到kube-apiserver中，拿到保存在ConfigMap中的cluster-info（保存APIServer的授权信息），而cluster-info里面的kube-apiserver的地址，端口，证书，kubelet就可以以https的方式连接到apiserver上







## kubeadm token

集群构建后管理用于加入集群时使用的认证令牌



## kubeadm reset

删除集群构建过程中生成的文件，回到初始状态



