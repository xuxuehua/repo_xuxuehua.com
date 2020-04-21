---
title: "ReplicaSet"
date: 2020-04-18 16:07
collection: 作业管理
---
[toc]





# ReplicaSet （Deployment 子集）

一个 ReplicaSet 对象，其实就是由副本数目的定义和一 个 Pod 模板组成的。不难发现，它的定义其实是 Deployment 的一个子集

确保给一个pod所指定数量的replicas 会一直运行

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp
  namespace: default
spec: #控制器的spec
  replicas: 2
  selector:
    matchLabels:
      app: myapp
      release: canary
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        release: canary
        environment: qa
    spec:  #pod 的spec
      containers:
      - name: myapp-container
        image: ikubernetes/myapp:v1
        ports:
        - name: http
          containerPort: 80
```





## 原理

Deployment 的控制器，实际上控制的是 ReplicaSet 的数目，以及每个 ReplicaSet 的属性。

而一个应用的版本，对应的正是一个 ReplicaSet;这个版本应用的 Pod 数量，则由 ReplicaSet 通过它自己的控制器(ReplicaSet Controller)来保证



