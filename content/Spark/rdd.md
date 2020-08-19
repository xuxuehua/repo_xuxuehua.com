---
title: "rdd"
date: 2020-05-06 02:44
---
[toc]





# RDD 弹性数据集

RDD是Spark的核心概念，是弹性数据集（Resilient Distributed Datasets）的缩写。RDD既是Spark面向开发者的编程模型，又是Spark自身架构的核心元素。

Spark则直接针对数据进行编程，将大规模数据集合抽象成一个RDD对象，然后在这个RDD上进行各种计算处理，得到一个新的RDD，继续计算处理，直到得到最后的结果数据。所以Spark可以理解成是**面向对象的大数据计算**。

Spark也是对大数据进行分片计算，Spark分布式计算的数据分片、任务调度都是以RDD为单位展开的，每个RDD分片都会分配到一个执行进程去处理。实质上RDD是一组分布式的JVM不可变对象集合，不可变决定了它是只读的，RDD在经过变换产生新的RDD时，原有RDD不会改变。

对数据的所有操作不外乎创建 RDD、转化已有 RDD 以及调用 RDD 操作进行求值。而在这一切背后，Spark 会自动将 RDD 中的数据分发到集群上，并将操作并行化执行。



用户可以将RDD看作一个数组，数组并没有保存数据，而是每个数据分区的引用，数据以分区中的元素的形式分散保存在集群中的各个节点上。从这个角度上来说，RDD存储的是元数据而非数据本身



## RDD 特征



### 分区

分区代表同一个 RDD 包含的数据被存储在系统的不同节点中，这也是它可以被并行处理的前提

逻辑上，我们可以认为 RDD 是一个大的数组。数组中的每个元素代表一个分区（Partition）

在物理存储中，每个分区指向一个存放在内存或者硬盘中的数据块（Block），而这些数据块是独立的，它们可以被存放在系统中的不同节点

而RDD 只是抽象意义的数据集合，分区内部并不会存储具体的数据

![image-20200703080032711](rdd.assets/image-20200703080032711.png)



RDD 中的每个分区存有它在该 RDD 中的 index。通过 RDD 的 ID 和分区的 index 可以唯一确定对应数据块的编号，从而通过底层存储层的接口中提取到数据进行处理

在集群中，各个节点上的数据块会尽可能地存放在内存中，只有当内存没有空间时才会存入硬盘。这样可以最大化地减少硬盘读写的开销

虽然 RDD 内部存储的数据是只读的，但是，我们可以去修改（例如通过 repartition 转换操作）并行计算单元的划分结构，也就是分区的数量



### 不可变性

代表每一个 RDD 都是只读的，它所包含的分区信息不可以被改变。既然已有的 RDD 不可以被改变，我们只可以对现有的 RDD 进行转换（Transformation）操作，得到新的 RDD 作为中间计算的结果



```
lines = sc.textFile("data.txt")
lineLengths = lines.map(lambda s: len(s))
totalLength = lineLengths.reduce(lambda a, b: a + b)
```

> 读入文本文件 data.txt，创建了第一个 RDD lines，它的每一个元素是一行文本。
>
> 然后调用 map 函数去映射产生第二个 RDD lineLengths，每个元素代表每一行简单文本的字数。
>
> 最后调用 reduce 函数去得到第三个 RDD totalLength，它只有一个元素，代表整个文本的总字数



在一个有 N 步的计算模型中，如果记载第 N 步输出 RDD 的节点发生故障，数据丢失，我们可以从第 N-1 步的 RDD 出发，再次计算，而无需重复整个 N 步计算过程。这样的容错特性也是 RDD 为什么是一个“弹性”的数据集的原因之一



### 并行操作

由于单个 RDD 的分区特性，使得它天然支持并行操作，即不同节点上的数据可以被分别处理，然后产生一个新的 RDD。



# RDD 结构

![image-20200703081302938](rdd.assets/image-20200703081302938.png)





## Spark Context 

