---
title: "git 命令"
date: 2019-03-21 19:10
---


[TOC]



# git





## add 添加到暂存区

把文件修改添加到暂存区，同时文件会被跟踪



## alias 设置别名

```
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.br branch
```

```
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```





## apply 合并diff变更

```
git diff > diff.txt

git apply diff.txt
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



###  -vv

查看当前所有被tracking的分支

```
git branch -vv
  main   aaf02f0 [main/master: ahead 25] Some other commit
* master add0a03 [jdsumsion/master] Some commit
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

本质上是通过移动HEAD检出版本，可以用于分支切换



### -b 指定分支

创建临时分支temp

```
git checkout -b temp COMMIT_ID
```



基于远端的分支进行创建

```
git checkout -b BRANCH_NAME/INFO  origin/BRANCH_NAME/INFO
```





### `--`  指定files  撤销工作区变更

即丢弃工作区的修改

`--` 很重要，如果没有 -- 的话，那么命令变成创建分支了

```
git checkout -- SOME_FILES
```



### `-` 恢复上一个分支

```
git checkout -
```



## clone 克隆仓库

克隆一个远程仓库作为本地仓库



### `--bare` 裸仓库

不带工作区的裸仓库





## commit 提交更改

把暂存区的所有内容提交到当前分支（若是多分支，就不一定是master分支）



### -a 直接提交

即工作目录到提交区

```
git commit -a -m "full commit"
```



### --amend 修改最近一次comment

```
git commit --amend
```



## config



标示提交信息，用户配置

```
git config --global user.name "Rick Xu"
git config --global user.email "rickxu1989@gmail.com"
```





配置显示颜色

```
git config --global color.ui true
```



### alias 配置别名

```
git config alias.SHORTNAME <full_command>
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



### --cached  暂存区与某次提交差异

默认是与HEAD之间的差异

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



## fetch 获取远程仓库的提交历史

但不会和本地的分支产生关联

```
git fetch origin master
```



所以需要进行merge操作

```
git merge origin/master
git push origin master
```



## init 初始化

```
git init ~/git-server --bare
```

> 初始化一个本地的远程服务器



## log 提交历史



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
git log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%dCreset %s %Cgreen(%cr)%C(bold blue)<%an>%Creset' --abbrev-commit
```



### -p 显示历史diff

 This option will add a **diff** with the introduced **changes** to each **commit** in the **log**





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





### --ours/--theirs

```
<<<<<<< 和 ======= 之间的部分称为 ours

======= 和 >>>>>>> 之间则称为 theirs
```



可以使用`git diff` 查看需要merge 的部分

然后checkout 到需要的文件

```
git checkout --ours some_file.py
```





## pull 拉取远端

等同于执行了git fetch + git merge



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

把分叉的提交历史“整理”成一条直线，看上去更直观。

缺点是本地的分叉提交已经被修改过了。

不要在共有的分支上使用rebase

![image-20200202123602247](git.assets/image-20200202123602247.png)

Rebases current branch onto origin/master

```
git rebase origin/master
```




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



### `--onto` 指定重演提交

![image-20200202123800871](git.assets/image-20200202123800871.png)

```
git rebase --onto master 5751363
```





## reflog 查看commit历史	

可查看reset --hard 之前的操作

可以查看分支之间的从属关系

```
git reflog show <branch name>
```



## remote 远程操作

### add 添加远程映射

```
git remote add REPO_NAME LOCATION
```



```
git remote add origin https://github.com/YOUR_NAME/YOUR_REPO.git
```

> 这里的origin指远程库的名称，默认共识用origin



### prune 清理旧分支

清理和远程不同步的分支

```
git remote prune
```



### show 查看远程分支

```
git remote show origin
```



## reset 回退

### `--mixed <commit_id>` 回退到前一个分支

默认值，可以不传入--mixed参数

同时将当前的内容复制到暂存区





### HEAD 撤销暂存区

取消暂存区所有的变更

```
git reset HEAD
```







### --filename 取消部分文件全部的修改

```
git reset HEAD -- FILENAME
```

> 将不会变更FILENAME的修改



多个文件

```
git reset HEAD -- FILE1 FILE2 FILE3
```

> 取消FILE1 FILE2 FILE3 的变更





### `--soft <commit_id>` 

仅仅是指针发生变化



### --hard 回退修改 (慎用)

回退到指定的变更到工作区

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

从暂存区与工作目录同时删除

```
git rm FILENAME
```



```
git rm --cached	
```

> 仅仅从暂存区删除



```
git rm $(git ls-files --deleted)
```

> 删除所有被跟踪，但是在工作目录被删除的文件



## tag 标签别名



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

```
git stash
# 等同于 
git stash save 
```



最新的存储保存在`refs/stash`中.老的存储可以通过相关的参数获得

```
stash@{0}						#获取最新的存储
stash@{1}						#获取次新
stash@{2.hour.ago}	#获取两小时之前的
stash@{n} 					#存储可以直接通过索引的位置来获得
```





### save 添加注解

```
git stash save "ANNOTATION"
```





不影响工作区的环境

```
git stash
```



### list 查看临时现场保存信息

查看工作现场信息

```
git stash list
```



### apply 恢复临时现场 (保留堆栈)

比pop会保留stash的堆栈信息

```
git stash apply 

#e.g.
git stash apply stash@{0}
```

> stash编号0为最新，以此类推 



### pop 恢复临时现场 (不保留堆栈)

不会保留stash的堆栈信息，会同时把stash 内容也删除掉

```
git stash pop
```



### drop 删除临时现场

```
git stash drop
```





### show 校验

```
git stash show
```



## status

对状态进行跟踪

![image-20200202003603169](git.assets/image-20200202003603169.png)



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







# reset vs checkout

![image-20200202122503975](git.assets/image-20200202122503975.png)



# rebase vs merge

![image-20200202123830861](git.assets/image-20200202123830861.png)









