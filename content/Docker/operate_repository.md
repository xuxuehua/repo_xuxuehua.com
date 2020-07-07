---
title: "operate_repository"
date: 2018-10-24 16:35
---


[TOC]


# operate_repository



## registry 注册服务器

一个容易混淆的概念是注册服务器（Registry）。实际上注册服务器是管理仓库的具体服务器，每个服务器上可以有多个仓库，而每个仓库下面有多个镜像。从这方面来说，仓库可以被认为是一个具体的项目或目录。例如对于仓库地址dl.dockerpool.com/ubuntu来说，dl.dockerpool.com 是注册服务器地址，ubuntu是仓库名。



## Docker Hub

* 通过 docker search 命令来查找官方仓库中的镜像
* 利用 docker pull 命令来将它下载到本地
* 在查找的时候通过 -s N 参数可以指定仅显示评价为 N 星以上的镜像（新版本Docker推荐使用--filter=stars=N参数）



### push 到docker hub

容器制成镜像

```
docker commit <exiting-Container> <hub-user>/<repo-name>[:<tag>]
```



若以存在镜像，打上标签

```
docker tag <existing-image> <hub-user>/<repo-name>[:<tag>]
docker tag 26ac9649d7da wdxtub/wdx-whale:latest
```



然后再 docker images 一次



命令登录 

```
docker login --username=yourhubusername --email=youremail@company.com
```



push

```
docker push <hub-user>/<repo-name>
docker push wdxtub/wdx-whale
```







## docker-registry 私有仓库

docker-registry 是官方提供的工具，可以用于构建私有的镜像仓库



安装运行 docker-registry

* 可以通过获取官方 registry 镜像来运行。

```
$ docker run -d -p 5000:5000 --restart=always --name registry registry
```

> 这将使用官方的 registry 镜像来启动本地的私有仓库。默认情况下，仓库会被创建在容器的 /var/lib/registry 下。可以通过 -v 参数来将镜像文件存放在本地的指定路径。例如下面的例子将上传的镜像放到本地的 /opt/data/registry 目录。


```
$ docker run -d \
    -p 5000:5000 \
    -v /opt/data/registry:/var/lib/registry \
    registry
```

> 在私有仓库上传、搜索、下载镜像
> 创建好私有仓库之后，就可以使用 docker tag 来标记一个镜像，然后推送它到仓库。例如私有仓库地址为 127.0.0.1:5000。





先在本机查看已有的镜像。

```
$ docker images
REPOSITORY                        TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
ubuntu                            latest              ba5877dc9bec        6 weeks ago         192.7 MB
使用 docker tag 将 ubuntu:latest 这个镜像标记为 127.0.0.1:5000/ubuntu:latest（格式为 docker tag IMAGE[:TAG] [REGISTRYHOST/][USERNAME/]NAME[:TAG]）。

$ docker tag ubuntu:latest 127.0.0.1:5000/ubuntu:latest
$ docker images
REPOSITORY                        TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
ubuntu                            latest              ba5877dc9bec        6 weeks ago         192.7 MB
127.0.0.1:5000/ubuntu:latest      latest              ba5877dc9bec        6 weeks ago         192.7 MB
```



### push 上传标记的镜像

使用 docker push 上传标记的镜像。

```
$ docker push 127.0.0.1:5000/ubuntu:latest
The push refers to repository [127.0.0.1:5000/ubuntu]
373a30c24545: Pushed
a9148f5200b0: Pushed
cdd3de0940ab: Pushed
fc56279bbb33: Pushed
b38367233d37: Pushed
2aebd096e0e2: Pushed
latest: digest: sha256:fe4277621f10b5026266932ddf760f5a756d2facd505a94d2da12f4f52f71f5a size: 1568
```

* 用 curl 查看仓库中的镜像。

```
$ curl 127.0.0.1:5000/v2/_catalog
{"repositories":["ubuntu"]}
这里可以看到 {"repositories":["ubuntu"]}，表明镜像已经被成功上传了。
```


* 先删除已有镜像，再尝试去下载这个镜像。

```
$ docker rmi 127.0.0.1:5000/ubuntu:latest

$ docker pull 127.0.0.1:5000/ubuntu:latest
Pulling repository 127.0.0.1:5000/ubuntu:latest
ba5877dc9bec: Download complete
511136ea3c5a: Download complete
9bad880da3d2: Download complete
25f11f5fb0cb: Download complete
ebc34468f71d: Download complete
2318d26665ef: Download complete

$ docker images
REPOSITORY                         TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
127.0.0.1:5000/ubuntu:latest       latest              ba5877dc9bec        6 weeks ago  
```



## 指定registry地址

找到可用IP

```
dig @114.114.114.114 registry-1.docker.io
```

修改`/etc/hosts`强制`docker.io`相关的域名解析到其它可用IP

```
52.87.94.70 registry-1.docker.io
```





## 国内docker hub

/etc/docker/daemon.json

```
{ 
"registry-mirrors": ["https://registry.docker-cn.com", "http://hub-mirror.c.163.com","https://docker.mirrors.ustc.edu.cn"] 
}
```