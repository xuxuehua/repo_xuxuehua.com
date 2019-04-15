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



基于远端的分支进行创建

```
git checkout -b BRANCH_NAME/INFO  origin/BRANCH_NAME/INFO
```





### --  指定files  工作区变回暂存区

```
git checkout -- SOME_FILES
```





## clone 备份



### --  bare 裸仓库

不带工作区的裸仓库







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



可以添加多个文件

```
git diff -- FILE1 FILE2
```



### --cached  暂存区与HEAD差异

```
git diff --cached
```



返回为空，表示暂存区和HEAD是一致的



### BRANCH1 BRANCH2 分支差异

```
git diff test master
```



#### -- 指定files

查看分支下具体的某个文件差异

```
git diff test master -- file1
```



### COMMIT1 COMMIT2 提交差异

```
git diff HASH_VALUE1 HASH_VALUE2
```



#### --  指定files

```
git diff HASH_VALUE1 HASH_VALUE2 -- file1
```



## fetch 拉取到本地

但不会和本地的分支产生关联



## merge 合并



### --allow-unrelated-histories   



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



## remote 远程提交

### add

```
git remote add REPO_NAME LOCATION
```



## reset



### HEAD 清空暂存区

取消暂存区所有的变更

```
git reset HEAD
```



#### --  filename 取消部分文件修改

```
git reset HEAD -- FILENAME
```

> 将不会变更FILENAME的修改



多个文件

```
git reset HEAD -- FILE1 FILE2 FILE3
```

> 取消FILE1 FILE2 FILE3 的变更





### --hard 回退修改 (慎用)

回退到指定的变更

```
git reset --hard HASH_VALUE
```



恢复到头指针位置

```
git reset --hard HEAD
```



## rm 删除文件

删除后续commit不需要的文件

```
git rm FILENAMES
```





## stash 保存临时现场

不影响工作区的环境

```
git stash
```



### --  list 查看保存信息

```
git stash --list
```



### apply 恢复临时现场

比pop会保留stash的堆栈信息

```
git stash apply
```



### pop 恢复临时现场

不会保留stash的堆栈信息

```
git stash pop
```





## status



### --porcelain 简单输出

```
$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   content/Kubernetes/kubernetes_with_centos.md
	modified:   content/Kubernetes/resource_management.md
	modified:   content/Kubernetes/yaml.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	content/Kubernetes/services.md
```

```
$ git status --porcelain
 M content/Kubernetes/kubernetes_with_centos.md
 M content/Kubernetes/resource_management.md
 M content/Kubernetes/yaml.md
?? content/Kubernetes/services.md
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



## pull 拉取远端



## push 推送到远端





### --force

不要用 git push --force，而要用 git push --force-with-lease 代替。在你上次提交之后，只要其他人往该分支提交给代码，git push --force-with-lease 会拒绝覆盖