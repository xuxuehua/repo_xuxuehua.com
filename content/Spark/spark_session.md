---
title: "spark_session"
date: 2020-07-18 16:04
---
[toc]





# SparkSession

Spark程序的入口是Driver中的SparkContext。与Spark 1.x相比，在Spark 2.0中，有一个变化是用SparkSession统一了与用户交互的接口，曾经熟悉的SparkContext、SqlContext、HiveContext都是SparkSession的成员变量，这样更加简洁。SparkContext的作用是连接用户编写的代码与运行作业调度和任务分发的代码





## 初始化

```
        val spark = SparkSession
        .builder
        .master("yarn-client")
        .config("spark.reducer.maxSizeInFlight", "128M")
        .appName("your_app_name")
        .getOrCreate()
```

