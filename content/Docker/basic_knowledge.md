---
title: "basic_knowledge"
date: 2018-10-22 12:57
---


[TOC]


# basic_knowledge



Docker 最初是 dotCloud 公司创始人 Solomon Hykes 在法国期间发起的一个公司内部项目，它是基于 dotCloud 公司多年云服务技术的一次革新，并于 2013 年 3 月以 Apache 2.0 授权协议开源，主要项目代码在 GitHub 上进行维护。Docker 项目后来还加入了 Linux 基金会，并成立推动 开放容器联盟。



容器技术的核心功能，就是通过约束和修改进程的动态表现，为进程创造出一个界限的效果



最核心的原理实际上是为待创建的用户进程

1. 启动Linux Namespace
2. 设置指定的Cgroups参数
3. 切换进程的根目录(Change root)



大数据所关注的计算密集型离线业务，其实并不像常规的 Web 服务那样适合用容器进行 托管和扩容，也没有对应用打包的强烈需求，所以 Hadoop、Spark 等项目到现在也没在容器 技术上投下更大的赌注



## Cgroups 限制作用

Linux Control Group

主要是限制一个进程组能够使用的资源上线，包括CPU，内存，磁盘，网络，带宽等

即Linux 内核中用来为进程设置资源限制的一个重要功能



Cgroups 还能够对进程进行优先级设置，审计，以及将进程挂起和恢复等操作



在 Linux 中，Cgroups 给用户暴露出来的操作接口是文件系统，即它以文件和目录的方式组织 在操作系统的 /sys/fs/cgroup 路径下。在 Ubuntu 16.04 机器里，我可以用 mount 指令把它 们展示出来

```
$ mount -t cgroup
cpuset on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,cpuset) cpu on /sys/fs/cgroup/cpu type cgroup (rw,nosuid,nodev,noexec,relatime,cpu)
cpuacct on /sys/fs/cgroup/cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,cpuacct) blkio on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,blkio) memory on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,memory) 
```

> 在 /sys/fs/cgroup 下面有很多诸如 cpuset、cpu、 memory 这样的子目录，叫子系统



在对应的子系统下面创建一个目录

```
root@ubuntu:/sys/fs/cgroup/cpu$ mkdir container
root@ubuntu:/sys/fs/cgroup/cpu$ ls container/
cgroup.clone_children cpu.cfs_period_us cpu.rt_period_us cpu.shares notify_on_release cgroup.procs cpu.cfs_quota_us cpu.rt_runtime_us cpu.stat tasks
```

> 会自动生成该子系统对应的资源限制文件

我们在后台执行这样一条脚本，可以把计算机的 CPU 吃到 100%

```
$ while : ; do : ; done &
[1] 226
```



向 container 组里的 cfs_quota 文件写入 20 ms(20000 us)， 即每 100 ms 的时间里，被该控制组 限制的进程只能使用 20 ms 的 CPU 时间，也就是说这个进程只能使用到 20% 的 CPU 带宽。

```
$ echo 226 > /sys/fs/cgroup/cpu/container/tasks

$ top
%Cpu0 : 20.3 us, 0.0 sy, 0.0 ni, 79.7 id, 0.0 wa, 0.0 hi, 0.0 si, 0.0 st
```

cpu使用率降下来了



Linux Cgroups 的设计还是比较易用的，简单粗暴地理解呢，它就是一个子系统目录加上一组 资源限制文件的组合。而对于 Docker 等 Linux 容器项目来说，它们只需要在每个子系统下，为每个容器创建一个控制组(即创建一个新目录)，然后在启动容器进程之后，把这个进程 的 PID 填写到对应控制组的 tasks 文件中就可以了。





## Namespace 隔离作用

用来修改进程视图的主要方法，即对被隔离应用的进程空间做了手脚，使其自认为是pid=1的进程，而在宿主机上，是原来的其他进程

即创建容器进程时，指定来这个进程所需要启动的一组Namespace 参数

```
docker run -it busybox /bin/sh

Status: Downloaded newer image for busybox:latest
/ # ps
PID   USER     TIME  COMMAND
    1 root      0:00 /bin/sh
    6 root      0:00 ps
/ #
```

