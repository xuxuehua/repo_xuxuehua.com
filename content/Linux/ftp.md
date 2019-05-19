---
title: "ftp"
date: 2019-05-14 19:23
---


[TOC]



# 共享类型

## ftp

属于应用层服务，可以跨平台使用（ linux<->unix<->windows）



## Nfs

属于内核模式，不可以跨平台（ linux<->linux）



## Samba

可以跨平台使用（ linux<->unix<->windows）





# 连接方式

## DAS

连接的磁盘



## NAS

通过nfs/cifs协议实现网络文件共享（文件存储方式）电子邮件、网页服务器多媒体流服务
、档案分享等就适用于NAS存储架构。



## SAS

通过网线或光纤实现ISCSI和FCSAN将物理存储设备连接起来使用（块存储方式较底层，需
要格式化并挂载当本地磁盘使用）数据库有关的应用适用于SAN存储架构。





# ftp



## 主动模式

一般用于服务端存在防火墙的情况，客户端无法主动连接至服务端的20数据端口，需要由服务端主动连接客户端的高位数
据端口。

1. 两端在建立TCP通信通道后，客户端会发送port请求与服务端的21号端口认证连接并发送开放用来建立数据连接
的高位端口号。
2. 服务端在收到后，会通过20号端口发送ACK响应请求
3. 服务端会通过20端口与客户端发送的高位端口建立数据连接通道。



## 被动模式

一般用于客户端存在防火墙的情况，服务端在收到连接请求后因为客户端防火墙而无法达到客户端高位端口，需要客户端
主动连接至服务端的数据传输端口。

1. 两端在建立TCP通信通道连接后，客户端会发送PASV请求给服务端。
2. 服务端在受到PASV端口后就会打开一个高位端口作为数据传输端口来响应给客户端等待客户端连接。
3. 客户端在收到响应后，就会去连接响应的端口建立数据连接通道





# vsftpd

Vsftpd是基于ftp协议来对网络数据交换的一种实现，是一个开源的解决方案



## 用户的类型

### 匿名用户

anonymous或ftp



### 本地用户

账号名称、密码等信息保存在passwd、 shadow文件中



### 虚拟用户

使用独立的账号/密码数据文件

```
user_list wangwu 123456 /var/pub
```



## 包架构

```
官方站点： http://vsftpd.beasts.org/
主程序: /usr/sbin/vsftpd
服务名： vsftpd
用户控制列表文件
/etc/vsftpd/ftpusers
/etc/vsftpd/user_list
主配置文件
/etc/vsftpd/vsftpd.conf
```





## installation

```
关闭防火墙和selinux
[root@node3 ~]# setenforce 0 #表示关闭selinux。
[root@node3 ~]# service iptables stop #表示关闭防火墙
[root@node3 ~]# yum -y install vsftpd #安装vsftpd
[root@node3 ~]# rpm -ql vsftpd #查看安装生成的文件
/etc/logrotate.d/vsftpd
/etc/pam.d/vsftpd #pam认证文件
/etc/rc.d/init.d/vsftpd #服务启动进程
/etc/vsftpd
/etc/vsftpd/ftpusers #限制登陆文件
/etc/vsftpd/user_list
/etc/vsftpd/vsftpd.conf #vsftp的主配置文件
/etc/vsftpd/vsftpd_conf_migrate.sh
/usr/sbin/vsftpd #程序文件
......
/var/ftp #FTP家目录
/var/ftp/pub
```



## 访问控制



### 匿名用户

```
配置基于匿名用户的访问控制，可以修改vsftpd的主配置文件
/etc/vsftpd/vsftpd.conf来进行，主要有下面几个参数
 anonymous_enable=YES
启动匿名用户
 anon_upload_enable=YES
是否允许匿名用户上传文件
 anon_mkdir_write_enable=YES
是否允许匿名用户有创建目录的权限，要考虑文件系统上的家目录，要具
备写权限
 anon_other_write_enable=YES
允许匿名登入者更多于上传或者建立目录之外的权限,譬如删除或者重命名
 anon_umask=077
指定上传文件的默认的所有者和权限
```



### 本地用户

```
默认情况下，操作系统的账户是可以直接使用用户名和密码来登陆的。并且登
陆成功之后，默认进入到了自己的家目录。

配置基于系统用户的访问控制
 local_enable=YES
是否允许Linux用户登陆，默认是允许的，当然也可以禁止。
 write_enable=YES
是否允许系统用户上传文件
 local_root=/ftproot
非匿名用户登陆所在目录，当使用linux用户登陆成功之后，就不会默认在
自己的家目录了。相反，会位于下面指定的目录里。
 local_umask=022
指定系统用户上传文件的默认权限
```

