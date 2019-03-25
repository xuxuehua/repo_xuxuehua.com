---
title: "authorization"
date: 2019-03-23 13:33
---


[TOC]



# Kubernetes 授权 RBAC

基于RBAC (Role-Based Access Control) 授权的工作机制



## Role 

其实是一组规则，定义了一组对Kubernetes API对象的操作权限



```
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: mynamespace
  name: example-role
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

> 这个 Role 对象指定了它能产生作用的 Namepace 是：mynamespace。





## Subject

被使用者，既可以是“人”，也可以是“机器”，也可以是在 Kubernetes 里定义的“用户”。



## RoleBinding

定义了“被作用者”和“角色”的绑定关系。

RoleBinding 本身也是一个 Kubernetes 的 API 对象。



```
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: example-rolebinding
  namespace: mynamespace
subjects:
- kind: User
  name: example-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: example-role
  apiGroup: rbac.authorization.k8s.io
```

> RoleBinding 对象里定义了一个 subjects 字段，即“被作用者”。它的类型是 User，即 Kubernetes 里的用户。这个用户的名字是 example-user。
>
> roleRef 字段。正是通过这个字段，RoleBinding 对象就可以直接通过名字，来引用我们前面定义的 Role 对象（example-role），从而定义了“被作用者（Subject）”和“角色（Role）”之间的绑定关系。

