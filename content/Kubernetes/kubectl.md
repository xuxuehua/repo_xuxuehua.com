title: "kubectl"
date: 2019-02-22 22:32

---



[TOC]



# kubectl

Kubernetes API Server最常用的客户端程序之一，功能强大，能够几乎完成除了安装部署之外的所有管理操作

连接API Server 对K8s相关对象资源的增删改查





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





# 基础命令

## create 创建资源

通过文件或标准输入创建资源

```
kubectl create -f nignx-deploy.yaml -f nginx-svc.yaml
```



### `--record`

记录下你每次操作所执行的命令， 以方便后面查看





## delete 删除

基于文件名，stdin，资源或名字，以及资源和选择器删除资源



删除默认名称空间中ngnix-svc的Service 资源对象 

```
kubectl delete services nginx-svc
```



删除默认名称空间中所有的Deployment控制器

```
kubectl delete deployment --all
```



### --all

删除kube-public 名称空间中的所有pod对象

```
kubectl delete pods --all -n kube-public
```



删除所有名称空间的所有资源

```
kubectl delete all --all
```



### -f 指定配置

```
kubectl delete -f pod-example.yaml
```



### --grace-period

默认删除操作为30s，使用此参数自定义其时常

若使用0表示直接强制删除指定的资源，需同时使用`--force` 选项



## edit 编辑资源

kubectl edit 并不神秘，它不过是把 API 对象的内容下载到了本地文件， 让你修改完成后再提交上去

```
$ kubectl edit deployment/nginx-deployment ...
...
	spec: 
		containers:
		- name: nginx
			image: nginx:1.9.1 # 1.7.9 -> 1.9.1 
			ports:
			- containerPort: 80

deployment.extensions/nginx-deployment edited
```



## explain 资源文档

获取相关帮助



解释Pod资源的一级字段

```
kubectl explain pods
```



某对象下的二级字段, 三四级依此类推

```
kubectl explain pods.spec
```





## expose 暴露服务

基于rc，service，deployment或pod创建Service资源

```
kubectl expose (-f FILENAME | TYPE NAME) [--port=port] [--protocol=TCP|UDP|SCTP]
[--target-port=number-or-name] [--name=name] [--external-ip=external-ip-of-service] [--type=type]
[options]
```

> --port指service 端口， 一般使用众所周知的端口
>
> --target-port指pod端口
>
> 两个端口可以一样



```
[root@master ~]# kubectl expose deployment nginx-deploy --name=nginx --port=80 --target-port=80 --protocol=TCP
service/nginx exposed
[root@master ~]# kubectl get service
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP   3h34m
nginx        ClusterIP   10.96.216.63   <none>        80/TCP    10s
```

> 10.96.216.63 service对外地址，代理后端pod



## get 显示资源

即从Kubernetes里面获取指定的API对象



### controllerrevision 记录Controller版本

用于记录某种Controller对象的版本

```
$ kubectl get controllerrevision -n kube-system -l name=fluentd-elasticsearch
NAME                               CONTROLLER                             REVISION   AGE
fluentd-elasticsearch-64dc6799c9   daemonset.apps/fluentd-elasticsearch   2          1h
```

> 查看fluentd-elasticsearch对应的ControllerRevision

这个 ControllerRevision 对象，实际上是在 Data 字段保存了该版本对应的完整的 DaemonSet 的 API 对象。并且，在 Annotation 字段保存了创建这个对象所使用的 kubectl 命令

```
kubectl describe controllerrevision fluentd-elasticsearch-64dc6799c9 -n kube-system
```



列出所有资源

```
kubectl get namespaces
```



查看多个资源

```
kubectl get pods,services -o wide
```



### -l 标签选择器

列出名称空间中拥有k8s-app标签名称的所有Pod 对象

```
kubectl get pods -l k8s-app -n kube-system
```



#### 等值关系

```
=, !=, ==
```



#### 集合关系

```
KEY in (VALUE1, VALUE2,...)
```

```
KEY notin (VALUE1, VALUE2,...)
```

```
!KEY
```



### -L 标签列表

显示每一个对象的标签值

```
kubectl get pods -l 'env in (production,dev),!tier' -L env,tier
```



### -o 输出

#### yaml 

```
kubectl get pods -l component=kube=apiserver -o yaml -n kube-system
```



#### json 



#### wide 额外信息

显示资源的额外信息

```
kubectl get pods -o wide 
```



