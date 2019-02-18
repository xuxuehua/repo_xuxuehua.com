---
title: "disk"
date: 2018-10-25 13:26
---


[TOC]


# disk

## 磁盘格式化

查看系统支持的分区类型： cat /etc/filesystems

```
[root@fxq-1 ~]# cat /etc/filesystems 
xfs
ext4
ext3
ext2
nodev 
procnodev 
devpts
iso9660
vfat
hfs
hfsplus*
[root@fxq-1 ~]# mountsysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)devtmpfs on /dev type devtmpfs (rw,nosuid,size=244792k,nr_inodes=61198,mode=755)securityfs on /sys/kernel/security type securityfs (rw,nosuid,nodev,noexec,relatime)tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev)devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)tmpfs on /run type tmpfs (rw,nosuid,nodev,mode=755)tmpfs on /sys/fs/cgroup type tmpfs (ro,nosuid,nodev,noexec,mode=755)cgroup on /sys/fs/cgroup/systemd type cgroup (rw,nosuid,nodev,noexec,relatime,xattr,release_agent=/usr/lib/systemd/systemd-cgroups-agent,name=systemd)pstore on /sys/fs/pstore type pstore (rw,nosuid,nodev,noexec,relatime)cgroup on /sys/fs/cgroup/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,freezer)cgroup on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,memory)cgroup on /sys/fs/cgroup/pids type cgroup (rw,nosuid,nodev,noexec,relatime,pids)cgroup on /sys/fs/cgroup/perf_event type cgroup (rw,nosuid,nodev,noexec,relatime,perf_event)cgroup on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,cpuset)cgroup on /sys/fs/cgroup/net_cls,net_prio type cgroup (rw,nosuid,nodev,noexec,relatime,net_prio,net_cls)cgroup on /sys/fs/cgroup/cpu,cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,cpuacct,cpu)cgroup on /sys/fs/cgroup/devices type cgroup (rw,nosuid,nodev,noexec,relatime,devices)cgroup on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,blkio)cgroup on /sys/fs/cgroup/hugetlb type cgroup (rw,nosuid,nodev,noexec,relatime,hugetlb)configfs on /sys/kernel/config type configfs (rw,relatime)/dev/sda3 on / type xfs (rw,relatime,attr2,inode64,noquota)systemd-1 on /proc/sys/fs/binfmt_misc type autofs (rw,relatime,fd=29,pgrp=1,timeout=300,minproto=5,maxproto=5,direct)mqueue on /dev/mqueue type mqueue (rw,relatime)debugfs on /sys/kernel/debug type debugfs (rw,relatime)hugetlbfs on /dev/hugepages type hugetlbfs (rw,relatime)/dev/sda1 on /boot type xfs (rw,relatime,attr2,inode64,noquota)tmpfs on /run/user/0 type tmpfs (rw,nosuid,nodev,relatime,size=50748k,mode=700)/dev/sdb4 on /mnt type ext4 (rw,relatime,data=ordered)
[root@fxq-1 ~]#
```

> /boot 都是xfs文件系统
>
> centos 7 xfs
>
> centos 6 ext4
>
> centos 5 ext3



### 格式化命令mke2fs

mke2fs -t xfs -b 2048 /dev/sdb1

格式化/dev/sdb1 类型为xfs 块大小2048

```
[root@fxq-1 ~]# mke2fs -t ext4 -b 2048  /dev/sdb1
mke2fs 1.42.9 (28-Dec-2013)Filesystem label=OS type: LinuxBlock size=2048 (log=1)Fragment size=2048 (log=1)Stride=0 blocks, Stripe width=0 blocks305152 inodes, 2440704 blocks122035 blocks (5.00%) reserved for the super userFirst data block=0Maximum filesystem blocks=271056896149 block groups16384 blocks per group, 16384 fragments per group2048 inodes per groupSuperblock backups stored on blocks: 
16384, 49152, 81920, 114688, 147456, 409600, 442368, 802816, 
1327104, 	2048000Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): doneWriting superblocks and filesystem accounting information: done   

[root@fxq-1 ~]#
```

mkfs.ext4 /dev/sdb1

```
[root@fxq-1 ~]# mkfs.ext4 /dev/sdb1
mke2fs 1.42.9 (28-Dec-2013)Filesystem label=OS type: LinuxBlock size=4096 (log=2)Fragment size=4096 (log=2)Stride=0 blocks, Stripe width=0 blocks305216 inodes, 1220352 blocks61017 blocks (5.00%) reserved for the super userFirst data block=0Maximum filesystem blocks=124990259238 block groups32768 blocks per group, 32768 fragments per group8032 inodes per groupSuperblock backups stored on blocks: 
32768, 98304, 163840, 229376, 294912, 819200, 884736Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): doneWriting superblocks and filesystem accounting information: done 

[root@fxq-1 ~]#
```



