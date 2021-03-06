---
title: "spark_on_kubernetes"
date: 2019-10-11 13:45
---
[TOC]



# Spark on K8s

利用原生的Kubernetes调度器进行集群资源的分配与管理Spark应用。在此模式下，Spark Driver和Executor均使用Pod来运行，进一步可通过指定Node Selector功能将应用运行于特定节点之上（如：带有GPU的节点实例）



## 特点

使用 kubernetes 原生调度的 spark on kubernetes 是对原有的 spark on yarn 革命性的改变，主要表现在以下几点：

1. Kubernetes 原生调度：不再需要二层调度，直接使用 kubernetes 的资源调度功能，跟其他应用共用整个 kubernetes 管理的资源池；
2. 资源隔离，粒度更细：原先 yarn 中的 queue 在 spark on kubernetes 中已不存在，取而代之的是 kubernetes 中原生的 namespace，可以为每个用户分别指定一个 namespace，限制用户的资源 quota；
3. 细粒度的资源分配：可以给每个 spark 任务指定资源限制，实际指定多少资源就使用多少资源，因为没有了像 yarn 那样的二层调度（圈地式的），所以可以更高效和细粒度的使用资源；
4. 监控的变革：因为做到了细粒度的资源分配，所以可以对用户提交的每一个任务做到资源使用的监控，从而判断用户的资源使用情况，所有的 metric 都记录在数据库中，甚至可以为每个用户的每次任务提交计量；
5. 日志的变革：用户不再通过 yarn 的 web 页面来查看任务状态，而是通过 pod 的 log 来查看，可将所有的 kuberentes 中的应用的日志等同看待收集起来，然后可以根据标签查看对应应用的日志；

所有这些变革都能帮助我们更高效的获的、有效的利用资源，提高生产效率。





## 基本原理

![image-20200405155837123](spark_on_kubernetes.assets/image-20200405155837123.png)



`spark-submit`将Spark作业提交到Kubernetes集群时，会执行以下流程：

- Spark在Kubernetes pod中创建Spark driver
- Driver调用Kubernetes API创建executor pods，executor pods执行作业代码
- 计算作业结束，executor pods回收并清理
- driver pod处于`completed`状态，保留日志，直到Kubernetes GC或者手动清理



### 用户及资源隔离

#### Kubernetes Namespace

在Kubernetes中，我们可以使用namespace在多用户间实现资源分配、隔离和配额。Spark On Kubernetes同样支持配置namespace创建Spark作业。

首先，创建一个Kubernetes namespace：

```
$ kubectl create namespace spark
```

由于我们的Kubernetes集群使用了RBAC，所以还需创建serviceaccount和绑定role：

```
$ kubectl create serviceaccount spark -n spark
$ kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=spark:spark --namespace=spark
```

并在spark-submit中新增以下配置：

```
$ bin/spark-submit \
    --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
    --conf spark.kubernetes.namespace=spark \
    ...
```



#### namespace -> node

考虑到我们Spark作业的一些特点和计算资源隔离，前期我们还是选择了较稳妥的物理隔离方案。具体做法是为每个组提供单独的Kubernetes namespace，计算任务都在各自namespace里提交。计算资源以物理机为单位，折算成cpu和内存，纳入Kubernetes统一管理。在Kubernetes集群里，通过`node label`和`PodNodeSelector`将计算资源和namespace关联。从而实现在提交Spark作业时，计算资源总是选择namespace关联的node。



具体做法如下：

1、创建node label

```
$ kubectl label nodes <node_name> spark:spark
```

2、开启Kubernetes admission controller
我们是使用kubeadm安装Kubernetes集群，所以修改/etc/kubernetes/manifests/kube-apiserver.yaml，在`--admission-control`后添加`PodNodeSelector`

```
$ cat /etc/kubernetes/manifests/kube-apiserver.yaml
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
    - --secure-port=6443
    - --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
    - --admission-control=Initializers,NamespaceLifecycle,LimitRanger,ServiceAccount,DefaultStorageClass,DefaultTolerationSeconds,NodeRestriction,ResourceQuota,MutatingAdmissionWebhook,ValidatingAdmissionWebhook,PodNodeSelector
...
```

