---
title: "ps"
date: 2018-08-06 16:01
---

[TOC]

# PS 命令



## etime

**etime** – elapsed time since the process was started, in the form [[DD-]hh:]mm:ss.

```
!6 $ ps -p 4261 -o etime
    ELAPSED
      25:05
```

> 25:05 表示分钟



```
ps -eo pid,comm,lstart,etime,time,args
```

- PID – The Process ID
- COMMAND(second column) – The command name without options and/or arguments.
- STARTED – The absolute starting time of the process.
- ELAPSED – The elapsed time since the process was started, in the form of [[dd-]hh:]mm:ss.
- TIME – Cumulative CPU time, “[dd-]hh:mm:ss” format.
- COMMAND (last column) – Command name with all its provided options and arguments.



## etimes

**etimes** – elapsed time since the process was started, in seconds.

```
!7 $ ps -p 4261 -o etimes
ELAPSED
   1516
```



```
ps -eo pid,comm,lstart,etimes,time,args
```





## -ef

显示父进程和子进程

```
# ps -ef 
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0  2018 ?        02:13:44 /usr/lib/systemd/systemd --switched-root --system --deserialize 21
root         2     0  0  2018 ?        00:01:02 [kthreadd]
root         4     2  0  2018 ?        00:00:00 [kworker/0:0H]
```

> C	即CPU用于计算执行优先级的因子，数值越大，表明进程是CPU密集型运算，执行优先级会降低；数值越小，表明进程是I/O密集型运算，执行优先级会提高
>
> STIME	进程启动的时间
>
> TTY	完整的终端名称
>
> TIME	CPU时间
>
> CMD	启动进程所用的命令和参数



## aux

可以查看CPU，内存占用率

