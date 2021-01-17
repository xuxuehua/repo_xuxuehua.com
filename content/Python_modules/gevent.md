---
title: "gevent"
date: 2020-12-28 21:50
---
[toc]



# gevent

gevent 通过 patch 的方式将各种常见的 IO 调用封装为协程，并且将整个调度过程完全封装，用户可以用近乎黑盒的方式来使用， 你唯一需要做的就是先手动 patch 一下，然后用 gevent.spawn 去发起任务，如果需要同步的话就再 joinall 一下。 可以说 gevent 选择了和 golang 一样的思路，gevent.spawn 就像是 golang 里的 goroutine，gevent 再继续优化升级下去，终极目标就是实现 golang 的 runtime 吧。



```
import time

import gevent
import gevent.monkey
import requests

gevent.monkey.patch_socket()


TARGET_URLS = (
    'http://httpbin.org/',
    'http://httpbin.org/ip',
    'http://httpbin.org/user-agent',
    'http://httpbin.org/headers',
    'http://httpbin.org/get'
)


def demo_task(url):
    start_ts = time.perf_counter()
    r = requests.get(url)
    end_ts = time.perf_counter()
    print(f'cost {end_ts-start_ts}s for url {url}')


def demo_handler():
    start_ts = time.perf_counter()
    tasks = [gevent.spawn(demo_task, url) for url in TARGET_URLS]
    gevent.joinall(tasks)
    end_ts = time.perf_counter()
    print(f'total cost {end_ts-start_ts}s')


def main():
    demo_handler()


if __name__ == '__main__':
    main()

>>>
cost 0.75631882s for url http://httpbin.org/
cost 0.7369034220000001s for url http://httpbin.org/user-agent
cost 0.73570187s for url http://httpbin.org/get
cost 0.7372718340000001s for url http://httpbin.org/headers
cost 0.7396256330000001s for url http://httpbin.org/ip
total cost 0.7593843419999999s
```

