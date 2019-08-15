---
title: "coroutine"
date: 2019-08-15 08:51
---
[TOC]



# coroutine 协程

协程是实现并发编程的一种方式。

协程相对于多线程，其为单线程



```
In [24]: import asyncio 
    ...:  
    ...:  
    ...: async def crawl_page(url): 
    ...:     print('crawling {}'.format(url)) 
    ...:     sleep_time = int(url.split('_')[-1]) 
    ...:     await asyncio.sleep(sleep_time) 
    ...:     print('OK {}'.format(url)) 
    ...:  
    ...:  
    ...: async def main(urls): 
    ...:     for url in urls: 
    ...:         await crawl_page(url) 
    ...:  
    ...: %time asyncio.run(main(['url_1', 'url_2', 'url_3', 'url_4'])) 
    ...:  
    ...:                                                                                              
crawling url_1
OK url_1
crawling url_2
OK url_2
crawling url_3
OK url_3
crawling url_4
OK url_4
CPU times: user 2.16 ms, sys: 2.87 ms, total: 5.04 ms
Wall time: 10 s
```





## async

声明异步函数， 而调用异步函数，便可以得到一个协程对象coroutine object



## await

可以通过await方法调用协程对象，await 执行的效果，和 Python 正常执行是一样的，也就是说程序会阻塞在这里，进入被调用的协程函数，执行完毕返回后再继续，而这也是 await 的字面意思

await是同步调用，所以结果为10s



## asyncio

### create_task()

可以通过 asyncio.create_task() 来创建任务

```
In [26]: import asyncio 
    ...:  
    ...:  
    ...: async def crawl_page(url): 
    ...:     print('crawling {}'.format(url)) 
    ...:     sleep_time = int(url.split('_')[-1]) 
    ...:     await asyncio.sleep(sleep_time) 
    ...:     print('OK {}'.format(url)) 
    ...:  
    ...:  
    ...: async def main(urls): 
    ...:     tasks = [asyncio.create_task(crawl_page(url)) for url in urls] 
    ...:     for task in tasks: 
    ...:         await task 
    ...:  
    ...: %time asyncio.run(main(['url_1', 'url_2', 'url_3', 'url_4'])) 
    ...:  
    ...:                                                                                              
crawling url_1
crawling url_2
crawling url_3
crawling url_4
OK url_1
OK url_2
OK url_3
OK url_4
CPU times: user 2.52 ms, sys: 1.77 ms, total: 4.29 ms
Wall time: 4.01 s
```



#### add_done_callback()

协程实现callback函数, 即绑定特定回调函数获得返回值

```
In [29]: import asyncio 
    ...:  
    ...:  
    ...: async def crawl_page(url): 
    ...:     print('crawling {}'.format(url)) 
    ...:     sleep_time = int(url.split('_')[-1]) 
    ...:     await asyncio.sleep(sleep_time) 
    ...:     print('OK {}'.format(url)) 
    ...:  
    ...:  
    ...: async def main(urls): 
    ...:     tasks = [asyncio.create_task(crawl_page(url)) for url in urls] 
    ...:     for task in tasks: 
    ...:         task.add_done_callback(lambda future: print('result: ', future.result())) 
    ...:     await asyncio.gather(*tasks) 
    ...:  
    ...:  
    ...: %time asyncio.run(main(['url_1', 'url_2', 'url_3', 'url_4'])) 
    ...:  
    ...:                                                                                              
crawling url_1
crawling url_2
crawling url_3
crawling url_4
OK url_1
result:  None
OK url_2
result:  None
OK url_3
result:  None
OK url_4
result:  None
CPU times: user 2.12 ms, sys: 1.85 ms, total: 3.97 ms
Wall time: 4 s
```





### gather()

另一种task写法

```
In [27]: import asyncio 
    ...:  
    ...:  
    ...: async def crawl_page(url): 
    ...:     print('crawling {}'.format(url)) 
    ...:     sleep_time = int(url.split('_')[-1]) 
    ...:     await asyncio.sleep(sleep_time) 
    ...:     print('OK {}'.format(url)) 
    ...:  
    ...:  
    ...: async def main(urls): 
    ...:     tasks = [asyncio.create_task(crawl_page(url)) for url in urls] 
    ...:     await asyncio.gather(*tasks) 
    ...:  
    ...: %time asyncio.run(main(['url_1', 'url_2', 'url_3', 'url_4'])) 
    ...:  
    ...:                                                                                              
crawling url_1
crawling url_2
crawling url_3
crawling url_4
OK url_1
OK url_2
OK url_3
OK url_4
CPU times: user 2.56 ms, sys: 2.5 ms, total: 5.05 ms
Wall time: 4.01 s
```



