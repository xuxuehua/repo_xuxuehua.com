---
title: "spark"
date: 2019-07-05 14:06
---
[TOC]



# Spark

Hadoop MapReduce虽然已经可以满足大数据的应用场景，但是UC Berkeley的AMP Lab推出的Spark应运而生，Spark拥有更快的执行速度和更友好的编程接口，在推出后短短两年就迅速抢占MapReduce的市场份额，成为主流的大数据计算框架。



## 特点

Spark支持Yarn和HDFS，公司迁移到Spark上的成本很小，于是很快，越来越多的公司用Spark代替MapReduce



除了速度更快，Spark和MapReduce相比，还有更简单易用的编程模型。使用Scala语言在Spark上编写WordCount程序，主要代码只需要三行。

```
val textFile = sc.textFile("hdfs://...")
val counts = textFile.flatMap(line => line.split(" "))
                 .map(word => (word, 1))
                 .reduceByKey(_ + _)
counts.saveAsTextFile("hdfs://...")
```

> 第1行：根据HDFS路径生成一个输入数据RDD。
>
> 第2行：在输入数据RDD上执行3个操作，得到一个新的RDD。
>
> - 将输入数据的每一行文本用空格拆分成单词。
> - 将每个单词进行转换，`word => (word, 1)`，生成<Key, Value>的结构。
> - 相同的Key进行统计，统计方式是对Value求和，`(_ + _)`。
>
> 第3行：将这个RDD保存到HDFS。



RDD是Spark的核心概念，是弹性数据集（Resilient Distributed Datasets）的缩写。RDD既是Spark面向开发者的编程模型，又是Spark自身架构的核心元素。