> 这里/bin/sh PID为1， 但在宿主机，就不是1



主要包含这个几个Namespaces

| namespace | 系统调用参数  | 隔离内容                   |
| --------- | ------------- | -------------------------- |
| UTS       | CLONE_NEWUTS  | 主机名和域名               |
| IPC       | CLONE_NEWIPC  | 信号量，消息队列和共享内存 |
| PID       | CLONE_NEWPID  | 进程编号                   |
| Network   | CLONE_NEWNET  | 网络设备，网络栈，端口等   |
| Mount     | CLONE_NEWNS   | 挂载点 （文件系统）        |
| User      | CLONE_NEWUSER | 用户和用户组               |





## rootfs 根文件系统 （容器镜像）

Mount Namespace 正是基于对 chroot 的不断改良才被发明出来的，它也是 Linux 操作系统里的第一个 Namespace。

当然，为了能够让容器的这个根目录看起来更“真实”，我们一般会在这个容器的根目录下挂载 一个完整操作系统的文件系统，比如 Ubuntu16.04 的 ISO。这样，在容器启动之后，我们在容 器里通过执行 "ls /" 查看根目录下的内容，就是 Ubuntu 16.04 的所有目录和文件。

而这个挂载在容器根目录上、用来为容器进程提供隔离后执行环境的文件系统，就是所谓的“容器镜像”

rootfs 只是一个操作系统所包含的文件、配置和目录，并不包括操作系 统内核。在 Linux 操作系统中，这两部分是分开存放的，操作系统只有在开机启动时才会加载 指定版本的内核镜像。



由于 rootfs 里打包的不只是应用，而是整个操作系统的文件和目录，也就意味着，应用以及它 运行所需要的所有依赖，都被封装在了一起。有了容器镜像“打包操作系统”的能力，这个最基础的依赖环境也终于变成了应用沙盒的一部 分。这就赋予了容器所谓的一致性:无论在本地、云端，还是在一台任何地方的机器上，用户只 需要解压打包好的容器镜像，那么这个应用运行所需要的完整的执行环境就被重现出来了



Docker 镜像使用的 rootfs，往往由多个“层”组成

通过“分层镜像”的设计，以 Docker 镜像为核心，来自不同公司、不同团队的技术人员被紧密 地联系在了一起。而且，由于容器镜像的操作是增量式的，这样每次镜像拉取、推送的内容，比 原本多个完整的操作系统的大小要小得多;而共享层的存在，可以使得所有这些容器镜像需要的 总空间，也比每个镜像的总和要小。这样就使得基于容器镜像的团队协作，要比基于动则几个 GB 的虚拟机磁盘镜像的协作要敏捷得多。

更重要的是，一旦这个镜像被发布，那么你在全世界的任何一个地方下载这个镜像，得到的内容 都完全一致，可以完全复现这个镜像制作者当初的完整环境。这，就是容器技术“强一致性”的 重要体现。





## 容器本质

容器的本质就是一个进程，用户的应用进程实际上就是容器里 PID=1 的进程，也是其他后续创建的所有进程的父进程。这就意味着，在一个容器中，你没办法同时运行两个不同的应用，除非你能事先找到一个公共的 PID=1 的程序来充当两个不同应用的父进程，这也是为什么很多人都会用 systemd 或者 supervisord 这样的软件来代替应用本身作为容器的启动进程。







## 缺点

在Linux内核中，很多资源时不可以被Namespace化的，比如时间

如果容器中的程序使用settimeofday(2) 系统调用修改了时间，整个宿主机的时间就会被更改

对比KVM这种，由Hypervisor来负责对宿主机的调用处理，Linux Namespace隔离机制并不彻底



### 解决方法

Linux下的/proc 目录存储的是记录当前内核运行状态的一组特殊文件

用户可以访问文件获取系统当前进程的信息

但运行top指令发现显示信息为宿主机的相关信息

因为/proc 文件系统并不知道用户通过Cgroups 给容器做了哪些资源限制，

所以可以通过lxcfs来实现此功能

如把宿主机的/var/lib/lxcfs/proc/memoinfo 的文件挂载到Docker 容器的/proc/meminfo 位置后，容器中进程读取相应文件内容时，lxcfs的fuse实现会从容器对应的Cgroup中读取正确的内存限制，获得正确的资源约束









