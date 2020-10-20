---
title: "multiprocessing 多进程"
date: 2018-10-05 02:51
---


[TOC]



# 进程

进程是竞争计算机资源的基本单位,进程是对正在运行中的程序的一个抽象。

一个进程就是一个正在执行的程序的实例，进程也包括程序计数器、寄存器和变量的当前值。从概念上来说，每个进程都有各自的虚拟 CPU，但是实际情况是 CPU 会在各个进程之间进行来回切换。

即使是多核的CPU，进程也是需要竞争的

调度算法可以将一个进程挂起，切换到另外一个进程中切换

每一个进程包括对应的地址空间，全局变量，打开的文件，子进程，即将发生的定时器，信号与信号处理程序，账户信息





## 进程的创建

系统初始化（init）

正在运行的程序执行了创建进程的系统调用（比如 fork）

用户请求创建一个新进程

初始化一个批处理工作



### fork

在 UNIX 中，仅有一个系统调用来创建一个新的进程，这个系统调用就是 `fork`。这个调用会创建一个与调用进程相关的副本。

在 fork 后，一个父进程和子进程会有相同的内存映像，相同的环境字符串和相同的打开文件。

在父进程中fork一个子进程，在子进程中调用exec函数启动新的程序。exec函数一共有六个，其中execve为内核级系统调用，其他（execl，execle，execlp，execv，execvp）都是调用execve的库函数。

例如，当一个用户在 shell 中输出 sort 命令时，shell 会 fork 一个子进程然后子进程去执行 sort 命令。这两步过程的原因是允许子进程在 fork 之后但在 execve 之前操作其文件描述符，以完成标准输入，标准输出和标准错误的重定向。



## 进程的终止

进程在创建之后，它就开始运行并做完成任务。然而，没有什么事儿是永不停歇的，包括进程也一样。进程早晚会发生终止，但是通常是由于以下情况触发的

正常退出(自愿的)

错误退出(自愿的)

严重错误(非自愿的)

被其他进程杀死(非自愿的)



### exit

多数进程是由于完成了工作而终止。当编译器完成了所给定程序的编译之后，编译器会执行一个系统调用告诉操作系统它完成了工作。这个调用在 UNIX 中是 `exit`



## UNIX 进程体系

在 UNIX 中，进程和它的所有子进程以及子进程的子进程共同组成一个进程组。



### init

init 进程开始运行时，它会读取一个文件，文件会告诉它有多少个终端。然后为每个终端创建一个新进程。这些进程等待用户登录。如果登录成功，该登录进程就执行一个 shell 来等待接收用户输入指令，这些命令可能会启动更多的进程，以此类推。

因此，整个操作系统中所有的进程都隶属于一个单个以 init 为根的进程树。





# multiprocessing

Python实现多线程/多进程，大家常常会用到标准库中的threading和multiprocessing模块。
但从Python3.2开始，标准库为我们提供了concurrent.futures模块，它提供了ThreadPoolExecutor和ProcessPoolExecutor两个类，实现了对threading和multiprocessing的进一步抽象，使得开发者只需编写少量代码即可让程序实现并行计算。



避免的subprocess包的局限性
1. 总是通过subprocess运行外部的程序，而不是运行一个Python脚本内部编写的函数
2. 进程之间只能通过管道方法进行文本交流

multiprocessing 与threading.Thread类似，利用multiprocessing.Process对象来创建一个进程。该进程可以运行在Python 程序内部编写的函数。

* multiprocessing不仅有start(), run(), join()的方法，也有Lock/Event/Semaphore/Condition类



## 注意点

1. 在UNIX平台上，当某个进程终结之后，该进程需要被其父进程调用wait，否则进程成为僵尸进程(Zombie)。所以，有必要对每个Process对象调用join()方法 (实际上等同于wait)。对于多线程来说，由于只有一个进程，所以不存在此必要性。
2. multiprocessing提供了threading包中没有的IPC(比如Pipe和Queue)，效率上更高。应优先考虑Pipe和Queue，避免使用 Lock/Event/Semaphore/Condition等同步方式 (因为它们占据的不是用户进程的资源)。
3. 多进程应该避免共享资源。在多线程中，我们可以比较容易地共享资源，比如使用全局变量或者传递参数。在多进程情况下，由于每个进程有自己独立的内存空间，以上方法并不合适。此时我们可以通过共享内存和Manager的方法来共享资源。但这样做提高了程序的复杂度，并因为同步的需要而降低了程序的效率。





