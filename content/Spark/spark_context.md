---
title: "spark_context"
date: 2020-06-15 01:38
---
[toc]



# SparkContext (Deprecated by SparkSession)



## 初始化SparkContext

在 Python 中初始化 Spark

```
from pyspark import SparkConf, SparkContext
conf = SparkConf().setMaster("local").setAppName("My App") 
sc = SparkContext(conf = conf)
```



在 Scala 中初始化 Spark

```
import org.apache.spark.SparkConf 
import org.apache.spark.SparkContext 
import org.apache.spark.SparkContext._
val conf = new SparkConf().setMaster("local").setAppName("My App") 
val sc = new SparkContext(conf)
```



在 Java 中初始化 Spark

```
import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaSparkContext;
SparkConf conf = new SparkConf().setMaster("local").setAppName("My App"); 
JavaSparkContext sc = new JavaSparkContext(conf);
```



## 关闭

关闭 Spark 可以调用 SparkContext 的 stop() 方法，或者直接退出应用(比如通过 System.exit(0) 或者 sys.exit())