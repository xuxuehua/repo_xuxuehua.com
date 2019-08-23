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



# asyncio

由于多线程的局限性， （多线程运行中容易被打断，即出现race condition情况， 再者，线程切换本身存在一定的损耗， 线程数不能无限增加，若I/O操作非常频繁，多线程很可能满足不了高效率，高质量需求）

因而asyncio产生来解决这些问题



## 上下文切换

操作系统及多线程/多进程中称为“上下文切换” (context switch)。其中“上下文”记录了某个线程执行的状态，包括线程里用到的各个变量，线程的调用栈等。而“切换”指的就是保存某个线程当前的运行状态，之后再从之前的状态中恢复。只不过线程相关的工作是由操作系统完成，而协程则是由应用程序自己来完成。

与线程不同的时，协程完成的功能通常较小，所以会有需求将不同的协程串起来，我们暂时称它为协程链 (coroutine chain)。



## asyncio 原理

Asyncio也是单线程的，只有一个主线程，可以进行多个不同的任务(就是future对象)， 不同的任务被event loop对象控制



### 局限性

常用的requests库不兼容asyncio ，只能使用aiohttp库

使用asyncio在任务调度有更多主权，但是逻辑书写要注意，很容易出错



## event loop

若任务有两个状态，

预备状态：任务当前空闲，随时准备运行。

等待状态：任务已经运行，但正在等待外部的操作完成，如I/O操作

event loop会维护这两个任务列表，并且选取预备状态的一个任务，使其运行，一直到把这个任务控制权交还给event loop 为止。

当任务把控制权交还给event loop， event loop会根据其是否完成，放置在不同的状态列表中，然后遍历等待状态列表中的任务，查看其是否完成。

* 完成就放到预备状态
* 未完成就继续放在等待状态

这样，所有任务被重新放置在合适的列表后，开始新的循环，直到所有任务完成。

根据event loop的特点， 任务运行时不会被外部的一些因素打断，因此Asyncio内的操作不会出现race condition情况，也就不会出现线程安全的问题



> async 和 await 







## create_task()

可以通过 asyncio.create_task() 来创建任务

asyncio.create_task(coro)表示对输入的协程 coro创建一个任务，安排其执行，并返回次任务对象

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



### add_done_callback()

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





## gather()

另一种task写法， 针对await的一系列操作，如果只是单个future，只用asyncio.wait() 即可

asyncio.gather(*coro, loop=None, return_exception=False) 表示在event loop中运行coro序列中的所有任务

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



## run()

asyncio.run 来触发运行, 是Asyncio的root call， 表示拿到event loop，直到结束才关闭这个event loop

asyncio.run 这个函数是 Python 3.7 之后才有的特性，可以让 Python 的协程接口变得非常简单，你不用去理会事件循环怎么定义和怎么使用的问题，一个非常好的编程规范是，asyncio.run(main()) 作为主程序的入口函数，在程序运行周期内，只调用一次 asyncio.run。





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





# example



## 效率对比

```
import asyncio
import requests
from bs4 import BeautifulSoup

def main():
    url = "https://movie.douban.com/cinema/later/beijing"
    init_page = requests.get(url).content
    init_soup = BeautifulSoup(init_page, 'lxml')

    all_movies = init_soup.find('div', id='showing-soon')
    for each_movie in all_movies.find_all('div', class_="item"):
        all_a_tag = each_movie.find_all('a')
        all_li_tag = each_movie.find_all('li')

        movie_name = all_a_tag[1].text
        url_to_fetch = all_a_tag[1]['href']
        movie_date = all_li_tag[0].text
        
        response_item = requests.get(url_to_fetch).content
        soup_item = BeautifulSoup(response_item, 'lxml')
        img_tag = soup_item.find('img')
        print('{} {} {}'.format(movie_name, movie_date, img_tag['src']))

%time main()

>>>
CPU times: user 1.3 s, sys: 46.2 ms, total: 1.35 s
Wall time: 19.8 s
```



```
import asyncio, aiohttp
import time
from bs4 import BeautifulSoup

def cost_time(func):
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        res = func(*args, **kwargs)
        end = time.perf_counter()
        print('Time costs: {}s'.format(end - start))
        return res
    return wrapper


async def fetch_content(url):
    header={"User-Agent": "Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.157 Safari/537.36"}
    async with aiohttp.ClientSession(
        headers=header,  connector=aiohttp.TCPConnector(ssl=False)
    ) as session:
        async with session.get(url) as response:
            return await response.text()
    
@cost_time
async def main():
    url = "https://movie.douban.com/cinema/later/beijing"
    init_page = await fetch_content(url)
    init_soup = BeautifulSoup(init_page, 'lxml')

    movie_names, urls_to_fetch, movie_dates = [], [], []

    all_movies = init_soup.find('div', id='showing-soon')
    for each_movie in all_movies.find_all('div', class_="item"):
        all_a_tag = each_movie.find_all('a')
        all_li_tag = each_movie.find_all('li')

        movie_names.append(all_a_tag[1].text)
        urls_to_fetch.append(all_a_tag[1]['href'])
        movie_dates.append(all_li_tag[0].text)

    tasks = [fetch_content(url) for url in urls_to_fetch]
    pages = await asyncio.gather(*tasks)

    for movie_name, movie_date, page in zip(movie_names, movie_dates, pages):
        soup_item = BeautifulSoup(page, 'lxml')
        img_tag = soup_item.find('img')
        print('{} {} {}'.format(movie_name, movie_date, img_tag['src']))

asyncio.run(main())

>>>
Time costs: 1.5269999948941404e-06s
```









# 多线程 vs Asyncio

```
if io_bound:
    if io_slow:
        print('Use Asyncio')
    else:
        print('Use multi-threading')
else if cpu_bound:
    print('Use multi-processing')
```

如果是 I/O bound，并且 I/O 操作很慢，需要很多任务 / 线程协同实现，那么使用 Asyncio 更合适。

如果是 I/O bound，但是 I/O 操作很快，只需要有限数量的任务 / 线程，那么使用多线程就可以了。

如果是 CPU bound，则需要使用多进程来提高程序运行效率。



# 多进程

```
import time
def cpu_bound(number):
    print(sum(i * i for i in range(number)))

def calculate_sums(numbers):
    for number in numbers:
        cpu_bound(number)

def main():
    start_time = time.perf_counter()  
    numbers = [10000000 + x for x in range(20)]
    calculate_sums(numbers)
    end_time = time.perf_counter()
    print('Calculation takes {} seconds'.format(end_time - start_time))
    
if __name__ == '__main__':
    main()

>>>
Calculation takes 17.826206894 seconds


```