Thread对象和Process对象在使用上的相似性和结果上是不相同的, Thread的PID都与主程序相同，但是每个Process都又一个不同的PID

```
import os
import threading
import multiprocessing

def worker(sign, lock):
    lock.acquire()
    print(sign, os.getpid())
    lock.release()


print('Main: ', os.getpid())

# Multi-thread
record = []
lock = threading.Lock()

for i in range(5):
    thread = threading.Thread(target=worker, args=('thread', lock))
    thread.start()
    record.append(thread)

for thread in record:
    thread.join()


# Multi-process
record = []
lock = multiprocessing.Lock()
for i in range(5):
    process = multiprocessing.Process(target=worker, args=('process', lock))
    process.start()
    record.append(process)

for process in record:
    process.join()
>>>
Main:  6337
thread 6337
thread 6337
thread 6337
thread 6337
thread 6337
process 6338
process 6339
process 6340
process 6341
process 6342
```





## Process 进程

```
import multiprocessing
import time


def get_html(n):
    time.sleep(n)
    print("sub_process")
    return n


if __name__ == '__main__':
    process = multiprocessing.Process(target=get_html, args=(2,))
    print(process.pid)
    process.start()
    print(process.pid)
    process.join()
    print("main_process")
    
>>>
None
88763
sub_process
main_process
```



## Pipe 和 Queue 

Pipe可以是单向的(half-duplex), 也可以是双向的(duplex)

通过multiprocessing.Pipe(duplex=False)来创建单向管道，默认是是双向的


```
import multiprocessing as mul

def proc1(pipe):
    pipe.send('hello')
    print('proc1 rec: ', pipe.recv())

def proc2(pipe):
    print('proc2 rec: ', pipe.recv())
    pipe.send('Hello, too')

pipe = mul.Pipe()

p1 = mul.Process(target=proc1, args=(pipe[0],))

p2 = mul.Process(target=proc2, args=(pipe[1],))

p1.start()
p2.start()
p1.join()
p2.join()
>>>
proc2 rec:  hello
proc1 rec:  Hello, too
```



Queue允许多个进程放入，多个进程从队列取出对象。

```
import os
import multiprocessing
import time

def inputQ(queue):
    info = str(os.getpid()) + '(put):' + str(time.time())
    queue.put(info)

def outputQ(queue, lock):
    info = queue.get()
    lock.acquire()
    print(str(os.getpid()) + '(get):' + info)
    lock.release()

record1 = []
record2 = []
lock = multiprocessing.Lock()
queue = multiprocessing.Queue(3)

for i in range(10):
    process = multiprocessing.Process(target=inputQ, args=(queue,))
    process.start()
    record1.append(process)

for i in range(10):
    process = multiprocessing.Process(target=outputQ, args=(queue,lock))
    process.start()
    record2.append(process)

for p in record1:
    p.join()

queue.close()

for p in record2:
    p.join()
>>>
6486(get):6477(put):1482138256.261425
6487(get):6476(put):1482138256.260579
6488(get):6478(put):1482138256.264274
6489(get):6479(put):1482138256.267834
6490(get):6480(put):1482138256.269334
6491(get):6481(put):1482138256.271232
6492(get):6482(put):1482138256.271742
6493(get):6483(put):1482138256.272549
6494(get):6484(put):1482138256.273723
6495(get):6485(put):1482138256.273805
```





## 进程池

```
import time
import multiprocessing


def get_html(n):
    time.sleep(n)
    print("sub_process success")
    return n


if __name__ == '__main__':
    pool = multiprocessing.Pool(multiprocessing.cpu_count())
    result = pool.apply_async(get_html, args=(3, ))
    pool.close()
    
    # Waiting for all tasks are return
    pool.join()
    print(result.get())
    
>>>
sub_process success
3
```

> join() wait 进程池中的全部进程。必须对Pool先调用close()方法才能join



### map

