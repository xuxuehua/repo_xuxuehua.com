---
title: "threading 线程"
date: 2018-09-27 19:39
---


[TOC]

# threading

线程是进程的一部分，比进程更灵活，小巧，切换起来更加节省CPU资源。

进程一般是用来分配资源，线程可以访问进程的资源

线程是利用CPU执行代码，不可以分配拥有资源。但是可以访问资源，即利用CPU执行代码。

多线程可以更加充分的利用CPU的性能优势，相互切换是比进程小很多的





threading是python中专门用作多线程，常用类是Thread

服务器利用多线程的方式来处理大量的请求，以提高对网络端口的读写效率。



Python实现多线程/多进程，大家常常会用到标准库中的threading和multiprocessing模块。
但从Python3.2开始，标准库为我们提供了concurrent.futures模块，它提供了ThreadPoolExecutor和ProcessPoolExecutor两个类，实现了对threading和multiprocessing的进一步抽象，使得开发者只需编写少量代码即可让程序实现并行计算。



## threading.enumerate()

查看线程数



## threading.current_thread()

当前线程名称

单线程需要等所有的代码执行完成之后，才会执行下一步代码

```
import threading
import time


def worker():
    print("I am a thread")
    t = threading.current_thread()
    time.sleep(10)
    print(t.getName())


worker()

t = threading.current_thread()
print(t.getName())

>>>
I am a thread
MainThread
MainThread
```

> 这里会等待10秒再执行剩下的



看起来会像是主线程和worker线程同时执行

```
import threading
import time


def worker():
    print("I am a thread")
    t = threading.current_thread()
    time.sleep(10)
    print(t.getName())


# worker()
new_t = threading.Thread(target=worker, name='my_thread')
new_t.start()

t = threading.current_thread()
print(t.getName())

>>>
I am a thread
MainThread
my_thread
```

> 主线程执行不依赖于worker线程的执行结果



## threading.Lock()

对修改全局变量的地方，需要加锁

```
gLock = threading.Lock()
gLock.acquire()
gLock.release()
```

```
import threading
import random
import time

gMoney = 1000
gLock = threading.Lock()
gTotalTimes = 10
gTimes = 0


class Producer(threading.Thread):
    def run(self):
        global gMoney
        global gTimes
        while True:
            money = random.randint(100, 1000)
            gLock.acquire()
            if gTimes >= gTotalTimes:
                gLock.release()
                break
            gMoney += money
            print('%s produced %d, remaining %d' % (threading.current_thread(), money, gMoney))
            gTimes += 1
            gLock.release()
            time.sleep(0.5)


class Consumer(threading.Thread):
    def run(self):
        global gMoney
        while True:
            money = random.randint(100, 1000)
            gLock.acquire()
            if gMoney >= money:
                gMoney -= money
                print('%s costs %d, remaining %d' % (threading.current_thread(), money, gMoney))
            else:
                if gTimes >= gTotalTimes:
                    gLock.release()
                    break
                print('%s consumer will cost %d, but remaining %d is not enough.' % (threading.current_thread(), money, gMoney))
            gLock.release()
            time.sleep(0.5)


def main():
    for x in range(5):
        t = Producer(name='Producer threading %d' % x)
        t.start()

    for x in range(3):
        t = Consumer(name='Consumer threading %d' % x)
        t.start()


if __name__ == '__main__':
    main()
    
>>>
<Producer(Producer threading 0, started 123145326419968)> produced 136, remaining 1136
<Producer(Producer threading 1, started 123145331675136)> produced 967, remaining 2103
<Producer(Producer threading 2, started 123145336930304)> produced 456, remaining 2559
<Producer(Producer threading 3, started 123145342185472)> produced 571, remaining 3130
<Producer(Producer threading 4, started 123145347440640)> produced 432, remaining 3562
<Consumer(Consumer threading 0, started 123145352695808)> costs 190, remaining 3372
<Consumer(Consumer threading 1, started 123145357950976)> costs 757, remaining 2615
<Consumer(Consumer threading 2, started 123145363206144)> costs 869, remaining 1746

<Producer(Producer threading 0, started 123145326419968)> produced 601, remaining 2347
<Producer(Producer threading 4, started 123145347440640)> produced 745, remaining 3092
<Producer(Producer threading 3, started 123145342185472)> produced 680, remaining 3772
<Producer(Producer threading 1, started 123145331675136)> produced 641, remaining 4413
<Producer(Producer threading 2, started 123145336930304)> produced 118, remaining 4531
<Consumer(Consumer threading 1, started 123145357950976)> costs 866, remaining 3665
<Consumer(Consumer threading 0, started 123145352695808)> costs 647, remaining 3018
<Consumer(Consumer threading 2, started 123145363206144)> costs 511, remaining 2507
<Consumer(Consumer threading 1, started 123145357950976)> costs 345, remaining 2162
<Consumer(Consumer threading 0, started 123145352695808)> costs 338, remaining 1824
<Consumer(Consumer threading 2, started 123145363206144)> costs 857, remaining 967
<Consumer(Consumer threading 1, started 123145357950976)> costs 451, remaining 516
<Consumer(Consumer threading 0, started 123145352695808)> costs 408, remaining 108
```



