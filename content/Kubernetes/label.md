---
title: "label"
date: 2019-03-07 13:20
---


[TOC]



# 标签

可以为资源在多个不同纬度实现灵活分组的管理功能



版本标签

```
"release": "stable", "release": "canary", "release": "beta"
```



架构标签

```
"tier": "frontend", "tier": "backend", "tier": "cache"
```



## 键名

键名通常由键前缀和键名组成

键前缀可选

kubernetes.io/ 前缀预留给Kubernetes核心组件使用



## Label Selector  标签选择器

很多object可能有相同的label通过label selector， 客户端可以指定object集合，通过label selector 对object的集合进行操作



### 特别逻辑

多个选择器之间的逻辑关系为与

空值的标签选择器意味着每个资源都会被选上

空的标签选择器将无法选出任何资源







### 等值关系选择 Equality-based

可以使用`=`, `!=`操作， 逗号分隔多个表达式



#### `=`

```
kubectl get pods -l 'environment=production,tier=frontend'
```



#### `!=`

```
kubectl get pods -l "env!=qa" -L env
```



### 集合关系选择 Set-based

可以使用`in`， `notin`， `exists`操作符



#### `in`

```
kubectl get pods -l 'environment in (production), tier in (frontend)'
```



#### `notin`

```
kubectl get pods -l 'environment notin (production)'
```



#### `exists`

```

```



## 调用



### -l 标签选择器

```
kubectl get pods -l "env!=qa" -L env
```



### --show-labels

在`kubectl get pods --show-labels`  的方式调用



### -L 标签列表

-L key1, key2 ....

```
kubectl get pods -L env,tier
```



## nodeSelector

Pod节点选择器时标签以及标签选择器的一种应用，能够让Pod对象基于集群中工作节点的标签来挑选倾向运行的目标节点

