---
title: "hadoop"
date: 2020-04-21 22:41
---
[toc]





# Hadoop





## 序列化

把内存中的数据字节化

hadoop 开发了一套序列化机制 Writable， 

相对于Java重量级的序列化，会携带额外的信息（校验，header，继承体）等，不便在网络中传输，hadoop只是序列化必要的信息，高效使用存储空间，读写数据开销小





# Hadoop fs cli



## Local to HDFS

​    put
​    copyFromLocal
​    moveFromLocal
​    appendToFile



## HDFS to HDFS

​    cp
​    mv
​    chown
​    chgrp
​    chmod
​    mkdir
​    du
​    df
​    cat
​    

## HDFS to Local

​    get
​    getmerge
​    copyToLocal



