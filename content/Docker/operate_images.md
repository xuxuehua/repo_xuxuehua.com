---
title: "operate_images"
date: 2018-10-24 15:52
---


[TOC]

# 操作镜像

## image 列出镜像

`docker images`

```
$ docker images
REPOSITORY           TAG                 IMAGE ID            CREATED             SIZE
redis                latest              5f515359c7f8        5 days ago          183 MB
nginx                latest              05a60462f8ba        5 days ago          181 MB
mongo                3.2                 fe9198c04d62        5 days ago          342 MB
<none>               <none>              00285df0df87        5 days ago          342 MB
ubuntu               16.04               f753707788c5        4 weeks ago         127 MB
ubuntu               latest              f753707788c5        4 weeks ago         127 MB
ubuntu               14.04               1e0c3dd64ccd        4 weeks ago         188 MB
```



**获取所有的IDs**

```
docker images | col 3

IMAGE
65a9e3ef7171
7c01ca6c30f2
9518620e6a0e
430707ee7fe8
1dbd7ebffe31
f47686df00df
df1e39df8dbf
```

```
docker images | col 3 | xargs

IMAGE 65a9e3ef7171 7c01ca6c30f2 9518620e6a0e 430707ee7fe8 1dbd7ebffe31 f47686df00df df1e39df8dbf c5e6cf38d985 e426f6ef897e 8cdd417ec611 511136ea3c5a
```

**前面的”IMAGE”字符去掉**

```
docker images | col 3 | xargs | skip 1

65a9e3ef7171 7c01ca6c30f2 9518620e6a0e 430707ee7fe8 1dbd7ebffe31 f47686df00df df1e39df8dbf c5e6cf38d985 e426f6ef897e 8cdd417ec611 511136ea3c5a
```

```
docker rmi $(docker images | col 3 | xargs | skip 1)
```



### 完成镜像名

```
$ docker image ls --no-trunc
REPOSITORY                   TAG                 IMAGE ID                                                                  CREATED             SIZE
selenium/standalone-chrome   latest              sha256:9e599fb82f84937004eedba8fa498886f095a959421e5f522af9c1b5e04c522a   9 days ago          840MB
browserless/chrome           latest              sha256:d2904ed5f20f33d85c102029561483aee7d9bf94138c1b1888ea6e84d0306819   2 weeks ago         2.14GB
consul                       latest              sha256:7d52b83f718fbd487246da5f655b048a2078cfb8d21fb6b06539608a3961dcba   6 weeks ago         115MB
```





### 显示虚悬镜像

```
$ docker images -f dangling=true
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
<none>              <none>              00285df0df87        5 days ago          342 MB
```

一般来说，虚悬镜像已经失去了存在的价值，是可以随意删除的，可以用下面的命令删除。

```
$ docker rmi $(docker images -q -f dangling=true)
```



### 中间层镜像

为了加速镜像构建、重复利用资源，Docker 会利用 中间层镜像。所以在使用一段时间后，可能会看到一些依赖的中间层镜像。默认的 docker images 列表中只会显示顶层镜像，如果希望显示包括中间层镜像在内的所有镜像的话，需要加 -a 参数。

```
$ docker images -a
```

> 这样会看到很多无标签的镜像，与之前的虚悬镜像不同，这些无标签的镜像很多都是中间层镜像，是其它镜像所依赖的镜像。这些无标签镜像不应该删除，否则会导致上层镜像因为依赖丢失而出错。实际上，这些镜像也没必要删除，因为之前说过，相同的层只会存一遍，而这些镜像是别的镜像的依赖，因此并不会因为它们被列出来而多存了一份，无论如何你也会需要它们。只要删除那些依赖它们的镜像后，这些依赖的中间层镜像也会被连带删除。



## pull 获取镜像

`docker pull [选项] [Docker Registry地址]<仓库名>:<标签>`

下载也是一层层的去下载，并非单一文件。下载过程中给出了每一层的 ID 的前 12 位。并且下载结束后，给出该镜像完整的 sha256 的摘要

