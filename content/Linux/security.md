---
title: "security"
date: 2019-05-13 02:09
---


[TOC]







# 异常系统用户

```
/etc/passwd
```

```
grep 0 /etc/passwd
```



## 特权用户

```
[root@node01 ~]# awk -F":" '{if($3==0){print $1}}' /etc/passwd
root
```



## 空口令用户

```
[root@node01 ~]# awk -F: '{if(length($2)==0){print $1}}' /etc/passwd
```





# 异常进程

## UID 为0



## 进程所打开的端口和文件

```
lsof -p pid 
```

```
lsof -i:80
```



## 隐藏进程

```
ps -ef | awk '{print $2}' | sort -n | uniq > 1
ls /proc/ | sort -n | uniq > 2
diff 1 2
```





# 异常文件



## 系统文件

```
find / -uid 0 -perm 4000 -print 
find / -size +10000k -print
find / -name "..." -print 
find / -name ".." -print
find / -name "." -print
find / -name "" -print
```

> SUID文件，可疑大于10M和空格文件



```
find / -name core -exec ls -l {} \
```

> 系统中的core文件





## 系统文件完整性

```
rpm -qf /bin/ls
rpm -qf /bin/login
md5sum -b FILENAME
md5sum -t FILENAME 
```



# 网络



## 混杂模式网口

```
ip link | grep PROMISC
```



## TCP/UDP

```
netstat -nap 
arp -a
```



## 统计IP重试次数

```
last root | awk '{print $3}' | sort | uniq -c | sort -nr | more 
```



# 异常配置

## 计划任务

```
crontab -u root -l 
cat /etc/crontab
ls /etc/cron.*
ls /var/spool/cron/
```



## 系统启动

```
cat /etc/rc.d/rc.local
ls /etc/rc.d
ls /etc/rc3.d
find / -type f -perm 4000
```



## 系统服务

```
chkconfig --list
```

