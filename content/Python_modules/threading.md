---
title: "threading 线程"
date: 2018-09-27 19:39
---


[TOC]

# 线程

线程是进程的一部分，比进程更灵活，小巧，切换起来更加节省CPU资源。

进程一般是用来分配资源，线程可以访问进程的资源

线程是利用CPU执行代码，不可以分配拥有资源。但是可以访问资源，即利用CPU执行代码。

多线程可以更加充分的利用CPU的性能优势，相互切换是比进程小很多的

多线程之间会共享同一块地址空间和所有可用数据的能力，这是进程所不具备的，

每个线程中还包括程序计数器，寄存器，堆栈，状态信息



## 堆栈

每个线程都会有自己的堆栈



## POSIX 线程 / pthreads

**POSIX线程**（通常称为**pthreads**）是一种独立于语言而存在的执行模型，以及并行执行模型。它允许程序控制时间上重叠的多个不同的工作流程。每个工作流程都称为一个线程，可以通过调用POSIX Threads API来实现对这些流程的创建和控制。

可以把它理解为线程的标准。POSIX Threads 的实现在许多类似且符合POSIX的操作系统上（Unix）可用

所有的 Pthreads 都有特定的属性，每一个都含有标识符、一组寄存器（包括程序计数器）和一组存储在结构中的属性。这个属性包括堆栈大小、调度参数以及其他线程需要的项目。



| 线程调用             | 描述                           |
| :------------------- | :----------------------------- |
| pthread_create       | 创建一个新线程                 |
| pthread_exit         | 结束调用的线程                 |
| pthread_join         | 等待一个特定的线程退出         |
| pthread_yield        | 释放 CPU 来运行另外一个线程    |
| pthread_attr_init    | 创建并初始化一个线程的属性结构 |
| pthread_attr_destory | 删除一个线程的属性结构         |









## 单线程，多线程，有限状态机

如果目前只有一个非阻塞版本的 read 系统调用可以使用，那么当请求到达服务器时，这个唯一的 read 调用的线程会进行检查，如果能够从高速缓存中得到响应，那么直接返回，如果不能，则启动一个非阻塞的磁盘操作

服务器在表中记录当前请求的状态，然后进入并获取下一个事件，紧接着下一个事件可能就是一个新工作的请求或是磁盘对先前操作的回答。如果是新工作的请求，那么就开始处理请求。如果是磁盘的响应，就从表中取出对应的状态信息进行处理。对于非阻塞式磁盘 I/O 而言，这种响应一般都是信号中断响应。

每次服务器从某个请求工作的状态切换到另一个状态时，都必须显示的保存或者重新装入相应的计算状态。这里，每个计算都有一个被保存的状态，存在一个会发生且使得相关状态发生改变的事件集合，我们把这类设计称为`有限状态机(finite-state machine)`，有限状态机被广泛的应用在计算机科学中。



单线程服务器保留了阻塞系统的简易性，但是却放弃了性能。

多线程使得顺序进程的思想得以保留下来，并且实现了并行性，但是顺序进程会阻塞系统调用；

有限状态机的处理方法运用了非阻塞调用和中断，通过并行实现了高性能，但是给编程增加了困难。







# threading

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





## join() 阻塞

join() 方法，调用该方法的线程将等待直到该Thread对象完成，再恢复运行，并根据情况阻塞某些进程。



## setDaemon() 常驻

作为常驻线程





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





# 线程通信



## 共享变量 （不推荐）

有线程安全问题，所以需要加锁，如dict



## queue （推荐）

queue的方式进行线程间同步， queue是线程安全的（内部通过deque实现）



# 线程同步



## 敏感区 Critical Section

我们将可能因并行操作而导致数据不一致的区域称为敏感区，也就是我们需要用锁锁住的区域。

更一般性的描述为：敏感区就是最多允许一个任务进入执行的代码区域。



## 饿死 Starvation

有一个或多个任务，永远无法进入敏感区。这一般是因为优先级设计不合理， 导致多个任务在试图获取资源时，某些任务永远排在后面。



## 优先级反转 Priority Inversion

不良的锁设计导致任务未按照应用的优先级顺序执行。