```
[root@instance-3 ~]# docker pull ubuntu:14.04
14.04: Pulling from library/ubuntu
bae382666908: Pull complete
29ede3c02ff2: Pull complete
da4e69f33106: Pull complete
8d43e5f5d27f: Pull complete
b0de1abb17d6: Pull complete
Digest: sha256:6e3e3f3c5c36a91ba17ea002f63e5607ed6a8c8e5fbbddb31ad3e15638b51ebc
Status: Downloaded newer image for ubuntu:14.04
```





## --filter/-f 过滤

docker images 还支持强大的过滤器参数 --filter，或者简写 -f。之前我们已经看到了使用过滤器来列出虚悬镜像的用法，它还有更多的用法。比如，我们希望看到在 mongo:3.2 之后建立的镜像，可以用下面的命令：

```
$ docker images -f since=mongo:3.2
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
redis               latest              5f515359c7f8        5 days ago          183 MB
nginx               latest              05a60462f8ba        5 days ago          181 MB
```

```
$ docker images -f label=com.example.version=0.1
```





## -q 特定格式显示

```
$ docker images -q
5f515359c7f8
05a60462f8ba
fe9198c04d62
00285df0df87
f753707788c5
f753707788c5
1e0c3dd64ccd
```

```
$ docker images --format "{{.ID}}: {{.Repository}}"
5f515359c7f8: redis
05a60462f8ba: nginx
fe9198c04d62: mongo
00285df0df87: <none>
f753707788c5: ubuntu
f753707788c5: ubuntu
1e0c3dd64ccd: ubuntu
```

```
$ docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
IMAGE ID            REPOSITORY          TAG
5f515359c7f8        redis               latest
05a60462f8ba        nginx               latest
fe9198c04d62        mongo               3.2
00285df0df87        <none>              <none>
f753707788c5        ubuntu              16.04
f753707788c5        ubuntu              latest
1e0c3dd64ccd        ubuntu              14.04
```



## 删除本地镜像

`docker rmi [选项] <镜像1> [<镜像2> ...]`



使用 docker images -q 来配合使用 docker rmi，这样可以成批的删除希望删除的镜像。

```
$ docker rmi $(docker images -q -f dangling=true)
```




删除所有仓库名为 redis 的镜像：

```
$ docker rmi $(docker images -q redis)
```

删除所有在 mongo:3.2 之前的镜像：

```
$ docker rmi $(docker images -q -f before=mongo:3.2)
```



### 用 ID、镜像名、摘要删除镜像

<镜像> 可以是 镜像短 ID、镜像长 ID、镜像名 或者 镜像摘要

```
$ docker images --digests
REPOSITORY                  TAG                 DIGEST                                                                    IMAGE ID            CREATED             SIZE
node                        slim                sha256:b4f0e0bdeb578043c1ea6862f0d40cc4afe32a4a582f3be235a3b164422be228   6e0c4c8e3913        3 weeks ago         214 MB

$ docker rmi node@sha256:b4f0e0bdeb578043c1ea6862f0d40cc4afe32a4a582f3be235a3b164422be228
Untagged: node@sha256:b4f0e0bdeb578043c1ea6862f0d40cc4afe32a4a582f3be235a3b164422be228
```



#### CentOS/RHEL 的用户需要注意的事项