### blkid 挂载的分区UUID

```
[root@fxq-1 ~]# blkid
/dev/sda1: UUID="e5344a19-9eec-459c-aa92-91dcb45bcb3e" TYPE="xfs" 
/dev/sda2: UUID="d2ed2e32-6864-483b-8626-2faa42e9a598" TYPE="swap" 
/dev/sda3: UUID="ed89722c-3249-4d7d-8d6f-c22416c9267b" TYPE="xfs" 
/dev/sdb1: UUID="91ab28a1-037e-4aea-8e3c-d9f408b5464c" TYPE="ext4" PARTLABEL="p1" PARTUUID="da815dbc-0437-431d-a2ab-22182868d970" 
/dev/sdb2: UUID="6cffe72d-a56e-4af6-aeeb-c3ce1aae8330" TYPE="ext4" PARTLABEL="p2" PARTUUID="d51c97c1-263b-406b-ad18-032967f302a1" 
/dev/sr0: UUID="2017-01-27-21-18-30-00" LABEL="CentOS 7 i686" TYPE="iso9660" 
/dev/sdb3: UUID="0f1a0771-561d-4882-88c7-71ea3700e64c" TYPE="ext4" PARTLABEL="p3" PARTUUID="24efcacf-98b8-444f-9680-47c24c556fc7" 
/dev/sdb4: UUID="7306780c-2706-4a34-922e-222fbb740b3e" TYPE="ext4" PARTLABEL="p4" PARTUUID="f30ed796-ed62-47ed-ae0d-aebe25b24703" 
[root@fxq-1 ~]#
```

预留磁盘空间改为1%

blocks reserved for the super user

```
[root@fxq-1 ~]# mke2fs -m 1 /dev/sdb1
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks305216 inodes, 1220352 blocks12203 blocks (1.00%) reserved for the super user
First data block=0Maximum filesystem blocks=124990259238 block groups32768 blocks per group, 32768 fragments per group8032 inodes per group
Superblock backups stored on blocks: 
32768, 98304, 163840, 229376, 294912, 819200, 884736Allocating group tables: done                            
Writing inode tables: done                            
Writing superblocks and filesystem accounting information: done
```



### inode 对应信息

默认大概1:4的关系：

```
305216 inodes, 1220352 blocks
```

mke2fs -i 8192 -t ext4 /dev/sdb1

```
610432 inodes, 1220352 blocks
```

mkfs.xfs -f /dev/sdb2

```
[root@fxq-1 ~]# blkid 
/dev/sda1: UUID="e5344a19-9eec-459c-aa92-91dcb45bcb3e" TYPE="xfs" 
/dev/sda2: UUID="d2ed2e32-6864-483b-8626-2faa42e9a598" TYPE="swap" 
/dev/sda3: UUID="ed89722c-3249-4d7d-8d6f-c22416c9267b" TYPE="xfs" 
/dev/sdb1: UUID="13eb97d6-fd9a-4205-b8a3-026f63a5fb55" TYPE="xfs" PARTLABEL="p1" PARTUUID="da815dbc-0437-431d-a2ab-22182868d970" 
/dev/sdb2: UUID="5601594d-ef23-4532-b275-1add4c5e0649" TYPE="xfs" PARTLABEL="p2" PARTUUID="d51c97c1-263b-406b-ad18-032967f302a1"
/dev/sr0: UUID="2017-01-27-21-18-30-00" LABEL="CentOS 7 i686" TYPE="iso9660" 
/dev/sdb3: UUID="0f1a0771-561d-4882-88c7-71ea3700e64c" TYPE="ext4" PARTLABEL="p3" PARTUUID="24efcacf-98b8-444f-9680-47c24c556fc7" 
/dev/sdb4: UUID="7306780c-2706-4a34-922e-222fbb740b3e" TYPE="ext4" PARTLABEL="p4" PARTUUID="f30ed796-ed62-47ed-ae0d-aebe25b24703"
```

du -sb 可查看原大小

## 磁盘挂载 mount

mount /dev/sdb4 /mnt