#### name 名称

仅仅打印资源名称



#### go-template go 模版

以自定义的go模版格式化输出API对象信息



#### custom-columns 自定义输出

自定义要输出的字段



### nodes

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

这个命令的好处就是，你可以不用像 kubectl edit 那样需要打开编辑器

```
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
        image: ikubernetes/myapp:v1
        ports:
        - name: http
          containerPort: 80
```



```
# kubectl set image deployment myapp-deploy myapp=ikubernetes/myapp:v3 && kubectl rollout pause deployment myapp-deploy
deployment.apps/myapp-deploy image updated
deployment.apps/myapp-deploy paused
```



```
root@master:~# kubectl set image deployment/nginx-deployment nginx=nginx:1.91
deployment.apps/nginx-deployment image updated
root@master:~# kubectl get rs
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-5bf87f5f59   4         4         4       60m
nginx-deployment-7789688b8f   3         3         0       6s
root@master:~# kubectl get rs
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-5bf87f5f59   4         4         4       61m
nginx-deployment-7789688b8f   3         3         0       12s
root@master:~# kubectl rollout undo deployment/nginx-deployment
deployment.apps/nginx-deployment rolled back
root@master:~# kubectl get rs
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-5bf87f5f59   5         5         4       61m
nginx-deployment-7789688b8f   0         0         0       51s
root@master:~# kubectl get rs
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-5bf87f5f59   5         5         5       61m
nginx-deployment-7789688b8f   0         0         0       53s
```





#### image

Deployment对应用进行版本控制

```
$ kubectl image deployment/nginx-deployment nginx=nginx:1.91
deployment.extensions/nginx-deployment image updated
```

> 这里的nginx:1.91 是错误的版本









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



```
[root@master ~]# kubectl scale --replicas=5 deployment myapp
deployment.extensions/myapp scaled
[root@master ~]# kubectl scale --replicas=1 deployment myapp
deployment.extensions/myapp scaled
```





### rollout 滚动更新

管理资源的滚动更新



#### history

查看Deployment 变更的对应版本

```
$ kubectl rollout history deployment/nginx-deployment
deployments "nginx-deployment"
REVISION    CHANGE-CAUSE
1           kubectl create -f nginx-deployment.yaml --record
2           kubectl edit deployment/nginx-deployment
3           kubectl set image deployment/nginx-deployment nginx=nginx:1.91
```



#### pause

由于Deployment进行的每一次更新操作，都会生成一个新的ReplicaSet对象，为此，可以指定多次更新操作之后，只生成一个ReplicaSet

即在更新Deployment之前，执行下列操作

```
$ kubectl rollout pause deployment/nginx-deployment
deployment.extensions/nginx-deployment paused
```

> Deployment将处于暂停状态，这样对Deployment的所有修改，都不会出发滚动更新



#### resume

恢复Deployment 滚动更新

```
[root@master ~]# kubectl rollout resume deployment myapp-deployment
deployment.extensions/myapp-deployment resumed
```

可以看到更新状况

```
[root@master ~]# kubectl rollout status deployment myapp-deployment
Waiting for deployment "myapp-deployment" rollout to finish: 1 out of 5 new replicas have been updated...
Waiting for deployment spec update to be observed...
Waiting for deployment spec update to be observed...
Waiting for deployment "myapp-deployment" rollout to finish: 1 out of 5 new replicas have been updated...
Waiting for deployment "myapp-deployment" rollout to finish: 1 out of 5 new replicas have been updated...
Waiting for deployment "myapp-deployment" rollout to finish: 2 out of 5 new replicas have been updated...
Waiting for deployment "myapp-deployment" rollout to finish: 2 out of 5 new replicas have been updated...
Waiting for deployment "myapp-deployment" rollout to finish: 2 out of 5 new replicas have been updated...
Waiting for deployment "myapp-deployment" rollout to finish: 3 out of 5 new replicas have been updated...
Waiting for deployment "myapp-deployment" rollout to finish: 3 out of 5 new replicas have been updated...
Waiting for deployment "myapp-deployment" rollout to finish: 4 out of 5 new replicas have been updated...
Waiting for deployment "myapp-deployment" rollout to finish: 4 out of 5 new replicas have been updated...
Waiting for deployment "myapp-deployment" rollout to finish: 4 out of 5 new replicas have been updated...
Waiting for deployment "myapp-deployment" rollout to finish: 4 out of 5 new replicas have been updated...
Waiting for deployment "myapp-deployment" rollout to finish: 1 old replicas are pending termination...
Waiting for deployment "myapp-deployment" rollout to finish: 1 old replicas are pending termination...
deployment "myapp-deployment" successfully rolled out
```



