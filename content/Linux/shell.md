---
title: "shell"
date: 2021-04-05 16:22
---
[toc]





# Shell





## Bash的配置文件

全局配置   即编辑下面的任何一个，对所有用户都会生效

```
/etc/profile
/etc/profile.d/*.sh
/etc/bashrc
```



个人配置

```
~/.bash_profile
~/.bashrc
```



profile类文件

```
设定环境变量
运行命令或脚本
```



bashrc类的文件

```
设定本地变量
定义命令别名
```







## 登录式shell读取配置文件

```
/etc/profile   ->   /etc/profile.d/*.sh   ->   ~/.bash_profile   ->   ~/.bashrc   ->   /etc/bashrc
```



## 非登录式shell读取配置文件

```
 ~/.bashrc   ->   /etc/bashrc   ->   /etc/profile.d/*.sh
```

