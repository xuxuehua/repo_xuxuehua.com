---
title: "storage"
date: 2019-04-14 14:43
---


[TOC]



# 配置和存储 Config & Storage

本地网络存储，如SAN (iSCSI), NAS (nfs, cifs)

分布式存储集群，支持多种存储确保存储资源持久化，如GlusterFS，ceph RBD， Flocker

云存储，如EBS， Azure Disk 



使用ConfigMap资源能够以环境变量或存储卷的方式接入到Pod中，还可以被同类Pod共享，不适合存储密钥等敏感数据











## Volume



在Pod上要定义volume，volume要指明关联到哪个存储卷上

在容器中使用volumeMounts，讲存储卷挂载才可使用



### emptyDir 最简单

最简单的一种存储卷

```
[root@master volumes]# cat pod-vol-demo.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-vol-demo
  namespace: default
  labels:
    app: myapp
    tier: frontend
  annotations:
    xuxuehua.com/created-by: "cluster admin"
spec:
  containers:
  - name: myapp
    image: ikubernetes/myapp:v1
    ports:
    - name: http
      containerPort: 80
    volumeMounts:
    - name: html
      mountPath: /data/web/html/
  - name: busybox
    image: busybox:latest
    imagePullPolicy: IfNotPresent
    volumeMounts:
    - name: html
      mountPath: /data/
    command:
    - "/bin/sh"
    - "-c"
    - "sleep 3600"
  volumes:
  - name: html
    emptyDir: {}
```



```
[root@master volumes]# kubectl apply -f pod-vol-demo.yaml
pod/pod-vol-demo created
[root@master volumes]# kubectl get pods
NAME           READY   STATUS    RESTARTS   AGE
pod-vol-demo   2/2     Running   0          24s


# 使用mount命令可以查看到被挂载的存储卷
[root@master volumes]# kubectl exec -it pod-vol-demo -c busybox -- /bin/sh
/ # mount
/dev/mapper/cl-root on /data type xfs (rw,seclabel,relatime,attr2,inode64,noquota)

/ # echo $(date) >> /data/index.html
/ # cat /data/index.html

Sun Apr 14 08:43:29 UTC 2019
```



也可以在另一个pod 中查看到该文件

```
[root@master volumes]# kubectl exec -it pod-vol-demo -c myapp -- /bin/sh
/ # cat /data/web/html/index.html

Sun Apr 14 08:43:29 UTC 2019
```





#### 存储卷在pod 容器间共享

```
[root@master volumes]# cat pod-vol-demo.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-vol-demo
  namespace: default
  labels:
    app: myapp
    tier: frontend
  annotations:
    xuxuehua.com/created-by: "cluster admin"
spec:
  containers:
  - name: myapp
    image: ikubernetes/myapp:v1
    imagePullPolicy: IfNotPresent
    ports:
    - name: http
      containerPort: 80
    volumeMounts:
    - name: html
      mountPath: /usr/share/nginx/html/
  - name: busybox
    image: busybox:latest
    imagePullPolicy: IfNotPresent
    volumeMounts:
    - name: html
      mountPath: /data/
    command: ["/bin/sh"]
    args: ["-c", "while true; do echo $(date) >> /data/index.html; sleep 2 ; done"]
  volumes:
  - name: html
    emptyDir: {}
```



```
[root@master volumes]# kubectl apply -f pod-vol-demo.yaml
pod/pod-vol-demo created
[root@master volumes]# kubectl get pods
NAME           READY   STATUS    RESTARTS   AGE
pod-vol-demo   2/2     Running   0          13s
[root@master volumes]# kubectl  get pods -o wide
NAME           READY   STATUS    RESTARTS   AGE   IP             NODE                    NOMINATED NODE   READINESS GATES
pod-vol-demo   2/2     Running   0          27s   10.244.2.236   localhost.localdomain   <none>           <none>
[root@master volumes]# curl 10.244.2.236
Sun Apr 14 12:10:17 UTC 2019
Sun Apr 14 12:10:19 UTC 2019
Sun Apr 14 12:10:21 UTC 2019
Sun Apr 14 12:10:23 UTC 2019
Sun Apr 14 12:10:25 UTC 2019
Sun Apr 14 12:10:27 UTC 2019
```



### gitRepo

把git 仓库中的内容clone 到本地上，并且作为存储卷定义到pod上

但本质上也是emptyDir

可以使用sidecar定时clone 到本地，保持数据的更新



### hostPath 重要

将pod所在的宿主机上的文件或者目录建立存储关系，但是存储数据不会在pod删除的时候，一同删除







## CSI



## ConfigMap



## DownwardAPI



