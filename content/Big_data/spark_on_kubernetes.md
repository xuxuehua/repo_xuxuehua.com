---
title: "spark_on_kubernetes"
date: 2019-10-11 13:45
---
[TOC]



# Spark on K8s

利用原生的Kubernetes调度器进行集群资源的分配与管理Spark应用。在此模式下，Spark Driver和Executor均使用Pod来运行，进一步可通过指定Node Selector功能将应用运行于特定节点之上（如：带有GPU的节点实例）



## 特点

使用 kubernetes 原生调度的 spark on kubernetes 是对原有的 spark on yarn 革命性的改变，主要表现在以下几点：

1. Kubernetes 原生调度：不再需要二层调度，直接使用 kubernetes 的资源调度功能，跟其他应用共用整个 kubernetes 管理的资源池；
2. 资源隔离，粒度更细：原先 yarn 中的 queue 在 spark on kubernetes 中已不存在，取而代之的是 kubernetes 中原生的 namespace，可以为每个用户分别指定一个 namespace，限制用户的资源 quota；
3. 细粒度的资源分配：可以给每个 spark 任务指定资源限制，实际指定多少资源就使用多少资源，因为没有了像 yarn 那样的二层调度（圈地式的），所以可以更高效和细粒度的使用资源；
4. 监控的变革：因为做到了细粒度的资源分配，所以可以对用户提交的每一个任务做到资源使用的监控，从而判断用户的资源使用情况，所有的 metric 都记录在数据库中，甚至可以为每个用户的每次任务提交计量；
5. 日志的变革：用户不再通过 yarn 的 web 页面来查看任务状态，而是通过 pod 的 log 来查看，可将所有的 kuberentes 中的应用的日志等同看待收集起来，然后可以根据标签查看对应应用的日志；

所有这些变革都能帮助我们更高效的获的、有效的利用资源，提高生产效率。

