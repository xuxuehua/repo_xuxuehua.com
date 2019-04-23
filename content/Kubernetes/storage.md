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

将pod所在的宿主机上的文件或者目录建立存储关系，但是存储数据不会在pod删除的时候一同删除



在work node上创建目录以及文件

```
[root@localhost ~]# mkdir -p /data/pod/volume1
[root@localhost ~]# echo "node-localhost" > /data/pod/volume1/index.html
```



```
[root@master volumes]# cat pod-hostpath-vol.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-vol-hostpath
  namespace: default
spec:
  containers:
  - name: myapp
    image: ikubernetes/myapp:v1
    volumeMounts:
    - name: html
      mountPath: /usr/share/nginx/html/
  volumes:
  - name: html
    hostPath:
      path: /data/pod/volume1
      type: DirectoryOrCreate
```



查看文件

```
[root@master volumes]# kubectl get pods -o wide
NAME               READY   STATUS    RESTARTS   AGE   IP             NODE                    NOMINATED NODE   READINESS GATES
pod-vol-hostpath   1/1     Running   0          16s   10.244.2.237   localhost.localdomain   <none>           <none>
[root@master volumes]# curl 10.244.2.237
node-localhost
```



#### NFS 存储



部署storage 节点

```
yum -y install nfs-utils

# 创建共享目录
mkdir -p /data/volumes
```

```
[root@store1 ~]# cat /etc/exports
/data/volumes   0.0.0.0/0(rw,no_root_squash)
[root@store1 ~]# systemctl start nfs
[root@store1 ~]# systemctl status nfs
● nfs-server.service - NFS server and services
   Loaded: loaded (/usr/lib/systemd/system/nfs-server.service; disabled; vendor preset: disabled)
   Active: active (exited) since Mon 2019-04-15 10:56:56 UTC; 59s ago
  Process: 3653 ExecStartPost=/bin/sh -c if systemctl -q is-active gssproxy; then systemctl restart gssproxy ; fi (code=exited, status=0/SUCCESS)
  Process: 3639 ExecStart=/usr/sbin/rpc.nfsd $RPCNFSDARGS (code=exited, status=0/SUCCESS)
  Process: 3637 ExecStartPre=/usr/sbin/exportfs -r (code=exited, status=0/SUCCESS)
 Main PID: 3639 (code=exited, status=0/SUCCESS)
   CGroup: /system.slice/nfs-server.service

Apr 15 10:56:56 store1 systemd[1]: Starting NFS server and services...
Apr 15 10:56:56 store1 systemd[1]: Started NFS server and services.
```



worker node 部署

```
[root@us ~]# yum -y install nfs-utils
[root@us ~]# mount -t nfs 202.182.104.162:/data/volumes /mnt



[root@store1 ~]# cat /etc/exports
/data/volumes   *(rw,no_root_squash)
```

> *表示允许所有mount请求，指定网段替换为192.168.1.0/24



```
[root@master volumes]# cat pod-vol-nfs.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-vol-nfs
  namespace: default
spec:
  containers:
  - name: myapp
    image: ikubernetes/myapp:v1
    volumeMounts:
    - name: html
      mountPath: /usr/share/nginx/html/
  volumes:
  - name: html
    nfs:
      path: /data/volumes
      server: 202.182.104.162
[root@master volumes]# kubectl apply -f pod-vol-nfs.yaml
pod/pod-vol-nfs created
[root@master volumes]# kubectl get pods -o wide
NAME          READY   STATUS    RESTARTS   AGE   IP             NODE                    NOMINATED NODE   READINESS GATES
pod-vol-nfs   1/1     Running   0          51s   10.244.2.240   localhost.localdomain   <none>           <none>
```



在存储节点的volumes下创建index文件

```
[root@store1 ~]# cd /data/volumes/
[root@store1 volumes]# ls
[root@store1 volumes]# echo "this is store1" >> index.html
```

```
[root@master volumes]# curl 10.244.2.240
this is store1
```



### PVC 

