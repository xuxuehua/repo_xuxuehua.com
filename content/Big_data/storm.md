---
title: "storm"
date: 2019-07-15 08:41
---
[TOC]



# Storm

实时处理最大的不同就是这类数据跟存储在HDFS上的数据不同，是实时传输过来的，或者形象地说是流过来的，所以针对这类大数据的实时处理系统也叫大数据流计算系统



其实大数据实时处理的需求早已有之，最早的时候，我们用消息队列实现大数据实时处理，如果处理起来比较复杂，那么就需要很多个消息队列，将实现不同业务逻辑的生产者和消费者串起来



![img](https://snag.gy/lovP7p.jpg)

图中的消息队列负责完成数据的流转；处理逻辑既是消费者也是生产者，也就是既消费前面消息队列的数据，也为下个消息队列产生数据。因为不同应用的生产者、消费者的处理逻辑不同，所以处理流程也不同，因此这个系统也就无法复用。



## 架构

![img](https://snag.gy/dSMbma.jpg)

有了Storm后，开发者无需再关注数据的流转、消息的处理和消费，只要编程开发好数据处理的逻辑bolt和数据源的逻辑spout，以及它们之间的拓扑逻辑关系toplogy，提交到Storm上运行就可以了。



![img](https://snag.gy/Gqv6ht.jpg)

nimbus是集群的Master，负责集群管理、任务分配等。supervisor是Slave，是真正完成计算的地方，每个supervisor启动多个worker进程，每个worker上运行多个task，而task就是spout或者bolt。supervisor和nimbus通过ZooKeeper完成任务分配、心跳检测等操作。







