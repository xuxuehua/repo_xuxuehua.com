---
title: "basic_knowledge"
date: 2019-05-24 00:55
---


[TOC]





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





