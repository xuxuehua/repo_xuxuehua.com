---
title: "sort"
date: 2020-03-22 20:22
---
[toc]



# sort



## -n 数值

按照数值大小排序



## -r 反向排序



## -t 分隔字符



## -k 排序的列

```
# lsof -p 30982  | sort -u -k 7 -n
COMMAND   PID     USER   FD   TYPE             DEVICE SIZE/OFF    NODE NAME
java    30982 zeppelin  mem    REG              259,2     2497 1048959 /usr/lib/zeppelin/lib/javax.inject-1.jar
java    30982 zeppelin  mem    REG              259,2     3723  656446 /usr/share/aws/aws-java-sdk/aws-java-sdk-1.11.615.jar
java    30982 zeppelin  cwd    DIR              259,2     4096 1049091 /var/lib/zeppelin
java    30982 zeppelin  mem    REG              259,2     4211 1048931 /usr/lib/zeppelin/lib/interpreter/plexus-component-annotations-1.5.5.jar
java    30982 zeppelin  mem    REG              259,2     4467  800685 /usr/share/aws/emr/emrfs/lib/aopalliance-1.0.jar
java    30982 zeppelin  mem    REG              259,2     4722 1048964 /usr/lib/zeppelin/lib/jcip-annotations-1.0-1.jar
java    30982 zeppelin  mem    REG              259,2     5706 1048862 /usr/lib/zeppelin/lib/google-auth-library-credentials-0.9.0.jar
```

