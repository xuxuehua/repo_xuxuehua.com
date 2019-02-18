---
title: "push"
date: 2018-08-03 17:31
---

[TOC]



# push 



## --force

不要用 git push --force，而要用 git push --force-with-lease 代替。在你上次提交之后，只要其他人往该分支提交给代码，git push --force-with-lease 会拒绝覆盖