在 Ubuntu/Debian 上有 UnionFS 可以使用，如 aufs 或者 overlay2，而 CentOS 和 RHEL 的内核中没有相关驱动。因此对于这类系统，一般使用 devicemapper 驱动利用 LVM 的一些机制来模拟分层存储。这样的做法除了性能比较差外，稳定性一般也不好，而且配置相对复杂。Docker 安装在 CentOS/RHEL 上后，会默认选择 devicemapper，但是为了简化配置，其 devicemapper 是跑在一个稀疏文件模拟的块设备上，也被称为 loop-lvm。这样的选择是因为不需要额外配置就可以运行 Docker，这是自动配置唯一能做到的事情。但是 loop-lvm 的做法非常不好，其稳定性、性能更差，无论是日志还是 docker info 中都会看到警告信息。官方文档有明确的文章讲解了如何配置块设备给 devicemapper 驱动做存储层的做法，这类做法也被称为配置 direct-lvm。
除了前面说到的问题外，devicemapper + loop-lvm 还有一个缺陷，因为它是稀疏文件，所以它会不断增长。用户在使用过程中会注意到 /var/lib/docker/devicemapper/devicemapper/data 不断增长，而且无法控制。很多人会希望删除镜像或者可以解决这个问题，结果发现效果并不明显。原因就是这个稀疏文件的空间释放后基本不进行垃圾回收的问题。因此往往会出现即使删除了文件内容，空间却无法回收，随着使用这个稀疏文件一直在不断增长。
所以对于 CentOS/RHEL 的用户来说，在没有办法使用 UnionFS 的情况下，一定要配置 direct-lvm 给 devicemapper，无论是为了性能、稳定性还是空间利用率。
或许有人注意到了 CentOS 7 中存在被 backports 回来的 overlay 驱动，不过 CentOS 里的这个驱动达不到生产环境使用的稳定程度，所以不推荐使用。





### Untagged 和 Deleted

镜像的唯一标识是其 ID 和摘要，而一个镜像可以有多个标签。
因此当我们使用上面命令删除镜像的时候，实际上是在要求删除某个标签的镜像。所以首先需要做的是将满足我们要求的所有镜像标签都取消，这就是我们看到的 Untagged 的信息。因为一个镜像可以对应多个标签，因此当我们删除了所指定的标签后，可能还有别的标签指向了这个镜像，如果是这种情况，那么 Delete 行为就不会发生。所以并非所有的 docker rmi 都会产生删除镜像的行为，有可能仅仅是取消了某个标签而已。

* 当该镜像所有的标签都被取消了，该镜像很可能会失去了存在的意义，因此会触发删除行为。镜像是多层存储结构，因此在删除的时候也是从上层向基础层方向依次进行判断删除。



## Import 导入镜像

从 rootfs 压缩包导入

`docker import [选项] <文件>|<URL>|- [<仓库名>[:<标签>]]`

创建 OpenVZ 的 Ubuntu 14.04 模板的镜像

```
$ docker import \
    http://download.openvz.org/template/precreated/ubuntu-14.04-x86_64-minimal.tar.gz \
    openvz/ubuntu:14.04
Downloading from http://download.openvz.org/template/precreated/ubuntu-14.04-x86_64-minimal.tar.gz
sha256:f477a6e18e989839d25223f301ef738b69621c4877600ae6467c4e5289822a79B/78.42 MB 
```

```
cat ubuntu.tar | sudo docker import - test/ubuntu:v1.0
# 也可以通过指定 URL 或者某个目录来导入，例如
docker import http://example.com/exampleimage.tgz example/imagerepo
```





# 管理镜像



## save 保存镜像到本地

以将镜像保存为一个 tar 文件，然后传输到另一个位置上，再加载进来。这是在没有 Docker Registry 时的做法，现在已经不推荐，镜像迁移应该直接使用 Docker Registry，无论是直接使用 Docker Hub 还是使用内网私有 Registry 都可以。



`$ docker save alpine | gzip > alpine-latest.tar.gz`

从一个机器将镜像迁移到另一个机器,并且带进度条的功能

`docker save <镜像名> | bzip2 | pv | ssh <用户名>@<主机名> 'cat | docker load'`



### -o output 写入到文件

```
docker save -o wdx-local-whale.tar wdxtub/wdx-whale
```





## load 加载本地镜像

### -i --input 读取tar文件

```
$ docker load -i alpine-latest.tar.gz
Loaded image: alpine:latest
```









## history

查看其历史

