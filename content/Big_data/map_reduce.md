---
title: "map_reduce"
date: 2019-06-12 15:54
---
[TOC]



# 分布式计算

Hadoop MapReduce的出现，使得大数据计算通用编程成为可能。我们只要遵循MapReduce编程模型编写业务处理逻辑代码，就可以运行在Hadoop分布式集群上，无需关心分布式计算是如何完成的。也就是说，我们只需要关心业务逻辑，不用关心系统调用与运行环境，这和我们目前的主流开发方式是一致的。



# Map Reduce 模型

**MapReduce既是一个编程模型，又是一个计算框架**。也就是说，开发人员必须基于MapReduce编程模型进行编程开发，然后将程序通过MapReduce计算框架分发到Hadoop集群中运行

该编程模型只包含Map和Reduce两个过程，map的主要输入是一对<Key, Value>值，经过map计算后输出一对<Key, Value>值；然后将相同Key合并，形成<Key, Value集合>；再将这个<Key, Value集合>输入reduce，经过计算输出零个或多个<Key, Value>对。

同时，MapReduce又是非常强大的，不管是关系代数运算（SQL计算），还是矩阵运算（图计算），大数据领域几乎所有的计算需求都可以通过MapReduce编程来实现



## 处理过程

WordCount主要解决的是文本处理中词频统计的问题，就是统计文本中每一个单词出现的次数。如果只是统计一篇文章的词频，几十KB到几MB的数据，只需要写一个程序，将数据读入内存，建一个Hash表记录每个词出现的次数就可以了。



```python
# 文本前期处理
strl_ist = str.replace('\n', '').lower().split(' ')
count_dict = {}
# 如果字典里有该单词则加1，否则添加入字典
for str in strl_ist:
if str in count_dict.keys():
    count_dict[str] = count_dict[str] + 1
    else:
        count_dict[str] = 1
```

简单说来，就是建一个Hash表，然后将字符串里的每个词放到这个Hash表里。如果这个词第一次放到Hash表，就新建一个Key、Value对，Key是这个词，Value是1。如果Hash表里已经有这个词了，那么就给这个词的Value + 1。

小数据量用单机统计词频很简单，但是如果想统计全世界互联网所有网页（数万亿计）的词频数（而这正是Google这样的搜索引擎的典型需求），不可能写一个程序把全世界的网页都读入内存，这时候就需要用MapReduce编程来解决



WordCount的MapReduce程序如下

```
public class WordCount {

  public static class TokenizerMapper
       extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
      }
    }
  }

  public static class IntSumReducer
       extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
    }
  }
}
```











