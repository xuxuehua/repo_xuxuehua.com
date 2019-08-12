---
title: "storage"
date: 2019-08-11 12:14
---
[TOC]



# Docker 数据管理



## 数据卷

- 数据卷是一个可供一个或多个容器使用的特殊目录
- 数据卷可以在容器之间共享和重用
- 对数据卷的修改会立马生效
- 对数据卷的更新，不会影响镜像
- 数据卷默认会一直存在，即使容器被删除

> 注意：数据卷的使用，类似于 Linux 下对目录或文件进行 mount，镜像中的被指定为挂载点的目录中的文件会隐藏掉，能显示看的是挂载的数据卷。



### 创建一个数据卷

`-v `

`-–mount`

```
$ docker volume create my-vol
```



### 查看所有的数据卷

```
$ docker volume ls

local               my-vol

```

```
$ docker volume inspect my-vol
[
    {
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/my-vol/_data",
        "Name": "my-vol",
        "Options": {},
        "Scope": "local"
    }
]
```



#### 查看数据卷的具体信息

在主机里使用以下命令可以查看 web 容器的信息

```
$ docker inspect web
```



### 启动一个挂载数据卷的容器

使用 --mount 标记来将数据卷挂载到容器里

```
$ docker run -d -P \
    --name web \
    --mount source=my-vol,target=/webapp \
    training/webapp \
    python app.py
```



### 挂载一个主机目录作为数据卷

使用 --mount 标记可以指定挂载一个本地主机的目录到容器中去。

```
$ docker run -d -P \
    --name web \
    # -v /src/webapp:/opt/webapp \
    --mount type=bind,source=/src/webapp,target=/opt/webapp \
    training/webapp \
    python app.py
```

Docker 挂载主机目录的默认权限是 读写，用户也可以通过增加 readonly 指定为 只读。

```
$ docker run -d -P \
    --name web \
    # -v /src/webapp:/opt/webapp:ro \
    --mount type=bind,source=/src/webapp,target=/opt/webapp,readonly \
    training/webapp \
    python app.py
```



### 挂载一个本地主机文件作为数据卷

--mount 标记也可以从主机挂载单个文件到容器中

```
$ docker run --rm -it \
   # -v $HOME/.bash_history:/root/.bash_history \
   --mount type=bind,source=$HOME/.bash_history,target=/root/.bash_history \
   ubuntu:17.10 \
   bash

root@2affd44b4667:/# history
1  ls
2  diskutil list
```



### 删除数据卷

```
$ docker volume rm my-vol
```

> 数据卷是被设计用来持久化数据的，它的生命周期独立于容器，Docker 不会在容器被删除后自动删除数据卷，并且也不存在垃圾回收这样的机制来处理没有任何容器引用的数据卷。

如果需要在删除容器的同时移除数据卷。可以在删除容器的时候使用 docker rm -v 这个命令。

无主的数据卷可能会占据很多空间，要清理请使用以下命令

```
$ docker volume prune
```





# Docker Machine 项目

Docker Machine 是 Docker 官方编排（Orchestration）项目之一，负责在多种平台上快速安装 Docker 环境。

## 安装

### macOS/Windows

```
$ docker-machine -v
docker-machine version 0.13.0, build 9ba6da9
```



### Linux

```
curl -L https://github.com/docker/machine/releases/download/v0.13.0/docker-machine-`uname -s`-`uname -m` >/tmp/docker-machine &&
chmod +x /tmp/docker-machine &&
sudo cp /tmp/docker-machine /usr/local/bin/docker-machine
```

- 完成后，查看版本信息

```
$ docker-machine -v
docker-machine version 0.13.0, build 9ba6da9
```



## Docker Machines 使用

- Docker Machine 支持多种后端驱动，包括虚拟机、本地主机和云平台等。

### 本地主机实例

- 使用 virtualbox 类型的驱动，创建一台 Docker 主机，命名为 test。

```
$ docker-machine create -d virtualbox test
```

- 查看主机

```
$ docker-machine ls

NAME      ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER       ERRORS
test      -        virtualbox   Running   tcp://192.168.99.187:2376           v17.10.0-ce
```

# Docker Swarm

Docker Swarm 是 Docker 官方三剑客项目之一，提供 Docker 容器集群服务，是 Docker 官方对容器云生态进行支持的核心方案。





