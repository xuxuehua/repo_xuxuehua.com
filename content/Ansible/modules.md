---
title: "modules"
date: 2019-02-20 17:24
---


[TOC]



# 常见模块

## comman  命令模块(默认)

用于在远程主机执行命令；不能使用变量，管道等

```
ansible all -a 'date'
```



## cron        计划任务  

```
   month   指定月份
   minute  指定分钟
   job     指定任务
   day     表示那一天
   hour    指定小时
   weekday 表示周几
   state   表示是添加还是删除
       present：安装
       absent：移除
```

```
ansible webserver -m cron -a 'minute="*/10" job="/bin/echo hello" name="test cron job"'   #不写默认都是*，每个任务都必须有一个名字 
ansible webserver -a 'crontab -l'
ansible webserver -m cron -a 'minute="*/10" job="/bin/echo hello" name="test cron job" state=absent'  #移除任务
```



## user    用户账号管理

```
   name    用户名
   uid     uid
   state   状态  
   group   属于哪个组
   groups  附加组
   home    家目录
   createhome  是否创建家目录
   comment 注释信息
   system  是否是系统用户
```

```
ansible all -m user -a 'name="user1"'
ansible all -m user -a 'name="user1" state=absent'
```



## group   组管理

```
   gid     gid      
   name    组名               
   state   状态           
   system  是否是系统组
```

```
ansible webserver -m group -a 'name=mysql gid=306 system=yes'
ansible webserver -m user -a 'name=mysql uid=306 system=yes group=mysql'
```



## copy    复制(复制本地到远程指定位置)

```
   src     定义本地源文件路径
   dest    定义远程目录文件路径(绝对路径)
   owner   属主
   group   属组
   mode    权限
   content 取代src=,表示直接用此处的信息生成为文件内容
```

```
yum -y install libselinux-python
ansible all -m copy -a 'src=/etc/fstab dest=/tmp/fstab.ansible owner=root mode=640'
ansible all -m copy -a 'content="hello ansible\nHi ansible" dest=/tmp/test.ansible'
```



## file    设置文件属性

```
   path|dest|name  对那个文件做设定
   
   创建文件的符号链接：
       src：    指定源文件
       path：   指明符号链接文件路径
```

```
ansible all -m file -a 'owner=mysql group=mysql mode=644 path=/tmp/fstab.ansible'
ansible all -m file -a 'path=/tmp/fstab.link src=/tmp/fstab.ansible state=link'
```



## ping    测试主机

```
ansible all -m ping
```



## service 管理服务运行状态

```
   enabled 是否开机自动启动
   name    指定服务名
   state   指定服务状态
       started     启动服务
       stoped      停止服务
       restarted   重启服务
   arguments   服务的参数
```

```
ansible webserver -m service -a 'enabled=true name=httpd state=started'
```



## shell   远程主机运行命令

尤其是用到管道变量等功能的复杂命令

```
ansible all -m shell -a 'echo magedu | passwd --stdin user1'
```



## script  本地脚本复制到远程主机并运行

```
ansible all -m script -a '/tmp/test.sh'
```



## yum     安装程序包



```
   name    程序包名称(不指定版本就安装最新的版本latest)
   state   present,latest表示安装，absent表示卸载
```

```
ansible webserver -m yum -a 'name=httpd'
ansible all -m yum -a 'name=ntpdate'  #默认就是安装
ansible all -m yum -a 'name=ntpdate state=absent'
```





## setup   收集远程主机信息（facts）

 每个被管理节点在接受并运行管理命令之前，会将自己主机相关信息，如操作系统版本，IP地址等报告给远程的ansible主机 

```
ansible all -m setup
```



