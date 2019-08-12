---
title: "network"
date: 2018-10-24 16:43
---


[TOC]

# network

![img](https://snag.gy/nuSP42.jpg)



Docker 允许通过外部访问容器或容器互联的方式来提供网络服务

```
$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
109df3c7dc8b        bridge              bridge              local
5ddf10f7dd1e        host                host                local
301b64cf3798        none                null                local
```

> bridge 即使docker0 网卡， 同时会生成4个虚拟网卡
>
> host 表示使用宿主机的网络名称空间
>
> none 不给使用网络，即loopback网络



```
# apt install bridge-utils


root@w:~# brctl show
bridge name	bridge id		STP enabled	interfaces
docker0		8000.02422fc7d74c	no		vethdefab17


root@w:~# ip link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: ens3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DEFAULT group default qlen 1000
    link/ether 56:00:02:37:5b:32 brd ff:ff:ff:ff:ff:ff
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default 
    link/ether 02:42:2f:c7:d7:4c brd ff:ff:ff:ff:ff:ff
9: vethdefab17@if8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP mode DEFAULT group default 
    link/ether be:51:1f:ef:0e:c0 brd ff:ff:ff:ff:ff:ff link-netnsid 0
```



```
# docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "51b5dfa10b6e2db69dfb8f2bb59f9f0c5a6a028fa2ad66b7484f0b66124f76f0",
        "Created": "2019-08-11T03:25:45.658544312Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "11dd458e175df74b0388dd7174d222c06f9fe8bdbd3b8f2f148cce2e187ad798": {
                "Name": "myNginx",
                "EndpointID": "7f0cb7baad73d87d3b88190335f973f2f70ab2125029040adca6ade432998630",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]

```



## 外部访问容器

* 通过 -P 或 -p 参数来指定端口映射
* 主机的 49155 被映射到了容器的 5000 端口。此时访问本机的 49155 端口即可访问容器内 web 应用提供的界面。

```
$ docker run -d -P training/webapp python app.py

$ docker ps -l
CONTAINER ID  IMAGE                   COMMAND       CREATED        STATUS        PORTS                    NAMES
bc533791f3f5  training/webapp:latest  python app.py 5 seconds ago  Up 2 seconds  0.0.0.0:49155->5000/tcp  nostalgic_morse
```



### -p 则可以指定要映射的端口

* 在一个指定端口上只可以绑定一个容器

`ip:hostPort:containerPort | ip::containerPort | hostPort:containerPort`



###  docker logs 命令来查看应用的信息

```
$ docker logs -f nostalgic_morse
* Running on http://0.0.0.0:5000/
10.0.2.2 - - [23/May/2014 20:16:31] "GET / HTTP/1.1" 200 -
10.0.2.2 - - [23/May/2014 20:16:31] "GET /favicon.ico HTTP/1.1" 404 -
```


### 映射所有接口地址

* 使用 hostPort:containerPort 格式本地的 5000 端口映射到容器的 5000 端口
* 此时默认会绑定本地所有接口上的所有地址。

```
$ docker run -d -p 5000:5000 training/webapp python app.py
```

#### 映射到指定地址的指定端口

* 可以使用 ip:hostPort:containerPort 格式指定映射使用一个特定地址，比如 localhost 地址 127.0.0.1

```
$ docker run -d -p 127.0.0.1:5000:5000 training/webapp python app.py
```

#### 映射到指定地址的任意端口

* 使用 ip::containerPort 绑定 localhost 的任意端口到容器的 5000 端口，本地主机会自动分配一个端口。

```
$ docker run -d -p 127.0.0.1::5000 training/webapp python app.py
```

* 还可以使用 udp 标记来指定 udp 端口

```
$ docker run -d -p 127.0.0.1:5000:5000/udp training/webapp python app.py
```

#### 查看映射端口配置

* 使用 docker port 来查看当前映射的端口配置，也可以查看到绑定的地址

```
$ docker port nostalgic_morse 5000
127.0.0.1:49155.
```

* 容器有自己的内部网络和 ip 地址（使用 docker inspect 可以获取所有的变量，Docker 还可以有一个可变的网络配置。）

* -p 标记可以多次使用来绑定多个端口

```
$ docker run -d \
    -p 5000:5000 \
    -p 3000:80 \
    training/webapp \
    python app.py
```



### 容器互联 

* 将容器加入自定义的 Docker 网络来连接多个容器

#### 新建网络

* 创建一个新的 Docker 网络
* -d 参数指定 Docker 网络类型，有 bridge overlay


```
$ docker network create -d bridge my-net
```

#### 连接容器

* 运行一个容器并连接到新建的 my-net 网络

```
$ docker run -it --rm --name busybox1 --net my-net busybox sh
```


* 打开新的终端，再运行一个容器并加入到 my-net 网络

```
$ docker run -it --rm --name busybox2 --net my-net busybox sh
```

* 再打开一个新的终端查看容器信息

```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
b47060aca56b        busybox             "sh"                11 minutes ago      Up 11 minutes                           busybox2
8720575823ec        busybox             "sh"                16 minutes ago      Up 16 minutes                           busybox1
```

* 通过 ping 来证明 busybox1 容器和 busybox2 容器建立了互联关系。

```
/ # ping busybox2
PING busybox2 (172.19.0.3): 56 data bytes
64 bytes from 172.19.0.3: seq=0 ttl=64 time=0.072 ms
64 bytes from 172.19.0.3: seq=1 ttl=64 time=0.118 ms

/ # ping busybox1
PING busybox1 (172.19.0.2): 56 data bytes
64 bytes from 172.19.0.2: seq=0 ttl=64 time=0.064 ms
64 bytes from 172.19.0.2: seq=1 ttl=64 time=0.143 ms
```



## 高级网络配置

> 当 Docker 启动时，会自动在主机上创建一个 docker0 虚拟网桥，实际上是 Linux 的一个 bridge，可以理解为一个软件交换机。它会在挂载到它的网口之间进行转发。
> 同时，Docker 随机分配一个本地未占用的私有网段（在 RFC1918 中定义）中的一个地址给 docker0 接口。比如典型的 172.17.42.1，掩码为 255.255.0.0。此后启动的容器内的网口也会自动分配一个同一网段（172.17.0.0/16）的地址。
> 当创建一个 Docker 容器的时候，同时会创建了一对 veth pair 接口（当数据包发送到一个接口时，另外一个接口也可以收到相同的数据包）。这对接口一端在容器内，即 eth0；另一端在本地并被挂载到 docker0 网桥，名称以 veth 开头（例如 vethAQI2QT）。通过这种方式，主机可以跟容器通信，容器之间也可以相互通信。Docker 就创建了在主机和所有容器之间一个虚拟共享网络。

![img](https://yeasy.gitbooks.io/docker_practice/content/advanced_network/_images/network.png)



## 快速配置指南

* 其中有些命令选项只有在 Docker 服务启动的时候才能配置，而且不能马上生效

> -b BRIDGE 或 --bridge=BRIDGE 指定容器挂载的网桥
> --bip=CIDR 定制 docker0 的掩码
> -H SOCKET... 或 --host=SOCKET... Docker 服务端接收命令的通道
> --icc=true|false 是否支持容器之间进行通信
> --ip-forward=true|false 请看下文容器之间的通信
> --iptables=true|false 是否允许 Docker 添加 iptables 规则
> --mtu=BYTES 容器网络中的 MTU

* 下面2个命令选项既可以在启动服务时指定，也可以在启动容器时指定。在 Docker 服务启动的时候指定则会成为默认值，后面执行 docker run 时可以覆盖设置的默认值。

> --dns=IP_ADDRESS... 使用指定的DNS服务器
> --dns-search=DOMAIN... 指定DNS搜索域

* 最后这些选项只有在 docker run 执行时使用，因为它是针对容器的特性内容

> -h HOSTNAME 或 --hostname=HOSTNAME 配置容器主机名
> --link=CONTAINER_NAME:ALIAS 添加到另一个容器的连接
> --net=bridge|none|container:NAME_or_ID|host 配置容器的桥接模式
> -p SPEC 或 --publish=SPEC 映射容器端口到宿主主机
> -P or --publish-all=true|false 映射容器所有端口到宿主主机

#### 配置 DNS

* Docker 利用虚拟文件来挂载容器的 3 个相关配置文件。

* 在容器中使用 mount 命令可以看到挂载信息：

```
$ mount
/dev/disk/by-uuid/1fec...ebdf on /etc/hostname type ext4 ...
/dev/disk/by-uuid/1fec...ebdf on /etc/hosts type ext4 ...
tmpfs on /etc/resolv.conf type tmpfs ...
```

* 这种机制可以让宿主主机 DNS 信息发生更新后，所有 Docker 容器的 DNS 配置通过 /etc/resolv.conf 文件立刻得到更新。

* 配置全部容器的 DNS ，也可以在 /etc/docker/daemon.json 文件中增加以下内容来设置。

```
{
  "dns" : [
    "114.114.114.114",
    "8.8.8.8"
  ]
}
```

* 使用以下命令来证明其已经生效。

```
$ docker run -it --rm ubuntu:17.10  cat etc/resolv.conf

nameserver 114.114.114.114
nameserver 8.8.8.8
```

* 如果用户想要手动指定容器的配置，可以利用下面的选项。

> -h HOSTNAME 或者 --hostname=HOSTNAME 设定容器的主机名，它会被写到容器内的 /etc/hostname 和 /etc/hosts。但它在容器外部看不到，既不会在 docker ps 中显示，也不会在其他的容器的 /etc/hosts 看到。
> --dns=IP_ADDRESS 添加 DNS 服务器到容器的 /etc/resolv.conf 中，让容器用这个服务器来解析所有不在 /etc/hosts 中的主机名。
> --dns-search=DOMAIN 设定容器的搜索域，当设定搜索域为 .example.com 时，在搜索一个名为 host 的主机时，DNS 不仅搜索 host，还会搜索 host.example.com。
> 注意： 如果没有上述最后 2 个选项，Docker 会默认用主机上的 /etc/resolv.conf 来配置容器。



容器访问控制

* 通过 Linux 上的 iptables 防火墙来进行管理和实现

##### 容器访问外部网络

* 容器要想访问外部网络，需要本地系统的转发支持。在Linux 系统中，检查转发是否打开,如果为 0，说明没有开启转发
* 如果在启动 Docker 服务的时候设定 --ip-forward=true, Docker 就会自动设定系统的 ip_forward 参数为 1

```
$sysctl net.ipv4.ip_forward
net.ipv4.ip_forward = 1
```

##### 容器之间访问

* 容器的网络拓扑是否已经互联。默认情况下，所有容器都会被连接到 docker0 网桥上。
* 本地系统的防火墙软件 -- iptables 是否允许通过。

###### 访问所有端口

> 当启动 Docker 服务时候，默认会添加一条转发策略到 iptables 的 FORWARD 链上。策略为通过（ACCEPT）还是禁止（DROP）取决于配置--icc=true（缺省值）还是 --icc=false。当然，如果手动指定 --iptables=false 则不会添加 iptables 规则。
> 可见，默认情况下，不同容器之间是允许网络互通的。如果为了安全考虑，可以在 /etc/default/docker 文件中配置 DOCKER_OPTS=--icc=false 来禁止它。



###### 访问指定端口

> 在通过 -icc=false 关闭网络访问后，还可以通过 --link=CONTAINER_NAME:ALIAS 选项来访问容器的开放端口。
> 例如，在启动 Docker 服务时，可以同时使用 icc=false --iptables=true 参数来关闭允许相互的网络访问，并让 Docker 可以修改系统中的 iptables 规则。

```
$ sudo iptables -nL
...
Chain FORWARD (policy ACCEPT)
target     prot opt source               destination
DROP       all  --  0.0.0.0/0            0.0.0.0/0
...
```

> 之后，启动容器（docker run）时使用 --link=CONTAINER_NAME:ALIAS 选项。Docker 会在 iptable 中为 两个容器分别添加一条 ACCEPT 规则，允许相互访问开放的端口（取决于 Dockerfile 中的 EXPOSE 指令）。
> 当添加了 --link=CONTAINER_NAME:ALIAS 选项后，添加了 iptables 规则。

* 注意：--link=CONTAINER_NAME:ALIAS 中的 CONTAINER_NAME 目前必须是 Docker 分配的名字，或使用 --name 参数指定的名字。主机名则不会被识别。

```
$ sudo iptables -nL
...
Chain FORWARD (policy ACCEPT)
target     prot opt source               destination
ACCEPT     tcp  --  172.17.0.2           172.17.0.3           tcp spt:80
ACCEPT     tcp  --  172.17.0.3           172.17.0.2           tcp dpt:80
DROP       all  --  0.0.0.0/0            0.0.0.0/0
```

#### 映射容器端口到宿主主机的实现

##### 容器访问外部实现

* 容器所有到外部网络的连接，源地址都会被 NAT 成本地系统的 IP 地址。这是使用 iptables 的源地址伪装操作实现的
* 将所有源地址在 172.17.0.0/16 网段，目标地址为其他网段（外部网络）的流量动态伪装为从系统网卡发出。MASQUERADE 跟传统 SNAT 的好处是它能动态从网卡获取地址。


```
$ sudo iptables -t nat -nL
...
Chain POSTROUTING (policy ACCEPT)
target     prot opt source               destination
MASQUERADE  all  --  172.17.0.0/16       !172.17.0.0/16
...
```

##### 外部访问容器实现

* 容器允许外部访问，可以在 docker run 时候通过 -p 或 -P 参数来启用。
* 不管用那种办法，其实也是在本地的 iptable 的 nat 表中添加相应的规则。

```
$ iptables -t nat -nL
...
Chain DOCKER (2 references)
target     prot opt source               destination
DNAT       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:49153 to:172.17.0.2:80
```

* 使用 -p 80:80 时：

```
$ iptables -t nat -nL
Chain DOCKER (2 references)
target     prot opt source               destination
DNAT       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:80 to:172.17.0.2:80
```

* 如果希望永久绑定到某个固定的 IP 地址，可以在 Docker 配置文件 /etc/docker/daemon.json 中添加如下内容。

```
{
  "ip": "0.0.0.0"
}
```

#### 配置 docker0 网桥

* Docker 服务默认会创建一个 docker0 网桥（其上有一个 docker0 内部接口），它在内核层连通了其他的物理或虚拟网卡，这就将所有容器和本地主机都放到同一个物理网络。
  *目前 Docker 网桥是 Linux 网桥，用户可以使用 brctl show 来查看网桥和端口连接信息。

```
$ sudo brctl show
bridge name     bridge id               STP enabled     interfaces
docker0         8000.3a1d7362b4ee       no              veth65f9
                                             vethdda6
```

* 注：brctl 命令在 Debian、Ubuntu 中可以使用 sudo apt-get install bridge-utils 来安装。


* 每次创建一个新容器的时候，Docker 从可用的地址段中选择一个空闲的 IP 地址分配给容器的 eth0 端口。使用本地主机上 docker0 接口的 IP 作为所有容器的默认网关。

```
$ sudo docker run -i -t --rm base /bin/bash
$ ip addr show eth0
24: eth0: <BROADCAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 32:6f:e0:35:57:91 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.3/16 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::306f:e0ff:fe35:5791/64 scope link
       valid_lft forever preferred_lft forever
$ ip route
default via 172.17.42.1 dev eth0
172.17.0.0/16 dev eth0  proto kernel  scope link  src 172.17.0.3
```

#### 自定义网桥

* 使用 -b BRIDGE或--bridge=BRIDGE 来指定使用的网桥。

* 如果服务已经运行，那需要先停止服务，并删除旧的网桥。

```
$ sudo systemctl stop docker
$ sudo ip link set dev docker0 down
$ sudo brctl delbr docker0
```

* 创建一个网桥 bridge0

```
$ sudo brctl addbr bridge0
$ sudo ip addr add 192.168.5.1/24 dev bridge0
$ sudo ip link set dev bridge0 up
```

* 确认网桥创建并启动。

```
$ ip addr show bridge0
4: bridge0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state UP group default
    link/ether 66:38:d0:0d:76:18 brd ff:ff:ff:ff:ff:ff
    inet 192.168.5.1/24 scope global bridge0
       valid_lft forever preferred_lft forever
```

* 在 Docker 配置文件 /etc/docker/daemon.json 中添加如下内容，即可将 Docker 默认桥接到创建的网桥上。

```
{
  "bridge": "bridge0",
}
```

#### pipework

* Jérôme Petazzoni 编写了一个叫 pipework 的 shell 脚本，可以帮助用户在比较复杂的场景中完成容器的连接

#### playground

* Brandon Rhodes 创建了一个提供完整的 Docker 容器网络拓扑管理的 Python库，包括路由、NAT 防火墙；以及一些提供 HTTP, SMTP, POP, IMAP, Telnet, SSH, FTP 的服务器。


#### 创建一个点到点连接

> 默认情况下，Docker 会将所有容器连接到由 docker0 提供的虚拟子网中。
> 用户有时候需要两个容器之间可以直连通信，而不用通过主机网桥进行桥接。
> 解决办法很简单：创建一对 peer 接口，分别放到两个容器中，配置成点到点链路类型即可。

* 首先启动 2 个容器：

```
$ docker run -i -t --rm --net=none base /bin/bash
root@1f1f4c1f931a:/#
$ docker run -i -t --rm --net=none base /bin/bash
root@12e343489d2f:/#
```

* 找到进程号，然后创建网络命名空间的跟踪文件。

```
$ docker inspect -f '{{.State.Pid}}' 1f1f4c1f931a
2989
$ docker inspect -f '{{.State.Pid}}' 12e343489d2f
3004
$ sudo mkdir -p /var/run/netns
$ sudo ln -s /proc/2989/ns/net /var/run/netns/2989
$ sudo ln -s /proc/3004/ns/net /var/run/netns/3004
```

* 创建一对 peer 接口，然后配置路由

```
$ sudo ip link add A type veth peer name B

$ sudo ip link set A netns 2989
$ sudo ip netns exec 2989 ip addr add 10.1.1.1/32 dev A
$ sudo ip netns exec 2989 ip link set A up
$ sudo ip netns exec 2989 ip route add 10.1.1.2/32 dev A

$ sudo ip link set B netns 3004
$ sudo ip netns exec 3004 ip addr add 10.1.1.2/32 dev B
$ sudo ip netns exec 3004 ip link set B up
$ sudo ip netns exec 3004 ip route add 10.1.1.1/32 dev B
```

> 现在这 2 个容器就可以相互 ping 通，并成功建立连接。点到点链路不需要子网和子网掩码。
> 此外，也可以不指定 --net=none 来创建点到点链路。这样容器还可以通过原先的网络来通信。
> 利用类似的办法，可以创建一个只跟主机通信的容器。但是一般情况下，更推荐使用 --icc=false 来关闭容器之间的通信。





#### Docker 网络实现

##### 基本原理

> 首先，要实现网络通信，机器需要至少一个网络接口（物理接口或虚拟接口）来收发数据包；此外，如果不同子网之间要进行通信，需要路由机制。
> Docker 中的网络接口默认都是虚拟的接口。虚拟接口的优势之一是转发效率较高。 Linux 通过在内核中进行数据复制来实现虚拟接口之间的数据转发，发送接口的发送缓存中的数据包被直接复制到接收接口的接收缓存中。对于本地系统和容器内系统看来就像是一个正常的以太网卡，只是它不需要真正同外部网络设备通信，速度要快很多。
> Docker 容器网络就利用了这项技术。它在本地主机和容器内分别创建一个虚拟接口，并让它们彼此连通（这样的一对接口叫做 veth pair）。

##### 创建网络参数

> Docker 创建一个容器的时候，会执行如下操作：
> 创建一对虚拟接口，分别放到本地主机和新容器中；
> 本地主机一端桥接到默认的 docker0 或指定网桥上，并具有一个唯一的名字，如 veth65f9；
> 容器一端放到新容器中，并修改名字作为 eth0，这个接口只在容器的命名空间可见；
> 从网桥可用地址段中获取一个空闲地址分配给容器的 eth0，并配置默认路由到桥接网卡 veth65f9。
> 完成这些之后，容器就可以使用 eth0 虚拟网卡来连接其他容器和其他网络。
> 可以在 docker run 的时候通过 --net 参数来指定容器的网络配置，有4个可选值：
> --net=bridge 这个是默认值，连接到默认的网桥。
> --net=host 告诉 Docker 不要将容器网络放到隔离的命名空间中，即不要容器化容器内的网络。此时容器使用本地主机的网络，它拥有完全的本地主机接口访问权限。容器进程可以跟主机其它 root 进程一样可以打开低范围的端口，可以访问本地网络服务比如 D-bus，还可以让容器做一些影响整个主机系统的事情，比如重启主机。因此使用这个选项的时候要非常小心。如果进一步的使用 --privileged=true，容器会被允许直接配置主机的网络堆栈。
> --net=container:NAME_or_ID 让 Docker 将新建容器的进程放到一个已存在容器的网络栈中，新容器进程有自己的文件系统、进程列表和资源限制，但会和已存在的容器共享 IP 地址和端口等网络资源，两者进程可以直接通过 lo 环回接口通信。
> --net=none 让 Docker 将新容器放到隔离的网络栈中，但是不进行网络配置。之后，用户可以自己进行配置。


##### 网络配置细节

> 用户使用 --net=none 后，可以自行配置网络，让容器达到跟平常一样具有访问网络的权限。通过这个过程，可以了解 Docker 配置网络的细节。

* 启动一个 /bin/bash 容器，指定 --net=none 参数。

```
$ docker run -i -t --rm --net=none base /bin/bash
root@63f36fc01b5f:/#
```

* 在本地主机查找容器的进程 id，并为它创建网络命名空间。

```
$ docker inspect -f '{{.State.Pid}}' 63f36fc01b5f
2778
$ pid=2778
$ sudo mkdir -p /var/run/netns
$ sudo ln -s /proc/$pid/ns/net /var/run/netns/$pid
```

* 检查桥接网卡的 IP 和子网掩码信息。

```
$ ip addr show docker0
21: docker0: ...
inet 172.17.42.1/16 scope global docker0
...
```

* 创建一对 “veth pair” 接口 A 和 B，绑定 A 到网桥 docker0，并启用它

```
$ sudo ip link add A type veth peer name B
$ sudo brctl addif docker0 A
$ sudo ip link set A up
```

* 将B放到容器的网络命名空间，命名为 eth0，启动它并配置一个可用 IP（桥接网段）和默认网关。

```
$ sudo ip link set B netns $pid
$ sudo ip netns exec $pid ip link set dev B name eth0
$ sudo ip netns exec $pid ip link set eth0 up
$ sudo ip netns exec $pid ip addr add 172.17.42.99/16 dev eth0
$ sudo ip netns exec $pid ip route add default via 172.17.42.1
```

> 当容器结束后，Docker 会清空容器，容器内的 eth0 会随网络命名空间一起被清除，A 接口也被自动从 docker0 卸载。
> 此外，用户可以使用 ip netns exec 命令来在指定网络命名空间中进行配置，从而配置容器内的网络。





# Overlay Network

通过在叠加网络，即封装一个新的IP封包形成隧道



## Docker Compose 项目



### Compose 简介



通过第一部分中的介绍，我们知道使用一个 Dockerfile 模板文件，可以让用户很方便的定义一个单独的应用容器。然而，在日常工作中，经常会碰到需要多个容器相互配合来完成某项任务的情况。例如要实现一个 Web 项目，除了 Web 服务容器本身，往往还需要再加上后端的数据库服务容器，甚至还包括负载均衡容器等。

Compose 恰好满足了这样的需求。它允许用户通过一个单独的 docker-compose.yml 模板文件（YAML 格式）来定义一组相关联的应用容器为一个项目（project）。

* Compose 中有两个重要的概念：
* 服务 (service)：一个应用的容器，实际上可以包括若干运行相同镜像的容器实例。
* 项目 (project)：由一组关联的应用容器组成的一个完整业务单元，在 docker-compose.yml 文件中定义。

* Compose 的默认管理对象是项目，通过子命令对项目中的一组容器进行便捷地生命周期管理。

* Compose 项目由 Python 编写，实现上调用了 Docker 服务提供的 API 来对容器进行管理。因此，只要所操作的平台支持 Docker API，就可以在其上利用 Compose 来进行编排管理。

### 安装

* Compose 可以通过 Python 的包管理工具 pip 进行安装，也可以直接下载编译好的二进制文件使用，甚至能够直接在 Docker 容器中运行。

```
$ docker-compose --version

docker-compose version 1.17.1, build 6d101fb

```

#### 二进制包

* 在 Linux 上的也安装十分简单，从 官方 GitHub Release 处直接下载编译好的二进制文件即可。

```
$ sudo curl -L https://github.com/docker/compose/releases/download/1.17.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
```

#### PIP 安装

* x86_64 架构的 Linux 建议按照上边的方法下载二进制包进行安装，如果您计算机的架构是 ARM (例如，树莓派)，再使用 pip 安装。

```
$ sudo pip install -U docker-compose
```

#### bash 补全命令

```
$ curl -L https://raw.githubusercontent.com/docker/compose/1.8.0/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose
```

#### 容器中执行

* Compose 既然是一个 Python 应用，自然也可以直接用容器来执行它。

```
$ curl -L https://github.com/docker/compose/releases/download/1.8.0/run.sh > /usr/local/bin/docker-compose
$ chmod +x /usr/local/bin/docker-compose
```

* 实际上，查看下载的 run.sh 脚本内容，如下

```
set -e

VERSION="1.8.0"
IMAGE="docker/compose:$VERSION"


# Setup options for connecting to docker host
if [ -z "$DOCKER_HOST" ]; then
    DOCKER_HOST="/var/run/docker.sock"
fi
if [ -S "$DOCKER_HOST" ]; then
    DOCKER_ADDR="-v $DOCKER_HOST:$DOCKER_HOST -e DOCKER_HOST"
else
    DOCKER_ADDR="-e DOCKER_HOST -e DOCKER_TLS_VERIFY -e DOCKER_CERT_PATH"
fi


# Setup volume mounts for compose config and context
if [ "$(pwd)" != '/' ]; then
    VOLUMES="-v $(pwd):$(pwd)"
fi
if [ -n "$COMPOSE_FILE" ]; then
    compose_dir=$(dirname $COMPOSE_FILE)
fi
# TODO: also check --file argument
if [ -n "$compose_dir" ]; then
    VOLUMES="$VOLUMES -v $compose_dir:$compose_dir"
fi
if [ -n "$HOME" ]; then
    VOLUMES="$VOLUMES -v $HOME:$HOME -v $HOME:/root" # mount $HOME in /root to share docker.config
fi

# Only allocate tty if we detect one
if [ -t 1 ]; then
    DOCKER_RUN_OPTIONS="-t"
fi
if [ -t 0 ]; then
    DOCKER_RUN_OPTIONS="$DOCKER_RUN_OPTIONS -i"
fi

exec docker run --rm $DOCKER_RUN_OPTIONS $DOCKER_ADDR $COMPOSE_OPTIONS $VOLUMES -w "$(pwd)" $IMAGE "$@"
```

### 卸载

* 如果是二进制包方式安装的，删除二进制文件即可。

```
$ sudo rm /usr/local/bin/docker-compose
```

* 如果是通过 pip 安装的，则执行如下命令即可删除。

```
$ sudo pip uninstall docker-compose
```