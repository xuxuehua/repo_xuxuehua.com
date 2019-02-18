---
title: "queue"
date: 2018-09-29 17:21
---


[TOC]

# queue

Python内置的线程安全，提供了同步的，线程安全的队列类，包括FIFO，LIFO



## 队列类型

FIFO队列先进先出。 queue.Queue(maxsize)

LIFO类似于堆，即先进后出。 queue.LifoQueue(maxsize)

优先级队列级别越低越先出来。 queue.PriorityQueue(maxsize)



## queue.qsize() 

返回队列的大小

```
from queue import Queue

q = Queue(4)
print(q.qsize())

>>>
0
```



## queue.empty() 

如果队列为空，返回True,反之False

```
from queue import Queue

q = Queue(4)
print(q.empty())

>>>
True
```



## queue.full() 

如果队列满了，返回True,反之False

queue.full 与 maxsize 大小对应

```
from queue import Queue

q = Queue(4)
print(q.full())
for i in range(4):
    q.put('x')

print(q.full())

>>>
False
True
```



## queue.get()

获取队列，立即取出一个元素， timeout超时时间

```
from queue import Queue

q = Queue(4)
q.put('x')
print(q.get())

>>>
x
```



## queue.get_nowait()

等价于get(False) 

成功返回一个元素，或者抛出empty 异常



## queue.put() 

```
Queue.put(item, block=True, timeout=None)
```

> block=True, timeout=None 一直阻塞直到有空位放出
>
> block=True, timeout=5	阻塞5s九抛出Full 异常
>
> block=False, timeout 失效，立即返回



## queue.put_nowait()

等价于put(item, block=False)



写入队列，立即放入一个元素， timeout超时时间

```
from queue import Queue

q = Queue(4)
print(q.full())
for i in range(4):
    q.put('x')

print(q.full())

>>>
False
True
```







## example

```
from queue import Queue
import threading
import time


def set_value(q):
    index = 0
    while True:
        q.put(index)
        index += 1
        time.sleep(3)


def get_value(q):
    while True:
        print(q.get())


def main():
    q = Queue(4)
    t1 = threading.Thread(target=set_value, args=[q])
    t2 = threading.Thread(target=get_value, args=[q])
    t1.start()
    t2.start()

if __name__ == '__main__':
    main()
```