```
[root@fxq-1 ~]# mount /dev/sdb2 /opt
[root@fxq-1 ~]# mount
sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)devtmpfs on /dev type devtmpfs (rw,nosuid,size=244792k,nr_inodes=61198,mode=755)securityfs on /sys/kernel/security type securityfs (rw,nosuid,nodev,noexec,relatime)tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev)devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)tmpfs on /run type tmpfs (rw,nosuid,nodev,mode=755)tmpfs on /sys/fs/cgroup type tmpfs (ro,nosuid,nodev,noexec,mode=755)cgroup on /sys/fs/cgroup/systemd type cgroup (rw,nosuid,nodev,noexec,relatime,xattr,release_agent=/usr/lib/systemd/systemd-cgroups-agent,name=systemd)pstore on /sys/fs/pstore type pstore (rw,nosuid,nodev,noexec,relatime)cgroup on /sys/fs/cgroup/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,freezer)cgroup on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,memory)cgroup on /sys/fs/cgroup/pids type cgroup (rw,nosuid,nodev,noexec,relatime,pids)cgroup on /sys/fs/cgroup/perf_event type cgroup (rw,nosuid,nodev,noexec,relatime,perf_event)cgroup on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,cpuset)cgroup on /sys/fs/cgroup/net_cls,net_prio type cgroup (rw,nosuid,nodev,noexec,relatime,net_prio,net_cls)cgroup on /sys/fs/cgroup/cpu,cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,cpuacct,cpu)cgroup on /sys/fs/cgroup/devices type cgroup (rw,nosuid,nodev,noexec,relatime,devices)cgroup on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,blkio)cgroup on /sys/fs/cgroup/hugetlb type cgroup (rw,nosuid,nodev,noexec,relatime,hugetlb)configfs on /sys/kernel/config type configfs (rw,relatime)/dev/sda3 on / type xfs (rw,relatime,attr2,inode64,noquota)systemd-1 on /proc/sys/fs/binfmt_misc type autofs (rw,relatime,fd=29,pgrp=1,timeout=300,minproto=5,maxproto=5,direct)mqueue on /dev/mqueue type mqueue (rw,relatime)debugfs on /sys/kernel/debug type debugfs (rw,relatime)hugetlbfs on /dev/hugepages type hugetlbfs (rw,relatime)/dev/sda1 on /boot type xfs (rw,relatime,attr2,inode64,noquota)tmpfs on /run/user/0 type tmpfs (rw,nosuid,nodev,relatime,size=50748k,mode=700)/dev/sdb4 on /mnt type ext4 (rw,relatime,data=ordered)/dev/sdb2 on /opt type xfs (rw,relatime,attr2,inode64,noquota)
[root@fxq-1 ~]#
[root@fxq-1 opt]# mount /dev/sdb2 /opt
[root@fxq-1 opt]# ls
[root@fxq-1 opt]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3        28G  960M   27G   4% /
devtmpfs        240M     0  240M   0% /dev
tmpfs           248M     0  248M   0% /dev/shm
tmpfs           248M  4.6M  244M   2% /run
tmpfs           248M     0  248M   0% /sys/fs/cgroup
/dev/sda1       197M  105M   93M  54% /boot
tmpfs            50M     0   50M   0% /run/user/0/dev/sdb4        11G   41M  9.9G   1% /mnt
/dev/sdb2       2.8G   33M  2.8G   2% /opt
[root@fxq-1 opt]# umount /dev/sdb2
[root@fxq-1 opt]# df -hFilesystem      Size  Used Avail Use% Mounted on
/dev/sda3        28G  960M   27G   4% /
devtmpfs        240M     0  240M   0% /dev
tmpfs           248M     0  248M   0% /dev/shm
tmpfs           248M  4.6M  244M   2% /run
tmpfs           248M     0  248M   0% /sys/fs/cgroup
/dev/sda1       197M  105M   93M  54% /boot
tmpfs            50M     0   50M   0% /run/user/0/dev/sdb4        11G   41M  9.9G   1% /mnt
[root@fxq-1 opt]
```

mount -o ro

默认选项：

```
defaults              Use default options: rw, suid, dev, exec, auto, nouser, and async.

              Note that the real set of the all default mount options depends on kernel and filesystem              type. See the begin of this section for more details.
remount
              Attempt to remount an already-mounted filesystem.  This is commonly used to  change  the
              mount flags for a filesystem, especially to make a readonly filesystem writable. It does              not change device or mount point.

              The remount functionality follows the standard way how  the  mount  command  works  with
              options  from fstab. It means the mount command doesn't read fstab (or mtab) only when a
              device and dir are fully specified.

              mount -o remount,rw /dev/foo /dir

              After this call all old mount options are replaced and arbitrary  stuff  from  fstab  is
              ignored,  except  the  loop=  option which is internally generated and maintained by the
              mount command.

              mount -o remount,rw  /dir

              After this call mount reads fstab (or mtab) and merges these options with  options  from
              command line ( -o ).
```

## 分区挂载配置文件fstab

/etc/fstab

配置文件中六列代表意思：

第一列：挂载的磁盘

第二列：挂载的位置

第三列：挂载磁盘的分区类型

第四列：挂载时的选项

第五列：能否被dump备份命令作用：dump是一个用来作为备份的命令。通常这个参 数的值为0或者1

- 0：代表不要做dump备份
- 1：代表要每天进行dump的操作
- 2：代表不定日期的进行dump操作

