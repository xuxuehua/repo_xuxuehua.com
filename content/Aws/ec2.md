---
title: "ec2"
date: 2019-06-24 09:48
---
[TOC]



# Elastic Compute Cloud

Deploy across AWS Regions and Availablity Zones for reliability



## Characteristic

All data is automatically deleted when an EC2 instance stops, fails or is terminated

## Launch 

Can be launch from a pre-configured Amazon Machine Image (AMI)



### User data

In Advanced Details, here we could input below data for bootstrap

```
#!/bin/bash
yum -y install httpd
systemctl start httpd 
if [ ! -f /var/www/html/lab2-app.tar.gz ]; then 
cd /var/www/html
wget https://us-west-2-aws-training.s3.amazonaws.com/awsu-ilt/AWS-100-ESS/v4.1/lab-2-configure-website-datastore/scripts/lab2-app.tar.gz

tar xvfz lab2-app.tar.gz
chown apache:root /var/www/html/rds.config.php
fi
```



query user-data info

```
curl http://169.254.169.254/latest/user-data
```



### Metadata Service

e.g.

```
http://169.254.169.254/latest/meta-data/iam/security-credentials/rolename
```





## Instance Technologies

![image-20200313170002408](ec2.assets/image-20200313170002408.png)



## Current Generation Instances

![image-20200313170015940](ec2.assets/image-20200313170015940.png)



## Reboot vs Stop vs Terminate 

![image-20200313170029266](ec2.assets/image-20200313170029266.png)





# Auto-scaling



## Launch configurations

Specify below info when launch a instance

```
AMI ID
Instance type
Key pair
Security groups
Block deivce mapping
User data
```



![image-20200313170041422](ec2.assets/image-20200313170041422.png)



## Load Balancer Types

![image-20200313170057040](ec2.assets/image-20200313170057040.png)







# EC2 Spot Instance

EC2 Spot 是 AWS服务中的可用空闲计算容量。与按需实例的价格相比，这类实例可提供超低折扣

可在预算相同的情况下将应用程序的吞吐量提高到10倍。您只需在启动 EC2 实例时选择“Spot”，即可节省按需实例价格的 90%；



The Spot price of each instance type in each Availability Zone is set by Amazon EC2, and adjusted gradually based on the long-term supply of and demand for Spot Instances. 

按需实例和 Spot 实例的唯一区别在于，当 EC2 需要更多容量时，它会发出两分钟的通知继而中断 Spot 实例

EC2 Spot 还可与其他 AWS 产品紧密集成，包括 EMR、Auto Scaling、Elastic Container Service (ECS)、CloudFormation等，让您可以灵活选择如何启动和维护 Spot 实例上运行的应用程序



## 购买

Spot实例的现货价格根据供需情况定期变化

价格将根据供需关系确定（不超过按需实例价格）；用户也可以设置一个最高价，当设置的最高价高于当前现货价格的期间内运行此类实例。

现货价格 – Spot 实例当前的每小时市场价格，该价格由 Amazon EC2 根据执行的最后出价设置



## Spot 实例池 Spot Instance Pool

一组未使用的 EC2 实例，具有相同的实例类型、操作系统、可用区,和网络平台



## Spot队列  Spot Fleet

一组基于指定条件启动的 Spot 实例。 

Spot 队列会选择满足您的需要的 Spot 实例池，并启动 Spot 实例以满足队列的目标容量。

默认情况下，在队列中的 Spot 实例终止之后，系统会启动替代实例以维持 Spot 队列的目标容量。您可以将 Spot 队列作为一次性请求来提交，这种请求在实例终止后不会被保留。





## Spot blocks

Spot Instances with a specified duration, which are designed not to be interrupted and will run continuously for the duration you select

 In rare situations, Spot blocks may be interrupted due to Amazon EC2 capacity needs. 





## 中断

用户可以通过EC2的元数据中instance-action条目获取到该实例的状态，可以按如下方式检索它：

```
curl http://169.254.169.254/latest/meta-data/spot/instance-action
```

instance-action 项目指定操作 (停止或终止) 和操作发生的大致时间 (用 UTC 表示)。以下示例指示将停止此实例的时间：

```
{"action": "stop", "time": "2017-09-18T08:22:00"}
```

以下示例指示将终止此实例的时间：

```
{"action": "terminate", "time": "2017-09-18T08:22:00"}
```











