---
title: "subprocess"
date: 2018-10-05 02:38
---


[TOC]


# subprocess

subprocess 包用于执行外部命令和程序。如调用shell命令wget下载文件



Python中，subprocess包来fork一个子进程，并运行一个外部的程序

subprocess包种定义有数个创建子进程的函数，函数分别以不同的方式创建子进程。

使用subprocess创建子进程的时候，需注意

1. 在创建子进程之后，父进程是否暂停，并等待子进程运行
2. 函数返回什么
3. 当return code不为0时，父进程如何处理





## subprocess.call()

父进程等待子进程完成，返回退出信息(return code 相当于exit code)

```
In [18]: import subprocess

In [19]: subprocess.call(['ls', '-l'])
total 8232
-rw-r--r--  1 xhxu  254449427        0 Sep 13 12:19 __init__.py
```



### shell=True

shell=True 参数， 表示使用整个字符串，而不是一个表来运行子进程。

```
In [20]: subprocess.call('ls -l', shell=True)
total 8232
-rw-r--r--  1 xhxu  254449427        0 Sep 13 12:19 __init__.py
```





## subprocess.check_call()

父进程等待子进程完成，返回()

在检查退出信息，如果return code不为0，则举出错误subprocess.CalledProcessError, 该对象包含有return code属性，可以用try except来检查。





## subprocess.check_output()

父进程等待子进程完成，返回子进程向标准输出的输出结果

检查退出信息，如果return code不为0， 则举出错误subprocess.CalledProcessError, 该对象包含return code属性和output属性，output为标准输出的输出结果。





## subprocess.Popen()

Popen对象创建后，主程序不会自动等待子进程完成。必须调用对象的wait()方法，父进程才会等待（也就是阻塞block）

```
import subprocess
child = subprocess.Popen(['ping', '-c', '1', 'xurick.com'])
child.wait()
print('parent process')
>>>
PING xurick.com (192.30.252.153): 56 data bytes
64 bytes from 192.30.252.153: icmp_seq=0 ttl=51 time=240.392 ms

--- xurick.com ping statistics ---
1 packets transmitted, 1 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 240.392/240.392/240.392/0.000 ms
parent process
```



### subprocess.PIPE

subprocess.PIPE 实际上为文本流提供一个缓存区。child1的stdout将文本输出到缓存区，随后child2的stdin从该PIPE中将文本读取走。child2的输出文本也被存放在PIPE中，直到communicate() 方法从PIPE中读取PIPE中的文本

```
import subprocess

child1 = subprocess.Popen(['ls', '-l'], stdout=subprocess.PIPE)
child2 = subprocess.Popen(['wc'], stdin=child1.stdout, stdout=subprocess.PIPE)
out = child2.communicate()
print(out)
>>>
(b'       5      42     338\n', None)
```



## 子进程

child.poll() 检查子进程状态

child.kill() 终止子进程

child.send_signal() 向子进程发送信号

child.terminate()  终止子进程

子进程的PID存储在 child.pid 中



### 子进程的文本流控制

* child.stdin 子进程标准输入

* child.stdout 子进程标准输出

* child.stderr 子进程标准错误