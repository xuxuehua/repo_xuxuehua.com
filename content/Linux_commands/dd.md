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

