---
title: "elb"
date: 2019-06-27 09:36
---
[TOC]



# Elastic Load Balance	



## Characteristics

Single CNAME for DNS configuration

Automatically scales based on demands placed on it





## ALB

ALB 是 Amazon Elastic Load Balancing（ELB） 服务的一个负载均衡选项，支持您在运行于一个或多个 EC2 实例上的多个服务或容器之间基于 内容定义路由规则。

可以基于主机的路由：您可以基于 HTTP 标头的“主机”字段路由客户端请求，以 便您从同一个负载均衡器路由到多个域。

也可以基于路径的路由：您可以基于 HTTP 标头的 URL 路径路由客户端请求。

 