SparkContext 是所有 Spark 功能的入口，它代表了与 Spark 节点的连接，可以用来创建 RDD 对象以及在节点中的广播变量等。一个线程只有一个 SparkContext。





## SparkConf

SparkConf 则是一些参数配置信息



## Partitions

代表 RDD 中数据的逻辑结构，每个 Partition 会映射到某个节点内存或硬盘的一个数据块。

Partitioner 决定了 RDD 的分区方式，目前有两种主流的分区方式：Hash partitioner 和 Range partitioner。Hash，顾名思义就是对数据的 Key 进行散列分区，Range 则是按照 Key 的排序进行均匀分区。此外我们还可以创建自定义的 Partitioner。





## Dependencies 依赖关系

Dependencies 是 RDD 中最重要的组件之一

Spark 不需要将每个中间计算结果进行数据复制以防数据丢失，因为每一步产生的 RDD 里都会存储它的依赖关系，即它是通过哪个 RDD 经过哪个转换操作得到的。



### 窄依赖（Narrow Dependency）

窄依赖就是父 RDD 的分区可以一一对应到子 RDD 的分区

窄依赖允许子 RDD 的每个分区可以被并行处理产生

一些转换操作如 map、filter 会产生窄依赖关系， 因为 map 是将分区里的每一个元素通过计算转化为另一个元素，一个分区里的数据不会跑到两个不同的分区

![image-20200703081907470](rdd.assets/image-20200703081907470.png)



窄依赖可以支持在同一个节点上链式执行多条命令，例如在执行了 map 后，紧接着执行 filter

因此，窄依赖的失败恢复更有效，因为它只需要重新计算丢失的父分区即可





### 宽依赖/Shuffle依赖（Wide Dependency）

宽依赖就是父 RDD 的每个分区可以被多个子 RDD 的分区使用

宽依赖则必须等待父 RDD 的所有分区都被计算好之后才能开始处理。

只有父RDD的分区被多个子RDD的分区利用的时候才是宽依赖，其他的情况就是窄依赖

宽依赖还有个名字，叫Shuffle依赖，也就是说宽依赖必然会发生Shuffle操作，在前面也提到过Shuffle也是划分Stage的依据。而窄依赖由于不需要发生Shuffle，所有计算都是在分区所在节点完成，它类似于MapReduce中的ChainMapper。

一些Join、groupBy 则会生成宽依赖关系，因为groupBy 则要将拥有所有分区里有相同 Key 的元素放到同一个目标分区，而每一个父分区都可能包含各种 Key 的元素，所以它可能被任意一个子分区所依赖

![image-20200703082231925](rdd.assets/image-20200703082231925.png)



宽依赖需要所有的父分区都是可用的，可能还需要调用类似 MapReduce 之类的操作进行跨节点传递

从失败恢复的角度考虑，宽依赖牵涉到 RDD 各级的多个父分区，失败恢复并没有窄依赖更有效





## Checkpoint

如果一个 RDD 的依赖链比较长，而且中间又有多个 RDD 出现故障的话，进行恢复可能会非常耗费时间和计算资源

检查点（Checkpoint）的引入，就是为了优化这些情况下的数据恢复

在计算过程中，对于一些计算过程比较耗时的 RDD，我们可以将它缓存至硬盘或 HDFS 中，标记这个 RDD 有被检查点处理过，并且清空它的所有依赖关系。同时，给它新建一个依赖于 CheckpointRDD 的依赖关系，CheckpointRDD 可以用来从硬盘中读取 RDD 和生成新的分区信息。这样，当某个子 RDD 需要错误恢复时，回溯至该 RDD，发现它被检查点记录过，就可以直接去硬盘中读取这个 RDD，而无需再向前回溯计算。



相对于持久化RDD，Checkpoint会清空该RDD的依赖关系，并新建一个CheckpointRDD依赖关系，让该RDD依赖，并保存在磁盘或HDFS文件系统中，当数据恢复时，可通过CheckpointRDD读取RDD进行数据计算；持久化RDD会保存依赖关系和计算结果至内存中，可用于后续计算。