```
配置基于本地用户的访问控制
❖ 要配置基于本地用户的访问控制，可以修改vsftpd的主配置文件
/etc/vsftpd/vsftpd.conf来进行，有如下两种现在方法：
❖ 1、限制指定的本地用户不能访问，而其他本地用户可以访问
❖ 例如下面的设置：
 userlist_enable=YES
 userlist_deny=YES
 userlist_file=/etc/vsftpd/user_list
❖ 使文件/etc/vsftpd/user_list中指定的本地用户不能访问FTP服
务器，而其他本地用户可以访问FTP服务器
```

```
限定指定的本地用户可以访问，而其他本地用户不可以访问
 例如下面的设置：
 userlist_enable=YES
 userlist_deny=NO
 userlist_file=/etc/vsftpd/user_list
 使文件/etc/vsftpd/user_list中指定的本地用户可以访问FTP服
务器，而其他本地用户不可以访问FTP服务器
```



## 设置CHROOT

```
在默认的配置中，本地用户可以切换到自己的家目录以外的目录进行浏览，并在权限许可范
围内进行下载和上传。这样的设置对于一个FTP服务器来说是不安全的。
❖ 如果希望用户登陆后不能切换到自家目录以为的目录，则需要设置chroot选项，具体是：
 chroot_local_user
 chroot_list_enable
 chroot_list_file
❖ 有两种设置chroot的方法：
 设置所有的本地用户执行chroot
 只要讲chroot_local_user的值设为YES即可，即：
 chroot_local_user=YES
 设置指定的用户执行chroot
 需要设置的参数如下：
 chroot_local_user=NO
 chroot_list_enable=YES
 chroot_list_file=/etc/vsftpd/chroot_list
这样， /etc/vsftpd/chroot_list文件中指定的用户不能执行chroot
```



## 登陆提示信息

```
登陆提示信息图形界面是看不到的，只适用于ftp作为客户端的时候。可以使用下面的
方式来进行配置。但是优先级却不一样。如果希望用户登陆后不能切换到自家目录以为
的目录，
 ftpd_banner=“welcome to mage ftp server"
这一句话优先生效
 banner_file=/etc/vsftpd/ftpbanner.txt
❖ 目录访问提示信息
 当用户进入到某一个目录之后，可以给用户一个提示消息。用来提示这个目录的作用。
在相应的目录下建立一个隐藏文件 .message，在该文件中进行信息提示描述。
 dirmessage_enable=YES (默认)
 message_file=.message(默认)
```



## 优化配置

```
设置最大传输速率限制
 例如下面的配置：
 local_max_rate=50000
 anon_max_rate=30000
将本地用户的最大传输率设置为50kbytes/sec，匿名用户的传输速率为30kbytes/sec
❖ 设置客户端连接时的端口范围
 例如下面的配置：
 pasv_min_port=50000
 pasv_max_port=60000
将使客户端连接时的端口范围在50000和60000之间，这提高了系统的安全性
```



```
设置基本的性能和安全选项
 1、设置空闲的用户会话中断时间
 例如下面的配置：
 idle_session_timout=6600
 将在用户会话空闲10分钟后被中断
 2、设置空闲的数据连接的终端时间
 例如下面的配置：
 date_connection_timeout=120
 将在数据连接空闲2分钟后被中断
 3、设置客户端空闲时的自动中断和激活连接的时间
 例如下面的配置：
 accept_timout=60
 connect_timeout=60
 将使客户端空闲1分钟后自动中断连接，并在中断一分钟后自动激活连
```





## 虚拟用户

```
 所有虚拟用户会统一映射为一个指定的系统帐号：访问共享位置，即为此系统帐号的家
目录。
 各虚拟用户可被赋予不同的访问权限，通过匿名用户的权限控制参数进行指定。
❖ 虚拟用户帐号的存储方式：
 文件：编辑文本文件，此文件需要被编码为hash格式
 奇数行为用户名，偶数行为密码
 db_load -T -t hash -f vusers.txt vusers.db
 关系型数据库中的表中：
 实时查询数据库完成用户认证
 mysql库： pam要依赖于pam-mysql
▪ /lib64/security/pam_mysql.so
▪ /usr/share/doc/pam_mysql-0.7/README
```