假如有 A、B、C 三个任务，优先级依次递增，正常情况下运行的顺序应该是 C -> B -> A。

但是我们考虑下述场景：

1. A 先运行，锁住了 C 所需的资源；
2. C 启动并抢占 A，但是获取锁失败，陷入睡眠；
3. A 恢复；
4. B 启动并抢占 A，B 运行结束；
5. A 恢复，运行结束，释放资源；
6. C 恢复，运行结束。

在这个例子中，我们发现任务的运行顺序变成了 B -> A -> C，拥有最高优先级的 C 竟然最后运行，这种情况就称之为 `优先级反转`。

对于优先级反转，有以下几种解决办法：

- 敏感区：给敏感区设定一个较高的优先级，所有进入敏感区的任务都将获得该优先级；
- 优先级继承：当一个高优先级任务的资源被一个低优先级任务锁住时，该低优先级的任务继承高优先级任务的优先级别；
- 中断禁止：进入敏感区的任务禁止被中断。



## Lock()

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





锁会影响性能，同时锁会产生死锁

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
            gLock.acquire()	# 这里多了一个acquire
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



## RLock() 可重入锁 (常用)

在同一个线程里面，可以多次调用acquire，但是要注意release的次数和acquire的次数要相等



## Condition 条件锁（常用）

条件变量，用于复杂的线程间同步

Wait 方法需要notify才可以唤醒

```
import threading


class A(threading.Thread):
    def __init__(self, cond):
        super().__init__(name='myA')
        self.cond = cond

    def run(self):
        with self.cond:
            self.cond.wait()
            print("Hi :%s" % self.name)
            self.cond.notify()

            self.cond.wait()
            print('This is A')
            self.cond.notify()


class B(threading.Thread):
    def __init__(self, cond):
        super().__init__(name='myB')
        self.cond = cond

    def run(self):
        with self.cond:
            print("Hi %s" % self.name)
            self.cond.notify()
            self.cond.wait()

            self.cond.notify()
            


if __name__ == '__main__':
    cond = threading.Condition()
    mya = A(cond)
    myb = B(cond)
    mya.start()
    myb.start()

>>>
Hi myB
Hi :myA
This is A
```



## Semaphore() 记录锁数量

semaphore，也就是计数锁(semaphore传统意义上是一种进程间同步工具)。

用于线程同步，是指在互斥的基础上（大多数情况），通过其它机制实现访问者对资源的有序访问。 在大多数情况下，同步已经实现了互斥，特别是所有写入资源的情况必定是互斥的。 少数情况是指可以允许多个访问者同时访问资源。

创建对象的时候，可以传递一个整数作为计数上限 (sema = threading.Semaphore(5))。它与Lock类似，也有Lock的两个方法。



```
import threading
import time


class HtmlSpider(threading.Thread):
    def __init__(self, url, sem):
        super().__init__()
        self.url = url
        self.sem = sem

    def run(self):
        time.sleep(2)
        print("got html text success")
        self.sem.release()


class UrlProducer(threading.Thread):
    def __init__(self, sem):
        super().__init__()
        self.sem = sem

    def run(self):
        for i in range(20):
            self.sem.acquire()
            html_thread = HtmlSpider("https://baidu.com/{}".format(i), self.sem)
            html_thread.start()


if __name__ == '__main__':
    sem = threading.Semaphore(3)
    url_producer = UrlProducer(sem)
    url_producer.start()         
```



# example

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

使用mutex 就是Python中的lock类对象，来实现线程同步

Python 使用threading.Thread对象来代表线程,用threading.Lock对象来代表一个互斥锁(mutex)

booth 中使用的两个doChore()函数，可以在未来改进程序，第一个doChore()依然在Lock内部，所以可以安全地使用共享资源 (critical operations, 比如打印剩余票数)。第二个doChore()时，Lock已经被释放，所以不能再去使用共享资源。这时候可以做一些不使用共享资源的操作 (non-critical operation, 比如找钱、喝水)。我故意让doChore()等待了0.5秒，以代表这些额外的操作可能花费的时间。你可以定义的函数来代替doChore()。



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