---
title: "basic_knowledge"
date: 2019-02-20 17:10
---


[TOC]



# Ansible



## 安装

### centos

```
yum -y install epel-release
yum list all *ansible*
yum info ansible
yum -y install ansible
```



## 配置文件

```
/etc/ansible/ansible.cfg    主配置文件
/etc/ansible/hosts          Inventory
/usr/bin/ansible-doc        帮助文件
/usr/bin/ansible-playbook   指定运行任务文件
```





## 定义Inventory



```
# cd /etc/ansible/
# cp hosts{,.bak}
# > hosts

# cat hosts
[webserver]
127.0.0.1
192.168.10.149

[dbserver]
192.168.10.113
```



## 使用秘钥方式连接



```
ssh-keygen -t rsa 
ssh-copy-id -i /root/.ssh/id_rsa.pub root@192.168.10.149
ssh-copy-id -i /root/.ssh/id_rsa.pub root@192.168.10.113
ssh-copy-id -i /root/.ssh/id_rsa.pub root@127.0.0.1
```



## 使用帮助



```
ansible-doc -l                列出ansible所有的模块
ansible-doc -s MODULE_NAME    查看指定模块具体适用
```





## Ansible命令

### Syntax

```
ansible <host-pattern> [-f forks] [-m module_name] [-a args]

<host-pattern>  这次命令对哪些主机生效的
   inventory group name
   ip
   all
-f forks        一次处理多少个主机
-m module_name  要使用的模块
-a args         模块特有的参数
```

```
ansible 192.168.10.113 -m command -a 'date'
ansible webserver -m command -a 'date'
ansible all -m command -a 'date'
```