## 虚拟化方式比较

![image-20200408130513298](basic_knowledge.assets/image-20200408130513298.png)

> 容器除了运行其中应用外，基本不消耗额外的系统资源，使得应用的性能很高，同时系统的开销尽量小。传统虚拟机方式运行 10 个不同的应用就要起 10 个虚拟机，而Docker 只需要启动 10 个隔离的应用即可。



# Docker 版本

CE 版本即社区版（免费，支持周期三个月）
> Docker CE 每月发布一个 edge 版本 (17.03, 17.04, 17.05...)，每三个月发布一个 stable 版本 (17.03, 17.06, 17.09...)

EE 即企业版，强调安全，付费使用。

> Docker EE 和 stable版本号保持一致，但每个版本提供一年维护。



## 安装

Kitematic 这个图形化工具（官方给出的定义是 Visual Docker Container Management on Mac & Windows），对于熟悉和了解 Docker 是很好的帮助



### CentOS

卸载旧版本

```
$ sudo yum remove docker \
                  docker-common \
                  docker-selinux \
                  docker-engine
```

使用 yum 源 安装

```
$ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

国内源添加（可选）

```
$ sudo yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

官方源添加（可选）   

```
$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

安装 Docker CE

```
$ sudo yum makecache fast
$ sudo yum install docker-ce
```

使用脚本自动安装

在测试或开发环境中 Docker 官方为了简化安装流程，提供了一套便捷的安装脚本，CentOS 系统上可以使用这套脚本安装：

```
$ curl -fsSL get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh --mirror Aliyun
```

启动 Docker CE

```
$ sudo systemctl enable docker
$ sudo systemctl start docker
```

建立 docker 用户组

默认情况下，docker 命令会使用 Unix socket 与 Docker 引擎通讯。而只有 root 用户和 docker 组的用户才可以访问 Docker 引擎的 Unix socket。出于安全考虑，一般 Linux 系统上不会直接使用 root 用户。因此，更好地做法是将需要使用 docker 的用户加入 docker 用户组。

```
$ sudo groupadd docker
$ sudo useradd -g docker docker -s /sbin/nologin
```



### docker.com

```
wget -qO- get.docker.com | bash 
```



## 卸载

```
dpkg -l | grep -i docker
sudo apt-get purge -y docker-engine docker docker.io docker-ce  
sudo apt-get autoremove -y --purge docker-engine docker docker.io docker-ce  
sudo rm -rf /var/lib/docker
sudo rm /etc/apparmor.d/docker
sudo groupdel docker
sudo rm -rf /var/run/docker.sock
```



## 守护进程

运行  Docker 守护进程时，可以用 -H 来改变绑定接口的方式，比如 sudo /usr/bin/docker -d -H  tcp://0.0.0.0:2375，如果不想每次都输入这么长的命令，需要加入以下环境变量 

```
export  DOCKER_HOST="tcp://0.0.0.0:2375"
```



## 图形用户界面

虽然我们可以用命令来控制 docker，但是如果能有一个 web 管理界面，操作什么的会方便很多，比较常见的有

- Shipyard
- Potainer



## 配置文件

环境配置文件

```
/etc/sysconfig/docker-network
/etc/sysconfig/docker-storage
/etc/sysconfig/docker
```



Unit File

/usr/lib/systemd/system/docker.service



Docker Registry

/etc/containers/registries.conf



docker-ce

/etc/docker/daemon.json

### docker CN repo

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
        "https://1nj0zren.mirror.aliyuncs.com",
        "https://docker.mirrors.ustc.edu.cn",
        "http://f1361db2.m.daocloud.io",
        "https://registry.docker-cn.com"
    ]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```



命令行执行 `docker info`，如果从结果中看到了如下内容，说明配置成功。

```
Registry Mirrors:
 [...]
 https://registry.docker-cn.com/
```

# 基本概念

## 镜像 Image

一个只读的模板，镜像可以用来创建 Docker 容器

```
docker run -d ubuntu:latest sleep 3600
```