## Storage Level

用来记录 RDD 持久化时的存储级别，常用的有以下几个：

MEMORY_ONLY：只缓存在内存中，如果内存空间不够则不缓存多出来的部分。这是 RDD 存储级别的默认值。

MEMORY_AND_DISK：缓存在内存中，如果空间不够则缓存在硬盘中。

DISK_ONLY：只缓存在硬盘中。

MEMORY_ONLY_2 和 MEMORY_AND_DISK_2 等：与上面的级别功能相同，只不过每个分区在集群中两个节点上建立副本。



## Iterator

迭代函数（Iterator）和计算函数（Compute）是用来表示 RDD 怎样通过父 RDD 计算得到的

迭代函数会首先判断缓存中是否有想要计算的 RDD，如果有就直接读取，如果没有，就查找想要计算的 RDD 是否被检查点处理过。如果有，就直接读取，如果没有，就调用计算函数向上递归，查找父 RDD 进行计算。

# HelloWorld

变量 lines 是一个 RDD，是从你电脑上的一个本地的文本文件创建出来的。我们可以在这个 RDD 上运行各种并行操作，比如统计这个数据集中的元素个数 (在这里就是文本的行数)，或者是输出第一个元素



## pyspark

Python 行数统计

```

In [1]: lines = sc.textFile("README.md")    # 创建一个名为lines的RDD                                                                       
In [2]: lines.count()                       # 统计RDD中的元素个数                                                                                Out[2]: 104                                                                     

In [3]: lines.first()     # 这个RDD中的第一个元素，也就是README.md的第一行 u'# Apache Spark'                                                                     
Out[3]: '# Apache Spark'

```



## scala

Scala 行数统计

```
scala> val lines = sc.textFile("README.md") // 创建一个名为lines的RDD
lines: spark.RDD[String] = MappedRDD[...]
scala> lines.count() // 统计RDD中的元素个数 res0: Long = 127
scala> lines.first() // 这个RDD中的第一个元素，也就是README.md的第一行 res1: String = # Apache Spark
```





# Spark 运行过程

Spark 在每次转换操作的时候，使用了新产生的 RDD 来记录计算逻辑，这样就把作用在 RDD 上的所有计算逻辑串起来，形成了一个链条。当对 RDD 进行动作时，Spark 会从计算链的最后一个 RDD 开始，依次从上一个 RDD 获取数据并执行计算逻辑，最后输出结果



## 惰性计算

Spark并不是按照代码写的操作顺序去生成RDD，比如`rdd2 = rdd1.map(func)`这样的代码并不会在物理上生成一个新的RDD。物理上，Spark只有在产生新的RDD分片时候，才会真的生成一个RDD，Spark的这种特性也被称作**惰性计算**。

RDD的执行是依靠血缘关系延迟计算，如果血缘关系过长，可以通过持久化来切段血缘关系

Spark 并不会立刻计算出新 RDD 中各个分区的数值。直到遇到一个动作时，数据才会被计算，并且输出结果给 Driver。





## RDD 持久化（缓存）

Spark 的 persist() 和 cache() 方法支持将 RDD 的数据缓存至内存或硬盘中，这样当下次对同一 RDD 进行 Action 操作时，可以直接读取 RDD 的结果，大幅提高了 Spark 的计算效率

```
rdd = sc.parallelize([1, 2, 3, 4, 5])
rdd1 = rdd.map(lambda x: x+5)
rdd2 = rdd1.filter(lambda x: x % 2 == 0)
rdd2.persist()
count = rdd2.count() // 3
first = rdd2.first() // 6
rdd2.unpersist()
```

> 在第四行我把 RDD2 的结果缓存在内存中，所以 Spark 无需从一开始的 rdd 开始算起了（持久化处理过的 RDD 只有第一次有 action 操作时才会从源头计算，之后就把结果存储下来，所以在这个例子中，count 需要从源头开始计算，而 first 不需要）。



