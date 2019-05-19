---
title: "faq 常见问题"
date: 2018-09-15 11:17
---


[TOC]


# FAQ



## Author name / email address

use Git's "filter-branch" command. It allows you to batch-process a (potentially large) number of commits with a script.
You can run the below sample script in your repository (filling in real values for the old and new email and name):

```
$ git filter-branch --env-filter '
WRONG_EMAIL="wrong@example.com"
NEW_NAME="New Name Value"
NEW_EMAIL="correct@example.com"

if [ "$GIT_COMMITTER_EMAIL" = "$WRONG_EMAIL" ]
then
    export GIT_COMMITTER_NAME="$NEW_NAME"
    export GIT_COMMITTER_EMAIL="$NEW_EMAIL"
fi
if [ "$GIT_AUTHOR_EMAIL" = "$WRONG_EMAIL" ]
then
    export GIT_AUTHOR_NAME="$NEW_NAME"
    export GIT_AUTHOR_EMAIL="$NEW_EMAIL"
fi
' --tag-name-filter cat -- --branches --tags
```



## credential

```
git config credential.helper 'cache --timeout 1800'
```





## detached HEAD

分离头指针状态，即没有分支的状态下产生的问题

需要将当前操作挂到一个分支上或一个tag上以确保变更不会被丢弃

```
$ git checkout 941a378ffcc76dc8fb30d3cc29a
Note: checking out '941a378ffcc76dc8fb30d3cc29a'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at 941a378 Updates
```



常用于测试的操作，其不在任何分支当中

若需撤销此操作，`git checkout` 到其他分支即可





## Everything up-to-date

确保配置了基本的user.name & user.email

```
$ git config -l
credential.helper=osxkeychain
core.repositoryformatversion=0
core.filemode=true
core.bare=false
core.logallrefupdates=true
core.ignorecase=true
core.precomposeunicode=true
remote.origin.url=git@github.com:xuxuehua/scripts.git
remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
branch.master.remote=origin
branch.master.merge=refs/heads/master
user.email=xuxuehua3@gmail.com
user.name=Xuehua Xu
```





出现这个问题的原因是git提交改动到缓存，要push的时候不会将本地所有的分支都push掉，所以出现这个问题。我们应该告诉git提交哪个分支。

这里有种特殊的情况是如果你是fork别人的仓库再clone到本地的话，即使git上只有一个主分支，他还是可能出现这个错误。那么我们就需要新建分支提交改动然后合并分支。

接下来先创建一个新分支提交改动

```
$ git branch newbranch
```

然后输入这条命令检查是否创建成功

```
$ git branch
```

这时终端输出

```
  newbranch
* master
```

这样就创建成功了，前面的*代表的是当前你所在的工作分支。我们接下来就要切换工作分支。

```
$ git checkout newbranch
```

这样就切换完了，可以`$ git branch`确认下。然后你要将你的改动提交到新的分支上。

```
$ git add .
$ git commit -a
```

此时可以`$ git status`检查下提交情况。如果提交成功，我们接下来就要回主分支了，代码和之前一样。

```
$ git checkout master
```

然后我们要将新分支提交的改动合并到主分支上

```
$ git merge newbranch
```

合并分支可能产生冲突这是正常的，虽然我们这是新建的分支不会产生冲突，但还是在这里记录下。下面的代码可以查看产生冲突的文件，然后做对应的修改再提交一次就可以了。

```
$ git diff
```

我们的问题就解决了，接下来就可以push代码了。

```
$ git push -u origin master
```

新建分支的朋友别忘了删除这个分支

```
$ git branch -D newbranch
```

如果想保留分支只是想删除已经合并的部分只要把大写的D改成小写的d就行了。





## The following untracked working tree files would be overwritten by merge:

we want to overwrite these files.

```
`git fetch --all``git reset --hard origin``/master`
```

This grabs the latest repository files without merging and resets the master branch and overwrites the modified ones.



## Git fire

```
程序员的消防演习：
alias incaseoffire="git checkout -b $USER-emergency; git add .; git commit -m fire; git push origin $USER-emergency -f"

```

