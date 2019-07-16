---
title: "salt-call"
date: 2019-07-11 14:14
---
[TOC]



# salt-call

用于无master架构中执行状态





## --local

告诉salt-minion在本地文件系统上寻找state tree，而不是去连接salt master

```
# salt-call --local state.highstate
```





