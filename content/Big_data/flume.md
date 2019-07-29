---
title: "flume 日志收集"
date: 2019-07-25 08:51
---
[TOC]



# Flume

日志也是大数据处理与分析的重要数据来源之一，应用程序日志一方面记录了系统运行期的各种程序执行状况，一方面也记录了用户的业务处理轨迹。依据这些日志数据，可以分析程序执行状况，比如应用程序抛出的异常；也可以统计关键业务指标，比如每天的PV、UV、浏览数Top N的商品等。

Flume是大数据日志收集常用的工具。Flume最早由Cloudera开发，后来捐赠给Apache基金会作为开源项目运营。



## 架构

![img](https://snag.gy/x6b8Hz.jpg)

Agent Source负责收集日志数据，支持从Kafka、本地日志文件、Socket通信端口、Unix标准输出、Thrift等各种数据源获取日志数据。

Source收集到数据后，将数据封装成event事件，发送给Channel。Channel是一个队列，有内存、磁盘、数据库等几种实现方式，主要用来对event事件消息排队，然后发送给Sink。

Sink收到数据后，将数据输出保存到大数据存储设备，比如HDFS、HBase等。Sink的输出可以作为Source的输入，这样Agent就可以级联起来，依据具体需求，组成各种处理结构



![img](https://snag.gy/0TMbSF.jpg)

这是一个日志顺序处理的多级Agent结构，也可以将多个Agent输出汇聚到一个Agent，还可以将一个Agent输出路由分发到多个Agent，根据实际需求灵活组合。