```
基于文件验证的vsftpd虚拟用户：
 创建用户数据库文件
 # 创建用户文件
 # vim /etc/vsftpd/vusers.txt
 ftp1 用户名
 ftp1pass 密码
 tom 用户名
 tompass 密码
 # 进入到相应的目录下
 cd /etc/vsftpd/
 # 创建数据库文件
 db_load -T -t hash -f vusers.txt vusers.db
 # 修改数据库权限
 chmod 600 vusers.db
```

```
创建系统用户和访问FTP目录：
 虚拟用户访问FTP服务器的时候，要进入到自己的家目录下，但是系统中没有虚拟账户
所对应的账号家目录，所以我们要创建一个系统用户，与虚拟账户关联起来，这样，当
虚拟用户登陆之后，就会进入到我们创建的系统用户的家目录，然后进行数据访问。
 # 创建系统用户并指定家目录
 useradd -d /var/ftproot -s /sbin/nologin vuser
 # 修改家目录权限
 chmod +rx /var/ftproot/
❖ 创建pam配置文件
 修改vsftpd的pam模块的配置文件 /etc/pam.d/vsftpd.db ,让vsftpd支持pam模块进行身
份验证。
 auth required pam_userdb.so db=/etc/vsftpd/vusers
 account required pam_userdb.so db=/etc/vsftpd/vusers
```

```
指定pam配置文件
 修改vsftpd的配置文件 /etc/vsftpd/vsftpd.conf ,给vsftpd指定pam模块。
 guest_enable=YES
 guest_username=vuser
 pam_service_name=vsftpd.db
❖ 创建pam配置文件虚拟用户建立独立的配置文件
 在vsftpd的配置文件中指定，用户配置文件的存储路径，然后在/etc/目录下，创建相
应的目录，并且在目录中定义与用户名一致的配置文件并写入相应权限就可以了。
在vsftpd的配置文件 /etc/vsftpd/vsftpd.conf 中指定如下路径.
 user_config_dir=/etc/vsftpd/vusers.d/
 在etc目录下，创建如下的文件目录，这一个目录与前面在vsftpd配置文件中定义的一
致。
 mdkir /etc/vsftpd/vusers.d/
```

```
进入到/etc/vsftpd/vusers.d/ 目录下，创建与用户名同名的配置文件，例如用户为tom，
那么就创建一个与tom一致的配置文件。 将下面的内容，写入配置文件，就可以进行权限控
制。
 # 虚拟用户上传权限
 anon_upload_enable=YES
 # 虚拟用户创建文件夹
 anon_mkdir_write_enable=YES
 # 虚拟的其他用户对指定用户目录的写权限
 anon_other_write_enable=YES
❖ 或者还可以改变用户的默认登陆目录。也就是FTP用户登陆成功之后的默认路径。
 #登录目录改变至指定的目录
 local_root=/ftproot
```



### 基于mysql的虚拟用户

```
安装数据库及pam_mysql插件
[root@lab01 ~]# yum -y install mysql-server pam_mysql
2、 创建用于vsftpd的数据库，在创建表和用户
mysql> CREATE DATABASE vsftpd; #创建数据库
mysql> use vsftpd； #进入数据库
mysql> create table users ( #创建表
-> id int AUTO_INCREMENT NOT NULL,
-> name char(20) binary NOT NULL,
-> password char(48) binary NOT NULL,
-> primary key(id)
-> );
mysql> INSERT INTO users(name,password) values('bjwf',password('123456'));
mysql> INSERT INTO users(name,password) values('zhangsan',password('bjwf.com'));
mysql> GRANT ALL ON vsftpd.* TO vsftpd@'%' IDENTIFIED BY 'bjwf.com'; #用户授权
mysql> FLUSH PRIVILEGES; #刷新授权
3、查看pam模块,并创建认证文件
[root@node3 ~]# rpm -ql pam_mysql
/lib64/security/pam_mysql.so #pam模块生成认证时需要的共享库
[root@node3 ~]# vim /etc/pam.d/vsftpd.mysql
auth required /lib64/security/pam_mysql.so user=vsftpd passwd=bjwf.com host=192.168.130.251 db=vsftpd table=users userco
lumn=name passwdcolumn=password crypt=2
account required /lib64/security/pam_mysql.so user=vsftpd passwd=bjwf.com host=192.168.130.251 db=vsftpd table=users use
rcolumn=name passwdcolumn=password crypt=2
4、 创建虚拟用户的映射用户
[root@node3 ~]# useradd -s /sbin/nologin -d /var/ftproot vuser
[root@node3 ~]# chmod go+rx /var/ftproot
```

