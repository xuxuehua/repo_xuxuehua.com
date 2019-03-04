---
title: "namespaces"
date: 2019-03-04 12:52
---


[TOC]



# 名称空间



## 查看

```
kubectl get namespaces
```



### 查看kube-system所有pod资源

```
kubectl get pods -n kube-system
```



## 创建

```
kubectl apply -f namespace-example.yaml
kubectl create namespace qa
```

