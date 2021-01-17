---
title: "basic_knowledge"
date: 2019-05-24 00:55
---


[TOC]





# 文件类型

c    Character 字符设备文件   /dev

l    Symbolic Link File 符号链接文件

p    Pipe 命令管道文件

s    Socket 套接字文件  /tmp



ls －l 后面的数字，表示文件被硬链接的次数

文件大小的默认是字节

每个文件有三个时间戳，这里显示的时间是文件最近一次被修改的时间戳

access 访问

modify  修改  文件内容发生改变

change 改变  metadata(元数据)

# PID

每一个进程都是由父进程创建，如果没有父进程，那就是进程为1的init。当所有进程关闭之后，init才

会退出

# Trap 信号

trap 在脚本中捕捉信号，并且可以待定处理

```
1. SIGHUP
2. SIGINT
9. SIGKILL
15. SIGTERM
18. SIGCONT
19. SIGSTOP
```

```
trap ‘ echo “No quit….”’ INT   不允许用户取消
```



```bash
#!/bin/bash
CLEANUP() {
     rm -rf /var/tmp/test
     echo “Cleanup …."
}
trap 'CLEANUP ; exit 5' INT
mkdir -p /var/tmp/test
while true; do
     touch /var/tmp/test/file-`date +%F-%H-%M-%S`
     sleep 2
done
```



# 运行级别



```
0	停机状态
1	单用户状态，root权限，用于系统维护，禁止远程登陆
2	多用户状态但没有NFS，不支持网络
3	完全多用户
4	系统保留
5	GUI模式
6	重启
```





# 异常关机

```
sudo last -x shutdown
```

> if you do not disable the history logs

or

```
sudo grep -E -i -r 'error|warning|panic' /var/log/
```

or

```
grep -iv ': starting\|kernel: .*: Power Button\|watching system buttons\|Stopped Cleaning Up\|Started Crash recovery kernel' \
  /var/log/messages /var/log/syslog /var/log/apcupsd* \
  | grep -iw 'recover[a-z]*\|power[a-z]*\|shut[a-z ]*down\|rsyslogd\|ups'
```



When an unexpected power off or hardware failure occurs the filesystems will not be properly unmounted so in the next boot you may get logs like this:

```
EXT4-fs ... INFO: recovery required ... 
Starting XFS recovery filesystem ...
systemd-fsck: ... recovering journal
systemd-journald: File /var/log/journal/.../system.journal corrupted or uncleanly shut down, renaming and replacing.
```

When the system powers off because user pressed the power button you get logs like this:

```
systemd-logind: Power key pressed.
systemd-logind: Powering Off...
systemd-logind: System is powering down.
```

Only when the system shuts down orderly you get logs like this:

```
rsyslogd: ... exiting on signal 15
```

When the system shuts down due to overheating you get logs like this:

```
critical temperature reached...,shutting down
```

If you have a UPS and running a daemon to monitor power and shutdown you should obviously check its logs (NUT logs on /var/log/messages but apcupsd logs on /var/log/apcupsd*)





# 命令光标跳转

ctl +a 跳行首

ctl +e 跳行尾

ctl +u 删除到行首

ctl +k 删除到行尾



# 快捷命令

! + number,    执行该编号的命令

!- + number    执行倒数编号的命令

!!            执行上一条命令

!$           引用前一个命令的最后一个参数 ＝esc +.

# 教程

https://www.w3cschool.cn/linux/