---
title: "iam"
date: 2019-06-24 14:47
---
[TOC]



# Identity Access Management

Control of the access and permissions

Top control for AWS 

![image-20200425202649794](iam.assets/image-20200425202649794.png)





## Policy Elements

![image-20200425202634292](iam.assets/image-20200425202634292.png)





## Temporary Security Credentials

Known as AWS STS

15 mins to 36 hours



## Roles

Similar to a user

Assumed by resources requiring the role 

No login credentials

No direct static access keys associated

Allow temporary security credentials

An alternative to credential sharing

No need to define permissions and manage on each entity

Use case: Third-party vendor account access

AWS resource can be launched into roles





# policy generator

https://awspolicygen.s3.amazonaws.com/policygen.html

