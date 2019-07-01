---
title: "sqs"
date: 2019-06-20 17:51
---
[TOC]



# Simple Queue Service

可扩展的可以持久化的消息队列服务

Decouple and scale microservices, distributed systems, and serverless applications. 

SQS eliminates the complexity and overhead associated with managing and operating message oriented middleware

send, store, and receive messages between software components at any volume, without losing messages or requiring other services to be available. 

SQS requires applications to poll constantly (pull approach)



![img](https://snag.gy/EJh9Py.jpg)





## Standard queues

offer maximum throughput, best-effort ordering, and at-least-once delivery. 



## SQS FIFO queues 

Designed to guarantee that messages are processed exactly once, in the exact order that they are sent.