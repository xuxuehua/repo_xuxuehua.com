---
title: "kubectl"
date: 2019-02-22 22:32
---


[TOC]



# kubectl

Kubernetes API Server最常用的客户端程序之一，功能强大，能够几乎完成除了安装部署之外的所有管理操作



## syntax

```
kubectl [command] [TYPE] [NAME] [flags]
```



## installation



### mac osx

#### binary

Download the latest release:

```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl
```

Make the kubectl binary executable.

```
chmod +x ./kubectl
```

Move the binary in to your PATH.

```
sudo mv ./kubectl /usr/local/bin/kubectl
```





#### brew

```
brew install kubernetes-cli
```



## minikube

### mac osx

```
curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.34.1/minikube-darwin-amd64 && chmod +x minikube && sudo cp minikube /usr/local/bin/ && rm minikube
```







## 基础命令

### create 创建资源

通过文件或标准输入创建资源

```
kubectl create -f nignx-deploy.yaml -f nginx-svc.yaml
```



### delete 删除

基于文件名，stdin，资源或名字，以及资源和选择器删除资源



删除默认名称空间中ngnix-svc的Service 资源对象 

```
kubectl delete services nginx-svc
```



删除默认名称空间中所有的Deployment控制器

```
kubectl delete deployment --all
```



#### --all

删除kube-public 名称空间中的所有pod对象

```
kubectl delete pods --all -n kube-public
```



删除所有名称空间的所有资源

```
kubectl delete all --all
```



#### -f 指定配置

```
kubectl delete -f pod-example.yaml
```



#### --grace-period

默认删除操作为30s，使用此参数自定义其时常

若使用0表示直接强制删除指定的资源，需同时使用`--force` 选项



### edit 编辑资源



### explain 资源文档

获取相关帮助



解释Pod资源的一级字段

```
kubectl explain pods
```



某对象下的二级字段, 三四级依此类推

```
kubectl explain pods.spec
```





### expose 暴露服务

基于rc，service，deployment或pod创建Service资源

```
kubectl expose deployment/nginx --name=nginx-svc --port=80
```

```
kubectl expose deployments/myapp --type="NodePort" --port=80 --name=myapp
```









### get 显示资源

即从Kubernetes里面获取指定的API对象



列出所有资源

```
kubectl get namespaces
```



查看多个资源

```
kubectl get pods,services -o wide
```



#### -l 标签选择器

列出名称空间中拥有k8s-app标签名称的所有Pod 对象

```
kubectl get pods -l k8s-app -n kube-system
```



#### -L 标签列表

```
kubectl get pods -l 'env in (production,dev),!tier' -L env,tier
```



#### -o 输出

##### yaml 

```
kubectl get pods -l component=kube=apiserver -o yaml -n kube-system
```



##### json 



##### wide 额外信息

显示资源的额外信息

```
kubectl get pods -o wide 
```



##### name 名称

仅仅打印资源名称



##### go-template go 模版

以自定义的go模版格式化输出API对象信息



##### custom-columns 自定义输出

自定义要输出的字段



##### nodes

查看键名为SSD标签的node资源

```
kubectl get nodes -l 'disktype' -L disktype
```





### run 运行

通过创建Deployment在集群中运行指定的镜像

```
kubectl run nginx-deploy --image=nginx:1.12 --replicas=2
```



### set 设置属性

设置指定资源的特定属性









## 部署命令

### autoscale 自动伸缩

对Deployment，ReplicaSet或RC进行自动伸缩





### scale 扩容/缩容

```
kubectl scale deployments/myapp --replicas=3 
```

> replicas 指明对象创建或管理Pod对象的副本数量



```
kubectl scale deployments/myapp --replicas=2
```



### rollout 滚动更新

管理资源的滚动更新



### rolling-update 滚动升级

对ReplicationController执行滚动升级







## 集群管理

### certificate 数字证书

配置数字证书资源



### cluster-info 集群信息

打印集群信息



### cordon 不可用状态

指定node 设定为不可用（unschedulable）状态



### drain 维护模式

值得node的负载以进入维护模式



### top 使用率

打印资源（cpu/memory/storage） 使用率



### taint 声明污点

为node声明污点及标准行为





### uncordon 可用状态

指定node 设定为可用（schedulable）状态







## 排错调试



### attach 附加终端

附加终端至一个运行中的容器

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



```
kubectl attach -it nginx -c shell
```





### auth 授权信息

打印授权信息



### cp 复制

在容器间复制文件或者目录





### exec 执行命令

容器内执行命令



```
kubectl exec kube-apiserver-master.xuxuehua.com -n kube-system --ps
```

> Pod对象中的容器里面运行ps命令



#### -it 交互Shell

```
kubectl exec -it $POD_NAME /bin/sh
```





### describe 详细信息

```
kubectl describe (-f FILENAME | TYPE [NAME_PREFIX | -l label] | TYPE/NAME)
```



#### pod

指定pod查看一个API对象的细节



显示指定的资源或者资源组的详细信息

```
kubectl describe pods -l component=kube-apiserver -n kube-system
```

```
kubectl describe services myapp-svc
```





### log/logs 日志

pod内某容器的日志

```
kubectl log [-f] [-p] (POD|TYPE/NAME) [-c CONTAINER] [options] 
```

> -f 类似于tail -f



```
kubectl logs kube-apiserver-master.xuxuehua.com -n kube-system
```



#### -c 指定容器名称





### port-forward 端口转发

将本地的一个或着多个端口转发至指定的pod



### proxy API Server代理

能够访问Kubernetes API Server的代理





## 高级命令

### apply 实现声明

基于文件或者stdin 将配置应用于资源

```
kubectl apply -f nginx-deploy.yaml -f nginx-svc.yaml
```



### convert API转换

为不通的API版本转换配置文件



### patch 补丁更新

使用策略合并补丁更新资源字段





### replace 替换资源

基于文件或者stdin替换一个资源





## 设置命令



### annotate 更新注释

添加资源注释

```
kubectl annotate pods pod-example ilinux.io/created-by="cluster admin"
```



查看注解

```
kubectl describe pods pod-example | grep "Annotations"
```





### completion 补全码

输出指定的shell （bash） 的补全码



### label 资源标签

更新指定资源标签



#### 添加标签

为pod-example 添加env=production 标签

```
kubectl label pods/pod-example env=production
```



#### 覆盖标签 --overwrite

```
kubectl label pods/pod-with-labels env=testing --overwrite
```



#### nodes

设置标签以及标识

```
kubectl label nodes node01.xurick.com disktype=ssd
```



查看键名为SSD标签的node资源

```
kubectl get nodes -l 'disktype' -L disktype
```





## 其他命令



### api-versions API版本信息

以group/version格式打印服务器支持的API版本信息





### config 配置内容

配置kubeconfig文件的内容



### help 帮助

打印任意命令的帮助信息



### option 通用选项

#### -s / --server 指定API Server

指定API Server的地址和端口



#### --kubeconfig 文件路径

是用kubeconfig 文件路径，默认为`~/.kube/config`



#### --namespace 名称空间

命令执行的目标名称空间



### plugin 命令行插件

运行命令行插件



### version 版本信息

打印Kubernetes的服务器端和客户端版本信息