3、配置PodNodeSelector 在namespace的annotations中添加`scheduler.alpha.kubernetes.io/node-selector: spark=spark`。

```
apiVersion: v1
kind: Namespace
metadata:
  annotations:
    scheduler.alpha.kubernetes.io/node-selector: spark=spark
  name: spark
```

完成以上配置后，可以通过`spark-submit`测试结果：

```
$ spark-submit
--conf spark.kubernetes.authenticate.driver.serviceAccountName=spark 
--conf spark.kubernetes.namespace=spark 
--master k8s://https://xxxx:6443     
--deploy-mode cluster     
--name spark-pi     
--class org.apache.spark.examples.SparkPi     
--conf spark.executor.instances=5     
--conf spark.kubernetes.container.image=xxxx/library/spark:v2.3     
http://xxxx:81/spark-2.3.0-bin-hadoop2.7/examples/jars/spark-examples_2.11-2.3.0.jar
```





## requirements

- Spark 2.3+
- Kubernetes 1.6+
- 具有Kubernetes pods的list, create, edit和delete权限
- Kubernetes集群必须正确配置[Kubernetes DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)





# installation



```
kubeadm init

kubeadm join --token <token> <master-ip>:<master-port> --discovery-token-ca-cert-hash sha256:<hash>
```



## 镜像

很多镜像无法从k8s.gcr.io获取，我们需要将之替换为第三方提供的镜像，比如：https://hub.docker.com/u/mirrorgooglecontainers/。



## 网络

Kubernetes网络默认是通过CNI实现，主流的CNI plugin有：Linux Bridge、MACVLAN、Flannel、Calico、Kube-router、Weave Net等。Flannel主要是使用VXLAN tunnel来解决pod间的网络通信，Calico和Kube-router则是使用BGP。由于软VXLAN对宿主机的性能和网络有不小的损耗，BGP则对硬件交换机有一定的要求，且我们的基础网络是VXLAN实现的大二层，所以我们最终选择了MACVLAN。



CNI MACVLAN的配置示例如下：

```
{
  "name": "mynet",
  "type": "macvlan",
  "master": "eth0",
  "ipam": {
    "type": "host-local",
    "subnet": "10.0.0.0/17",
    "rangeStart": "10.0.64.1",
    "rangeEnd": "10.0.64.126",
    "gateway": "10.0.127.254",
    "routes": [
      {
        "dst": "0.0.0.0/0"
      },
      {
        "dst": "10.0.80.0/24",
        "gw": "10.0.0.61"
      }
    ]
  }
}
```

Pod subnet是10.0.0.0/17，实际pod ip pool是10.0.64.0/20。cluster cidr是10.0.80.0/24。我们使用的IPAM是host-local，规则是在每个Kubernetes node上建立/25的子网，可以提供126个IP。我们还配置了一条到cluster cidr的静态路由`10.0.80.0/24`，网关是宿主机。这是因为容器在macvlan配置下egress并不会通过宿主机的iptables，这点和Linux Bridge有较大区别。在Linux Bridge模式下，只要指定内核参数`net.bridge.bridge-nf-call-iptables = 1`，所有进入bridge的流量都会通过宿主机的iptables。经过分析kube-proxy，我们发现可以使用`KUBE-FORWARD`这个chain来进行pod到service的网络转发：

```
-A FORWARD -m comment --comment "kubernetes forward rules" -j KUBE-FORWARD
-A KUBE-FORWARD -m comment --comment "kubernetes forwarding rules" -m mark --mark 0x4000/0x4000 -j ACCEPT
-A KUBE-FORWARD -s 10.0.0.0/17 -m comment --comment "kubernetes forwarding conntrack pod source rule" -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A KUBE-FORWARD -d 10.0.0.0/17 -m comment --comment "kubernetes forwarding conntrack pod destination rule" -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
```

最后通过`KUBE-SERVICES`使用DNAT到后端的pod。pod访问其他网段的话，就通过物理网关10.0.127.254。

还有一个需要注意的地方是出于kernel security的考虑，link物理接口的macvlan是无法直接和物理接口通信的，这就导致容器并不能将宿主机作为网关。我们采用了一个小技巧，避开了这个限制。我们从物理接口又创建了一个macvlan，将物理IP移到了这个接口上，物理接口只作为网络入口：

