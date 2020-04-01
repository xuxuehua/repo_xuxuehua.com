---
title: "lsof"
date: 2020-04-01 02:28
---
[toc]





# lsof 



## 恢复文件

```
$ rm /var/log/messages

$ lsof |grep /var/log/messages

rsyslogd   1737      root    1w      REG                8,2   5716123     652638 /var/log/messages (deleted)
```



```
$ cd /proc/1737/fd/

$ ll

lrwx------ 1 root root 64 Dec 23 13:00 0 -> socket:[11442]
l-wx------ 1 root root 64 Dec 23 13:00 1 -> /var/log/messages (deleted)
l-wx------ 1 root root 64 Dec 23 13:00 2 -> /var/log/secure
lr-x------ 1 root root 64 Dec 23 13:00 3 -> /proc/kmsg
l-wx------ 1 root root 64 Dec 23 13:00 4 -> /var/log/maillog
```



```
$ head -5 1

Nov 14 03:11:11 localhost kernel: imklog 5.8.10, log source = /proc/kmsg started.
Nov 14 03:11:11 localhost rsyslogd: [origin software="rsyslogd" swVersion="5.8.10" x-pid="1241" x-info="http://www.rsyslog.com"] start
Nov 14 03:11:11 localhost kernel: Initializing cgroup subsys cpuset
Nov 14 03:11:11 localhost kernel: Initializing cgroup subsys cpu
Nov 14 03:11:11 localhost kernel: Linux version 2.6.32-431.el6.x86_64 (mockbuild@c6b8.bsys.dev.centos.org) (gcc version 4.4.7 20120313 (Red Hat 4.4.7-4) (GCC) ) #1 SMP Fri Nov 22 03:15:09 UTC 2013



$ head -5 /var/log/message_bac

Nov 14 03:11:11 localhost kernel: imklog 5.8.10, log source = /proc/kmsg started.
Nov 14 03:11:11 localhost rsyslogd: [origin software="rsyslogd" swVersion="5.8.10" x-pid="1241" x-info="http://www.rsyslog.com"] start
Nov 14 03:11:11 localhost kernel: Initializing cgroup subsys cpuset
Nov 14 03:11:11 localhost kernel: Initializing cgroup subsys cpu
Nov 14 03:11:11 localhost kernel: Linux version 2.6.32-431.el6.x86_64 (mockbuild@c6b8.bsys.dev.centos.org) (gcc version 4.4.7 20120313 (Red Hat 4.4.7-4) (GCC) ) #1 SMP Fri Nov 22 03:15:09 UTC 2013


$ cat 1 > /var/log/messages
```

