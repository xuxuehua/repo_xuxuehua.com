---
title: "git 命令"
date: 2019-03-21 19:10
---


[TOC]



# git





## add 添加到暂存区

把文件修改添加到暂存区



## alias 设置别名

```
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.br branch
```

```
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```



## branch



### -D 强力删除

处理-d删除不了的分支



### -d 删除

清除分支

```
git branch -d BRANCH_NAME
```



### -v verbose

查看本地分支

```
git branch -v
```





### --set-upstream-to 关联分支

`no tracking information`，则说明本地分支和远程分支的链接关系没有创建

```
git branch --set-upstream-to <branch-name> origin/<branch-name>
```

```
git branch --set-upstream-to=origin/master master
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



## check-ignore

检查`.gitignore` 是否配置正确

```
git check-ignore -v App.class
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

即丢弃工作区的修改

`--` 很重要，如果没有 -- 的话，那么命令变成创建分支了

```
git checkout -- SOME_FILES
```





## clone 备份



### --  bare 裸仓库

不带工作区的裸仓库







## commit 提交更改

把暂存区的所有内容提交到当前分支（若是多分支，就不一定是master分支）



### --amend 修改最近一次comment

```
git commit --amend
```



## config

配置显示颜色

```
git config --global color.ui true
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



### --pretty 编辑输出信息

```
git log --pretty=oneline
```





## merge 合并

git merge命令用于合并指定分支到当前分支上



在master 分支上合并dev 分支内容

```
git merge dev
```



### --allow-unrelated-histories   



### --no-ff 

git一般使用”Fast forward”模式，在这种模式下，删除分支后，会丢掉分支信息，带参数 –no-ff来禁用”Fast forward”模式

```
git merge --no-ff -m "Merged with no-ff mode" dev
```

合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。





## pull 拉取远端



## push 推送到远端

```
git push https://user:pass@example.com
```



### -u/--set-upstream 关联分支

set upstream for git pull/status

Git 不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令

```
git push -u origin master
```





### --force

不要用 git push --force，而要用 git push --force-with-lease 代替。在你上次提交之后，只要其他人往该分支提交给代码，git push --force-with-lease 会拒绝覆盖



## rebase 

把分叉的提交历史“整理”成一条直线，看上去更直观。缺点是本地的分叉提交已经被修改过了。



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



## reflog 查看回退历史	

可查看reset --hard 之前的操作



## remote 远程提交

### add

```
git remote add REPO_NAME LOCATION
```



```
git remote add origin https://github.com/YOUR_NAME/YOUR_REPO.git
```

> 这里的origin指远程库的名称



## reset



### HEAD 清空暂存区

取消暂存区所有的变更

```
git reset HEAD
```



#### --filename 取消部分文件修改

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



回到上一个版本

```
git reset --hard HEAD^
```



回到上上一个版本

```
git reset --hard HEAD^^
```



回到前100个版本

```
git reset --hard HEAD~100
```



## rm 删除文件

删除后续commit不需要的文件，即放置到暂存区里面

```
git rm FILENAMES
```



## tag



创建标签

默认为`HEAD`，也可以指定一个commit id

```
git tag <tagname>
```



查看标签

```
git tag
```





### show 查看标签

```
git show v0.1
```



#### -a 指定标签名

```
git tag -a v0.1 -m "version 0.1 released" 1094adb
```



#### -m 指定说明文字

```
git tag -a v0.1 -m "version 0.1 released" 1094adb
```





### -d 删除

```
git tag -d v0.1
```





推送某个标签到远程

```
git push origin v1.0
```



一次性推送全部尚未推送到远程的本地标签

```
git push origin --tags
```



从远程删除。删除命令也是push

```
git push origin :refs/tags/v0.9
```





## stash 保存临时现场

设A为游戏软件 

```
1、master 上面发布的是A的1.0版本 
2、dev 上开发的是A的2.0版本 
3、这时，用户反映 1.0版本存在漏洞，有人利用这个漏洞开外挂 
4、需要从dev切换到master去填这个漏洞，正常必须先提交dev目前的工作，才能切换。 
5、而dev的工作还未完成，不想提交，所以先把dev的工作stash一下。然后切换到master 
6、在master建立分支issue101并切换. 
7、在issue101上修复漏洞。 
8、修复后，在master上合并issue101 
9、切回dev，恢复原本工作，继续工作。
```



不影响工作区的环境

```
git stash
```



### --list 查看临时现场保存信息

查看工作现场信息

```
git stash --list
```



### apply 恢复临时现场 (保留堆栈)

比pop会保留stash的堆栈信息

```
git stash apply
```



### pop 恢复临时现场 (不保留堆栈)

不会保留stash的堆栈信息，会同时把stash 内容也删除掉

```
git stash pop
```



### drop 删除临时现场

```
git stash drop
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




