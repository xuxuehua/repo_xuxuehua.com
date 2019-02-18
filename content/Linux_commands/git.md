---
title: "git"
date: 2018-08-27 18:14
---

[TOC]

# git



## proxy

可以用git config --list查看是否设置成功

```
git config --global http.proxy http://localhost:8123
git config --global https.proxy http://localhost:8123
```





## Commands

### clone 

克隆分支

```
git clone -b mybranch --single-branch git://sub.domain.com/repo.git
```







## 创建 Create

```
# Clone an existing repository
$ git clone ssh://git@github.com/username/repo.git

# Create a new local repository
$ git init
```

------

## 本地修改 Local Change

```
# Changed files in your working directory
$ git status


# Changes to tracked files
$ git diff

# Add all current changes to the next commit 
$ git add .

# Add some changes in <file> to the next commit
$ git add -p <file>

# Commit all local changes in tracked files
$ git commit -a 

# Commit previously Staged changes
$ git commit

# Change the last commit 
* Don't amend published commits
$ git commit --amend
```

------

## 提交历史 Commit History

```
# Show all commits, starting with newest
$ git log

# Show changes over time for a specific file
$ git log -p <file>

# Who changed what and when in <file>
$ git blame <file>
```

------

## 分支和标签 Branch & Tags

```
# List all existing branches
$ git branch -av

# Switch HEAD branch
$ git checkout <branch>

# Create a new branch based on your current HEAD
$ git branch <new-branch>

# Create a new tracking branch base on a remote branch
$ git checkout --track <remote/branch>

# Delete a local branch
$ git branch -d <branch_name>

# Mark the current commit with a tag
$ git tag <tag_name>
```

------

## 更新和提交 Update & Publish

```
# List all currently configured remotes
$ git remote -v 

# Show information about a remote
$ git remote show <remote>

# Add new remote repository, named <remote>
$ git remote add <shortname> <url>

# Download all changes from <remote>, but don't integrate into HEAD
$ git fetch <remote>

# Download changes and directly merge/integrate into HEAD
$ git pull <remote> <branch>

# Publish local changes on a remote
$ git push <remote> <branch>

# Delete a branch on the remote 
$ git branch -dr <remote/branch>

# Publish your tags
$ git push --tags
```

------

## 合并和重建 Merge & Rebase

```
# Merge <branch> into your current HEAD
$ git merge <branch>

# Rebase your current HEAD onto <branch>
* Don't rebase published commits
$ git rebase <branch>

# Abort a rebase
$ git rebase --abort

# Continue a rebase after resolving conflicts
$ git rebase --continue

# Use your configurd merge tool to solve conflicts
$ git mergetool

# Use your editor to manually solve conflicts and (after resloving) mark file as resolved 
$ git add <resolved-file>
$ git rm <resolved-file>
```

------

## 撤销 Undo

```
# Discard all local changes in your working directory
$ git reset --hard HEAD

# Discard local changes in a specific file
$ git checkout HEAD <file>

# Revert a commit (by producing a enw commit and discard all changes since then)
$ git reset --hard <commit>

# And preserve all changes as unstaged changes
$ git reset <commit>

# And preserve uncommitted local changes
$ git reset --keep <commit>
```