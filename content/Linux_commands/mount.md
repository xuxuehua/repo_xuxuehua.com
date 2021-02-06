---
title: "mount"
date: 2021-01-19 22:56
---
[toc]



# mount



## nfs

```
mount -t nfs 192.168.10.204:/DataVolume/Public /mnt/Public
```



使用 showmount 命令查看 BlackArmor 输出什么：

```
showmount -e <IPaddress>
```

-e 表示“export”或者 BA 上面输出一系列 share。
它将显示这样的信息：

```
Export list for 192.168.10.204:
/DataVolume/Public *
```





使用 CIFS 安装

几乎所有的内容都相同，除了您实际安装共享所使用的语法规则。

```
sudo mount -t cifs -o noperm //<IP Address>/<NameofShare> /mnt/<FolderyouCreated>
```



### 开机挂载

```
[root@clientlinux ~]# vim /etc/rc.d/rc.local
mount -t nfs -o nosuid,noexec,nodev,rw,bg,soft,rsize=32768,wsize=32768 \
192.168.100.254:/home/public /home/nfs/public
```

