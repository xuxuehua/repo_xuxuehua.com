---
title: "privilege"
date: 2021-04-05 18:29
---
[toc]



# 特殊权限

## SUID   谨慎操作

   即运行某程序时，相应进程的属主是程序文件自身的属主，而不是启动者的属主

```
   chmod u+s FILE

   chmod u-s FILE

​     如果FILE本身有x权限，则SUID显示s，否则是S

​     /usr/bin/passwd    拥有s权限

​     exp.

​     若将/bin/cat 加上s 权限，那么在hadoop用户下是可以直接访问/etc/shadow 文件内容
```



## SGID

   即程序运行的基本组，是程序文件自身的基本组，而不是启动者所属的基本组

   chmod g+s FILE

   chmod g-s FILE

​     在目录下创建的文件，不是以基本组为什么，而是以文件自身属组为其身份

```
     exp.

​     develop team-> hadoop, base, hive这三个用户都可以访问/tmp/project/

​     groupadd developteam

​     chown -R :developteam hadoop

​     usermod -a -G developteam hadoop

​     usermod -a -G developteam hbase

​     usermod -a -G developteam hive

​     su - hadoop

​     cannot touch a file at /tmp/project

​     chmod g+s /tmp/project

​     su - hadoop

​     touch /tmp/project/b.hadoop

​     su - hbase

​     touch /tmp/project.b.hbase

​     上面的两个文件属组都是developteam
```



## Sticky

   在一个公共目录，每个人都可以创建文件，删除自己的文件，但是不能删除别人的文件

```
   chmod o+t FILE

   chmod o-t FILE
```



其三个权限之和为421, 也就是权限位为4位