```
$ docker history openvz/ubuntu:14.04
IMAGE               CREATED              CREATED BY          SIZE                COMMENT
f477a6e18e98        About a minute ago                       214.9 MB            Imported from http://download.openvz.org/template/precreated/ubuntu-14.04-x86_64-minimal.tar.gz
```



## search 查找镜像

```
docker search
```



## system prune 删除所有

Purging All Unused or Dangling Images, Containers, Volumes, and Networks

```
docker system prune
```





# Dockerfile 定制镜像

Dockerfile 是一个文本文件，其内包含了一条条的指令(Instruction)，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建。



## FROM 指定基础镜像

FROM 就是指定基础镜像，因此一个 Dockerfile 中 FROM 是必备的指令，并且必须是第一条指令。

scratch 镜像是虚拟的概念，并不实际存在，它表示一个空白的镜像。

```
FROM scratch
...
```

> 如果你以 scratch 为基础镜像的话，意味着你不以任何镜像为基础，接下来所写的指令将作为镜像第一层开始存在。



## RUN 执行命令

RUN 指令是用来执行命令行命令的。由于命令行的强大能力，RUN 指令在定制镜像时是最常用的指令之一

`RUN ["可执行文件", "参数1", "参数2"]，这更像是函数调用中的格式。`



### shell 格式

RUN <命令>，就像直接在命令行中输入的命令一样。刚才写的 Dockerfile 中的 RUN 指令就是这种格式。 

`RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html`

### exec 格式

```
FROM debian:jessie

RUN apt-get update
RUN apt-get install -y gcc libc6-dev make
RUN wget -O redis.tar.gz "http://download.redis.io/releases/redis-3.2.5.tar.gz"
RUN mkdir -p /usr/src/redis
RUN tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1
RUN make -C /usr/src/redis
RUN make -C /usr/src/redis install
```

> 而上面的这种写法，创建了 7 层镜像。这是完全没有意义的，而且很多运行时不需要的东西，都被装进了镜像里，比如编译环境、更新的软件包等等。结果就是产生非常臃肿、非常多层的镜像，不仅仅增加了构建部署的时间，也很容易出错。 这是很多初学 Docker 的人常犯的一个错误。



## Union FS 实现原理

通常Union FS 有两个用途, 一方面可以实现不借助 LVM、RAID 将多个 disk 
挂到同一个目录下,另一个更常用的就是将一个只读的分支和一个可写的分支联合在一起，Live CD 
正是基于此方法可以允许在镜像不变的基础上允许用户在其上进行一些写操作。 Docker 在 AUFS 上构建的容器也是利用了类似的原理。

Union FS 是有最大层数限制的，比如 AUFS，曾经是最大不得超过 42 层，现在是不得超过 127 层。

上面的正确写法 (包含清理工作)

```
FROM debian:jessie

RUN buildDeps='gcc libc6-dev make' \
    && apt-get update \
    && apt-get install -y $buildDeps \
    && wget -O redis.tar.gz "http://download.redis.io/releases/redis-3.2.5.tar.gz" \
    && mkdir -p /usr/src/redis \
    && tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
    && make -C /usr/src/redis \
    && make -C /usr/src/redis install \
    && rm -rf /var/lib/apt/lists/* \
    && rm redis.tar.gz \
    && rm -r /usr/src/redis \
    && apt-get purge -y --auto-remove $buildDeps
```



## build 构建镜像

`docker build [选项] <上下文路径/URL/->`

在Dockerfile文件所在目录中执行

```
$ docker build -t nginx:v3 .
Sending build context to Docker daemon 2.048 kB
Step 1 : FROM nginx
 ---> e43d811ce2f4
Step 2 : RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
 ---> Running in 9cdc27646c7b
 ---> 44aa4490ce2c
Removing intermediate container 9cdc27646c7b
Successfully built 44aa4490ce2c
```



### -t tag 标签

给镜像打上标签





### 镜像构建上下文（Context）

如果在 Dockerfile 中这么写：

`COPY ./package.json /app/`

