---
title: "load_balance"
date: 2018-08-10 02:17
---

[TOC]

# Load Balance

[NFV 实战：如何构建 100G 线速负载均衡 2016-03-31](http://dbaplus.cn/news-21-333-1.html)

负载均衡的发展历程：

- 1996 F5 成立
- 1998 LVS 项目成立
- 2000 HAProxy 项目成立
- 2004 NGINX 推出公共版本
- 2004 F5 推出 TMOS 平台
- 2007 F5 开始提供应用交付（ADC）产品

负载平衡、SSL 卸载、压缩优化、TCP 连接优化

[Multi-tier load-balancing with Linux 2018-05-23](https://vincent.bernat.im/en/blog/2018-multi-tier-loadbalancer)

## ~~LVS~~

LVS 项目 **多年未更新**，基于 DPDK 的负载均衡将是未来发展方向

<http://www.linuxvirtualserver.org/software/index.html>

| Branch              | Version | Release date |
| ------------------- | ------- | ------------ |
| IPVS for kernel 2.6 | 1.2.1   | 2004-12-24   |
| IPVS for kernel 2.5 | 1.1.7   | 2003-07-05   |
| IPVS for kernel 2.4 | 1.0.12  | 2004-11-17   |
| IPVS for kernel 2.2 | 1.0.8   | 2001-05-14   |

<https://github.com/alibaba/LVS>

最后更新 2013

<https://github.com/yubo/ip_vs_ca>

小米 IPVS CA (get ip vs **fullnat** client add)

## DPDK

<https://dpdk.org/>

[USENIX 研讨会（NSDI 2016） Google Maglev：基于商用服务器的负载均衡器 2016-03-21](http://www.infoq.com/cn/news/2016/03/google-maglev)

[PDF：Maglev 快速、可靠的软件网络负载均衡器](http://static.googleusercontent.com/media/research.google.com/en//pubs/archive/44824.pdf)

[Google's Maglev analysis 2016-03-31](http://hushi55.github.io/2016/03/31/Google%27s-Maglev-analysis)

而已 LVS 代表的开源软件解决方案，通常以 active-passive 模式部署提供 1＋1 冗余， 使其中一个闲置，造成容量浪费。

[Google 是如何做负载均衡的？ 2016-11-21](https://zhuanlan.zhihu.com/p/23826170)

简单的说虽然 LVS 已经做到 Linux 内核里了，但是在 Google 看来 Linux 是性能的瓶颈， 到 LVS 之前还要经过完整的 TCP/IP 协议栈以及内核的一系列 filter 模块， 而这些对于转发来说是没有必要的。于是 Google 的做法就是简单粗暴的绕过内核， 把 Maglev **直接架在网卡上对接网卡的输入和输出队列**，来一个数据包也不需要完整的 TCP/IP 协议栈的解析， 进来的包只要分析前几个字节，拿出源地址，源端口，目标地址，目标端口和协议号这个五元组对于转发来说就已经足够了。 剩下的诸如 payload，序列号之类的东西统统不关心直接塞到网卡输出口给后面就行了。

[从 Maglev 到 Vortex 揭秘 100G＋ 线速负载均衡的设计与实现 2016-03-29](http://www.infoq.com/cn/articles/Maglev-Vortex)

国内云服务商 UCloud 进一步迭代了负载均衡产品 —— Vortex 成功地提升了单机性能。 在技术实现上，UCloud Vortex 与 Google Maglev 颇为相似。 以一台普通性价比的 x86 1U 服务器为例，Vortex 可以实现吞吐量达 14M PPS (10G, 64字节线速)， 新建连接 200k CPS 以上，并发连接数达到 3000 万、10G 线速的转发。

[MGW 美团点评高性能四层负载均衡 2017-01-05](https://tech.meituan.com/MGW.html)

**中断** 问题以及 **协议栈路径** 性能过长问题

中断是影响 LVS 性能最重要的一个因素，假如我们一秒需要处理 600 万的数据包，每 6 个数据包产生一个硬件中断的话，那一秒就会产生 100 万个硬件中断，每一次产生硬件中断都会打断正在进行密集计算的负载均衡程序，中间产生大量的 cache miss 对性能的影响异常大。

同时由于 LVS 是基于内核 netfilter 开发的一个应用程序，而 netfilter 是运行在内核协议栈的一个钩子框架。这就意味着当数据包到达 LVS 时，已经经过了一段很长的协议栈处理，但是这段处理对于 LVS 来说都不是必需的，这也造成了一部分不必要的性能损耗。

[性能超过 LVS 几倍的开源负载均衡器 DPVS 2017-10-19](http://www.yunweipai.com/archives/23023.html)

DPVS 是基于 DPDK 的高性能第 4 层负载均衡器。基于阿里巴巴 LVS 修改而来，出于蓝而胜于蓝。

<https://github.com/iqiyi/dpvs/>

<https://www.usenix.org/conference/nsdi18/presentation/olteanu>

[Stateless datacenter load-balancing with Beamer 2018-05-03](https://blog.acolyer.org/2018/05/03/stateless-datacenter-load-balancing-with-beamer/)

<https://github.com/Beamer-LB>

[LinuxConfAu 2018 (re)implementing (Google) maglev in Rust 2018-01-24](https://youtu.be/_GRM1Ij_3t0)

- rust
- DPDK
- netmap
- SR-IOV

Backport of ip_vs_mh for Linux IPVS (consistent hashing with Google's Maglev algorithm)

<https://github.com/vincentbernat/ip_vs_mh>

[Consistent Hashing algorithms from Google:](https://twitter.com/dgryski/status/979097147265007616)

- Jump Hash (2014)
- Multi-Probe Consistent Hashing (2015)
- Maglev Hashing (2016)
- Consistent Hashing with Bounded Loads (2017)

[Consistent Hashing with Bounded Loads 2017-04-03](https://ai.googleblog.com/2017/04/consistent-hashing-with-bounded-loads.html)

[Consistent Hashing with Bounded Loads 2017-07-27](https://arxiv.org/abs/1608.01350)

[Improving load balancing with a new consistent-hashing algorithm 2016-12-20](https://medium.com/vimeo-engineering-blog/improving-load-balancing-with-a-new-consistent-hashing-algorithm-9f1bd75709ed)

[Consistent Hashing: Algorithmic Tradeoffs 2018-04-03](https://medium.com/@dgryski/consistent-hashing-algorithmic-tradeoffs-ef6b8e2fcae8)

[Consistent hashing algorithmic tradeoffs 2018-04-08](https://www.slideshare.net/EvansLin/consistent-hashing-algorithmic-tradeoffs)

[Open-sourcing Katran, a scalable network load balancer 2018-05-22](https://code.facebook.com/posts/1906146702752923)

Facebook just released **Katran**, an L4 load-balancer implemented with **XDP** and **eBPF** and using **consistent hashing**.

<https://github.com/facebookincubator/katran>

katran is a cpp library and bpf program to build high performance layer 4 load balancing forwarding plane. katran leverages XDP infrastructure from the kernel to provide in-kernel facility for fast packet's processing.