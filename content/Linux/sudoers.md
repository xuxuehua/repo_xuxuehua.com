---
title: "sudoers"
date: 2018-06-07 01:51
---

[TOC]

# sudoers 文件配置



## 配置定义

```
使用者帳號  登入者的來源主機名稱=(可切換的身份)  可下達的指令
root                         ALL=(ALL)           ALL   <==這是預設值
```

> 1. 『使用者帳號』：系統的哪個帳號可以使用 sudo 這個指令的意思；
> 2. 『登入者的來源主機名稱』：當這個帳號由哪部主機連線到本 Linux 主機，意思是這個帳號可能是由哪一部網路主機連線過來的， 這個設定值可以指定用戶端電腦(信任的來源的意思)。預設值 root 可來自任何一部網路主機
> 3. 『(可切換的身份)』：這個帳號可以切換成什麼身份來下達後續的指令，預設 root 可以切換成任何人；
> 4. 『可下達的指令』：可用該身份下達什麼指令？這個指令請務必使用絕對路徑撰寫。 預設 root 可以切換任何身份且進行任何指令之意。



### 單一使用者可進行 root 所有指令

* 讓 vbird1 這個帳號可以使用 root 的任何指令

```
[root@study ~]# visudo
....(前面省略)....
root    ALL=(ALL)       ALL  <==找到這一行，大約在 98 行左右
vbird1  ALL=(ALL)       ALL  <==這一行是你要新增的！
....(底下省略)....
```

* 利用 wheel 群組以及免密碼的功能處理 visudo

* 我們在本章前面曾經建立過 pro1, pro2, pro3 ，這三個用戶能否透過群組的功能讓這三個人可以管理系統？ 可以的，而且很簡單！同樣我們使用實際案例來說明：

```
[root@study ~]# visudo  <==同樣的，請使用 root 先設定
....(前面省略)....
%wheel     ALL=(ALL)    ALL <==大約在 106 行左右，請將這行的 # 拿掉！
# 在最左邊加上 % ，代表後面接的是一個『群組』之意！改完請儲存後離開

[root@study ~]# usermod -a -G wheel pro1 <==將 pro1 加入 wheel 的支援
```



  ### 有限制的指令操作

* 系統上面的 myuser1 僅能夠幫 root 修改其他使用者的密碼

```
[root@study ~]# visudo  <==注意是 root 身份
myuser1      ALL=(root)    /usr/bin/passwd  <==最後指令務必用絕對路徑
```

> 上面的設定值指的是『myuser1 可以切換成為 root 使用 passwd 這個指令』的意思



* 可以執行『 passwd 任意字元』，但是『 passwd 』與『 passwd root 』這兩個指令例外

```
[root@study ~]# visudo  <==注意是 root 身份
myuser1	ALL=(root)  !/usr/bin/passwd, /usr/bin/passwd [A-Za-z]*, !/usr/bin/passwd root
```



####  禁止sudo等操作

```
useradd michelle -a -G admin
useradd sam -a -G client

%admin            ALL=(root)       NOPASSWD: ALL
%client            ALL=(root)       NOPASSWD: !SHELLS,!SUROOT,!SUDO,!SU


Cmnd_Alias SHELLS = /bin/sh, /bin/bash, /bin/csh, /bin/tcsh, /bin/ksh, /bin/zsh, /sbin/nash, /sbin/nologin, !/bin/sh -c *
Cmnd_Alias SU = /usr/bin/su - [A-z]*, /usr/bin/su [A-z]*, /bin/su - [A-z]*, /bin/su [A-z]*, /sbin/su - [A-z]*, /sbin/su [A-z]*
Cmnd_Alias SUROOT = /usr/bin/su -, /usr/bin/su "", /usr/bin/su - root, /usr/bin/su root, /usr/bin/su -[a-z]*, /bin/su -, /bin/su "", /bin/su - root, /bin/su root, /bin/su -[a-z]*, /sbin/su -, /sbin/su "", /sbin/su - root, /sbin/su root, /sbin/su -[a-z]*, /sbin/su.static -, /sbin/su.static "", /sbin/su.static - root, /sbin/su.static root, /sbin/su.static -[a-z]*
Cmnd_Alias SUDO = /usr/bin/sudo [A-Za-z]*
```





##  透過別名建置 visudo

* 假設我的 pro1, pro2, pro3 與 myuser1, myuser2 要加入上述的密碼管理員的 sudo 列表中， 那我可以創立一個帳號別名稱為 ADMPW 的名稱，然後將這個名稱處理一下即可

```
[root@study ~]# visudo  <==注意是 root 身份
User_Alias ADMPW = pro1, pro2, pro3, myuser1, myuser2
Cmnd_Alias ADMPWCOM = !/usr/bin/passwd, /usr/bin/passwd [A-Za-z]*, !/usr/bin/passwd root
ADMPW   ALL=(root)  ADMPWCOM
```

> Cmnd_Alias(命令別名)、Host_Alias(來源主機名稱別名) 都需要使用大寫字元的



##  sudo 的時間間隔問題

* 兩次執行 sudo 的間隔在五分鐘內，那麼再次執行 sudo 時就不需要再次輸入密碼了



## sudo 搭配 su 的使用方式

* 使用 sudo 搭配 su ， 一口氣將身份轉為 root ，而且還用使用者自己的密碼來變成 root 

```
[root@study ~]# visudo
User_Alias  ADMINS = pro1, pro2, pro3, myuser1
ADMINS ALL=(root)  /bin/su -
```

> 上述的 pro1, pro2, pro3, myuser1 這四個人，只要輸入『 sudo su - 』並且輸入『自己的密碼』後， 立刻變成 root 的身份





# example

如果要让普通用户user01可访问这个文件，可以在/etc/sudoers添加如下

```
user01     ALL = /bin/more /etc/shadow
这样，通过如下方式user01用户就可访问/etc/shadow文件：
[user01@unknown ~]$ sudo more /etc/shadow
[sudo] password for user01:
```



要让普通用户centreon具有/etc/init.d/nagios脚本重启的权限，可以在/etc/sudoers添加如下设置：

```
CENTREON   ALL = NOPASSWD: /etc/init.d/nagios restart

```