进程池(Process Pool) 可以创建多个进程

map() 方法 会将f()函数作用到表的每个元素上

```
import multiprocessing as mul

def f(x):
    return x**2

pool = mul.Pool(5)
rel = pool.map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
print(rel)

>>>
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```



### imap

```
import time
import multiprocessing


def get_html(n):
    time.sleep(n)
    print("sub_process success")
    return n


if __name__ == '__main__':
    pool = multiprocessing.Pool(multiprocessing.cpu_count())

    for result in pool.imap(get_html, [1, 5, 3]):
        print("%s sleep success" % result)

>>>
sub_process success
1 sleep success
sub_process success
sub_process success
5 sleep success
3 sleep success
```



### imap_unordered

```
import time
import multiprocessing


def get_html(n):
    time.sleep(n)
    print("sub_process success")
    return n


if __name__ == '__main__':
    pool = multiprocessing.Pool(multiprocessing.cpu_count())

    for result in pool.imap_unordered(get_html, [1, 5, 3]):
        print("%s sleep success" % result)

>>>
sub_process success
1 sleep success
sub_process success
3 sleep success
sub_process success
5 sleep success
```









## 共享内存

* 主进程的内存空间中创建共享的内存，也就是Value和Array两个对象

```
import multiprocessing

def f(n, a):
    n.value = 3.14
    a[0] = 5

num = multiprocessing.Value('d', 0.0)
arr = multiprocessing.Array('i', range(10))

p = multiprocessing.Process(target=f, args=(num, arr))
p.start()
p.join()

print(num.value)
print(arr[:])
>>>
3.14
[5, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```



## Manager

* Manager 对象类似于服务器与客户端之间的通信。当用一个进程作为服务器，建立Manager来真正存放资源

* manager 利用list()的方法提供了表的共享方式


```
import multiprocessing

def f(x, arr, l):
    x.value = 3.14
    arr[0] = 5
    l.append('Hello')

server = multiprocessing.Manager()
x = server.Value('id', 0.0)
arr = server.Array('i', range(10))
l = server.list()

proc = multiprocessing.Process(target=f, args=(x, arr, l))
proc.start()
proc.join()

print(x.value)
print(arr)
print(l)
>>>
3.14
array('i', [5, 1, 2, 3, 4, 5, 6, 7, 8, 9])
['Hello']
```







# 进程间通信

共享变量不能适用于多进程通信，可以适用于多进程



## Queue

```
import time
from multiprocessing import Process, Queue


def producer(queue):
    queue.put("a")
    time.sleep(2)


def consumer(queue):
    time.sleep(2)
    data = queue.get()
    print(data)


if __name__ == '__main__':
    queue = Queue(10)
    my_producer = Process(target=producer, args=(queue, ))
    my_consumer = Process(target=consumer, args=(queue, ))
    my_producer.start()
    my_consumer.start()
    my_producer.join()
    my_consumer.join()
    
>>>
a
```



## Manager



### Queue

multiprocessing 中的queue不能用于pool进程池

pool中的进程间通信需要使用manager中的queue

```
import time
from multiprocessing import Pool, Manager


def producer(queue):
    queue.put("a")
    time.sleep(2)


def consumer(queue):
    time.sleep(2)
    data = queue.get()
    print(data)


if __name__ == '__main__':
    queue = Manager().Queue(10)
    pool = Pool(2)
    pool.apply_async(producer, args=(queue, ))
    pool.apply_async(consumer, args=(queue, ))
    pool.close()
    pool.join()

>>>
a
```



### dict

需要注意数据同步

```
from multiprocessing import Process
from multiprocessing import Manager


def add_data(p_dict, key, value):
    p_dict[key] = value


if __name__ == '__main__':
    progress_dict = Manager().dict()

    first_progress = Process(target=add_data, args=(progress_dict, "Rick", "rick"))
    second_progress = Process(target=add_data, args=(progress_dict, "Xu", "xu"))

    first_progress.start()
    second_progress.start()

    first_progress.join()
    second_progress.join()

    print(progress_dict)

>>>
{'Rick': 'rick', 'Xu': 'xu'}
```





## Pipe （只能是两个进程）

简化版的Queue, 性能高于Queue



