---
title: "kubernetes_with_centos"
date: 2019-03-31 18:26
---


[TOC]





# master node

## repo

docker ce

```
wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```



kubernetes.repo

```
[kubernetes]
name=Kubernetes Repo
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
enabled=1
```





查看每台机器上是否可以读取docker 和kubernetes repo

```
yum repolist
```



## install

```
wget https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg

rpm --import rpm-package-key.gpg
```



```
yum install docker-ce kubelet kubeadm kubectl 
```





## docker config

Use your own proxy

```
vim /usr/lib/systemd/system/docker.service

# Add new env
[Service]
Environment="HTTPS_PROXY=http://www.ik8s.io:10080"
Environment="NO_PROXY=127.0.0.0/8,172.20.0.0/16"
```



激活iptables call

```
vim /etc/sysctl.conf

net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
```

```
[root@localhost ~]# cat /proc/sys/net/bridge/bridge-nf-call-ip6tables
1
[root@localhost ~]# cat /proc/sys/net/bridge/bridge-nf-call-iptables
1
```

```
systemctl enable docker
```



## kubelet (local)

关闭swap以防止报错

```
vim /etc/sysconfig/kubelet

KUBELET_EXTRA_ARGS="--fail-swap-on=false"
```



添加ipvs 模块 (未测试)

```
KUBE_PROXY_MODE=ipvs
```

并且导入模块

```
ip_vs, ip_vs_rr, ip_vs_wrr, ip_vs_sh, nf_conntrack_ipv4
```





 若无法启动服务

```
kubeadm reset 
echo 'Environment="KUBELET_EXTRA_ARGS=--fail-swap-on=false"' >> /etc/systemd/system/kubelet.service.d/10-kubeadm.conf

systemctl daemon-reload
systemctl restart kubelet
```



## kubelet (cloud)

```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kube*
EOF

# Set SELinux in permissive mode (effectively disabling it)
setenforce 0
sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes

systemctl enable --now kubelet
```



## kubeadm

```
kubeadm init --kubernetes-version=v1.14.0 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12 --ignore-preflight-errors=Swap
```



返回结果

```
To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 10.0.3.15:6443 --token 4r0o8m.uq64pq6xmvsdejip \
    --discovery-token-ca-cert-hash sha256:657d1fb969a9c1f08c0b2fe763d06adfe3d33fe21d89dedda2c0ddbbb5569a2c
```



查看状态

```
[root@localhost yum.repos.d]# kubectl get cs
NAME                 STATUS    MESSAGE             ERROR
scheduler            Healthy   ok
controller-manager   Healthy   ok
etcd-0               Healthy   {"health":"true"}
[root@localhost yum.repos.d]# kubectl get componentstatus
NAME                 STATUS    MESSAGE             ERROR
controller-manager   Healthy   ok
scheduler            Healthy   ok
etcd-0               Healthy   {"health":"true"}
```





```
[root@localhost ~]# kubectl get nodes
NAME                    STATUS     ROLES    AGE     VERSION
localhost.localdomain   NotReady   master   6m40s   v1.14.0
```

> 这里是NotReady，表示flannel网络组件没有安装



## flannel 

For Kubernetes v1.7+ 

```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```



可以看到加载的镜像文件

```
[root@localhost ~]# docker images -a
REPOSITORY                           TAG                 IMAGE ID            CREATED             SIZE
k8s.gcr.io/kube-proxy                v1.14.0             5cd54e388aba        5 days ago          82.1MB
k8s.gcr.io/kube-apiserver            v1.14.0             ecf910f40d6e        5 days ago          210MB
k8s.gcr.io/kube-controller-manager   v1.14.0             b95b1efa0436        5 days ago          158MB
k8s.gcr.io/kube-scheduler            v1.14.0             00638a24688b        5 days ago          81.6MB
quay.io/coreos/flannel               v0.11.0-amd64       ff281650a721        2 months ago        52.6MB
k8s.gcr.io/coredns                   1.3.1               eb516548c180        2 months ago        40.3MB
k8s.gcr.io/etcd                      3.3.10              2c4adeb21b4f        4 months ago        258MB
k8s.gcr.io/pause                     3.1                 da86e6ba6ca1        15 months ago       742kB
```





# Worker nodes

## Install

```
wget https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg

rpm --import rpm-package-key.gpg
```



```
yum install docker-ce kubelet kubeadm
```

激活iptables call

```
vim /etc/sysctl.conf

net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
```

```
[root@localhost ~]# cat /proc/sys/net/bridge/bridge-nf-call-ip6tables
1
[root@localhost ~]# cat /proc/sys/net/bridge/bridge-nf-call-iptables
1
```

```
systemctl enable docker kubelet
systemctl start docker
```



## kubelet

关闭swap以防止报错

```
vim /etc/sysconfig/kubelet

KUBELET_EXTRA_ARGS="--fail-swap-on=false"
```

若无法启动服务

```
kubeadm reset 

mkdir -p /etc/systemd/system/kubelet.service.d/
echo 'Environment="KUBELET_EXTRA_ARGS=--fail-swap-on=false"' >> /etc/systemd/system/kubelet.service.d/10-kubeadm.conf

systemctl daemon-reload
systemctl restart kubelet
```





## add to master

```
kubeadm join 172.20.1.102:6443 --token 4r0o8m.uq64pq6xmvsdejip \
    --discovery-token-ca-cert-hash sha256:657d1fb969a9c1f08c0b2fe763d06adfe3d33fe21d89dedda2c0ddbbb5569a2c --ignore-preflight-errors=Swap
```

> 需要添加swap ignore



```
kubeadm join 198.13.42.46:6443 --token 9tq75r.423i3wru3pde14xd \
    --discovery-token-ca-cert-hash sha256:d1922d52df8870b787d4e068997bc9890e44bcc580206c08e965c043c6c8c58f
```



查看加入的node状态

```
[root@master ~]# kubectl get nodes
NAME     STATUS   ROLES    AGE     VERSION
master   Ready    master   154m    v1.14.0
node01   Ready    <none>   3m52s   v1.14.0
node02   Ready    <none>   3m14s   v1.14.0
```



##  kubedns

```
[root@master ~]# kubectl get svc -n kube-system
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   12h
```



解析pod的地址，需要指定所在的default domain

```
[root@master ~]# kubectl run client --image=busybox --replicas=1 -it --restart=Never
If you don't see a command prompt, try pressing enter.
/ # cat /etc/resolv.conf
nameserver 10.96.0.10
search default.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
```

```
[root@master ~]# dig -t A nginx.default.svc.cluster.local @10.96.0.10

; <<>> DiG 9.9.4-RedHat-9.9.4-73.el7_6 <<>> -t A nginx.default.svc.cluster.local @10.96.0.10
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 61822
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;nginx.default.svc.cluster.local. IN    A

;; ANSWER SECTION:
nginx.default.svc.cluster.local. 5 IN   A       10.96.216.63

;; Query time: 0 msec
;; SERVER: 10.96.0.10#53(10.96.0.10)
;; WHEN: Tue Apr 02 00:44:52 UTC 2019
;; MSG SIZE  rcvd: 107
```

