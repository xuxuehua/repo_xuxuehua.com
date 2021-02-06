---
title: "selinux"
date: 2021-01-30 16:14
---
[toc]





# selinux

`sestatus` is showing the current mode as `permissive`.

In `permissive` mode, SELinux will not block anything, but merely warns you. The line will show `enforcing` when it's actually blocking.



On CentOS 7:

```
echo 0 > /sys/fs/selinux/enforce
```

