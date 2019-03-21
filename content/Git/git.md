---
title: "git 指令"
date: 2019-03-21 19:10
---


[TOC]



# git



## branch



### -d 删除

清除分支

```
git branch -d
```



### -v verbose

查看本地分支

```
git branch -v
```



## cat-file



### -p 内容

查看HASH_VALUE 内容

```
git cat-file -p HASH_VALUE
```





### -t 类型

查看文件类型

```
git cat-file -t HASH_VALUE
```





## checkout

切换分支



### -b 指定分支

创建临时分支temp

```
git checkout -b temp COMMIT_ID
```





## diff

变更比较

```
git diff HASH_V1 HASH_V2
```



对比head的上级

```
git diff HEAD HEAD^
or
git diff HEAD HEAD~1
```

上级的上级

```
git diff HEAD HEAD^^
or
git diff HEAD HEAD~2
```





## log



### --graph 图形化

开启图形化





### -n 行数

```
git log -n4 
```





### --oneline 一行信息

```
git log --oneline
```