持久化可以选择不同的存储级别。正如我们讲 RDD 的结构时提到的一样，有 MEMORY_ONLY，MEMORY_AND_DISK，DISK_ONLY 等。cache() 方法会默认取 MEMORY_ONLY 这一级别



# Transformation 转换函数/算子

转换算子主要负责改变RDD中数据、切分RDD中数据、过滤掉某些数据等。转换算子按照一定顺序组合，Spark会将其放入到一个计算的有向无环图中，并不立刻执行，当Driver请求某些数据时，才会真正提交作业并触发计算

函数返回值还是RDD

Spark提供了很多对 RDD 的操作，如 Map、Filter、flatMap、groupByKey 和 Union 等等，极大地提升了对各种复杂场景的支持。开发者既不用再绞尽脑汁挖掘 MapReduce 模型的潜力，也不用维护复杂的 MapReduce 状态机。



## 通用类

这一类可以满足绝大多数需要，特别适合通用分析型需求。





### map 计算 

map 是最基本的转换操作

一对一映射

将原RDD分区中T类型的数据元素转换成U类型，并返回为一个新RDD。map算子会作用于分区内的每个元素

与 MapReduce 中的 map 一样，它把一个 RDD 中的所有数据通过一个函数，映射成一个新的 RDD，任何原 RDD 中的元素在新 RDD 中都有且只有一个元素与之对应

![image-20200718182119691](rdd.assets/image-20200718182119691.png)

```
rdd = sc.parallelize(["b", "a", "c"])
rdd2 = rdd.map(lambda x: (x, 1)) 

>>>
[('b', 1), ('a', 1), ('c', 1)]
```





### mapPartitions

mapPartitions 是 map 的变种。不同于 map 的输入函数是应用于 RDD 中每个元素，mapPartitions 的输入函数是应用于 RDD 的每个分区，也就是把每个分区中的内容作为整体来处理的，所以输入函数的类型是 Iterator[T] => Iterator[U]

mapPartitions的高阶函数是直接将分区数据看成一个迭代器，因此map的高阶函数执行次数与元素个数相同，mapPartitions的高阶函数执行次数与分区数相同，后者对初始化特别耗费资源的场景特别友好，例如，在插入数据库时，mapPartitions处理每个分区的元素只需要每个分区初始化一次连接，而不用像map一样每个元素都初始化一次。DataFrame API和Spark SQL默认会对程序进行mapPartitions的优化。

```

rdd = sc.parallelize([1, 2, 3, 4], 2)

def f(iterator): 
    yield sum(iterator)

rdd2 = rdd.mapPartitions(f) 

>>>
[3, 7]
```

> mapPartitions 的输入函数是对每个分区内的元素求和，所以返回的 RDD 包含两个元素：1+2=3 和 3+4=7



### reduceByKey 聚合 

reduceByKey算子执行的是归约操作，针对相同键的数据元素两两进行合并。在合并之前，reduceByKey算子需要将相同键的元素分发到同一个分区中去，分发规则可以自定义，分发的分区数量也可以自定义，因此该算子还可以接收分区器或者分区数作为参数，分区器在没有指定时，采用的是RDD内部的散列分区器

![image-20200718182356258](rdd.assets/image-20200718182356258.png)

```
rdd.reduceByKey((x, y) => x + y)

>>>
{(1, 2), (3, 10)}
```





### flatMap

flatMap算子的字面意思是“展平”, flatMap算子的函数参数的作用是将T类型的数据元素转换为元素类型为U的集合，如果处理过程到此为止，那么将RDD_1的一个分区看成一个集合的话，分区数据结构就相当于集合的集合，这种数据结构就不是“平”的，而是有层次的，因此flatMap算子还做了一个操作——将集合的集合合并为一个集合

![image-20200718182454998](rdd.assets/image-20200718182454998.png)







### distinct

distinct算子的功能是对RDD所有数据元素进行去重，它由reduceByKey算子实现。