### run()

 asyncio.run 来触发运行。asyncio.run 这个函数是 Python 3.7 之后才有的特性，可以让 Python 的协程接口变得非常简单，你不用去理会事件循环怎么定义和怎么使用的问题，一个非常好的编程规范是，asyncio.run(main()) 作为主程序的入口函数，在程序运行周期内，只调用一次 asyncio.run。





# 原理

```
import asyncio

async def worker_1():
    print('worker_1 start')
    await asyncio.sleep(1)
    print('worker_1 done')

async def worker_2():
    print('worker_2 start')
    await asyncio.sleep(2)
    print('worker_2 done')

async def main():
    print('before await')
    await worker_1()
    print('awaited worker_1')
    await worker_2()
    print('awaited worker_2')

%time asyncio.run(main())

>>>
before await
worker_1 start
worker_1 done
awaited worker_1
worker_2 start
worker_2 done
awaited worker_2
Wall time: 3 s
```



```
import asyncio

async def worker_1():
    print('worker_1 start')
    await asyncio.sleep(1)	# 2. 从当前任务切出，调度worker2  # 3. 事件调度器这时候开始暂停调度，等待1秒后，sleep完成，事件调度器控制器交给task1
    print('worker_1 done') # 4. 完成任务退出

async def worker_2():
    print('worker_2 start')
    await asyncio.sleep(2)	# 3. 从当前任务切出
    print('worker_2 done') # 6. 2秒后事件调度器将控制器交给task2， 输出结果

async def main():
    task1 = asyncio.create_task(worker_1()) 
    task2 = asyncio.create_task(worker_2())
    print('before await')
    await task1		# 1. 从main任务中切出，调度worker1
    print('awaited worker_1') # 5. 完成task1， 控制器交给主任务， 输出结果
    await task2 
    print('awaited worker_2') # 7. 输出结果，协程结束

%time asyncio.run(main())	

>>>

before await
worker_1 start
worker_2 start
worker_1 done
awaited worker_1
worker_2 done
awaited worker_2
Wall time: 2.01 s
```





## 错误处理

worker3执行过长会被cancel掉

```
import asyncio

async def worker_1():
    await asyncio.sleep(1)
    return 1

async def worker_2():
    await asyncio.sleep(2)
    return 2 / 0

async def worker_3():
    await asyncio.sleep(3)
    return 3

async def main():
    task_1 = asyncio.create_task(worker_1())
    task_2 = asyncio.create_task(worker_2())
    task_3 = asyncio.create_task(worker_3())

    await asyncio.sleep(2)
    task_3.cancel()

    res = await asyncio.gather(task_1, task_2, task_3, return_exceptions=True)
    print(res)

%time asyncio.run(main())

>>>

[1, ZeroDivisionError('division by zero'), CancelledError()]
Wall time: 2 s

```



## 生产者消费者模型

```
import asyncio
import random

async def consumer(queue, id):
    while True:
        val = await queue.get()
        print('{} get a val: {}'.format(id, val))
        await asyncio.sleep(1)

async def producer(queue, id):
    for i in range(5):
        val = random.randint(1, 10)
        await queue.put(val)
        print('{} put a val: {}'.format(id, val))
        await asyncio.sleep(1)

async def main():
    queue = asyncio.Queue()

    consumer_1 = asyncio.create_task(consumer(queue, 'consumer_1'))
    consumer_2 = asyncio.create_task(consumer(queue, 'consumer_2'))

    producer_1 = asyncio.create_task(producer(queue, 'producer_1'))
    producer_2 = asyncio.create_task(producer(queue, 'producer_2'))

    await asyncio.sleep(10)
    consumer_1.cancel()
    consumer_2.cancel()
    
    await asyncio.gather(consumer_1, consumer_2, producer_1, producer_2, return_exceptions=True)

%time asyncio.run(main())

>>>

producer_1 put a val: 5
producer_2 put a val: 3
consumer_1 get a val: 5
consumer_2 get a val: 3
producer_1 put a val: 1
producer_2 put a val: 3
consumer_2 get a val: 1
consumer_1 get a val: 3
producer_1 put a val: 6
producer_2 put a val: 10
consumer_1 get a val: 6
consumer_2 get a val: 10
producer_1 put a val: 4
producer_2 put a val: 5
consumer_2 get a val: 4
consumer_1 get a val: 5
producer_1 put a val: 2
producer_2 put a val: 8
consumer_1 get a val: 2
consumer_2 get a val: 8
Wall time: 10 s
```







