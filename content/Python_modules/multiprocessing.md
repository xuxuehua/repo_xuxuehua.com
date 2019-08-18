---
title: "multiprocessing"
date: 2018-10-05 02:51
---


[TOC]

# multiprocessing

Python实现多线程/多进程，大家常常会用到标准库中的threading和multiprocessing模块。
但从Python3.2开始，标准库为我们提供了concurrent.futures模块，它提供了ThreadPoolExecutor和ProcessPoolExecutor两个类，实现了对threading和multiprocessing的进一步抽象，使得开发者只需编写少量代码即可让程序实现并行计算。



避免的subprocess包的局限性
1. 总是通过subprocess运行外部的程序，而不是运行一个Python脚本内部编写的函数
2. 进程之间只能通过管道方法进行文本交流

multiprocessing 与threading.Thread类似，利用multiprocessing.Process对象来创建一个进程。该进程可以运行在Python 程序内部编写的函数。

* multiprocessing不仅有start(), run(), join()的方法，也有Lock/Event/Semaphore/Condition类



使用这些multiprocessing时，需要注意
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



## 多线程

指IO密集型

web请求中，为IO密集型



异步是单线程的，更优雅的处理IO密集型



## 多进程

指CPU密集型





## Pipe 和 Queue 

* Pipe可以是单向的(half-duplex), 也可以是双向的(duplex)

* 通过multiprocessing.Pipe(duplex=False)来创建单向管道，默认是是双向的


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

* 进程池(Process Pool) 可以创建多个进程

* map() 方法 会将f()函数作用到表的每个元素上

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

除了map()方法外, Pool还有下列常用方法
1. apply_async(func, args)从进程池中取出一个进行执行func，args为func的参数。它将返回一个AsyncResult的对象，可以对该对象调用get()方法获得结果
2. close() 进程池不在创建新的进程
3. join() wait 进程池中的全部进程。必须对Pool先调用close()方法才能join





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