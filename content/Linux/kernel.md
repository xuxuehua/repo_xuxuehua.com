---
title: "kernel 内核"
date: 2018-11-30 20:28
---


[TOC]

# kernel

Kernel是协调多个程序和硬件之间到交流， kernel通过系统调用（system call）管理硬件资源





## 更换内核



### Centos 

```
wget https://buildlogs.centos.org/c7.1511.00/kernel/20151119220809/3.10.0-327.el7.x86_64/kernel-3.10.0-327.el7.x86_64.rpm
yum install https://buildlogs.centos.org/c7.1511.00/kernel/20151119220809/3.10.0-327.el7.x86_64/kernel-3.10.0-327.el7.x86_64.rpm


https://buildlogs.centos.org/c7.01.u/kernel/20150327030147/3.10.0-229.1.2.el7.x86_64/kernel-3.10.0-229.1.2.el7.x86_64.rpm


grub2-mkconfig -o /boot/grub2/grub.cfg
awk -F\' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg
https://wiki.centos.org/HowTos/Grub2
```



- 查看内核是否安装成功

```
rpm -qa | grep kernel
```

- 删除旧内核(可选)

```
rpm -ev 旧内核  
```

- 更新 grub 系统引导文件并重启

```
sed -i 's:default=.*:default=0:g' /etc/grub.conf
reboot
```



下载更换内核
最新内核查看[这里](http://elrepo.org/linux/kernel/el7/x86_64/RPMS/)

```
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
yum --enablerepo=elrepo-kernel install kernel-ml -y
```

- 查看内核是否安装成功

```
rpm -qa | grep kernel
```

- 删除旧内核(可选)

```
rpm -ev 旧内核  
```

- 更新 grub 系统引导文件并重启

```
egrep ^menuentry /etc/grub2.cfg | cut -f 2 -d \'
grub2-set-default 0  # default 0 表示第一个内核设置为默认运行, 选择最新内核就对了
reboot
```



### Ubuntu

下载最新内核,最新内核查看[这里](http://kernel.ubuntu.com/~kernel-ppa/mainline)

下载内核文件

```
wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.16/linux-image-4.16.0-041600-generic_4.16.0-041600.201804012230_amd64.deb
```

安装内核

```
sudo dpkg -i linux-image-4.*.deb
```

查看，删除旧内核(可选)

```
dpkg -l | grep linux-image 

root@vultr:/boot# mv *125-generic /tmp

apt-get purge 旧内核
```



更新 grub 系统引导文件并重启

```
update-grub
reboot
```

```

wget http://security.ubuntu.com/ubuntu/pool/main/l/linux/linux-image-3.13.0-24-generic_3.13.0-24.47_amd64.deb
dpkg -i linux-image-3.13.0-24-generic_3.13.0-24.47_amd64.deb
dpkg -l  | grep linux-headers | grep ii

root@vultr:/boot# mv *125-generic /tmp


vim /etc/default/grub


Linux vultr.guest 3.13.0-125-generic #174-Ubuntu SMP Mon Jul 10 18:51:24 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
```





### Debian

```
debian
wget http://download.proxmox.com/debian/dists/jessie/pve-no-subscription/binary-amd64/pve-kernel-4.4.10-1-pve_4.4.10-54_amd64.deb


wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.12.8/linux-image-4.12.8-041208-generic_4.12.8-041208.201708161815_amd64.deb
```





## BBR 

### Ubuntu

开机后 `uname -r` 看看是不是内核 >= 4.9

执行 `lsmod | grep bbr`，如果结果中没有 `tcp_bbr` 的话就先执行

```
modprobe tcp_bbr
echo "tcp_bbr" | sudo tee --append /etc/modules-load.d/modules.conf
```

执行

```
echo "net.core.default_qdisc=fq" | sudo tee --append /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" | sudo tee --append /etc/sysctl.conf
```

保存生效

```
sysctl -p
```

执行

```
sysctl net.ipv4.tcp_available_congestion_control
sysctl net.ipv4.tcp_congestion_control
```





### Debian

```
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
sysctl -p
```



```
查看是否开启成功
sysctl net.ipv4.tcp_available_congestion_control
显示如下内容表示开启成功：


sysctl net.ipv4.tcp_available_congestion_control
net.ipv4.tcp_available_congestion_control = bbr cubic reno

查看是否启动工作

lsmod | grep bbr
显示以下即启动成功：
lsmod | grep bbr
tcp_bbr                20480  14
```