第六列：是否检验扇区：开机的过程中，系统默认会以fsck检验我们系统是否为完整（clean）。

- 0：不要检验
- 1：最早检验（一般根目录会选择）
- 2：1级别检验完成之后进行检验-

```
## /etc/fstab# Created by anaconda on Tue Aug  1 06:43:54 2017## Accessible filesystems, by reference, are maintained under '/dev/disk'# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info#UUID=ed89722c-3249-4d7d-8d6f-c22416c9267b /                       xfs     defaults        0 0UUID=e5344a19-9eec-459c-aa92-91dcb45bcb3e /boot                   xfs     defaults        0 0UUID=d2ed2e32-6864-483b-8626-2faa42e9a598 swap                    swap    defaults        0 0
## /etc/fstab# Created by anaconda on Tue Aug  1 06:43:54 2017## Accessible filesystems, by reference, are maintained under '/dev/disk'# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info#UUID=ed89722c-3249-4d7d-8d6f-c22416c9267b /                       xfs     defaults        0 0UUID=e5344a19-9eec-459c-aa92-91dcb45bcb3e /boot                   xfs     defaults        0 0UUID=d2ed2e32-6864-483b-8626-2faa42e9a598 swap                    swap    defaults        0 0/dev/sdb2                                 /opt                    xfs     defaults        0 0
```

man mount man fstab

## 

查看UUID 命令blkid

```
[root@fxq-1 opt]# blkid  /dev/sdb4/dev/sdb4: UUID="7306780c-2706-4a34-922e-222fbb740b3e" TYPE="ext4" PARTLABEL="p4" PARTUUID="f30ed796-ed62-47ed-ae0d-aebe25b24703" 
[root@fxq-1 opt]#
```

写入fstab

```
## /etc/fstab# Created by anaconda on Tue Aug  1 06:43:54 2017## Accessible filesystems, by reference, are maintained under '/dev/disk'# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info#
UUID=ed89722c-3249-4d7d-8d6f-c22416c9267b /                       xfs     defaults        0 0
UUID=e5344a19-9eec-459c-aa92-91dcb45bcb3e /boot                   xfs     defaults        0 0
UUID=d2ed2e32-6864-483b-8626-2faa42e9a598 swap                    swap    defaults        0 0
/dev/sdb2                                 /opt                    xfs     defaults        0 0
UUID=7306780c-2706-4a34-922e-222fbb740b3e     /mnt                     ext4    defaults        0 0
```



## 手动增加swap空间

当有需求要增加swap空间时，增加步骤如下 ：

- 1、dd if=/dev/zero of=/tmp/newdisk bs=1M count=100 生成100M磁盘
- 2、chmod 600 /tmp/newdisk 修改磁盘文件权限
- 3、mkswap -f /tmp/newdisk 格式化磁盘
- 4、free -m 查看原swap 大小
- 5、swapon /tmp/newdisk 挂载新磁盘到swap中
- 6、free -m 再查看swap大小
- 7、swapoff /tmp/newdisk 卸载新加swap
- 8、free -m 最后看swap大小是否恢复
- 9、rm -rf /tmp/newdisk 删除生成的磁盘文件

```
[root@fxq-1 opt]# dd if=/dev/zero of=/tmp/newdisk bs=1M count=100
100+0 records in100+0 records out104857600 bytes (105 MB) copied, 3.73145 s, 28.1 MB/s
[root@fxq-1 opt]# ll /tmp
total 102400-rw-r--r-- 1 root root 104857600 Aug 17 01:28 newdisk
[root@fxq-1 opt]# ls -lh /tmp
total 100M
-rw-r--r-- 1 root root 100M Aug 17 01:28 newdisk
[root@fxq-1 opt]# mkswap -f /tmp/newdisk 
Setting up swapspace version 1, size = 102396 KiB
no label, UUID=14cdc2cb-dc3c-4d7e-a486-deb1f7f3201c
[root@fxq-1 opt]# free -m
              total        used        free      shared  buff/cache   available
Mem:            495          68         142           4         285         401Swap:          2047           0        2047
[root@fxq-1 opt]# swapon /tmp/newdisk 
swapon: /tmp/newdisk: insecure permissions 0644, 0600 suggested.
[root@fxq-1 opt]# free -m
              total        used        free      shared  buff/cache   available
Mem:            495          68         142           4         285         401Swap:          2147           0        2147
[root@fxq-1 opt]# chmod 600 /tmp/newdisk
 [root@fxq-1 opt]# 
 [root@fxq-1 opt]# swapoff /tmp/newdisk 
 [root@fxq-1 opt]# free -m
              total        used        free      shared  buff/cache   available
Mem:            495          68         142           4         285         401Swap:          2047           0        2047
[root@fxq-1 opt]#  rm -rf /tmp/newdisk 
[root@fxq-1 opt]#
```