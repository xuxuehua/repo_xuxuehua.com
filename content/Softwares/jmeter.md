---
title: "jmeter"
date: 2020-10-20 17:24
---
[toc]



# jmeter



## JMeter的环境变量

vim .bash_profile进入到vim编辑器，输入以下命令：

```
export JMETER_HOME=/Users/maniac/Downloads/apache-jmeter-5.1.1
export PATH=$JAVA_HOME/bin:$PATH:.:$JMETER_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JMETER_HOME/lib/ext/ApacheJMeter_core.jar:$JMETER_HOME/lib/jorphan.jar:$JMETER_HOME/lib/logkit-2.0.jar
```





## test result output

```
jmeter -n -t test.jmx -l result.csv
```



# Thread Group



## Thread Properties



### number of Threads 

线程数，模拟多少个用户



### Ramp-up period

准备时间间隔

0表示立刻发送所有请求

大于0表示间隔几秒发送