```
$ cat /etc/sysconfig/network-scripts/ifcfg-eth0

DEVICE=eth0
IPV6INIT=no
BOOTPROTO=none

$ cat /etc/sysconfig/network-scripts/ifcfg-macvlan

DEVICE=macvlan
NAME=macvlan
BOOTPROTO=none
ONBOOT=yes
TYPE=macvlan
DEVICETYPE=macvlan
DEFROUTE=yes
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=no
IPADDR=10.0.0.61
PREFIX=17
GATEWAY=10.0.127.254
MACVLAN_PARENT=eth0
MACVLAN_MODE=bridge
```

> 这样两个macvlan是可以互相通信的。



## Kube-dns

默认配置下，Kubernetes使用kube-dns进行DNS解析和服务发现。但在实际使用时，我们发现在pod上通过service domain访问service总是有5秒的延迟。使用tcpdump抓包，发现延迟出现在DNS AAAA。进一步排查，发现问题是由于netfilter在`conntrack`和`SNAT`时的`Race Condition`导致。简言之，DNS A和AAAA记录请求报文是并行发出的，这会导致netfilter在`_nf_conntrack_confirm`时认为第二个包是重复的（因为有相同的五元组），从而丢包。具体可看我提的issue：https://github.com/kubernetes/kubernetes/issues/62628。一个简单的解决方案是在`/etc/resolv.conf`中增加`options single-request-reopen`，使DNS A和AAAA记录请求报文使用不同的源端口。我提的PR在：https://github.com/kubernetes/kubernetes/issues/62628，大家可以参考。我们的解决方法是不使用Kubernetes service，设置`hostNetwork=true`使用宿主机网络提供DNS服务。因为我们的基础网络是大二层，所以pod和node可以直接通信，这就避免了`conntrack`和`SNAT`。



## Kubernetes HA

Kubernetes的集群状态基本都保存在etcd中，所以etcd是HA的关键所在。由于我们目前还处在半生产状态，HA这方面未过多考虑。有兴趣的同学可以查看：https://kubernetes.io/docs/setup/independent/high-availability/



## 日志

在Spark On Yarn下，可以开启`yarn.log-aggregation-enable`将日志收集聚合到HDFS中，以供查看。但是在Spark On Kubernetes中，则缺少这种日志收集机制，我们只能通过Kubernetes pod的日志输出，来查看Spark的日志：

```
$ kubectl -n=<namespace> logs -f <driver-pod-name>
```





# 操作



## Docker镜像

由于Spark driver和executor都运行在Kubernetes pod中，并且我们使用Docker作为container runtime enviroment，所以首先我们需要建立Spark的Docker镜像。

在Spark distribution中已包含相应脚本和Dockerfile，可以通过以下命令构建镜像：

```
$ ./bin/docker-image-tool.sh -r <repo> -t my-tag build
$ ./bin/docker-image-tool.sh -r <repo> -t my-tag push
```



## 提交作业

在构建Spark镜像后，我们可以通过以下命令提交作业：

```
$ bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --jars https://path/to/dependency1.jar,https://path/to/dependency2.jar
    --files hdfs://host:port/path/to/file1,hdfs://host:port/path/to/file2
    --conf spark.executor.instances=5 \
    --conf spark.kubernetes.container.image=<spark-image> \
    https://path/to/examples.jar
```

其中，Spark master是Kubernetes api server的地址，可以通过以下命令获取：

```
$ kubectl cluster-info
Kubernetes master is running at http://127.0.0.1:6443
```

Spark的作业代码和依赖，我们可以在`--jars`、`--files`和最后位置指定，协议支持http、https和hdfs。



## Spark Driver UI

可以在本地使用`kubectl port-forward`访问Driver UI：

```
$ kubectl port-forward <driver-pod-name> 4040:4040
```

执行完后通过http://localhost:4040访问。



## 访问日志

Spark的所有日志都可以通过Kubernetes API和kubectl CLI进行访问：

```
$ kubectl -n=<namespace> logs -f <driver-pod-name>
```







