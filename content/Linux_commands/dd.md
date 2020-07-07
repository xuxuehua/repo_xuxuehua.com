---
title: "dd"
date: 2019-05-16 01:35
---


[TOC]



# dd



## 备份bootsector 引导扇区

```
dd if=/dev/hda of=bootsector.img bs=512 count=1
```

很显然，你应该改变这个装置来显示你的boot驱动（有可能是SCSI的sda）。还要非常小心，别把事情搞砸——你可能会轻而易举地毁掉你的驱动！恢复时使用：

```
dd if=bootsector.img of=/dev/hda 
```





## 生成数据

```
[root@localhost text]# dd if=/dev/zero of=/mnt0/rxu_test.txt bs=3G count=1
1+0 records in
1+0 records out
1048576 bytes (1.0 MB) copied, 0.006107 seconds, 172 MB/s

[root@localhost text]# du -sh sun.txt 
1.1M    sun.txt
```

> **if** 代表输入文件。如果不指定if，默认就会从stdin中读取输入。
> **of** 代表输出文件。如果不指定of，默认就会将stdout作为默认输出。
> **bs** 代表字节为单位的块大小。
> **count** 代表被复制的块数。
> **/dev/zero** 是一个字符设备，会不断返回0值字节（\0）。