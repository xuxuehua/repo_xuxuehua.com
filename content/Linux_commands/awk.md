---
title: "awk"
date: 2019-01-20 17:43
---


[TOC]



# awk

```
Usage: awk [POSIX or GNU style options] -f progfile [--] file ...
Usage: awk [POSIX or GNU style options] [--] 'program' file ...
```



## -F 分隔符

```
# cat /etc/passwd | awk -F : '/^ubuntu/{print $1}'
ubuntu
```



## -v 变量

赋值一个用户定义的变量





## regexp

```
 格式为/regular expression/
```



```
# awk -F: '/^r/{print $1}' /etc/passwd
root
rpc
rtkit
radvd
rpcuser
```



## expression

表达式，其值为非0 或为非空字符时满足条件，如 

```
$1 ~ /foo/ 或 $1 == ‘xurick’，
```

用运算符~(匹配)和 !~(不匹配) 



```
[root@jp test]# awk -F: '$3>=500{print $1,$3}' /etc/passwd
systemd-bus-proxy 999
systemd-network 998
polkitd 997
ghostlyman 1000
geoclue 996
unbound 995
saslauth 994
chrony 993
colord 992
nfsnobody 65534
setroubleshoot 991
gnome-initial-setup 990
[root@jp test]# awk -F: '$7~"bash$"{print $1,$7}' /etc/passwd
root /bin/bash
ghostlyman /bin/bash
mysql /bin/bash
```





## ranges

指定的匹配范围，格式为pattern1 pattern2 

```
[root@jp test]# awk -F: '/^r/,/^m/{print $1,$7}' /etc/passwd
root /bin/bash
bin /sbin/nologin
daemon /sbin/nologin
adm /sbin/nologin
lp /sbin/nologin
sync /bin/sync
shutdown /sbin/shutdown
halt /sbin/halt
mail /sbin/nologin
rpc /sbin/nologin
unbound /sbin/nologin
saslauth /sbin/nologin
rtkit /sbin/nologin
pulse /sbin/nologin
chrony /sbin/nologin
colord /sbin/nologin
abrt /sbin/nologin
radvd /sbin/nologin
qemu /sbin/nologin
rpcuser /sbin/nologin
nfsnobody /sbin/nologin
setroubleshoot /sbin/nologin
gdm /sbin/nologin
gnome-initial-setup /sbin/nologin
avahi /sbin/nologin
mysql /bin/bash
[root@jp test]# awk -F: '$3==0,$7~"nologin"{print $1,$2,$7}' /etc/passwd
root x /bin/bash
bin x /sbin/nologin
```



# 内置变量 

## ARGV  

数组，保存命令行本身整个字符串，如awk ‘{print $0}’ a.txt b.txt 整个命令中，ARGV[0]保存awk，ARGV[1]保存a.txt



## ARGC 

awk命令的参数个数



## FILENAME  

awk处理的文件的名称



## ENVIRON  

当前shell环境变量及其值的关联数组   如 awk ‘BEGIN{print ENVIRON[“PATH"]}’



## Empty

(空模式)  匹配任意输入行

```
[root@jp test]# awk -F: '{printf "%-10s%-10s-20s\n",$1,$3,$7}' /etc/passwd
root      0         -20s
bin       1         -20s
daemon    2         -20s
adm       3         -20s
lp        4         -20s
sync      5         -20s
shutdown  6         -20s
halt      7         -20s
mail      8         -20s
operator  11        -20s
games     12        -20s
ftp       14        -20s
nobody    99        -20s
avahi-autoipd170       -20s
systemd-bus-proxy999       -20s
systemd-network998       -20s
dbus      81        -20s
polkitd   997       -20s
ntp       38        -20s
tss       59        -20s
postfix   89        -20s
sshd      74        -20s
ghostlyman1000      -20s
usbmuxd   113       -20s
geoclue   996       -20s
rpc       32        -20s
unbound   995       -20s
saslauth  994       -20s
rtkit     172       -20s
pulse     171       -20s
chrony    993       -20s
colord    992       -20s
abrt      173       -20s
radvd     75        -20s
qemu      107       -20s
rpcuser   29        -20s
nfsnobody 65534     -20s
setroubleshoot991       -20s
gdm       42        -20s
gnome-initial-setup990       -20s
avahi     70        -20s
mysql     27        -20s
```



## FS分隔符

 Field Separator 读取文本时，所使用的字段分隔符



## FNR

与NR不同的是，FNR用于记录正处理的行是当前这一文件中被总共处理的行数



## NF 最后一列数据

Number of Field   当前记录的field个数

浏览记录的域的个数，即切割后列的个数

```
$ echo $a
1, 2, 3, 4

$ echo $a | awk '{print $NF}'
4
```







## NR 已读的记录数

The number of input records: awk命令所处理的记录数，如果有多个文件，这个数目会把处理的多个文件中行统一计数

NF = Number of Fields

```
awk 'NF==1' file_name
```



取反

```
awk 'NR!=1' file_name
```



## BEGIN 开头行





## END 最后一行

```
awk "END{print $0}" file_name
```







## OFS 输出字段分隔符

Output Field Operator

默认为空格

```
awk -F' ' 'BEGIN{OFS="--"}{print $1,$2,$3}' num.txt
```

> 使用`--` 进行分割





## ORS 输出记录分隔符

Output Row Separator 

默认为换行符

```
awk -F' ' 'BEGIN{OFS="--";ORS="#"}{print $1,$2,$3}' num.txt
```

> 换行符变为#



## RS 换行符

 Record Separator  输入文本信息所使用的换行符

