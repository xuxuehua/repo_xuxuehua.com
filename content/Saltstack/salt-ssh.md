---
title: "salt-ssh"
date: 2019-06-19 17:35
---
[TOC]



# Salt ssh

通过ssh 协议管理



## installation

```
yum -y install salt-ssh
```



```
git clone https://github.com/saltstack/salt.git
python setup.py  install
```





## 

# usage

```
Usage: salt-ssh [options]
```





## -c/--config-dir 	指定配置目录

-c CONFIG_DIR, --config-dir=CONFIG_DIR
Pass in an alternative configuration directory.
Default: /etc/salt





##  -i/--ignore-host-keys 忽略keys

By default ssh host keys are honored and connections
will ask for approval

当ssh连接时，忽略keys





## -r/--raw/--raw-shell 直接使用shell命令

Don't execute a salt routine on the targets, execute a
raw shell command



## –priv 	指定SSH Key

指定SSH私有密钥文件



## –roster 	指定roster系统

定义使用哪个roster系统，如果定义了一个后端数据库，扫描方式，或者用户自定义的的roster系统，默认的就是/etc/salt/roster文件



## –roster-file 	指定roster文件





## –refresh/–refresh-cache 	刷新cache

刷新cache，如果target的grains改变会自动刷新



## –max-procs	指定进程数

指定进程数，默认为25





## –passwd	指定默认密码

指定默认密码



## –key-deploy	配置keys

配置keys 设置这个参数对于所有minions用来部署ssh-key认证， 这个参和–passwd结合起来使用会使初始化部署很快很方便。当调用master模块时，并加上参数 –key-deploy 即可在minions生成keys，下次开始就不使用密码





# 使用

## 配置roster

翻译过来是花名册，登记簿的意思，roster定义存放主机列表文件，默认存放位置在/etc/salt/roster，里边有提供默认案例。

Roster 系统编译了一个内部数据结构，称为 Targets。Targets 是一个目标系统和关于如何连接到系统属性的列表。对于一个在 Salt 中的 Roster 模块来说，唯一要求是返回 Targets 数据结构：

```
Salt ID：   # ID，用于salt-ssh引用
    host:    # IP或域名
　　 user:    # 登录用户名
    passwd:  # 登录密码
 
    # 可选参数
    port:    # 自定义的端口
    sudo:    # 是否允许sudo到root,默认不允许
    priv:    # ssh登录key路径，默认为salt-ssh.rsa
    timeout:  # 等待超时
```



/etc/salt/roster

```
minion:
  host: 192.168.1.11
  user: root
  passwd: centos
  port: 22
  sudo: True
```



## 使用salt-ssh

第一次运行 Salt SSH 会提示进行 salt-ssh key 的部署，需要在 Rosters 中配置用户的密码，即可进行 Key 的部署



