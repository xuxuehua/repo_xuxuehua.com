---
title: "top"
date: 2018-11-27 01:05
---


[TOC]


# top



top指令是从`/proc/stats` 目录下获取数据



## -d 更新间隔

默认是3秒



## -i  不显示僵死

不限时任何闲置或者僵死的进程



## -p 

指定监控进程ID来监控进程状态





## -H Threads-mode operation

Instructs  top to display individual threads.  Without this command-line option a summation of all threads in each process is shown.  Later this can be changed with the `H` interactive command



```
top -H -p <process_id>
```



# example

```
top - 13:08:18 up 356 days,  5:36,  2 users,  load average: 1.58, 0.68, 0.30
Tasks: 201 total,   2 running, 198 sleeping,   0 stopped,   1 zombie
%Cpu(s):  5.3 us, 21.1 sy,  0.0 ni, 73.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  2047724 total,   220252 free,  1410216 used,   417256 buff/cache
KiB Swap:        0 total,        0 free,        0 used.   308616 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
  982 root      20   0  157752   4312   3552 R  5.6  0.2   0:00.03 top
    1 root      20   0  193668   6392   3576 S  0.0  0.3  86:40.63 systemd
    2 root      20   0       0      0      0 S  0.0  0.0   0:39.48 kthreadd
    4 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kworker/0:0H
    6 root       0 -20       0      0      0 S  0.0  0.0   0:00.04 mm_percpu_wq
    7 root      20   0       0      0      0 S  0.0  0.0   6:26.21 ksoftirqd/0
    8 root      20   0       0      0      0 S  0.0  0.0  29:22.01 rcu_sched
    9 root      20   0       0      0      0 S  0.0  0.0   0:00.00 rcu_bh
   10 root      20   0       0      0      0 R  0.0  0.0  29:54.98 rcuos/0
   11 root      20   0       0      0      0 S  0.0  0.0   0:00.00 rcuob/0
   12 root      rt   0       0      0      0 S  0.0  0.0   0:00.00 migration/0
   13 root      rt   0       0      0      0 S  0.0  0.0   2:49.41 watchdog/0
   14 root      20   0       0      0      0 S  0.0  0.0   0:00.00 cpuhp/0
   15 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kdevtmpfs
   16 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 netns
   17 root      20   0       0      0      0 S  0.0  0.0   5:33.92 khungtaskd
   18 root      20   0       0      0      0 S  0.0  0.0   0:00.15 oom_reaper
   19 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 writeback
   20 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kcompactd0
```



## headers

```
users: 当前用户在线 

zombie：僵死的进程数 

5.3 us：用户态进程占用CPU时间百分比，不包含renice值为负的任务占用的CPU的时间。 

21.1 sy：内核占用CPU时间百分比 

0.0 ni：renice值为负的任务的用户态进程的CPU时间百分比。nice是优先级的意思 

73.7 id：空闲CPU时间百分比 

0.0 wa：等待I/O的CPU时间百分比 

0.0 hi：CPU硬中断时间百分比 

0.0 si：CPU软中断时间百分比 
```



## content

PID：进程的ID 

USER：进程所有者 

PR（priority）：进程的优先级别，越小越优先被执行 

NI（nice）：值 

VIRT（virtual）：进程占用的虚拟内存 

RES：进程占用的物理内存 

SHR：进程使用的共享内存 

S：进程的状态。S表示休眠，R表示正在运行，Z表示僵死状态，N表示该进程优先值为负数 

%CPU：进程占用CPU的使用率 

%MEM：进程使用的物理内存和总内存的百分比 

TIME+：该进程启动后占用的总的CPU时间，即占用CPU使用时间的累加值。 

COMMAND：进程启动命令名称 





# 功能操作



## P 以CPU排序

默认是此项



## M 以内存排序



## N 以PID排序



## q 退出



