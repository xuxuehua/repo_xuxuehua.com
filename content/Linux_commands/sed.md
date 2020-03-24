---
title: "sed"
date: 2018-09-27 16:01
---


[TOC]

# sed

流编辑器，处理时，把当前处理的行存储在临时缓冲中，然后sed处理缓冲中的内容，然后返回

接下来处理下一行，并重复以上动作直到结束

```
sed [-nefri] 'command' filename
```





## -e 

直接在列模式上进行sed的动作编辑





## -i 直接编辑

直接编辑文件

### 删除

```
sed -i '6d' ~/.ssh/known_hosts
```



用命令行往文件的顶部添加文字

```
sed -i '1s/^/line to insert\n/' path/to/file/you/want/to/change.txt
```



用命令行往配置文件里插入多行文本

```
cat >> path/to/file/to/append-to.txt << "EOF"
export PATH=$HOME/jdk1.8.0_31/bin:$PATH
export JAVA_HOME=$HOME/jdk1.8.0_31/
EOF
```







### 替换

```
sed -i 'columns/.*/replacement-word/' file.txt
```

```
sed -i 's/some_a/some_b/g' file.txt
```





## a 新增

在a的后面可以接字符串，在下一行出现

```
$ ifconfig | sed '2a123'
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 108.166.209.94  netmask 255.255.255.192  broadcast 108.166.209.127
123
        inet6 fe80::216:3cff:fee1:3ddf  prefixlen 64  scopeid 0x20<link>
        inet6 2607:f130:0:ee:216:3cff:fee1:3ddf  prefixlen 64  scopeid 0x0<global>
        ether 00:16:3c:e1:3d:df  txqueuelen 1000  (Ethernet)
        RX packets 375584  bytes 1380914096 (1.3 GB)
        RX errors 0  dropped 3895  overruns 0  frame 0
        TX packets 475843  bytes 255423182 (255.4 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```



## d 删除

```
$ ifconfig | sed '2d'
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::216:3cff:fee1:3ddf  prefixlen 64  scopeid 0x20<link>
        inet6 2607:f130:0:ee:216:3cff:fee1:3ddf  prefixlen 64  scopeid 0x0<global>
        ether 00:16:3c:e1:3d:df  txqueuelen 1000  (Ethernet)
        RX packets 375921  bytes 1380967304 (1.3 GB)
        RX errors 0  dropped 3921  overruns 0  frame 0
        TX packets 475986  bytes 255443460 (255.4 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

```
$ ifconfig | sed '1,4d'
        ether 00:16:3c:e1:3d:df  txqueuelen 1000  (Ethernet)
        RX packets 376502  bytes 1381112705 (1.3 GB)
        RX errors 0  dropped 3955  overruns 0  frame 0
        TX packets 476379  bytes 255496381 (255.4 MB)
```



## s 查找并替换

