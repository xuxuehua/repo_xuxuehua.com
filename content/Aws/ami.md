---
title: "ami"
date: 2019-06-27 09:24
---
[TOC]





# Amazon Machine Image

Packaged environment and settings

Basic unit of deployment

Deploy one or several



## Typical components

Template for the root volume

Launch permissions

Block device mapping





# Amazon Linux 2 RHEL compatibility

```
[root@ip-172-31-9-108 pgpool-II]# rpm -E %{rhel}
7
[root@ip-172-31-9-108 pgpool-II]# cat /proc/version 
Linux version 4.14.193-149.317.amzn2.x86_64 (mockbuild@ip-10-0-1-32) (gcc version 7.3.1 20180712 (Red Hat 7.3.1-9) (GCC)) #1 SMP Thu Sep 3 19:04:44 UTC 2020
[root@ip-172-31-9-108 pgpool-II]# uname -a
Linux ip-172-31-9-108.ec2.internal 4.14.193-149.317.amzn2.x86_64 #1 SMP Thu Sep 3 19:04:44 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
```

