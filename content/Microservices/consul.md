---
title: "consul 服务发现"
date: 2019-06-13 09:29
---
[TOC]

# 服务发现

因为使用微服务架构。传统的单体架构不够灵活不能很好的适应变化，从而向微服务架构进行转换，而伴随着大量服务的出现，管理运维十分不便，于是开始搞一些自动化的策略，服务发现应运而生。所以如果需要使用服务发现，你应该有一些对服务治理的痛点。



![img](https://snag.gy/JePm0f.jpg)

服务发现就以K-V的方式记录下，K一般是服务名，V就是IP:PORT。服务发现模块定时的轮询查看这些服务能不能访问的了（这就是健康检查）。

客户端完全不需要记录这些服务网络位置，客户端和服务端完全解耦！



做服务发现的框架常用的有

zookeeper, eureka, etcd, consul



## Consul 特性

Consul 使用 Go 编写，用 binary package 发布，可以非常容易安装和使用，在此页面
https://www.consul.io/downloads.html 下载所需平台的包，下载对应的 binary 包解压并且配置 consul binary 包对应的 Path 即可使用。



使用 Raft 算法来保证一致性, 比复杂的 Paxos 算法更直接. 相比较而言, zookeeper 采用的是 Paxos, 而 etcd 使用的则是 Raft.

Consul 的 raft 协议要求必须过半数的节点都写入成功才认为注册成功 

若Leader 挂掉时，重新选举期间整个 Consul 不可用。保证了强一致性但牺牲了可用性。



consul是分布式的、高可用、横向扩展的。consul提供的一些关键特性：

- service discovery：consul通过DNS或者HTTP接口使服务注册和服务发现变的很容易，一些外部服务，例如saas提供的也可以一样注册。
- health checking：健康检测使consul可以快速的告警在集群中的操作。和服务发现的集成，可以防止服务转发到故障的服务上面。
- key/value storage：一个用来存储动态配置的系统。提供简单的HTTP接口，可以在任何地方操作。
- multi-datacenter：无需复杂的配置，即可支持任意数量的区域。



Consul 的地址和端口号默认是 localhost:8500 





# Installation 

## Linux

```
curl -o consul.zip https://releases.hashicorp.com/consul/1.5.1/consul_1.5.1_linux_amd64.zip

unzip consul_${CONSUL_VERSION}_linux_amd64.zip
sudo chown root:root consul
sudo mv consul /usr/local/bin/
consul --version
```



The `consul` command features opt-in autocompletion for flags, subcommands, and arguments (where supported). Enable autocompletion.

```
consul -autocomplete-install
complete -C /usr/local/bin/consul consul
```

Create a unique, non-privileged system user to run Consul and create its data directory.

```
sudo useradd --system --home /etc/consul.d --shell /bin/false consul
sudo mkdir --parents /opt/consul
sudo chown --recursive consul:consul /opt/consul
```



### systemd

```
[Unit]
Description="HashiCorp Consul - A service mesh solution"
Documentation=https://www.consul.io/
Requires=network-online.target
After=network-online.target
ConditionFileNotEmpty=/etc/consul.d/consul.hcl

[Service]
User=consul
Group=consul
ExecStart=/usr/local/bin/consul agent -config-dir=/etc/consul.d/
ExecReload=/usr/local/bin/consul reload
KillMode=process
Restart=on-failure
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
```

> ConditionFileNotEmpty - Check for a non-zero sized configuration file before consul is started
>
> LimitNOFILE - Set an increased Limit for File Descriptors
>
> 



# 配置

## General configuration

```
sudo mkdir --parents /etc/consul.d
sudo touch /etc/consul.d/consul.hcl
sudo chown --recursive consul:consul /etc/consul.d
sudo chmod 640 /etc/consul.d/consul.hcl
```



Add this configuration to the `consul.hcl` configuration file:

Replace the `datacenter` parameter value with the identifier you will use for the datacenter this Consul cluster is deployed in. Replace the `encrypt` parameter value with the output from running `consul keygen` on any host with the `consul` binary installed.

```
datacenter = "dc1"
data_dir = "/opt/consul"
encrypt = "Luj2FZWwlt8475wD1WtwUQ=="
```

> datacenter - The datacenter in which the agent is running.
>
> data_dir - The data directory for the agent to store state.
>
> encrypt - Specifies the secret key to use for encryption of Consul network traffic.



### Cluster auto-join

`retry_join` parameter allows you to configure all Consul agents to automatically form a cluster using a common Consul server accessed via DNS address, IP address or using Cloud Auto-join. This removes the need to manually join the Consul cluster nodes together.

Add the retry_join parameter to the `consul.hcl` configuration file

```
retry_join = ["172.16.0.11"]
```





## Server configuration

```
sudo mkdir --parents /etc/consul.d
sudo touch /etc/consul.d/server.hcl
sudo chown --recursive consul:consul /etc/consul.d
sudo chmod 640 /etc/consul.d/server.hcl
```



 Replace the `bootstrap_expect` value with the number of Consul servers you will use; three or five [is recommended](https://www.consul.io/docs/internals/consensus.html#deployment-table).

```
server = true
bootstrap_expect = 3
```



### Consul UI

add the UI configuration to the `server.hcl` configuration file to enable the Consul UI

```
ui = true
```









# Consul 原理

![img](https://snag.gy/4M6gno.jpg)



* **Agent**

Consul 是一个分布式，高可用的系统，组成 Consul 服务的每个节点都是一个 agent，agent 可以 server 或 client 的模式启动

* **CLIENT**

CLIENT表示consul的client模式，就是客户端模式。是consul节点的一种模式，这种模式下，所有注册到当前节点的服务会被转发到SERVER，本身是**不持久化**这些信息。



- **SERVER**

SERVER表示consul的server模式，表明这个consul是个server，这种模式下，功能和CLIENT都一样，唯一不同的是，它会把所有的信息持久化的本地，这样遇到故障，信息是可以被保留的。

为了提高通信效率，只有Server节点才可以加入跨数据中心的通信。



- **SERVER-LEADER**

中间那个SERVER下面有LEADER的字眼，表明这个SERVER是它们的老大，它和其它SERVER不一样的一点是，它需要负责同步注册的信息给其它的SERVER，同时也要负责各个节点的健康监测。

Server节点有一个Leader和多个Follower，Leader节点会将数据同步到Follower，Server的数量推荐是3个或者5个，在Leader挂掉的时候会启动选举机制产生一个新的Leader。



集群内的Consul节点通过gossip协议（流言协议）维护成员关系，也就是说某个节点了解集群内现在还有哪些节点，这些节点是Client还是Server。

单个数据中心的流言协议同时使用TCP和UDP通信，并且都使用8301端口。

跨数据中心的流言协议也同时使用TCP和UDP通信，端口使用8302。

集群内数据的读写请求既可以直接发到Server，也可以通过Client使用RPC转发到Server，请求最终会到达Leader节点，在允许数据轻微陈旧的情况下，读请求也可以在普通的Server节点完成，集群内数据的读写和复制都是通过TCP的8300端口完成。





## 端口

| Use                                                          | Default Ports    |
| :----------------------------------------------------------- | :--------------- |
| DNS: The DNS server (TCP and UDP)                            | 8600             |
| HTTP: The HTTP API (TCP Only)                                | 8500             |
| HTTPS: The HTTPs API                                         | disabled (8501)* |
| gRPC: The gRPC API                                           | disabled (8502)* |
| LAN Serf: The Serf LAN port (TCP and UDP)                    | 8301             |
| Wan Serf: The Serf WAN port TCP and UDP)                     | 8302             |
| server: Server RPC address (TCP Only)                        | 8300             |
| Sidecar Proxy Min: Inclusive min port number to use for automatically assigned sidecar service registrations. | 21000            |
| Sidecar Proxy Max: Inclusive max port number to use for automatically assigned sidecar service registrations. | 21255            |

# example 

## 启动节点1（server模式）

```
  docker run -d -e 'CONSUL_LOCAL_CONFIG={"skip_leave_on_interrupt": true}' --name=node1 consul agent -server -bind=172.17.0.2  -bootstrap-expect=3 -node=node1
```

> -node：节点的名称
>  -bind：绑定的一个地址，用于节点之间通信的地址，可以是内外网，必须是可以访问到的地址
>  -server：这个就是表示这个节点是个SERVER
>  -bootstrap-expect：这个就是表示期望提供的SERVER节点数目，数目一达到，它就会被激活，然后就是LEADER了







## 启动节点2-3（server模式）

```
  docker run -d -e 'CONSUL_LOCAL_CONFIG={"skip_leave_on_interrupt": true}' --name=node2 consul agent -server -bind=172.17.0.3  -join=172.17.0.2 -node-id=$(uuidgen | awk '{print tolower($0)}')  -node=node2

  docker run -d -e 'CONSUL_LOCAL_CONFIG={"skip_leave_on_interrupt": true}' --name=node3 consul agent -server -bind=172.17.0.4  -join=172.17.0.2 -node-id=$(uuidgen | awk '{print tolower($0)}')  -node=node3 -client=172.17.0.4
```

> -join：这个表示启动的时候，要加入到哪个集群内，这里就是说要加入到节点1的集群
>  -node-id：这个貌似版本8才加入的，这里用这个来指定唯一的节点ID，可以查看这个[issue](https://link.jianshu.com?t=https://github.com/hashicorp/consul/issues/2877)
>  -client：这个表示注册或者查询等一系列客户端对它操作的IP，如果不指定这个IP，默认是127.0.0.1。



## 启动节点4（client模式）

```
  docker run -d -e 'CONSUL_LOCAL_CONFIG={"leave_on_terminate": true}' --name=node4 consul agent -bind=172.17.0.5 -retry-join=172.17.0.2 -node-id=$(uuidgen | awk '{print tolower($0)}')  -node=node4
```

> 除了没有**-server**，其它都是一样的，没有这个就说明这个节点是CLIENT



## 查看集群状态

```
$ docker exec -t node1 consul members
Node   Address          Status  Type    Build  Protocol  DC   Segment
node1  172.17.0.2:8301  alive   server  1.5.1  2         dc1  <all>
node2  172.17.0.3:8301  alive   server  1.5.1  2         dc1  <all>
node3  172.17.0.4:8301  alive   server  1.5.1  2         dc1  <all>
node4  172.17.0.5:8301  alive   client  1.5.1  2         dc1  <default>
```



## 注册个服务

使用HTTP API 注册个服务，使用[接口API]([https://www.consul.io/api/agent/service.html](https://link.jianshu.com?t=https://www.consul.io/api/agent/service.html) API)调用

调用 [http://consul:8500/v1/agent/service/register](https://link.jianshu.com?t=http://consul:8500/v1/agent/service/register) PUT 注册一个服务。request body:

```
{
  "ID": "userServiceId", //服务id
  "Name": "userService", //服务名
  "Tags": [              //服务的tag，自定义，可以根据这个tag来区分同一个服务名的服务
    "primary",
    "v1"
  ],
  "Address": "127.0.0.1",//服务注册到consul的IP，服务发现，发现的就是这个IP
  "Port": 8000,          //服务注册consul的PORT，发现的就是这个PORT
  "EnableTagOverride": false,
  "Check": {             //健康检查部分
    "DeregisterCriticalServiceAfter": "90m",
    "HTTP": "http://www.baidu.com", //指定健康检查的URL，调用后只要返回20X，consul都认为是健康的
    "Interval": "10s"   //健康检查间隔时间，每隔10s，调用一次上面的URL
  }
}
```

使用curl调用

```
curl http://172.17.0.4:8500/v1/agent/service/register -X PUT -i -H "Content-Type:application/json" -d '{
 "ID": "userServiceId",  
 "Name": "userService",
 "Tags": [
   "primary",
   "v1"
 ],
 "Address": "127.0.0.1",
 "Port": 8000,
 "EnableTagOverride": false,
 "Check": {
   "DeregisterCriticalServiceAfter": "90m",
   "HTTP": "http://www.baidu.com",
   "Interval": "10s"
 }
}'
```



## 发现个服务

刚刚注册了名为userService的服务，我们现在发现（查询）下这个服务

```
curl http://172.17.0.4:8500/v1/catalog/service/userService
```

返回的响应：

```
[
    {
        "Address": "172.17.0.4",
        "CreateIndex": 880,
        "ID": "e6e9a8cb-c47e-4be9-b13e-a24a1582e825",
        "ModifyIndex": 880,
        "Node": "node3",
        "NodeMeta": {},
        "ServiceAddress": "127.0.0.1",
        "ServiceEnableTagOverride": false,
        "ServiceID": "userServiceId",
        "ServiceName": "userService",
        "ServicePort": 8000,
        "ServiceTags": [
            "primary",
            "v1"
        ],
        "TaggedAddresses": {
            "lan": "172.17.0.4",
            "wan": "172.17.0.4"
        }
    }
]
```

内容有了吧，这个就是我们刚刚注册的服务的信息，就可以获取到

作者：不小下

链接：https://www.jianshu.com/p/f8746b81d65d

来源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。

# 测试部署



## 获取镜像

获取consul 镜像

```
docker pull consul
```



## 启动集群

启动集群了，这里启动4个Consul Agent，3个Server（会选举出一个leader），1个Client。

```
#启动第1个Server节点，集群要求要有3个Server，将容器8500端口映射到主机8900端口，同时开启管理界面
docker run -d --name=consul1 -p 8900:8500 -e CONSUL_BIND_INTERFACE=eth0 consul agent --server=true --bootstrap-expect=3 --client=0.0.0.0 -ui
 
#启动第2个Server节点，并加入集群
docker run -d --name=consul2 -e CONSUL_BIND_INTERFACE=eth0 consul agent --server=true --client=0.0.0.0 --join 172.17.0.2
 
#启动第3个Server节点，并加入集群
docker run -d --name=consul3 -e CONSUL_BIND_INTERFACE=eth0 consul agent --server=true --client=0.0.0.0 --join 172.17.0.2
 
#启动第4个Client节点，并加入集群
docker run -d --name=consul4 -e CONSUL_BIND_INTERFACE=eth0 consul agent --server=false --client=0.0.0.0 --join 172.17.0.2
```





测试状态，访问本地映射端口127.0.0.1:8900

```
http://127.0.0.1:8900
```



写了一个hello服务，通过配置文件的方式注册到Consul，服务的相关信息：

- name：hello，服务名称，需要能够区分不同的业务服务，可以部署多份并使用相同的name注册。
- id：hello1，服务id，在每个节点上需要唯一，如果有重复会被覆盖。
- address：172.17.0.5，服务所在机器的地址。
- port：5000，服务的端口。
- 健康检查地址：http://localhost:5000/，如果返回HTTP状态码为200就代表服务健康，每10秒Consul请求一次，请求超时时间为1秒。

请将下面的内容保存成文件services.json，并上传到容器的/consul/config目录中。

```
{
  "services": [
    {
      "id": "hello1",
      "name": "hello",
      "tags": [
        "primary"
      ],
      "address": "172.17.0.5",
      "port": 5000,
      "checks": [
        {
        "http": "http://localhost:5000/",
        "tls_skip_verify": false,
        "method": "GET",
        "interval": "10s",
        "timeout": "1s"
        }
      ]
    }
  ]
}
```

复制到consul config目录：

```
docker cp {这里请替换成services.json的本地路径} consul4:/consul/config
```

重新加载consul配置：

```
consul reload
```

然后这个服务就注册成功了。可以将这个服务部署到多个节点，比如部署到consul1和consul4，并同时运行。





## 服务发现

服务注册成功以后，调用方获取相应服务地址的过程就是服务发现。Consul提供了多种方式。

### HTTP API 方式

```
curl -L http://127.0.0.1:8900/v1/health/service/hello?passing=false

#进入容器访问
curl http://curl http://172.17.0.5:8500/v1/health/service/hello
```

返回的信息包括注册的Consul节点信息、服务信息及服务的健康检查信息。这里用了一个参数passing=false，会自动过滤掉不健康的服务，包括本身不健康的服务和不健康的Consul节点上的服务，从这个设计上可以看出Consul将服务的状态绑定到了节点的状态。

如果服务有多个部署，会返回服务的多条信息，调用方需要决定使用哪个部署，常见的可以随机或者轮询。为了提高服务吞吐量，以及减轻Consul的压力，还可以缓存获取到的服务节点信息，不过要做好容错的方案，因为缓存服务部署可能会变得不可用。具体是否缓存需要结合自己的访问量及容错规则来确定。

上边的参数passing默认为false，也就是说不健康的节点也会返回，结合获取节点全部服务的方法，这里可以做到获取全部服务的实时健康状态，并对不健康的服务进行报警处理。



### DNS 方式

hello服务的域名是：hello.service.dc1.consul，后边的service代表服务，固定；dc1是数据中心的名字，可以配置；最后的consul也可以配置。

官方在介绍DNS方式时经常使用dig命令进行测试，但是alpine系统中没有dig命令，也没有相关的包可以安装，但是有人实现了，下载下来解压到bin目录就可以了。

```
curl -L https://github.com/sequenceiq/docker-alpine-dig/releases/download/v9.10.2/dig.tgz |tar -xzv -C /usr/local/bin
```

然后执行dig命令：

```
dig @127.0.0.1 -p 8600 hello.service.dc1.consul. ANY


/consul/config # dig @127.0.0.1 -p 8600 hello.service.dc1.consul. ANY

; <<>> DiG 9.10.2 <<>> @127.0.0.1 -p 8600 hello.service.dc1.consul. ANY
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 23442
;; flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;hello.service.dc1.consul.	IN	ANY

;; AUTHORITY SECTION:
consul.			0	IN	SOA	ns.consul. hostmaster.consul. 1560404699 3600 600 86400 0

;; Query time: 1 msec
;; SERVER: 127.0.0.1#8600(127.0.0.1)
;; WHEN: Thu Jun 13 05:44:59 GMT 2019
;; MSG SIZE  rcvd: 103
```

> 这里使用8600 端口表示 service 是通过8600端口与Consul Client进行DNS通信的

如果报错：*parse of /etc/resolv.conf failed* ，请将resolv.conf中的search那行删掉。

使用DNS的方式可以在程序中集成一个DNS解析库，也可以自定义本地的DNS Server。自定义本地DNS Server是指将.consul域的请求全部转发到Consul Agent，Windows上有[DNS Agent](https://github.com/stackia/DNSAgent)，Linux上有Dnsmasq；对于非Consul提供的服务则继续请求原DNS；使用DNS Server时Consul会随机返回具体服务的多个部署中的一个，仅能提供简单的负载均衡。

DNS缓存问题：DNS缓存一般存在于应用程序的网络库、本地DNS客户端或者代理，Consul Sever本身可以认为是没有缓存的（为了提高集群DNS吞吐量，可以设置使用普通Server上的陈旧数据，但影响一般不大），DNS缓存可以减轻Consul Server的访问压力，但是也会导致访问到不可用的服务。使用时需要根据实际访问量和容错能力确定DNS缓存方案。



### Consul Template

Consul Template是Consul官方提供的一个工具，严格的来说不是标准的服务发现方式。这个工具会通过Consul监听数据变化然后替换模板中使用的标签，并发布替换后的文件到指定的目录。在nginx等web服务器做反向代理和负载均衡时特别有用。



Consul的docker镜像中没有集成这个工具，需要自己安装，比较简单：

```
curl -L https://releases.hashicorp.com/consul-template/0.19.5/consul-template_0.19.5_linux_amd64.tgz |tar -xzv -C /usr/local/bin
```

然后创建一个文件：in.tpl，内容为：

```
{{ range service "hello" }}
server {{ .Name }}{{ .Address }}:{{ .Port }}{{ end }}
```

这个标签会遍历hello服务的所有部署，并按照指定的格式输出。在此文件目录下执行：

```
nohup consul-template -template "in.tpl:out.txt" &
```

现在你可以cat out.txt查看根据模板生产的内容，新增或者关闭服务，文件内容会自动更新。



# 节点和服务注销

节点和服务的注销可以使用HTTP API:

- 注销任意节点和服务：/catalog/deregister
- 注销当前节点的服务：/agent/service/deregister/:service_id

注意：

如果注销的服务还在运行，则会再次[同步到catalog中](https://www.consul.io/docs/internals/anti-entropy.html)，因此应该只在agent不可用时才使用catalog的注销API。

节点在宕机时状态会变为failed，默认情况下72小时后会被从集群移除。

如果某个节点不继续使用了，也可以在本机使用*consul leave*命令，或者在其它节点使用*consul force-leave 节点Id*，则节点上的服务和健康检查全部注销。



## Consul的健康检查

Consul做服务发现是专业的，健康检查是其中一项必不可少的功能，其提供Script/TCP/HTTP+Interval，以及TTL等多种方式。服务的健康检查由服务注册到的Agent来处理，这个Agent既可以是Client也可以是Server。

很多同学都使用ZooKeeper或者etcd做服务发现，使用Consul时发现节点挂掉后服务的状态变为不可用了，所以有同学问服务为什么不在各个节点之间同步？这个根本原因是服务发现的实现原理不同。



## Consul与ZooKeeper、etcd的区别

后边这两个工具是通过键值存储来实现服务的注册与发现。

- ZooKeeper利用临时节点的机制，业务服务启动时创建临时节点，节点在服务就在，节点不存在服务就不存在。
- etcd利用TTL机制，业务服务启动时创建键值对，定时更新ttl，ttl过期则服务不可用。

ZooKeeper和etcd的键值存储都是强一致性的，也就是说键值对会自动同步到多个节点，只要在某个节点上存在就可以认为对应的业务服务是可用的。

Consul的数据同步也是强一致性的，服务的注册信息会在Server节点之间同步，相比ZK、etcd，服务的信息还是持久化保存的，即使服务部署不可用了，仍旧可以查询到这个服务部署。但是业务服务的可用状态是由注册到的Agent来维护的，Agent如果不能正常工作了，则无法确定服务的真实状态，并且Consul是相当稳定了，Agent挂掉的情况下大概率服务器的状态也可能是不好的，此时屏蔽掉此节点上的服务是合理的。Consul也确实是这样设计的，DNS接口会自动屏蔽挂掉节点上的服务，HTTP API也认为挂掉节点上的服务不是passing的。

鉴于Consul健康检查的这种机制，同时避免单点故障，所有的业务服务应该部署多份，并注册到不同的Consul节点。部署多份可能会给你的设计带来一些挑战，因为调用方同时访问多个服务实例可能会由于会话不共享导致状态不一致，这个有许多成熟的解决方案，可以去查询，这里不做说明。



## 健康检查能不能支持故障转移？

上边提到健康检查是由服务注册到的Agent来处理的，那么如果这个Agent挂掉了，会不会有别的Agent来接管健康检查呢？答案是否定的。

从问题产生的原因来看，在应用于生产环境之前，肯定需要对各种场景进行测试，没有问题才会上线，所以显而易见的问题可以屏蔽掉；如果是新版本Consul的BUG导致的，此时需要降级；如果这个BUG是偶发的，那么只需要将Consul重新拉起来就可以了，这样比较简单；如果是硬件、网络或者操作系统故障，那么节点上服务的可用性也很难保障，不需要别的Agent接管健康检查。

从实现上看，选择哪个节点是个问题，这需要实时或准实时同步各个节点的负载状态，而且由于业务服务运行状态多变，即使当时选择出了负载比较轻松的节点，无法保证某个时段任务又变得繁重，可能造成新的更大范围的崩溃。如果原来的节点还要启动起来，那么接管的健康检查是否还要撤销，如果要，需要记录服务们最初注册的节点，然后有一个监听机制来触发，如果不要，通过服务发现就会获取到很多冗余的信息，并且随着时间推移，这种数据会越来越多，系统变的无序。

从实际应用看，节点上的服务可能既要被发现，又要发现别的服务，如果节点挂掉了，仅提供被发现的功能实际上服务还是不可用的。当然发现别的服务也可以不使用本机节点，可以通过访问一个Nginx实现的若干Consul节点的负载均衡来实现，这无疑又引入了新的技术栈。

如果不是上边提到的问题，或者你可以通过一些方式解决这些问题，健康检查接管的实现也必然是比较复杂的，因为分布式系统的状态同步是比较复杂的。同时不要忘了服务部署了多份，挂掉一个不应该影响系统的快速恢复，所以没必要去做这个接管。



# Consul的其它部署架构

如果你实在不想在每个主机部署Consul Client，还有一个多路注册的方案可供选择

![img](https://snag.gy/VJNoe4.jpg)



在专门的服务器上部署Consul Client，然后每个服务都注册到多个Client，这里为了避免服务单点问题还是每个服务部署多份，需要服务发现时，程序向一个提供负载均衡的程序发起请求，该程序将请求转发到某个Consul Client。这种方案需要注意将Consul的8500端口绑定到私网IP上，默认只有127.0.0.1。

这个架构的优势：

- Consul节点服务器与应用服务器隔离，互相干扰少；
- 不用每台主机都部署Consul，方便Consul的集中管理；
- 某个Consul Client挂掉的情况下，注册到其上的服务仍有机会被访问到；

但也需要注意其缺点：

- 引入更多技术栈：负载均衡的实现，不仅要考虑Consul Client的负载均衡，还要考虑负载均衡本身的单点问题。
- Client的节点数量：单个Client如果注册的服务太多，负载较重，需要有个算法（比如hash一致）合理分配每个Client上的服务数量，以及确定Client的总体数量。
- 服务发现要过滤掉重复的注册，因为注册到了多个节点会认为是多个部署（DNS接口不会有这个问题）。

这个方案其实还可以优化，服务发现使用的负载均衡可以直接代理Server节点，因为相关请求还是会转发到Server节点，不如直接就发到Server。

**是否可以只有Server？**

这个问题的答案还是有关服务数量的问题，首先Server的节点数量不是越多越好，3个或者5个是推荐的数量，数量越多数据同步的处理越慢（强一致性）；然后每个节点可以注册的服务数量是有上限的，这个受限于软硬件的处理能力。所以如果你的服务只有10个左右，只有Server问题是不大的，但是这时候有没有必要使用Consul呢？因此正常使用Consul的时候还是要有Client才好，这也符合Consul的反熵设计。

大家可以将这个部署架构与前文提到的普适架构对比下，看看哪个更适合自己，或者你有更好的方案欢迎分享出来。