### filter 过滤 

filter 这个操作，是选择原 RDD 里所有数据中满足某个特定条件的数据，去返回一个新的 RDD

按照某种规则过滤掉某些数据，若f返回值为true则保留，false则丢弃，filter算子作用之后，可能会造成大量零碎分区，不利于后面计算过程，需要在计算之前进行合并

```
rdd = sc.parallelize([1, 2, 3, 4, 5])
rdd2 = rdd.filter(lambda x: x % 2 == 0) 

>>>
[2, 4]
```

> 返回所有的偶数



### sortByKey

sortByKey算子是一个有意思的算子，它虽然只是完成了简单的排序操作，但是其过程值得探讨。从字面意思来看，执行过程先按照数据元素的键进行分发，然后各分区再进行排序操作，但是如果分发规则按照默认的散列分区器来执行，无疑达不到全排序的效果，除非分区数参数设置（numPartitions）为1



### groupByKey( ) 分组

groupByKey([numPartitions])

多对多映射

groupByKey 和 SQL 中的 groupBy 类似，是把对象的集合按某个 Key 来归类，返回的 RDD 中每个 Key 对应一个序列。即对具有相同键的值进行分组

groupByKey在统计分析中经常用到，是分组计算的前提，它默认按照散列分区器进行分发，将同一个键的数据元素放入同一个迭代器中，为后面的汇总操作做准备，它的可选参数分区数、分区器

```
rdd = sc.parallelize([("a", 1), ("b", 1), ("a", 2)])
rdd.groupByKey().collect()
>>>
"a" [1, 2]
"b" [1]
```

















### combineByKey

combineByKey算子属于RDD核心算子，很多聚合类算子底层都是由该算子实现，如groupByKey、reduceByKey、aggregateByKey等，它相比于其他算子，也是最灵活的。createCombiner会将分区内第一个V类型元素转换成C类型，mergeValue的作用是合并前面得到的C类型元素与当前V类型元素并得到C类型元素结果，mergeValue会不断执行，直到最后一个元素。MapSide Combine与mergeCombiners是优化项，默认开启，类似于MapReduce中的Combiner，它可以在Map端，也就是在Shuffle之前就做合并，这样可以大大减少Shuffle数据量，这种优化适用于求最大值、最小值这类操作，mergeCombiners作用在其他分区的输出结果上，在Map端合并数据



### mapValues 

计算每个键的对应值的均值

![image-20200712110336978](rdd.assets/image-20200712110336978.png)



## 数学/统计类

这类算子实现的是某些常用的数学或者统计功能，如分层抽样等。





### sampleByKey

分层抽样是将数据元素按照不同特征分成不同的组，然后从这些组中分别抽样数据元素。Spark内置了实现这一功能的算子sampleByKey, withReplacement参数表示此次抽象是重置抽象还是不重置抽样，所谓重置抽样就是“有放回的抽象”，单次抽样后会放回。fractions是每个键的抽样比例，以Map的形式提供。seed为随机数种子，一般设置为当前时间戳

## 集合论与关系类



这类算子主要实现的是像连接数据集这种功能和其他关系代数的功能，如交集、差集、并集、笛卡儿积等。

### cogroup

cogroup算子相当于多个数据集一起做groupByKey操作，生成的Pair RDD的数据元素类型为(K, (Iterable[V],Iterable[W]))，其中第1个迭代器为当前键在RDD_0中的分组结果，第2个迭代器为RDD_1的结果

![image-20200719085657855](rdd.assets/image-20200719085657855.png)







### union 合并数据集 

union(otherDataset)

多对一映射

union算子将两个同类型的RDD合并为一个RDD，类似于求并集的操作





### join 连接数据集 

join(otherDataset, [numPartitions])