这里的Ubuntu 镜像，实际上就是一个Ubuntu操作系统的rootfs，内容是Ubuntu操作系统的所有文件和目录



任何镜像里面的内容都属于只读层，commit之后的东西也属于只读层



### rootfs

用于为容器进程提供隔离后执行环境的文件系统，即所谓的容器镜像rootfs



就相当于一个root文件系统。官方镜像Ubuntu:14.04 就包含了完整的一套 Ubuntu 14.04 最小系统的 root 文件系统

镜像不包含任何动态数据，其内容在构建之后也不会被改变。



rootfs只是一个操作系统包含的文件，配置和目录，并不包括系统内核，在Linux系统中，两部分是分开存放的，操作系统只有在开机启动的时候才会夹在指定版本的内核镜像





### 分层存储 Union FS

Docker 设计时，就充分利用 Union FS 的技术，将其设计为分层存储的架构，即将多个不同位置的目录联合挂载到同一个目录下

镜像构建时，会一层层构建，前一层是后一层的基础。每一层构建完就不会再发生改变，后一层上的任何改变只发生在自己这一层。

> 比如，删除前一层文件的操作，实际不是真的删除前一层的文件，而是仅在当前层标记为该文件已删除。在最终容器运行的时候，虽然不会看到这个文件，但是实际上该文件会一直跟随镜像。因此，在构建镜像的时候，需要额外小心，每一层尽量只包含该层需要添加的东西，任何额外的东西应该在该层构建结束前清理掉。



所有的层都保存在diff目录下



Docker 镜像使用的rootfs，往往由多个层组成

```
# docker image inspect ubuntu:latest | grep -i rootfs -C 12

"RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:bebe7ce6215aee349bee5d67222abeb5c5a834bbeaa2f2f5d05363d9fd68db41",
                "sha256:283fb404ea9415ab48456fd8a82b153b1a719491cdf7b806d1853b047d00f27f",
                "sha256:663e8522d78b5b767f15b2e43885da5975068e3195bbbfa8fc3a082297a361c1",
                "sha256:4b7d93055d8781d27259ba5780938e6a78d8ef691c94ee9abc3616c1b009ec4a"
            ]
```

> 这里的每一层即是一个增量的rootfs

然后将所有增量联合一起挂在在一个统一的挂在点上

```
root@localhost:~# find / -name '663e8522d78b5b767f15b2e43885da5975068e3195bbbfa8fc3a082297a361c1'
/var/lib/docker/image/overlay2/distribution/v2metadata-by-diffid/sha256/663e8522d78b5b767f15b2e43885da5975068e3195bbbfa8fc3a082297a361c1
```



#### 镜像的实现原理

Docker 镜像是怎么实现增量的修改和维护的？ 每个镜像都由很多层次构成，Docker 使用 Union FS 将这些不同的层结合到一个镜像中去。
通常 Union FS 有两个用途, 一方面可以实现不借助 LVM、RAID 将多个 disk 挂到同一个目录下,另一个更常用的就是将一个只读的分支和一个可写的分支联合在一起，Live CD 正是基于此方法可以允许在镜像不变的基础上允许用户在其上进行一些写操作。 Docker 在 AUFS 上构建的容器也是利用了类似的原理。



![image-20200409003015301](basic_knowledge.assets/image-20200409003015301.png)

init 层，用来存放临时修改过的/etc/hosts等文件

Copy on Write 存放任何对只读层的修改，容器声明的Volume挂载点，也出现在这一层



#### Aufs

aufs 是之前的UnionFS的重新实现，竞争产品是overlayfs



#### overlayfs

从3.18版本开始合并到Linux内核， 新版使用overlay2

```
$ docker info
Containers: 5
 Running: 0
 Paused: 0
 Stopped: 5
Images: 3
Server Version: 18.09.2
Storage Driver: overlay2
```







### 镜像体积

Docker Hub 中显示的体积是压缩后的体积。在镜像下载和上传过程中镜像是保持着压缩状态的，因此 Docker Hub 所显示的大小是网络传输中更关心的流量大小。



### 虚悬镜像


镜像既没有仓库名，也没有标签，均为 `<none>`