## join() 方法

join() 方法，调用该方法的线程将等待直到该Thread对象完成，再恢复运行，并根据情况阻塞某些进程。



## threading.Semaphore()

semaphore，也就是计数锁(semaphore传统意义上是一种进程间同步工具)。创建对象的时候，可以传递一个整数作为计数上限 (sema = threading.Semaphore(5))。它与Lock类似，也有Lock的两个方法。



## threading.Event()

与threading.Condition相类似，相当于没有潜在的Lock保护的condition variable。对象有True和False两个状态。可以多个线程使用wait()等待，直到某个线程调用该对象的set()方法，将对象设置为True。线程可以调用对象的clear()方法来重置对象为False状态。





## threading.condition()

Lock 上锁是很消耗CPU的行为，condition可以在没有数据的时候，处于一个阻塞的状态。一旦有合适的数据，可以使用notify相关的函数来通知其他处于等待状态的线程。

condition variable，建立该对象时，会包含一个Lock对象 (因为condition variable总是和mutex一起使用)。

### acquire 

上锁



### release 

解锁



### wait

当前线程处于等待状态，并且会释放锁，可以被其他线程使用notify和notify_all函数唤醒

被唤醒后会继续等待上锁，上锁后继续执行下面的代码



### notify

通知某个正在等待的线程，默认是第一个等待的线程



### notify_all

通知所有正在等待的线程，并且不会释放锁。需要在release之前调用







## example

```
import threading
import time


class Listening(threading.Thread):
    def run(self):
        for i in range(3):
            print('Listening %s' % i)
            time.sleep(1)


class Writing(threading.Thread):
    def run(self):
        for i in range(3):
            print('Writing %s' % i)
            time.sleep(1)


def main():
    t1 = Listening()
    t2 = Writing()
    
    t1.start()
    t2.start()

if __name__ == '__main__':
    main()
```



## 同步

多线程售票以及同步

* 使用mutex 就是Python中的lock类对象，来实现线程同步

* Python 使用threading.Thread对象来代表线程,用threading.Lock对象来代表一个互斥锁(mutex)

* booth 中使用的两个doChore()函数，可以在未来改进程序，第一个doChore()依然在Lock内部，所以可以安全地使用共享资源 (critical operations, 比如打印剩余票数)。第二个doChore()时，Lock已经被释放，所以不能再去使用共享资源。这时候可以做一些不使用共享资源的操作 (non-critical operation, 比如找钱、喝水)。我故意让doChore()等待了0.5秒，以代表这些额外的操作可能花费的时间。你可以定义的函数来代替doChore()。



```
import threading
import time
import os

def doChore():
    time.sleep(0.5)

def booth(tid):
    global i
    global lock
    while True:
        lock.acquire()
        if i != 0:
            i = i - 1
            print(tid, ':now left:', i)
            doChore()
        else:
            print('Thread_id', tid, "No more tickets")
            os._exit(0)
        lock.release()
        doChore()

i = 10
lock = threading.Lock()

for k in range(10):
    new_thread = threading.Thread(target=booth, args=(k,))

    new_thread.start()
>>>
0 :now left: 9
1 :now left: 8
2 :now left: 7
3 :now left: 6
4 :now left: 5
5 :now left: 4
6 :now left: 3
7 :now left: 2
8 :now left: 1
9 :now left: 0
Thread_id 0 No more tickets
```



### OOP创建线程

* 通过修改thread类的run() 方法来定义线程要执行的命令
* 定义了一个类BoothThread, 这个类继承自thread.Threading类。然后我们把booth()所进行的操作统统放入到BoothThread类的run()方法中。
* 避免了global声明用法

```
import threading
import time
import os

def doChore():
    time.sleep(0.5)

class BoothThread(threading.Thread):
    def __init__(self, tid, monitor):
        self.tid = tid
        self.monitor = monitor
        threading.Thread.__init__(self)

    def run(self):
        while True:
            monitor['lock'].acquire()

            if monitor['tick'] != 0:
                monitor['tick'] = monitor['tick'] - 1

                print(self.tid, ':now left:', monitor['tick'])

                doChore()

            else:
                print('Thread_id', self.tid, 'No more tickets')
                os._exit(0)

            monitor['lock'].release()
            doChore()

monitor = {'tick':10, 'lock': threading.Lock()}

for k in range(10):
    new_thread = BoothThread(k, monitor)
    new_thread.start()
>>>
0 :now left: 9
1 :now left: 8
2 :now left: 7
3 :now left: 6
4 :now left: 5
5 :now left: 4
6 :now left: 3
7 :now left: 2
8 :now left: 1
9 :now left: 0
Thread_id 0 No more tickets
```