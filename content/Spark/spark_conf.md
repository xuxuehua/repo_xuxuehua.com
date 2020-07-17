---
title: "spark_conf"
date: 2020-07-12 19:31
---
[toc]



# SparkConf

对 Spark 进行性能调优，通常就是修改 Spark 应用的运行时配置选项。Spark 中最主要的配 置机制是通过 SparkConf 类对 Spark 进行配置



## spark.driver.memory



## spark.driver.cores



## spark.driver.memoryOverhead





## spark.executor.memory

为每个执行器进程分配的内存，格式与 JVM 内存字符串 格式一样(例如 512m，2g)



## spark.executor.cores

限制应用使用的核心个数的配置项。在 YARN 模式下， spark.executor.cores 会为每个任务分配指定数目的核心



## spark.executor.memoryOverhead



## spark.sql.shuffle.partitions





## spark.default.parallelism





## spark.jars.packages



## spark.jars.repositories



## spark.yarn.dist.files



## spark.jars



## spark.submit.pyFiles



## spark.serializer

默认值为`org.apache.spark.serializer.JavaSerializer`

指定用来进行序列化的类库，包括通过网络传输数据或 缓存数据时的序列化。默认的 Java 序列化对于任何可 以被序列化的 Java 对象都适用，但是速度很慢。我们 推 荐 在 追 求 速 度 时 使 用 org.apache.spark.serializer. KryoSerializer 并且对 Kryo 进行适当的调优。该项可以 配置为任何 org.apache.spark.Serializer 的子类



## spark.sql.catalogImplementation