> 这个镜像原本是有镜像名和标签的，原来为 mongo:3.2，随着官方镜像维护，发布了新版本后，重新 docker pull mongo:3.2 时，mongo:3.2 这个镜像名被转移到了新下载的镜像身上，而旧的镜像上的这个名称则被取消，从而成为了 `<none>`。
> docker build 也同样可以导致这种现象。由于新旧镜像同名，旧镜像名称被取消，从而出现仓库名、标签均为 `<none>` 的镜像

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



### scratch 镜像

本身即是空镜像，万能的base镜像

如centos等镜像的FROM处






## 容器 Container

镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例一样

可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台

容器的实质是进程，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的 命名空间。

容器可以拥有自己的 root 文件系统、自己的网络配置、自己的进程空间，甚至自己的用户 ID 空间。



### 容器存储层

每一个容器运行时，是以镜像为基础层，在其上创建一个当前容器的存储层

容器存储层的生存周期和容器一样，容器消亡时，容器存储层也随之消亡。因此，任何保存于容器存储层的信息都会随容器删除而丢失。

> 按照 Docker 最佳实践的要求，容器不应该向其存储层内写入任何数据，容器存储层要保持无状态化。所有的文件写入操作，都应该使用 数据卷（Volume）、或者绑定宿主目录，在这些位置的读写会跳过容器存储层，直接对宿主(或网络存储)发生读写，其性能和稳定性更高。



## 仓库 Repository

即Docker Store，存储和分享docker images

集中存放镜像文件的场所，可以是公有的，也可以是私有的

最大的公开仓库是 Docker Hub

国内的公开仓库包括 Docker Pool 等

当用户创建了自己的镜像之后就可以使用 push 命令将它上传到公有或者私有仓库，这样下次在另外一台机器上使用这个镜像时候，只需要从仓库上 pull 下来就可以了

Docker 仓库的概念跟 Git 类似，注册服务器可以理解为 GitHub 这样的托管服务





### Docker Registry

集中的存储、分发镜像的服务

一个 Docker Registry 中可以包含多个仓库（Repository）；每个仓库可以包含多个标签（Tag）；每个标签对应一个镜像

> 以 Ubuntu 镜像 为例，ubuntu 是仓库的名字，其内包含有不同的版本标签，如，14.04, 16.04。我们可以通过 ubuntu:14.04，或者 ubuntu:16.04 来具体指定所需哪个版本的镜像。如果忽略了标签，比如 ubuntu，那将视为 ubuntu:latest。

仓库名经常以 两段式路径 形式出现，比如 jwilder/nginx-proxy，前者往往意味着 Docker Registry 多用户环境下的用户名，后者则往往是对应的软件名。但这并非绝对，取决于所使用的具体 Docker Registry 的软件或服务。



### 公开 Registry

最常使用的 Registry 公开服务是官方的 Docker Hub
> 这也是默认的 Registry，并拥有大量的高质量的官方镜像。除此以外，还有 CoreOS 的 Quay.io
>
>

国内的一些云服务商提供了针对 Docker Hub 的镜像服务（Registry Mirror），这些镜像服务被称为加速器。
> 常见的有 阿里云加速器、DaoCloud 加速器、灵雀云加速器等。使用加速器会直接从国内的地址下载 Docker Hub 的镜像，比直接从官方网站下载速度会提高很多。



国内也有一些云服务商提供类似于 Docker Hub 的公开服务

> 比如 时速云镜像仓库、网易云镜像服务、DaoCloud 镜像市场、阿里云镜像库等。





### 私有 Registry

Docker 官方提供了 Docker Registry 镜像，可以直接使用做为私有 Registry 服务。
> 开源的 Docker Registry 镜像只提供了 Docker Registry API 的服务端实现，足以支持 docker 命令，不影响使用。但不包含图形界面，以及镜像维护、用户管理、访问控制等高级功能。在官方的商业化版本 Docker Trusted Registry 中，提供了这些高级功能。



第三方软件实现了 Docker Registry API

> 甚至提供了用户界面以及一些高级功能。比如，VMWare Harbor 和 Sonatype Nexus。





# CoreOS

