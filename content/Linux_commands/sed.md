---
title: "sed"
date: 2018-09-27 16:01
---


[TOC]

# sed

流编辑器，处理时，把当前处理的行存储在临时缓冲中，然后sed处理缓冲中的内容，然后返回

只是用于纯ASCII的纯文本文件，逐行进行处理，但是对sed而言，并不是处理原文本，而是处理文件在内存中模式空间中的副本。处理结束后，将模式空间打印至屏幕

接下来处理下一行，并重复以上动作直到结束

```
sed [-nefri] 'command' filename
```





## -e 

直接在列模式上进行sed的动作编辑,可以同时执行多个脚本





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



## -f 

```
sed -f /path/to/script     file     将脚本一个一个的处理后面的文件
history | see 's/^[[:space:]]*//g' | cut -d' ' -f1     截取history前面的编号，不好空格
```





## -r   使用扩展regexp





## -n   

静默模式，不再显示模式空间中的内容







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



```
sed ‘/^\//a \# Hello World\n# hello Linux’ /etc/fstab
```



## i

```
\string      在指定的行前面追加新行，内容为string
          same way as above a parameter
```





## r  

将指定的文件的内容添加至指定位置

```
     sed ‘1,2r /etc/issue’ /etc/fstab   在1行和2行之间追加/etc/issue文件到/etc/fstab
```



##  w  

将指定范围内的内容另存至指定的文件中

```
     sed ‘/oot/w /tmp/oot.txt’ /etc/fstab 将/etc/fstab 中oot开头的文件另存到/tmp/oot.txt文件中
```







## d 删除

删除符合条件的行

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



```
          sed ‘1,2d’ /etc/fstab     删除1到2行
          sed ‘3,$d’ /etc/fstab     删除第三行到最后一行
          sed ‘/oot/d’ /etc/fstab     删除还有oot的行
          sed ‘1,+2d’ /etc/fstab     删除第一行和后面的两行
          sed ’1d’ /etc/fstab          删除第1行
```





## p   显示符合条件的行

```
     sed -n ‘/^\//p’ /etc/fstab   只打印符合条件以斜线开头的行
```



## s 查找并替换



查找并替换, pattern可以使用regexp，但是string不可以

```
sed ’s/oot/OOT/‘ /etc/fstab   将文件中小写的oot换成大写的OOT

默认只替换每一行中第一次被匹配的字符串
加修饰符
g   全局替换
sed ’s/\//#/g’ /etc/fstab   or 这里可以使用其他相同的特殊字符替换，@ ＃等

sed ’s@/@#@g’ /etc/fstab

i   查找时，忽略字符大小写

&   引用模式匹配整个串

此处也可以使用后项引用

sed ’s#\(l..e\)#\1r#g’ sed.txt

sed ’s/l..e/&r/g’ sed.txt    这里将like和love替换成liker和lover

sed ’s#l\(..e\)#L\l#g’ sed.txt   这里必须使用后项引用，将like和love替换成Like和Love
```





