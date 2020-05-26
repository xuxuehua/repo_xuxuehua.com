---
title: "rdd"
date: 2020-05-06 02:44
---
[toc]





# RDD 弹性数据集

RDD是Spark的核心概念，是弹性数据集（Resilient Distributed Datasets）的缩写。RDD既是Spark面向开发者的编程模型，又是Spark自身架构的核心元素。



Spark则直接针对数据进行编程，将大规模数据集合抽象成一个RDD对象，然后在这个RDD上进行各种计算处理，得到一个新的RDD，继续计算处理，直到得到最后的结果数据。所以Spark可以理解成是**面向对象的大数据计算**。



所以在上面WordCount的代码示例里，第2行代码实际上进行了3次RDD转换，每次转换都得到一个新的RDD，因为新的RDD可以继续调用RDD的转换函数，所以连续写成一行代码。事实上，可以分成3行。

```
val rdd1 = textFile.flatMap(line => line.split(" "))
val rdd2 = rdd1.map(word => (word, 1))
val rdd3 = rdd2.reduceByKey(_ + _)
```





Spark也是对大数据进行分片计算，Spark分布式计算的数据分片、任务调度都是以RDD为单位展开的，每个RDD分片都会分配到一个执行进程去处理。



## HelloWorld

变量 lines 是一个 RDD，是从你电脑上的一个本地的文本文件创建出来的。我们可以在这个 RDD 上运行各种并行操作，比如统计这个数据集中的元素个数 (在这里就是文本的行数)，或者是输出第一个元素



### pyspark

Python 行数统计

```

In [1]: lines = sc.textFile("README.md")    # 创建一个名为lines的RDD                                                                       
In [2]: lines.count()                       # 统计RDD中的元素个数                                                                                Out[2]: 104                                                                     

In [3]: lines.first()     # 这个RDD中的第一个元素，也就是README.md的第一行 u'# Apache Spark'                                                                     
Out[3]: '# Apache Spark'

```



### scala

Scala 行数统计

```
scala> val lines = sc.textFile("README.md") // 创建一个名为lines的RDD
lines: spark.RDD[String] = MappedRDD[...]
scala> lines.count() // 统计RDD中的元素个数 res0: Long = 127
scala> lines.first() // 这个RDD中的第一个元素，也就是README.md的第一行 res1: String = # Apache Spark
```





## 惰性计算

Spark并不是按照代码写的操作顺序去生成RDD，比如`rdd2 = rdd1.map(func)`这样的代码并不会在物理上生成一个新的RDD。物理上，Spark只有在产生新的RDD分片时候，才会真的生成一个RDD，Spark的这种特性也被称作**惰性计算**。





## Transformation 转换函数

函数返回值还是RDD

调用以后得到的还是一个RDD，RDD的计算逻辑主要通过转换函数完成。



RDD定义了很多转换函数, 包括很多种



### RDD不会出现新的分片

map 计算

filter 过滤

一个RDD数据分片，经过map或者filter转换操作后，结果还在当前分片。就像你用map函数对每个数据加1，得到的还是这样一组数据，只是值不同。



### RDD会出现新的分片

reduceByKey 聚合

reduceByKey(func, [numPartitions])

来自不同分片的相同Key必须聚合在一起进行操作，这样就会产生新的RDD分片。实际执行过程中，是否会产生新的RDD分片，并不是根据转换函数名就能判断出来的









union 合并数据集

union(otherDataset)



join 连接数据集

join(otherDataset, [numPartitions])



groupByKey( ) 分组

groupByKey([numPartitions])





## Action 执行函数

函数不再返回RDD



count( ) 计数

返回RDD中数据的元素个数





**saveAsTextFile**(path)

将RDD数据存储到path路径下







