---
title: "lambda"
date: 2019-06-28 10:03
---
[TOC]



# Lambda

Just specific coded functions are running only when are needed and without any knowledge of the servers or OS or the language runtime configuration



## Components

### The Function

Event handler

IAM role

Compute amount

Execution timeout





### Event source

S3

DynamoDB

SNS

Kinesis

Gateway API





## Lambda限制

![image-20200324152135665](lambda.assets/image-20200324152135665.png)

![image-20200324152210408](lambda.assets/image-20200324152210408.png)





## 处理指标

![image-20200324152307731](lambda.assets/image-20200324152307731.png)





## 环境变量引用

![image-20200324155832684](lambda.assets/image-20200324155832684.png)





## 开发工具

### SAM

![image-20200324160839021](lambda.assets/image-20200324160839021.png)



![image-20200324160920193](lambda.assets/image-20200324160920193.png)

![image-20200324161007610](lambda.assets/image-20200324161007610.png)





### AWS CDK

![image-20200324161120122](lambda.assets/image-20200324161120122.png)

# 原理

![image-20200324153024282](lambda.assets/image-20200324153024282.png)



## 启动

![image-20200324153305342](lambda.assets/image-20200324153305342.png)



## 优化

![image-20200324153424079](lambda.assets/image-20200324153424079.png)

![image-20200324153718932](lambda.assets/image-20200324153718932.png)

# 调用



## 同步

![image-20200324150557142](lambda.assets/image-20200324150557142.png)



### HTTP API

只是简单的http 调用

![image-20200324150713358](lambda.assets/image-20200324150713358.png)



### HTTP API vs REST API

![image-20200324150839360](lambda.assets/image-20200324150839360.png)



## 异步

![image-20200324151102793](lambda.assets/image-20200324151102793.png)



![image-20200324151300798](lambda.assets/image-20200324151300798.png)



### API GW

![image-20200324151436778](lambda.assets/image-20200324151436778.png)





## 流式

![image-20200324151508727](lambda.assets/image-20200324151508727.png)



### 增强

![image-20200324151820795](lambda.assets/image-20200324151820795.png)

![image-20200324151942870](lambda.assets/image-20200324151942870.png)





# SQS 触发Lambda

![image-20200324152436598](lambda.assets/image-20200324152436598.png)

![image-20200324152558955](lambda.assets/image-20200324152558955.png)



# 解藕

![image-20200324152659640](lambda.assets/image-20200324152659640.png)

![image-20200324152746850](lambda.assets/image-20200324152746850.png)





![image-20200324152900740](lambda.assets/image-20200324152900740.png)





# lambda 并发



## 原理

![image-20200324154300157](lambda.assets/image-20200324154300157.png)



## 并发上升

![image-20200324154358929](lambda.assets/image-20200324154358929.png)



## 并发扩展

![image-20200324154438759](lambda.assets/image-20200324154438759.png)



## 提高并发限制

![image-20200324154513536](lambda.assets/image-20200324154513536.png)

![image-20200324154534274](lambda.assets/image-20200324154534274.png)



## 预配置并发

![image-20200324155001455](lambda.assets/image-20200324155001455.png)

![image-20200324155040904](lambda.assets/image-20200324155040904.png)



## auto scaling

![image-20200324155313570](lambda.assets/image-20200324155313570.png)





# Lambda 层

![image-20200324155723565](lambda.assets/image-20200324155723565.png)



## layer打包

![image-20200324155750532](lambda.assets/image-20200324155750532.png)





