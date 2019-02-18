---
title: "threading"
date: 2018-09-27 19:39
---


[TOC]


# threading

threading是python中专门用作多线程，常用类是Thread

服务器利用多线程的方式来处理大量的请求，以提高对网络端口的读写效率。





## threading.enumerate()

查看线程数



## threading.current_thread()

当前线程名称



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