## Docker Machine

将主机配置成可以加入到Swarm集群中里面



## Docker Compose

仅适合单机的容器编排工具



# etcd

etcd 是 CoreOS 团队发起的一个管理配置信息和服务发现（service discovery）的项目



etcd 是 CoreOS 团队于 2013 年 6 月发起的开源项目，它的目标是构建一个高可用的分布式键值（key-value）数据库，基于 Go 语言实现。



受到 Apache ZooKeeper 项目和 doozer 项目的启发，etcd 在设计的时候重点考虑了下面四个要素：
简单：支持 REST 风格的 HTTP+JSON API
安全：支持 HTTPS 方式的访问
快速：支持并发 1k/s 的写操作
可靠：支持分布式结构，基于 Raft 的一致性算法



一般情况下，用户使用 etcd 可以在多个节点上启动多个实例，并添加它们为一个集群。同一个集群中的 etcd 实例将会保持彼此信息的一致性。



## etcd 安装

- etcd 基于 Go 语言实现，因此，用户可以从 项目主页 下载源代码自行编译，也可以下载编译好的二进制文件，甚至直接使用制作好的 Docker 镜像文件来体验。



### 二进制文件方式下载

- 编译好的二进制文件都在 github.com/coreos/etcd/releases 页面，用户可以选择需要的版本，或通过下载工具下载

```
curl -L  https://github.com/coreos/etcd/releases/download/v3.1.11/etcd-v3.1.11-linux-amd64.tar.gz -o etcd-v3.1.11-linux-amd64.tar.gz
tar xzvf etcd-v3.1.11-linux-amd64.tar.gz
cd etcd-v3.1.11-linux-amd64.tar.gz
```

- 其中 etcd 是服务主文件，etcdctl 是提供给用户的命令客户端，etcd-migrate 负责进行迁移。
  推荐通过下面的命令将三个文件都放到系统可执行目录 /usr/local/bin/ 或 /usr/bin/。

```
$ sudo cp etcd* /usr/local/bin/
```

- 运行 etcd，将默认组建一个两个节点的集群。数据库服务端默认监听在 2379 和 4001 端口，etcd 实例监听在 2380 和 7001 端口。显示类似如下的信息：

```
[root@nagios etcd-v3.1.11-linux-amd64]# etcd
2017-12-02 08:28:04.518576 I | etcdmain: etcd Version: 3.1.11
2017-12-02 08:28:04.518616 I | etcdmain: Git SHA: 960f4604b
2017-12-02 08:28:04.518619 I | etcdmain: Go Version: go1.8.5
2017-12-02 08:28:04.518622 I | etcdmain: Go OS/Arch: linux/amd64
2017-12-02 08:28:04.518626 I | etcdmain: setting maximum number of CPUs to 1, total number of available CPUs is 1
2017-12-02 08:28:04.518632 W | etcdmain: no data-dir provided, using default data-dir ./default.etcd
2017-12-02 08:28:04.518895 I | embed: listening for peers on http://localhost:2380
2017-12-02 08:28:04.519026 I | embed: listening for client requests on localhost:2379
2017-12-02 08:28:04.531349 I | etcdserver: name = default
2017-12-02 08:28:04.531370 I | etcdserver: data dir = default.etcd
2017-12-02 08:28:04.531375 I | etcdserver: member dir = default.etcd/member
2017-12-02 08:28:04.531379 I | etcdserver: heartbeat = 100ms
2017-12-02 08:28:04.531382 I | etcdserver: election = 1000ms
2017-12-02 08:28:04.531385 I | etcdserver: snapshot count = 10000
2017-12-02 08:28:04.531392 I | etcdserver: advertise client URLs = http://localhost:2379
2017-12-02 08:28:04.531396 I | etcdserver: initial advertise peer URLs = http://localhost:2380
2017-12-02 08:28:04.531403 I | etcdserver: initial cluster = default=http://localhost:2380
2017-12-02 08:28:04.534043 I | etcdserver: starting member 8e9e05c52164694d in cluster cdf818194e3a8c32
2017-12-02 08:28:04.534068 I | raft: 8e9e05c52164694d became follower at term 0
2017-12-02 08:28:04.534077 I | raft: newRaft 8e9e05c52164694d [peers: [], term: 0, commit: 0, applied: 0, lastindex: 0, lastterm: 0]
2017-12-02 08:28:04.534082 I | raft: 8e9e05c52164694d became follower at term 1
2017-12-02 08:28:04.543383 I | etcdserver: starting server... [version: 3.1.11, cluster version: to_be_decided]
2017-12-02 08:28:04.544244 I | etcdserver/membership: added member 8e9e05c52164694d [http://localhost:2380] to cluster cdf818194e3a8c32
2017-12-02 08:28:04.734379 I | raft: 8e9e05c52164694d is starting a new election at term 1
2017-12-02 08:28:04.734472 I | raft: 8e9e05c52164694d became candidate at term 2
2017-12-02 08:28:04.734490 I | raft: 8e9e05c52164694d received MsgVoteResp from 8e9e05c52164694d at term 2
2017-12-02 08:28:04.734505 I | raft: 8e9e05c52164694d became leader at term 2
2017-12-02 08:28:04.734512 I | raft: raft.node: 8e9e05c52164694d elected leader 8e9e05c52164694d at term 2
2017-12-02 08:28:04.735011 I | etcdserver: setting up the initial cluster version to 3.1
2017-12-02 08:28:04.735063 I | etcdserver: published {Name:default ClientURLs:[http://localhost:2379]} to cluster cdf818194e3a8c32
2017-12-02 08:28:04.735155 E | etcdmain: forgot to set Type=notify in systemd service file?
2017-12-02 08:28:04.735163 I | embed: ready to serve client requests
2017-12-02 08:28:04.735380 N | embed: serving insecure client requests on 127.0.0.1:2379, this is strongly discouraged!
2017-12-02 08:28:04.737414 N | etcdserver/membership: set the initial cluster version to 3.1
2017-12-02 08:28:04.737465 I | etcdserver/api: enabled capabilities for version 3.1
```

