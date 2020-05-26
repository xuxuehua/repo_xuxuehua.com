---
title: "spark_shell"
date: 2020-05-04 18:25
---
[toc]



# Spark shell

Spark 带有交互式的 shell，可以作即时数据分析。

类似 R、Python、Scala 所 提供的 shell，然而和其他 shell 工具不一样的是，在其他 shell 工具中你只能使 用单机的硬盘和内存来操作数据，而 Spark shell 可用来与分布式存储在许多机器的内存或 者硬盘上的数据进行交互，并且处理过程的分发由 Spark 自动控制完成。



## pyspark



### Initialize locally

```
wget https://mirrors.tuna.tsinghua.edu.cn/apache/spark/spark-2.4.5/spark-2.4.5-bin-hadoop2.7.tgz

export SPARK_LOCAL_HOSTNAME=localhost
```



```
wget https://mirrors.huaweicloud.com/java/jdk/8u201-b09/jdk-8u201-linux-x64.tar.gz

vim /etc/profile
export JRE_HOME=/root/java_web/jdk1.8.0_201/jre
export JAVA_HOME=/root/java_web/jdk1.8.0_201
export JAVA_BIN=/root/java_web/jdk1.8.0_201/bin
PATH=$PATH:$JAVA_BIN
```



```
root@ubuntu:/mnt/spark-2.4.5-bin-hadoop2.7# ./bin/pyspark 
Python 2.7.17 (default, Apr 15 2020, 17:20:14) 
[GCC 7.5.0] on linux2
Type "help", "copyright", "credits" or "license" for more information.
20/05/05 18:18:17 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties
Setting default log level to "WARN".
To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /__ / .__/\_,_/_/ /_/\_\   version 2.4.5
      /_/

Using Python version 2.7.17 (default, Apr 15 2020 17:20:14)
SparkSession available as 'spark'.
```



### log4j 

Spark 开发者们已经在 Spark 中加入了一个日志设置文件的模版，叫作 log4j.properties.template

可以先把这个日志设置模版文件复制一份到 conf/log4j. properties 来作为日志设置文件，接下来找到下面这一行:

```
log4j.rootCategory=INFO, console
```

然后通过下面的设定降低日志级别，只显示警告及更严重的信息:

```
log4j.rootCategory=WARN, console
```

从而减少日志输出



### ipython

IPython 是一个受许多 Python 使用者喜爱的增强版 Python shell，能够提供自 动补全等好用的功能。

```

(spark-2.4.5-bin-hadoop2.7) root@ubuntu:/mnt/spark-2.4.5-bin-hadoop2.7# PYSPARK_DRIVER_PYTHON=ipython ./bin/pyspark
Python 3.6.9 (default, Apr 18 2020, 01:56:04) 
Type 'copyright', 'credits' or 'license' for more information
IPython 7.14.0 -- An enhanced Interactive Python. Type '?' for help.
20/05/05 18:32:32 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties
Setting default log level to "WARN".
To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /__ / .__/\_,_/_/ /_/\_\   version 2.4.5
      /_/

Using Python version 3.6.9 (default, Apr 18 2020 01:56:04)
SparkSession available as 'spark'.

In [1]:  
```