> 这并不是要复制执行 docker build 命令所在的目录下的 package.json，也不是复制 Dockerfile 所在目录下的 package.json，而是复制 上下文（context） 目录下的 package.json。

一般来说，应该会将 Dockerfile 置于一个空目录下，或者项目根目录下。

> 如果该目录下没有所需文件，那么应该把所需文件复制一份过来。如果目录下有些东西确实不希望构建时传给 Docker 引擎，那么可以用 .gitignore 一样的语法写一个 .dockerignore，该文件是用于剔除不需要作为上下文传递给 Docker 引擎的。

实际上 Dockerfile 的文件名并不要求必须为 Dockerfile，而且并不要求必须位于上下文目录中，比如可以用 -f ../Dockerfile.php 参数指定某个文件作为 Dockerfile。

当然，一般大家习惯性的会使用默认的文件名 Dockerfile，以及会将其置于镜像构建上下文目录中。



### Git repo 进行构建

```
$ docker build https://github.com/twang2218/gitlab-ce-zh.git#:8.14
docker build https://github.com/twang2218/gitlab-ce-zh.git\#:8.14
Sending build context to Docker daemon 2.048 kB
Step 1 : FROM gitlab/gitlab-ce:8.14.0-ce.0
8.14.0-ce.0: Pulling from gitlab/gitlab-ce
aed15891ba52: Already exists
773ae8583d14: Already exists
...
```



### tar 压缩包构建

`$ docker build http://server/context.tar.gz`



### stdin 读取 Dockerfile 构建

这种形式由于直接从标准输入中读取 Dockerfile 的内容，它没有上下文，因此不可以像其他方法那样可以将本地文件 COPY 进镜像之类的事情。

```
docker build - < Dockerfile
or 
cat Dockerfile | docker build -
```



### stdin 读取上下文压缩包构建

`$ docker build - < context.tar.gz`



## commit 保存镜像

Docker 提供了一个 docker commit 命令，可以将容器的存储层保存下来成为镜像。

> 就是在原有镜像的基础上，再叠加上容器的存储层，并构成新的镜像。以后我们运行这个新镜像的时候，就会拥有原有容器最后的文件变化。

`docker commit [选项] <容器ID或容器名> [<仓库名>[:<标签>]]`

```
$ docker commit \
    --author "Tao Wang <twang2218@gmail.com>" \
    --message "修改了默认网页" \
    webserver \
    nginx:v2
sha256:07e33465974800ce65751acc279adc6ed2dc5ed4e0838f8b86f0c87aa1795214
```



`docker exec` 命令进入容器

```
$ docker exec -it webserver bash
root@3729b97e8226:/# echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
root@3729b97e8226:/# exit
exit
```

修改了容器的文件，也就是改动了容器的存储层

```
$ docker diff webserver
C /root
A /root/.bash_history
C /run
C /usr
C /usr/share
C /usr/share/nginx
C /usr/share/nginx/html
C /usr/share/nginx/html/index.html
C /var
C /var/cache
C /var/cache/nginx
A /var/cache/nginx/client_temp
A /var/cache/nginx/fastcgi_temp
A /var/cache/nginx/proxy_temp
A /var/cache/nginx/scgi_temp
A /var/cache/nginx/uwsgi_temp
```



### 慎用 docker commit

使用 docker commit 命令虽然可以比较直观的帮助理解镜像分层存储的概念，但是实际环境中并不会这样使用

> 使用 docker commit 意味着所有对镜像的操作都是黑箱操作，生成的镜像也被称为黑箱镜像，就是除了制作镜像的人知道执行过什么命令、怎么生成的镜像，别人根本无从得知。而且，即使是这个制作镜像的人，过一段时间后也无法记清具体在操作的。虽然 docker diff 或许可以告诉得到一些线索，但是远远不到可以确保生成一致镜像的地步。这种黑箱镜像的维护工作是非常痛苦的。

docker commit 命令除了学习之外，还有一些特殊的应用场合，比如被入侵后保存现场等。





