---
title: "configs"
date: 2018-12-31 22:05
---


[TOC]



# Configs

## project

 Project configs are only available for the current project and stored in .git/config in the project's directory.

```
$ git config user.name "John Doe" 
```



```
cat .git/config
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
	ignorecase = true
	precomposeunicode = true
[remote "origin"]
	url = git@github.com:xxx/xx.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[user]
	name = username
	email = email@example.com
[branch "master"]
	remote = origin
	merge = refs/heads/master
```



### global

 Global configs are available for all projects for the current user and stored in ~/.gitconfig.



```
$ git config --global user.name "John Doe"
```



In your `.gitconfig` you can put something like this.

```
[includeIf "gitdir:~/company_a/"]
  path = .gitconfig-company_a
[includeIf "gitdir:~/company_b/"]
  path = .gitconfig-company_b
```

Example contents of .gitconfig-company_a

```
[user]
name = John Smith
email = john.smith@companya.net
```

Example contents of .gitconfig-company_b

```
[user]
name = John Smith
email = js@companyb.com
```

### system

System configs are available for all the users/projects and stored in /etc/gitconfig.



```
$ git config --system user.name "John Doe" 
```



## proxy

```
git config --local http.proxy 'socks5://127.0.0.1:1080'
git config --local https.proxy 'socks5://127.0.0.1:1080'
```