- 可以使用 etcdctl 命令进行测试
- 说明 etcd 服务已经成功启动了

```
[root@nagios ~]# etcdctl  set testkey 'helloworld'
helloworld
[root@nagios ~]# etcdctl get testkey
helloworld
```

- 也可以通过 HTTP 访问本地 2379 或 4001 端口的方式来进行操作，例如查看 testkey 的值：

```
[root@nagios ~]# curl -L http://127.0.0.1:2379/v2/keys/testkey
{"action":"get","node":{"key":"/testkey","value":"helloworld","modifiedIndex":4,"createdIndex":4}}
```

## 使用 etcdctl

- etcdctl 是一个命令行客户端，它能提供一些简洁的命令，供用户直接跟 etcd 服务打交道，而无需基于 HTTP API 方式。这在某些情况下将很方便，例如用户对服务进行测试或者手动修改数据库内容。我们也推荐在刚接触 etcd 时通过 etcdctl 命令来熟悉相关的操作，这些操作跟 HTTP API 实际上是对应的。



### 数据库操作

- 数据库操作围绕对键值和目录的 CRUD （符合 REST 风格的一套操作：Create）完整生命周期的管理。
  etcd 在键的组织上采用了层次化的空间结构（类似于文件系统中目录的概念），用户指定的键可以为单独的名字，如 testkey，此时实际上放在根目录 / 下面，也可以为指定目录结构，如 cluster1/node2/testkey，则将创建相应的目录结构。

