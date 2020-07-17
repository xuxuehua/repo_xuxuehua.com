---
title: "iterm2"
date: 2018-12-15 17:56
---


[TOC]



# iterm2



## sshpass

```
git clone git://github.com/kevinburke/sshpass.git
cd sshpass
./configure
make && make install
```



## 关闭选中复制

Preferences -> General -> Selection -> `Copy to pasteboard on selection`



## paste 速度

Edit -> Paste Special -> Advanced paste

Chunk size 16B

interchunk Delay 251.19ms 





## expect 免输入登录

进入/usr/local/bin目录，新建remote.exp文件，文件内容如下

```
#!/usr/bin/expect
set jumpusr [lindex $argv 0] 
set jumphost [lindex $argv 1] 
set usr [lindex $argv 2] 
set host [lindex $argv 3] 
catch {spawn ssh -l $jumpusr $jumphost} 
expect "*$jumphost*" { send "ssh -l $usr $host\r" } 
sleep 2
expect "*$host*" { send "cd ~\r"; interact }
interact # 执行完成保持交互状态，把控制权返回控制台
```

执行下面命令，修改文件属性为可执行 chmod  777  remote.exp 

在iTerm中测试刚才的程序。测试命令格式如下： 

// user1为登录堡垒机的用户名
// host1为堡垒机IP地址
// user2为登录目标服务器的用户名 
// host2为目标服务器的IP地址 

remote.exp  user1  host1  user2  host2 

如：remote.exp xiaowang 123.4.56.78 hadoop 132.45.6.89



```
#!/usr/bin/expect
set target_host [lindex $argv 0]
set TERMSERV 8.8.8.8
set USER rxu
set PORT 20002
set PASSWORD xxxxx

spawn ssh -p $PORT $USER@$TERMSERV
expect {
 "yes/no" {send "yes\r";exp_continue;}
 "Password:" { send "$PASSWORD\r" }
 }

expect "bdpops" {send "$target_host\r"}
expect {
 "Password:" { send "$PASSWORD\r" }
 }
expect "rxu@" {send "sudo su - \r"}
expect "password for rxu:" {send "$PASSWORD\r"}
interact
```