join算子实现的是SQL中的内连接操作，该算子是一个组合算子，由cogroup与flatMap算子组合而成。在前面，我们知道了cogroup算子最后会将同一个键在左表（RDD_0）和右表（RDD_1）中的数据分别保存在两个集合里，要完成连接操作，还需要对这两个集合进行笛卡儿积，完成后需要展开为多行，该操作由flatMap算子完成。fullOuterJoin（全连接）、leftOuterJoin（左外连接）、rightOuterJoin（右外连接）会根据连接语义在Shuffle之前保留或过滤某些键的数据





### subtractByKey

假设当前RDD的数据为全集，subtractByKey算子返回当前RDD与other RDD的交集的补集



### intersection

假设当前RDD的数据为全集，intersection算子返回当前RDD与other RDD的交集



### cartesian

cartesian算子的功能是对数据集中的数据元素两两连接，即求笛卡儿积，在大数据量下，该操作的性能消耗非常大，且输出文件的大小也会暴涨，需要谨慎使用



## 数据结构类

这类算子主要改变的是RDD中底层的数据结构，即RDD中的分区。在这些算子中，你可以直接操作分区而不需要访问这些分区中的元素。在Spark应用中，当你需要更高效地控制集群中的分区和分区的分发时，这些算子会非常有用。通常，根据集群状态、数据规模和使用方式有针对性地对数据进行重分区可以显著提升性能。默认情况下，RDD使用散列分区器对集群中的数据进行分区。分区数与集群中的节点数无关，很可能集群中的单个节点有几个数据分区。数据分区数一般取决于数据量和集群节点数。作业中的某个计算任务的输入是否在本地，这称为数据的本地性，计算任务会尽可能地优先选择本地数据。

### partitionBy

partitionBy会按照传入的分发规则对RDD进行重分区，分发规则由自定义分区器实现



### coalesce

coalesce会试图将RDD中分区数变为用户设定的分区数（numPartitions），从而调整作业的并行程度。如果用户设定的分区数（100）小于RDD原有分区数（1000），则会进行本地合并，而不会进行Shuffle，如果用户设定的分区数大于RDD原有分区数，则不会触发操作。如果需要增大分区数，则需要将shuffle参数设定为true，这样数据就会通过散列分区器将数据进行分发，以达到增加分区的效果。还有一种情况，如果当用户设置分区数为1时，这时如果shuffle参数为false，会在某些节点造成极大的性能负担，用户可以设置shuffle参数为true来汇总分区的上游计算过程并行执行。repartition是coalesce默认开启shuffle的简单封装



### zipWithIndex

zipWithIndex算子与Scala中的zipWithIndex函数类似，都是用一个自增长的整数集合与目标集合做一个类似“拉链”的操作，合并为一个新的集合，集合中每一个元素都会有一个自增长的序号。RDD的zipWithIndex算子也是如此，RDD分区内的每一个元素都会有一个自增长的序号，从zipWithIndex算子生成的RDD也可看出端倪，此RDD的第一个分区内的第一个元素的编号为0，最后一个分区的最后一个元素的编号最大。





# Action 执行函数/算子

与转换算子的最大不同之处在于：转换算子返回的还是RDD，行动算子返回的是非RDD类型的值，如整数，或者根本没有返回值。

行动算子可以分为Driver和分布式两类。

## Driver

这种算子返回值通常为Driver内部的内存变量，如collect、count、countByKey等。这种算子会在远端Executor执行计算完成后将结果数据传回Driver。这种算子有个缺点是返回的数据如果太大，很容易会突破Driver内存限制，因此使用这种算子作为作业结束需要谨慎。



### collect

RDD 中的动作操作 collect 与函数式编程中的 collect 类似，它会以数组的形式，返回 RDD 的所有元素。

需要注意的是，collect 操作只有在输出数组所含的数据数量较小时使用，因为所有的数据都会载入到程序的内存中，如果数据量较大，会占用大量 JVM 内存，导致内存溢出

```
rdd = sc.parallelize(["b", "a", "c"])
rdd.map(lambda x: (x, 1)).collect() 

>>>
[('b', 1), ('a', 1), ('c', 1)]
```