```
为单个用户提供配置文件
[root@node3 ~]# vim /etc/vsftpd/vsftpd.conf
user_config_dir=/etc/vsftpd/vuser_config
[root@node3 ~]# mkdir /etc/vsftpd/vuser_config
[root@node3 ~]# cd /etc/vsftpd/vuser_config
[root@node3 vuser_config]# cat bjwf
anon_upload_enable=YES
anon_mkdir_write_enable=YES
anon_other_write_enable=YES
[root@node3 vuser_config]# cat zhangsan
anon_upload_enable=YES
anon_mkdir_write_enable=NO
anon_other_write_enable=NO
7、 重启服务验证权限
[root@node3 ~]# service vsftpd restart
Shutting down vsftpd: [ OK ]
Starting vsftpd for vsftpd: [ OK ]
[root@node3 ~]# netstat -tnlp|grep 21
tcp 0 0 0.0.0.0:21 0.0.0.0:* LISTEN 1850/vsftpd
[root@node3 ~]# cp install.log /var/ftproot
```



```
验证
[root@node1 tmp]# lftp -u bjwf 192.168.130.251 #使用可读、可写账号
Password:
lftp bjwf@192.168.130.251:~> ls #查看文件
-rw-r--r-- 1 0 0 9545 Jul 01 09:16 install.log
lftp bjwf@192.168.130.251:/> lcd /etc/ #切换到本地目录
lcd ok, local cwd=/etc
lftp bjwf@192.168.130.251:/> put fstab #上传文件
541 bytes transferred
lftp bjwf@192.168.130.251:/> ls
-rw------- 1 500 500 541 Jul 01 09:20 fstab
-rw-r--r-- 1 0 0 9545 Jul 01 09:16 install.log
lftp bjwf@192.168.130.251:/> put passwd #上传文件
1228 bytes transferred
lftp bjwf@192.168.130.251:/> ls
-rw------- 1 500 500 541 Jul 01 09:20 fstab
-rw-r--r-- 1 0 0 9545 Jul 01 09:16 install.log
-rw------- 1 500 500 1228 Jul 01 09:21 passwd
lftp bjwf@192.168.130.251:/> mkdir haha #创建目录
mkdir ok, `haha' created
```

```
lftp bjwf@192.168.130.251:/> rm install.log #删除文件
rm ok, `install.log' removed
lftp bjwf@192.168.130.251:/> ls
-rw------- 1 500 500 541 Jul 01 09:20 fstab
drwx------ 2 500 500 4096 Jul 01 09:21 haha
-rw------- 1 500 500 1228 Jul 01 09:21 passwd
[root@node1 tmp]# lftp -u zhangsan 192.168.130.251 #切换另一个用户
Password:
lftp zhangsan@192.168.130.251:~> ls
-rw------- 1 500 500 541 Jul 01 09:20 fstab
drwx------ 2 500 500 4096 Jul 01 09:21 haha
-rw------- 1 500 500 1228 Jul 01 09:21 passwd
lftp zhangsan@192.168.130.251:/> lcd /etc
lcd ok, local cwd=/etc
lftp zhangsan@192.168.130.251:/> put issue #上传成功
23 bytes transferred
lftp zhangsan@192.168.130.251:/> ls
-rw------- 1 500 500 541 Jul 01 09:20 fstab
drwx------ 2 500 500 4096 Jul 01 09:21 haha
-rw------- 1 500 500 23 Jul 01 09:23 issue
-rw------- 1 500 500 1228 Jul 01 09:21 passwd
lftp zhangsan@192.168.130.251:/> rm issue
rm: Access failed: 550 Permission denied. (issue) #不能删除
lftp zhangsan@192.168.130.251:/> mkdir data
mkdir: Access failed: 550 Permission denied. (data) #不能创建目录
lftp zhangsan@192.168.130.251:/> ls
-rw------- 1 500 500 541 Jul 01 09:20 fstab
drwx------ 2 500 500 4096 Jul 01 09:21 haha
-rw------- 1 500 500 23 Jul 01 09:23 issue
-rw------- 1 500 500 1228 Jul 01 09:21 passw
```