PersistentVolumeClaim

![img](https://snag.gy/lwaxUs.jpg)



PVC 的存储卷必须与当前名称空间的PVC建立绑定关系

PVC与存储空间PV建立绑定关系

真实的存储空间(NFS, iSCSI) 映射到PV

PVC 是标准K8s资源，存储在Etcd中，只有pod才会存储在节点上





#### example



存储卷定义

```
[root@store1 ~]# cd /data/volumes/
[root@store1 volumes]# mkdir v{1,2,3,4,5}
[root@store1 volumes]# ls
index.html  v1  v2  v3  v4  v5
[root@store1 volumes]# vim /etc/exports
[root@store1 volumes]# exportfs -arv
exporting *:/data/volumes/v5
exporting *:/data/volumes/v4
exporting *:/data/volumes/v3
exporting *:/data/volumes/v2
exporting *:/data/volumes/v1
[root@store1 volumes]# showmount -e
Export list for store1:
/data/volumes/v5 *
/data/volumes/v4 *
/data/volumes/v3 *
/data/volumes/v2 *
/data/volumes/v1 *
```



NFS格式的PV创建

```
[root@master volumes]# cat pv-demo.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv001
  labels:
    name: pv001
spec:
  nfs:
    path: /data/volumes/v1
    server: 202.182.104.162
  accessModes: ["ReadWriteMany", "ReadWriteOnce"]
  capacity:
    storage: 1Gi
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv002
  labels:
    name: pv002
spec:
  nfs:
    path: /data/volumes/v2
    server: 202.182.104.162
  accessModes: ["ReadWriteOnce"]
  capacity:
    storage: 2Gi
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv003
  labels:
    name: pv003
spec:
  nfs:
    path: /data/volumes/v3
    server: 202.182.104.162
  accessModes: ["ReadWriteMany", "ReadWriteOnce"]
  capacity:
    storage: 3Gi
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv004
  labels:
    name: pv004
spec:
  nfs:
    path: /data/volumes/v4
    server: 202.182.104.162
  accessModes: ["ReadWriteMany", "ReadWriteOnce"]
  capacity:
    storage: 4Gi
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv005
  labels:
    name: pv005
spec:
  nfs:
    path: /data/volumes/v5
    server: 202.182.104.162
  accessModes: ["ReadWriteMany", "ReadWriteOnce"]
  capacity:
    storage: 5Gi
---
[root@master volumes]# kubectl apply -f pv-demo.yaml
persistentvolume/pv001 created
persistentvolume/pv002 created
persistentvolume/pv003 created
persistentvolume/pv004 created
persistentvolume/pv005 created
[root@master volumes]# kubectl get pv
NAME    CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
pv001   1Gi        RWO,RWX        Retain           Available                                   7s
pv002   2Gi        RWO            Retain           Available                                   7s
pv003   3Gi        RWO,RWX        Retain           Available                                   7s
pv004   4Gi        RWO,RWX        Retain           Available                                   7s
pv005   5Gi        RWO,RWX        Retain           Available                                   7s
```

> 定义pv不能定义namespace，pv属于集群级别的，所有名称空间共用







定义pvc

```
[root@master volumes]# cat pod-vol-pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypvc
  namespace: default
spec:
  accessModes: ["ReadWriteMany"]
  resources:
    requests:
      storage: 3Gi
---
apiVersion: v1
kind: Pod
metadata:
  name: pod-vol-pvc
  namespace: default
spec:
  containers:
  - name: myapp
    image: ikubernetes/myapp:v1
    volumeMounts:
    - name: html
      mountPath: /usr/share/nginx/html/
  volumes:
  - name: html
    persistentVolumeClaim:
      claimName: mypvc

[root@master volumes]# kubectl  apply -f pod-vol-pvc.yaml
persistentvolumeclaim/mypvc created
pod/pod-vol-pvc created
[root@master volumes]# kubectl get pv
kNAME    CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM           STORAGECLASS   REASON   AGE
pv001   1Gi        RWO,RWX        Retain           Available                                           44m
pv002   2Gi        RWO            Retain           Available                                           44m
pv003   3Gi        RWO,RWX        Retain           Bound       default/mypvc                           44m
pv004   4Gi        RWO,RWX        Retain           Available                                           44m
pv005   5Gi        RWO,RWX        Retain           Available                                           44m
[root@master volumes]# kubectl get pvc
NAME    STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
mypvc   Bound    pv003    3Gi        RWO,RWX                       10s
```







### storageClass

通过存储类，将尚未做成pv的磁盘空间，做成一个分类



## CSI













## ConfigMap

特殊类型的存储卷，存储配置信息，启动pod的时候，可以共享同一个ConfigMap资源

实现外部应用注入到集群内部应用

可以通过传递配置文件参数将配置信息传递到pod内部

也可以通过挂载configMap存储卷到Pod的指定目录中，实现配置文件信息读取



明文存储配置信息信息，所以不能存储敏感信息

属于名称空间的资源



### 创建configMap

#### 命令行

```
[root@master ~]# kubectl create configmap nginx-config  --from-literal=nginx_port=80 --from-literal=server_name=myapp.xuxuehua.com
configmap/nginx-config created
[root@master ~]# kubectl  get cm
NAME           DATA   AGE
nginx-config   2      3s
[root@master ~]# kubectl describe cm nginx-config
Name:         nginx-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
nginx_port:
----
80
server_name:
----
myapp.xuxuehua.com
Events:  <none>
```



```
[root@master configmap]# kubectl create configmap nginx-www --from-file=./www.conf
configmap/nginx-www created
[root@master configmap]# kubectl get cm
NAME           DATA   AGE
nginx-config   2      6m23s
nginx-www      1      11s
[root@master configmap]# kubectl describe cm nginx-www
Name:         nginx-www
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
www.conf:
----
server {
   server_name myapp.xuxuehua.com;
   listen 80;
   root /data/web/html/;
}

Events:  <none>
```



#### 通过env方式调用

```
[root@master configmap]# cat pod-configmap.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-cm-1
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
    env:
    - name: NGINX_SERVER_PORT
      valueFrom:
        configMapKeyRef:
          name: nginx-config
          key: nginx_port
    - name: NGINX_SERVER_NAME
      valueFrom:
        configMapKeyRef:
          name: nginx-config
          key: server_name
[root@master configmap]# kubectl  apply -f pod-configmap.yaml
pod/pod-cm-1 created
[root@master configmap]# kubectl get pods
NAME          READY   STATUS    RESTARTS   AGE
pod-cm-1      1/1     Running   0          5s
[root@master configmap]# kubectl exec -it pod-cm-1 -- /bin/sh
/ # printenv | grep -i nginx
NGINX_SERVER_PORT=80
NGINX_SERVER_NAME=myapp.xuxuehua.com
NGINX_VERSION=1.12.2
```



#### 通过volume 调用

```
[root@master configmap]# cat pod-configmap-2.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-cm-2
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
    - name: nginxconf
      mountPath: /etc/nginx/config.d/
      readOnly: true
  volumes:
  - name: nginxconf
    configMap:
      name: nginx-config
[root@master configmap]# kubectl  apply -f pod-configmap-2.yaml
pod/pod-cm-2 created

[root@master configmap]# kubectl  get pods
NAME          READY   STATUS    RESTARTS   AGE
pod-cm-2      1/1     Running   0          57s
pod-vol-nfs   1/1     Running   0          4d16h
pod-vol-pvc   1/1     Running   0          3d
```



#### 手动nginx 重载

```
[root@master configmap]# cat pod-configmap-3.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-cm-3
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
    - name: nginxconf
      mountPath: /etc/nginx/conf.d/
      readOnly: true
  volumes:
  - name: nginxconf
    configMap:
      name: nginx-www
[root@master configmap]# kubectl apply -f pod-configmap-3.yaml
pod/pod-cm-3 created
[root@master configmap]# kubectl  get pods
NAME          READY   STATUS    RESTARTS   AGE
pod-cm-3      1/1     Running   0          4s
pod-vol-nfs   1/1     Running   0          4d16h
pod-vol-pvc   1/1     Running   0          3d
[root@master configmap]# kubectl exec -it pod-cm-3 -- /bin/sh
/ # ls /etc/nginx/conf.d/
www.conf
/ # cat /etc/nginx/conf.d/www.conf
server {
        server_name myapp.xuxuehua.com;
        listen 80;
        root /data/web/html/;
}
/ # mkdir /data/web/html/ -p
/ # echo "<h1>Nginx Server configured by CM</h1>" >  /data/web/html/index.html

```





节点测试

```
[root@localhost ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.244.3.3  myapp.xuxuehua.com
[root@localhost ~]# curl myapp.xuxuehua.com
<h1>Nginx Server configured by CM</h1>
```



##### 动态修改



编辑监听端口为8080

```
[root@master configmap]# kubectl edit cm nginx-www
configmap/nginx-www edited
www.conf: "server {\n\tserver_name myapp.xuxuehua.com;\n\tlisten 8080;\n \troot /data/web/html/;\n}\n"
```



稍等后，端口变化了

```
[root@master configmap]# kubectl  exec -it pod-cm-3 -- /bin/sh
/ # cat /etc/nginx/conf.d/www.conf
server {
        server_name myapp.xuxuehua.com;
        listen 8080;
        root /data/web/html/;
}

# 需要重置一下监听端口
/ # netstat -tunpl
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      1/nginx: master pro
/ # nginx -s reload
2019/04/20 04:27:19 [notice] 35#35: signal process started
/ # netstat -tunpl
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:8080            0.0.0.0:*               LISTEN      1/nginx: master pro
```

> 这里可以通过调用脚本的方式，做自动化部署



修改完成

```
[root@localhost ~]# curl myapp.xuxuehua.com
curl: (7) Failed connect to myapp.xuxuehua.com:80; Connection refused
[root@localhost ~]# curl myapp.xuxuehua.com:8080
<h1>Nginx Server configured by CM</h1>
```









## secret

实现和ConfigMap类似，只是存储的数据都是加密的(Base64编码)





### generic 类型

```
[root@master configmap]# kubectl  create secret generic mysql-root-password --from-literal=password=MyP@ss123
secret/mysql-root-password created
[root@master configmap]# kubectl get secret
NAME                  TYPE                                  DATA   AGE
default-token-fvb6d   kubernetes.io/service-account-token   3      6d14h
mysql-root-password   Opaque                                1      6s
[root@master configmap]# kubectl describe secret mysql-root-password
Name:         mysql-root-password
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
password:  9 bytes
```

> 无法看到明文密码



但是base64的伪加密

```
[root@master configmap]# kubectl get secret mysql-root-password -o yaml
apiVersion: v1
data:
  password: TXlQQHNzMTIz
kind: Secret
metadata:
  creationTimestamp: "2019-04-20T04:49:08Z"
  name: mysql-root-password
  namespace: default
  resourceVersion: "812265"
  selfLink: /api/v1/namespaces/default/secrets/mysql-root-password
  uid: a1eb882c-6327-11e9-8bd1-00163e5f32f0
type: Opaque
[root@master configmap]# echo TXlQQHNzMTIz | base64 -d
MyP@ss123[root@master configmap]#
```





#### 通过env注入到pod中

```
[root@master configmap]# cat pod-secret-1.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-secret-1
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
    env:
    - name: MYSQL_ROOT_PASSWORD
      valueFrom:
        secretKeyRef:
          name: mysql-root-password
          key: password
[root@master configmap]# kubectl apply -f pod-secret-1.yaml
pod/pod-secret-1 created
[root@master configmap]# kubectl  exec pod-secret-1 -- printenv
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=pod-secret-1
MYSQL_ROOT_PASSWORD=MyP@ss123
```

> 这里也是解密的明文密码