### count 计数

返回RDD中数据的元素个数

```
sc.parallelize([2, 3, 4]).count() 

>>>
3
```



### countByKey

仅适用于 Key-Value pair 类型的 RDD，返回具有每个 key 的计数的 的 map

```
rdd = sc.parallelize([("a", 1), ("b", 1), ("a", 1)])
sorted(rdd.countByKey().items()) 

>>>
[('a', 2), ('b', 1)]
```



### reduce

与转换算子reduce类似，会用函数参数两两进行归约，直到最后一个值，返回值类型与RDD元素相同

```
from operator import add
sc.parallelize([1, 2, 3, 4, 5]).reduce(add)  

>>>
15
```



### take

take算子会取出RDD前n个元素作为数组返回。first算子等于take(1)。这两个算子会直接根据下表返回而不会排序



### takeOrdered

takeOrdered算子与top算子类似于实现Top N的功能，但对于同样的数据集与参数，takeOrdered算子与top算子总是按照相反的排序规则返回前n个元素。top算子内部是利用takeOrdered算子并反转排序器实现





### max

返回数据集中的最大值，利用reduce算子实现。



### min

返回数据集中的最小值，利用reduce算子实现。



### countByKey

按照值进行分组统计，返回值类型为Map。



### countByValue

按照键进行分组统计，返回值类型为Map。



### foreach(

foreach算子迭代RDD中的每个元素，并且可以自定义输出操作，通过用户传入的函数，可以实现打印、插入到外部存储、修改累加器等迭代所带来的副作用。



## 分布式

与前一类算子将结果回传到Driver不同，这类算子会在集群中的节点上“就地”分布式执行，如saveAsTextFile。这是一种最常用的分布式行动算子。



### saveAsTextFile

该算子会将RDD输出到外部文件系统中，例如HDFS。



### saveAsObjectFile

该算子会将RDD中的元素序列化成对象存储到文件中，以SequenceFile的形式存储。



### persist  / unpersist

其中cache()=persist(MEMORY_ONLY),Spark会在作业执行过程中采用LRU策略来更新缓存，如果用户想要手动移除缓存的话，也可以采用unpersist算子手动释放缓存。其中persist可以选择存储级别

![image-20200719093623990](rdd.assets/image-20200719093623990.png)

> 如果内存足够大，使用MEMORY_ONLY无疑是性能最好的选择，想要节省点空间的话，可以采取MEMORY_ONLY_SER，可以序列化对象使其所占空间减少一点。DISK是在重算的代价特别昂贵时的不得已的选择。MEMORY_ONLY_2和MEMORY_AND_DISK_2拥有最佳的可用性，但是会消耗额外的存储空间





# Example

## RDD for wordCount

在 Spark 2.0 之后，随着新的 DataFrame/DataSet API 的普及化，Spark 引入了新的 SparkSession 对象作为所有 Spark 任务的入口。

SparkSession 不仅有 SparkContext 的所有功能，它还集成了所有 Spark 提供的 API，比如 DataFrame、Spark Streaming 和 Structured Streaming，我们再也不用为不同的功能分别定义 Context。

```
spark = SparkSession
       .builder
       .appName(appName)
       .getOrCreate()
text_file = spark.read.text("file://….").rdd.map(lambda r: r[0])
```



1. 把每行的文本拆分成一个个词语,  可以用 flatMap 去把行转换成词语

2. 统计每个词语的频率, 可以先把每个词语转换成（word, 1）的形式，然后用 reduceByKey 去把相同词语的次数相加起来。

```
counts = lines.flatMap(lambda x: x.split(' '))
                  .map(lambda x: (x, 1))
                  .reduceByKey(add)
```

3. 只有当碰到 action 操作后，这些转换动作才会被执行, 可以用 collect 操作把结果按数组的形式返回并输出

```
output = counts.collect()
for (word, count) in output:
    print("%s: %i" % (word, count))
spark.stop() // 停止SparkSession
```



