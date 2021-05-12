---
title: "eks"
date: 2020-06-20 11:21
---
[toc]





# 容器服务

![image-20210512090824582](eks.assets/image-20210512090824582.png)

# EKS





## 架构组件

![image-20210512090947462](eks.assets/image-20210512090947462.png)



![image-20210512091347124](eks.assets/image-20210512091347124.png)



![image-20210512091521562](eks.assets/image-20210512091521562.png)



![image-20210512092037568](eks.assets/image-20210512092037568.png)



![image-20210512092932484](eks.assets/image-20210512092932484.png)





## Compute nodes comparison

![image-20210512095600262](eks.assets/image-20210512095600262.png)





## Maintenance

![image-20210512095726236](eks.assets/image-20210512095726236.png)





![image-20210512095919553](eks.assets/image-20210512095919553.png)





![image-20210512100010924](eks.assets/image-20210512100010924.png)



![image-20210512100117740](eks.assets/image-20210512100117740.png)



![image-20210512100420136](eks.assets/image-20210512100420136.png)





## Service Level Agreement

```
master == 99.95%
```





# Network



![image-20210512133724504](eks.assets/image-20210512133724504.png)



![image-20210512133812545](eks.assets/image-20210512133812545.png)



## Amazon VPC CNI



![image-20210512134138810](eks.assets/image-20210512134138810.png)





![image-20210512134418423](eks.assets/image-20210512134418423.png)



## Pod security group (推荐)

![image-20210512134729613](eks.assets/image-20210512134729613.png)



## Network policy

EKS 兼容 Network policy

Network policy是allow list

![image-20210512134904542](eks.assets/image-20210512134904542.png)





## Ingress 暴露服务

![image-20210512135703445](eks.assets/image-20210512135703445.png)





![image-20210512135716639](eks.assets/image-20210512135716639.png)









![image-20210512135740483](eks.assets/image-20210512135740483.png)

> Mode instance node port 有限制，3200-33000
>
> Mode IP 没有node port 限制， 推荐使用





### NLB

适用自定义的协议和端口

只能做单纯的转发

![image-20210512140122920](eks.assets/image-20210512140122920.png)



### ALB

支持ALB可以做的很多事情

![image-20210512140535395](eks.assets/image-20210512140535395.png)



![image-20210512140805037](eks.assets/image-20210512140805037.png)



![image-20210512140908233](eks.assets/image-20210512140908233.png)



![image-20210512141209461](eks.assets/image-20210512141209461.png)





![image-20210512141600754](eks.assets/image-20210512141600754.png)



### Ingress Group （复用ALB）

![image-20210512141630988](eks.assets/image-20210512141630988.png)



![image-20210512141720488](eks.assets/image-20210512141720488.png)



### TLS加密

ALB无法通过一本证书通过https连到后面的pod，如果需要只能通过NLB实现

![image-20210512141826604](eks.assets/image-20210512141826604.png)





# Cluster

## worker node scale in/out

![image-20210512100609224](eks.assets/image-20210512100609224.png)



![image-20210512100523178](eks.assets/image-20210512100523178.png)



![image-20210512100658543](eks.assets/image-20210512100658543.png)







## Cluster Autoscaler

![image-20210512101004826](eks.assets/image-20210512101004826.png)



![image-20210512161102694](eks.assets/image-20210512161102694.png)



![image-20210512161614383](eks.assets/image-20210512161614383.png)





## CA + HPA



![image-20210512162336328](eks.assets/image-20210512162336328.png)



![image-20210512162005800](eks.assets/image-20210512162005800.png)





### handle spot node

```
https://github.com/aws/aws-node-termination-handler
```









# Pod

## Horizontal Pod Autoscaler (HPA)

![image-20210512100808400](eks.assets/image-20210512100808400.png)



![image-20210512100830157](eks.assets/image-20210512100830157.png)





![image-20210512155825666](eks.assets/image-20210512155825666.png)





## Vertical Pod Autoscaler (VPA)

VPA监控所有的pod，去收集数据，升级container配置的推荐值，防止OOM

目前，VPA不能在运行的pod，应用pod的变更

![image-20210512160519335](eks.assets/image-20210512160519335.png)



# Storage



## Container Storage Interface (CSI)

![image-20210512150329386](eks.assets/image-20210512150329386.png)



![image-20210512150523843](eks.assets/image-20210512150523843.png)



![image-20210512151013242](eks.assets/image-20210512151013242.png)







## PV/PVC/StorageClass

![image-20210512150808947](eks.assets/image-20210512150808947.png)





![image-20210512151057177](eks.assets/image-20210512151057177.png)



![image-20210512151437674](eks.assets/image-20210512151437674.png)







## 生命周期

![image-20210512151356430](eks.assets/image-20210512151356430.png)





# Workload Scaling





# ECR

![image-20210512093910364](eks.assets/image-20210512093910364.png)





# Tools

## Eks best practices

https://aws.github.io/aws-eks-best-practices/



## EKS workshop

https://www.eksworkshop.com/010_introduction/