```
set

指定某个键的值。例如

$ etcdctl set /testdir/testkey "Hello world"
Hello world
支持的选项包括：

--ttl '0'            该键值的超时时间（单位为秒），不配置（默认为 0）则永不超时
--swap-with-value value 若该键现在的值是 value，则进行设置操作
--swap-with-index '0'    若该键现在的索引值是指定索引，则进行设置操作
get

获取指定键的值。例如

$ etcdctl set testkey hello
hello
$ etcdctl update testkey world
world
当键不存在时，则会报错。例如

$ etcdctl get testkey2
Error:  100: Key not found (/testkey2) [1]
支持的选项为

--sort    对结果进行排序
--consistent 将请求发给主节点，保证获取内容的一致性
update

当键存在时，更新值内容。例如

$ etcdctl set testkey hello
hello
$ etcdctl update testkey world
world
当键不存在时，则会报错。例如

$ etcdctl update testkey2 world
Error:  100: Key not found (/testkey2) [1]
支持的选项为

--ttl '0'    超时时间（单位为秒），不配置（默认为 0）则永不超时
rm

删除某个键值。例如

$ etcdctl rm testkey
当键不存在时，则会报错。例如

$ etcdctl rm testkey2
Error:  100: Key not found (/testkey2) [8]
支持的选项为

--dir        如果键是个空目录或者键值对则删除
--recursive        删除目录和所有子键
--with-value     检查现有的值是否匹配
--with-index '0'    检查现有的 index 是否匹配
mk

如果给定的键不存在，则创建一个新的键值。例如

$ etcdctl mk /testdir/testkey "Hello world"
Hello world
当键存在的时候，执行该命令会报错，例如

$ etcdctl set testkey "Hello world"
Hello world
$ ./etcdctl mk testkey "Hello world"
Error:  105: Key already exists (/testkey) [2]
支持的选项为

--ttl '0'    超时时间（单位为秒），不配置（默认为 0）则永不超时
mkdir

如果给定的键目录不存在，则创建一个新的键目录。例如

$ etcdctl mkdir testdir
当键目录存在的时候，执行该命令会报错，例如

$ etcdctl mkdir testdir
$ etcdctl mkdir testdir
Error:  105: Key already exists (/testdir) [7]
支持的选项为

--ttl '0'    超时时间（单位为秒），不配置（默认为 0）则永不超时
setdir

创建一个键目录，无论存在与否。

支持的选项为

--ttl '0'    超时时间（单位为秒），不配置（默认为 0）则永不超时
updatedir

更新一个已经存在的目录。 支持的选项为

--ttl '0'    超时时间（单位为秒），不配置（默认为 0）则永不超时
rmdir

删除一个空目录，或者键值对。

若目录不空，会报错

$ etcdctl set /dir/testkey hi
hi
$ etcdctl rmdir /dir
Error:  108: Directory not empty (/dir) [13]
ls

列出目录（默认为根目录）下的键或者子目录，默认不显示子目录中内容。

例如

$ ./etcdctl set testkey 'hi'
hi
$ ./etcdctl set dir/test 'hello'
hello
$ ./etcdctl ls
/testkey
/dir
$ ./etcdctl ls dir
/dir/test
支持的选项包括

--sort    将输出结果排序
--recursive    如果目录下有子目录，则递归输出其中的内容
-p        对于输出为目录，在最后添加 `/` 进行区分
```

### 非数据库操作

```
backup

备份 etcd 的数据。

支持的选项包括

--data-dir         etcd 的数据目录
--backup-dir     备份到指定路径
watch

监测一个键值的变化，一旦键值发生更新，就会输出最新的值并退出。

例如，用户更新 testkey 键值为 Hello world。

$ etcdctl watch testkey
Hello world
支持的选项包括

--forever        一直监测，直到用户按 `CTRL+C` 退出
--after-index '0'    在指定 index 之前一直监测
--recursive        返回所有的键值和子键值
exec-watch

监测一个键值的变化，一旦键值发生更新，就执行给定命令。

例如，用户更新 testkey 键值。

$etcdctl exec-watch testkey -- sh -c 'ls'
default.etcd
Documentation
etcd
etcdctl
etcd-migrate
README-etcdctl.md
README.md
支持的选项包括

--after-index '0'    在指定 index 之前一直监测
--recursive        返回所有的键值和子键值
member

通过 list、add、remove 命令列出、添加、删除 etcd 实例到 etcd 集群中。

例如本地启动一个 etcd 服务实例后，可以用如下命令进行查看。

$ etcdctl member list
ce2a822cea30bfca: name=default peerURLs=http://localhost:2380,http://localhost:7001 clientURLs=http://localhost:2379,http://localhost:4001
```



### 命令选项

```
--debug 输出 cURL 命令，显示执行命令的时候发起的请求
--no-sync 发出请求之前不同步集群信息
--output, -o 'simple' 输出内容的格式 (simple 为原始信息，json 为进行json格式解码，易读性好一些)
--peers, -C 指定集群中的同伴信息，用逗号隔开 (默认为: "127.0.0.1:4001")
--cert-file HTTPS 下客户端使用的 SSL 证书文件
--key-file HTTPS 下客户端使用的 SSL 密钥文件
--ca-file 服务端使用 HTTPS 时，使用 CA 文件进行验证
--help, -h 显示帮助命令信息
--version, -v 打印版本信息
```

## 