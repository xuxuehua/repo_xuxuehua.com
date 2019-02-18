---
title: "signal"
date: 2018-10-05 02:44
---


[TOC]


# signal

signal 包主要针对Unix平台



## 定义信号名

```
import signal
print(signal.SIGALRM)
print(signal.SIGCONT)
>>> 
14
19
```





## 预设信号处理函数

* signal包的核心是使用signal.signal() 函数来预设(register)信号处理函数

```
singnal.signal(signalnum, handler)
```

* signalnum为某信号，handler为该信号的处理函数

* 当handler为signal.SIG_IGN时，信号被无视(ignore). 当handler为signal.SIG_DFL, 进程采取默认操作

```
import signal

def myHandler(signum, frame):
    print('I received: ', signum)

signal.signal(signal.SIGTSTP, myHandler)
signal.pause()
print('End of Signal Demo')
>>>
CTRL+Z会可以发生SIGTSTP信号
```





## 定时发送SIGALARM 信号

* signal.alarm() 用于一段时间之后，向进程自身发送SIGALRM信号

```
import signal

def myHandler(signum, frame):
    print("Now, it's the time")
    exit()

signal.signal(signal.SIGALRM, myHandler)
signal.alarm(1)
while True:
    print('not yet')
>>>
not yet
not yet
not yet
Now, it's the time
```



## 发送信号

* os包中有类似kill命令函数

```
os.kill(pid, sid)
os.killpg(pgid, sid)
```