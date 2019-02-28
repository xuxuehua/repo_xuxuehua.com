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



## run

```
kubectl run nginx-deploy --image=nginx:1.12 --replicas=2
```



## expose 暴露服务

```
kubectl expose deployment/nginx --name=nginx-svc --port=80
```

```
kubectl expose deployments/myapp --type="NodePort" --port=80 --name=myapp
```





## create

```
kubectl create -f nignx-deploy.yaml -f nginx-svc.yaml
```



## apply 实现声明

```
kubectl apply -f nginx-deploy.yaml -f nginx-svc.yaml
```





## get

列出所有资源

```
kubectl get namespaces
```



查看多个资源

```
kubectl get pods,services -o wide
```



列出名称空间中拥有k8s-app标签名称的所有Pod 对象

```
kubectl get pods -l k8s-app -n kube-system
```



### -o yaml | json

```
kubectl get pods -l component=kube=apiserver -o yaml -n kube-system
```



### -o wide

```
kubectl get pods -o wide 
```





## describe 详细信息

```
kubectl describe pods -l component=kube-apiserver -n kube-system
```

```
kubectl describe services myapp-svc
```



## log 日志

```
kubectl log [-f] [-p] (POD|TYPE/NAME) [-c CONTAINER] [options] 
```

> -f 类似于tail -f



```
kubectl logs kube-apiserver-master.xuxuehua.com -n kube-system
```



### -c 指定容器名称



## exec 执行命令

```
kubectl exec kube-apiserver-master.xuxuehua.com -n kube-system --ps
```

> Pod对象中的容器里面运行ps命令



### -it 交互Shell

```
kubectl exec -it $POD_NAME /bin/sh
```



## delete 删除

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





## scale 扩容/缩容

```
kubectl scale deployments/myapp --replicas=3 
```

> replicas 指明对象创建或管理Pod对象的副本数量



```
kubectl scale deployments/myapp --replicas=2
```





## api-versions

获取api server 上的相关信息



## explain 

获取相关帮助



解释Pod资源的一级字段

```
kubectl explain pods
```



某对象下的二级字段, 三四级依此类推

```
kubectl explain pods.spec
```

