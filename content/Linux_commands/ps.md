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



