---
title: "basic_knowledge"
date: 2019-03-21 18:42
---


[TOC]

# Git



最优的存储能力

非凡的性能

开源

容易备份

支持离线操作

很容易定制工作流程



## WorkFlow 流程



![img](https://snag.gy/0KAW7k.jpg)



### Workspace 工作区



### Index / Stage 暂存区



### Repository  仓库区/本地仓库



### Remote 远程仓库









## Installation

### CentOS

```
sudo yum -y install git
```



### Ubuntu

```
sudo apt -y install git
```





## 基本配置



### 配置用户

```
git config --global user.name "USERNAME"
git config --global user.email "USERNAME@DOMAIN.com"
```





## config 作用域



### `--local` 缺省

缺省为--local

只对某个仓库有效





### `--global`

对当前用户所有仓库有效





### `--system`

对系统中所有登陆的用户都有效



### `--list` 查看配置









## git 对象

![img](https://snag.gy/6iJuLn.jpg)







### commit

一个commit会对应一棵树

当前commit操作所对应的所有文件夹和文件的快照



### tree

树也是文件夹，或者文件





### blob 

只要文件的内容相同，那么就是唯一的blob











# .git 目录



## HEAD 

文本信息，分支信息



若处于Detached HEAD状态，会指向具体的某个commit上，不和任何分支挂钩



## config

git的配置信息



## objects







## refs



### heads

分支，即独立的开发空间



#### master

git仓库存放的对象，是commit的类型

即master的指针指向的信息



### tags

项目标签信息



## index 暂存区

存放暂存区信息



# .gitignore

忽略加入git仓库中的文件



## 定义

```
test/		# 表示忽略test文件夹下面的文件，但不包含test命名的文件

test		# 表示忽略test文件及test文件夹下面的文件
```



## example

[https://github.com/github/gitignore](https://github.com/github/gitignore)

