---
title: "git 命令"
date: 2019-03-21 19:10
---


[TOC]



# git



## branch



### -D 强力删除

处理-d删除不了的分支



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





### --  指定files  工作区变回暂存区

```
git checkout -- SOME_FILES
```



## commit



### --amend 修改最近一次comment

```
git commit --amend
```





## diff 

默认的比较是工作区与暂存区差异



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



### --  指定files

```
git diff -- SOME_FILES
```





### --cached  暂存区与HEAD差异

```
git diff --cached
```



返回为空，表示暂存区和HEAD是一致的



## rebase 



### -i 交互式操作

```
git rebase -i HASH_VALUE
```





#### r/reword 编辑历史commit 



```
# 将开头的pick修改成需要的内容，如编辑commit 消息
r 4123213 some project info.
```



#### s/squash 合并commit

```
# 将开头的pick修改成需要的内容，如合并commit 消息
pick
s
s
```



合并间隔的commit 直接`git rebase -i HASH_VALUE`





## reset



### HEAD 清空暂存区







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