#### status

监听更新状态

```
[root@master ~]# kubectl rollout status deployment myapp-deployment
Waiting for deployment "myapp-deployment" rollout to finish: 1 out of 5 new replicas have been updated...
```



#### undo

撤销滚动版本, 默认为上一个版本



```
$ kubectl rollout undo deployment/nginx-deployment --to-revision=2
deployment.extensions/nginx-deployment
```

> 这里指定了滚动的版本号2



```
[root@master ~]# kubectl rollout history deployment myapp-deployment
deployment.extensions/myapp-deployment
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
3         <none>

[root@master ~]# kubectl rollout undo deployment myapp-deployment --to-revision=1
deployment.extensions/myapp-deployment rolled back
[root@master ~]#
[root@master ~]# kubectl rollout history deployment myapp-deployment
deployment.extensions/myapp-deployment
REVISION  CHANGE-CAUSE
2         <none>
3         <none>
4         <none>
```

> 这里的4 表示revision号码变动了



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



移除worker node

```
kubectl drain swarm1 --delete-local-data --force --ignore-daemonsets

kubectl delete node swarm1
```





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





#### service/svc

查看service 对象

```
[root@master ~]# kubectl describe svc nginx
Name:              nginx
Namespace:         default
Labels:            run=nginx-deploy
Annotations:       <none>
Selector:          run=nginx-deploy
Type:              ClusterIP
IP:                10.96.216.63
Port:              <unset>  80/TCP
TargetPort:        80/TCP
Endpoints:         10.244.2.2:80
Session Affinity:  None
Events:            <none>
```

> 这里指定了pod标签，因此才可以查看到对应的信息

```
[root@master ~]# kubectl get pods --show-labels
NAME                           READY   STATUS    RESTARTS   AGE   LABELS
client                         0/1     Error     0          19m   run=client
nginx-deploy-55d8d67cf-7zkxm   1/1     Running   0          9h    pod-template-hash=55d8d67cf,run=nginx-deploy
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

既可以创建，也可以更新

基于文件或者stdin 将配置应用于资源

```
kubectl apply -f nginx-deploy.yaml -f nginx-svc.yaml
```



### convert API转换

为不通的API版本转换配置文件



### patch 补丁更新

使用策略合并补丁更新资源字段

```
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
        image: ikubernetes/myapp:v1
        ports:
        - name: http
          containerPort: 80
```

```
# kubectl patch deployment myapp-deploy -p '{"spec":{"strategy":{"rollingUpdate":{"maxSurge":1,"maxUnavailable":0}}}}'
deployment.apps/myapp-deploy patched
```





### replace 替换资源

基于文件或者stdin替换一个资源

推荐使用apply 命令



## 设置命令



### annotate 资源注释

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



### g 资源标签

更新指定资源标签

```
kubectl label [--overwrite] (-f FILENAME | TYPE NAME) KEY_1=VAL_1 ... KEY_N=VAL_N
[--resource-version=version] [options]
```



#### 添加标签

为pod-example 添加env=production 标签

```
kubectl label pods/pod-example env=production
```



```
[root@master ~]# kubectl label pods pod-demo release=canary
pod/pod-demo labeled
[root@master ~]# kubectl get pods -l app --show-labels
NAME       READY   STATUS    RESTARTS   AGE     LABELS
pod-demo   2/2     Running   0          6m17s   app=myapp,release=canary,tier=frontend
```



#### 覆盖标签 --overwrite

```
[root@master ~]# kubectl label pods pod-demo release=canary
pod/pod-demo labeled
[root@master ~]# kubectl get pods -l app --show-labels
NAME       READY   STATUS    RESTARTS   AGE     LABELS
pod-demo   2/2     Running   0          6m17s   app=myapp,release=canary,tier=frontend
[root@master ~]# kubectl label pods pod-demo release=stable --overwrite
pod/pod-demo labeled
[root@master ~]# kubectl get pods -l app --show-labels
NAME       READY   STATUS    RESTARTS   AGE     LABELS
pod-demo   2/2     Running   0          7m13s   app=myapp,release=stable,tier=frontend
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