CoreOS 的设计是为你提供能够像谷歌一样的大型互联网公司一样的基础设施管理能力来动态扩展和管理的计算能力。
CoreOS 的安装文件和运行依赖非常小,它提供了精简的 Linux 系统。它使用 Linux 容器在更高的抽象层来管理你的服务，而不是通过常规的 YUM 和 APT 来安装包。



## 特性

```
一个最小化操作系统

CoreOS 被设计成一个基于容器的最小化的现代操作系统。它比现有的 Linux 安装平均节省 40% 的 RAM（大约 114M ）并允许从 PXE 或 iPXE 非常快速的启动。

无痛更新

利用主动和被动双分区方案来更新 OS，使用分区作为一个单元而不是一个包一个包的更新。这使得每次更新变得快速，可靠，而且很容易回滚。

Docker容器

应用作为 Docker 容器运行在 CoreOS 上。容器以包的形式提供最大得灵活性并且可以在几毫秒启动。

支持集群

CoreOS 可以在一个机器上很好地运行，但是它被设计用来搭建集群。

可以通过 k8s 很容易得使应用容器部署在多台机器上并且通过服务发现把他们连接在一起。

分布式系统工具

内置诸如分布式锁和主选举等原生工具用来构建大规模分布式系统得构建模块。

服务发现

很容易定位服务在集群的那里运行并当发生变化时进行通知。它是复杂高动态集群必不可少的。在 CoreOS 中构建高可用和自动故障负载。
```





## etcd 

CoreOS 的第一个重要组件就是使用 etcd 来实现的服务发现。

* 配置文件里有一个 token，你可以通过访问 https://discovery.etcd.io/new 来获取一个包含你 teoken 的 URL。

```
#cloud-config

hostname: coreos0
ssh_authorized_keys:
  - ssh-rsa AAAA...
coreos:
  units:
    - name: etcd.service
      command: start
    - name: fleet.service
      command: start
  etcd:
    name: coreos0
    discovery: https://discovery.etcd.io/<token>
```



## 容器管理

第二个组件就是 Docker，它用来运行你的代码和应用。CoreOS 内置 Docker





## 快速搭建 CoreOS 集群




# Kubernetes

建于 Docker 之上的 Kubernetes 可以构建一个容器的调度服务，其目的是让用户透过 Kubernetes 集群来进行云端容器集群的管理，而无需用户进行复杂的设置工作。系统会自动选取合适的工作节点来执行具体的容器集群调度处理工作。其核心概念是 Container Pod。一个 Pod 由一组工作于同一物理工作节点的容器构成。这些组容器拥有相同的网络命名空间、IP以及存储配额，也可以根据实际情况对每一个 Pod 进行端口映射。此外，Kubernetes 工作节点会由主系统进行管理，节点包含了能够运行 Docker 容器所用到的服务。



Kubernetes 是 Google 团队发起的开源项目，它的目标是管理跨多个主机的容器，提供基本的部署，维护以及运用伸缩，主要实现语言为 Go 语言。


## 快速上手

Kubernetes 依赖 Etcd 服务来维护所有主节点的状态。 

## 启动 Etcd 服务

```
docker run --net=host -d gcr.io/google_containers/etcd:3.1.10 /usr/local/bin/etcd --addr=127.0.0.1:4001 --bind-addr=0.0.0.0:4001 --data-dir=/var/etcd/data
```

## 启动主节点

```
docker run --net=host -d -v /var/run/docker.sock:/var/run/docker.sock  gcr.io/google_containers/hyperkube:v1.17.11 /hyperkube kubelet --api_servers=http://localhost:8080 --v=2 --address=0.0.0.0 --enable_server --hostname_override=127.0.0.1 --config=/etc/kubernetes/manifests
```

## 启动服务代理

```
docker run -d --net=host --privileged gcr.io/google_containers/hyperkube:v1.17.11 /hyperkube proxy --master=http://127.0.0.1:8080 --v=2
```

## 测试状态

```
$ curl 127.0.0.1:8080
{
  "paths": [
    "/api",
    "/api/v1beta1",
    "/api/v1beta2",
    "/api/v1beta3",
    "/healthz",
    "/healthz/ping",
    "/logs/",
    "/metrics",
    "/static/",
    "/swagger-ui/",
    "/swaggerapi/",
    "/validate",
    "/version"
  ]
}
```