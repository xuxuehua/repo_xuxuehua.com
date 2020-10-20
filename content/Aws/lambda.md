---
title: "lambda"
date: 2019-06-28 10:03
---
[TOC]



# Lambda

Just specific coded functions are running only when are needed and without any knowledge of the servers or OS or the language runtime configuration

AWS Lambda enables developers to write code functions that only contain what their logic requires and have their code be deployed, invoked, made highly reliable, and scaled without having to manage infrastructure whatsoever. 



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

基于ec2

https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html





基于docker

```
docker run -v "$PWD":/var/task "lambci/lambda:build-python3.7" /bin/sh -c "pip install psycopg2-binary -t python/lib/python3.7/site-packages/; exit"
```



```
zip -r9 mypythonlibs.zip python > /dev/null
```



```
aws lambda publish-layer-version --layer-name mypythonlibs --description "My python libs" --zip-file fileb://mypythonlibs.zip --compatible-runtimes "python2.7" "python3.6" "python3.7"

aws lambda update-function-configuration --layers arn:aws:lambda:us-east-2:123456789012:layer:mypythonlibs:1 --function-name my-function
```





You can also use the lambci/lambda Docker images directly for your Lambda package, without creating a layer. Run the following command to get the required versions of your dependencies:

**Note:** Replace **3.6** with **3.7** or **3.8** depending on the compatible libraries that you want to install.

```plainText
docker run -v "$PWD":/var/task "lambci/lambda:build-python3.6" /bin/sh -c "pip install -r requirements.txt -t libs; exit"
```



# example



## lambda to rds

https://aws.amazon.com/blogs/database/query-your-aws-database-from-your-serverless-application/



