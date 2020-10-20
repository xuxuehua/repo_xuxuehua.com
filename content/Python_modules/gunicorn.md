---
title: "gunicorn"
date: 2019-10-24 22:16
---
[TOC]



# gunicorn

[Gunicorn](http://gunicorn.org/) 是一个 Python 的 WSGI HTTP 服务器。它所在的位置通常是在[反向代理](https://en.wikipedia.org/wiki/Reverse_proxy)（如 [Nginx](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/)）或者 [负载均衡](https://f5.com/glossary/load-balancer)（如 [AWS ELB](https://aws.amazon.com/elasticloadbalancing/)）和一个 web 应用（比如 Django 或者 Flask）之间。



Gunicorn 启动了被分发到的一个主线程，然后因此产生的子线程就是对应的 worker。

主进程的作用是确保 worker 数量与设置中定义的数量相同。因此如果任何一个 worker 挂掉，主线程都可以通过分发它自身而另行启动。

worker 的角色是处理 HTTP 请求。

预分发意味着主线程在处理 HTTP 请求之前就创建了 worker。

操作系统的内核就负责处理 worker 进程之间的负载均衡。







# 参数定义






## `-c CONFIG, --config=CONFIG`

Specify a config file in the form `$(PATH)`, `file:$(PATH)`, or `python:$(MODULE_NAME)`.






## `-b BIND, --bind=BIND` 

Specify a server socket to bind. Server sockets can be any of `$(HOST)`, `$(HOST):$(PORT)`, or `unix:$(PATH)`. An IP is a valid `$(HOST)`.


## `-w WORKERS, --workers=WORKERS` 

The number of worker processes. This number should generally be between 2-4 workers per core in the server. Check the [FAQ](http://docs.gunicorn.org/en/latest/faq.html#faq) for ideas on tuning this parameter.

## `-k WORKERCLASS, --worker-class=WORKERCLASS` 

The type of worker process to run. You’ll definitely want to read the production page for the implications of this parameter. You can set this to `$(NAME)` where `$(NAME)` is one of `sync`, `eventlet`, `gevent`, `tornado`, `gthread`, `gaiohttp` (deprecated). `sync` is the default. See the [worker_class](http://docs.gunicorn.org/en/latest/settings.html#worker-class) documentation for more information.


## `-n APP_NAME, --name=APP_NAME` 

If [setproctitle](https://pypi.python.org/pypi/setproctitle) is installed you can adjust the name of Gunicorn process as they appear in the process system table (which affects tools like `ps` and `top`).





# worker type

flask_app.py

```
from flask import Flask
import time

app = Flask(__name__)


@app.route('/io_task')
def io_task():
    time.sleep(2)
    return 'io_task has completed.\n'


@app.route('/cpu_task')
def cpu_task():
    for i in range(1000000000):
        n = i*i*i
    return "CPU task has finished.\n"


if __name__ == '__main__':
    app.run()
```



## sync

sync 底層實作是每個請求都由一個 process 處理。

if your service process is not thread-safe, you should use sync

當 gunicorn worker type 使用 sync 時，web 啟動時會預先開好對應數量的 process 處理請求，理論上 concurrency 的上限等同於 worker 數量。

這種類型的好處是錯誤隔離高，一個 process 掛掉只會影響該 process 當下服務的請求，而不會影響其他請求。

壞處則為 process 資源開銷較大，開太多 worker 時對記憶體或 CPU 的影響很大，因此 concurrency 理論上限極低。



每个 worker 都是一个加载 Python 应用程序的 UNIX 进程。worker 之间没有共享内存。

建议的 [`workers` 数量](http://docs.gunicorn.org/en/latest/design.html#how-many-workers)是 `(2*CPU)+1`





如下gunicorn 啟動時開了一個 pid 為 569 的 process 來處理請求，理論上每次只能處理一個請求：

```
$ gunicorn -w 1 -k sync flask_app:app -b 0.0.0.0:8888
[2020-08-22 11:34:52 +0800] [74297] [INFO] Starting gunicorn 20.0.4
[2020-08-22 11:34:52 +0800] [74297] [INFO] Listening at: http://0.0.0.0:8888 (74297)
[2020-08-22 11:34:52 +0800] [74297] [INFO] Using worker: sync
[2020-08-22 11:34:52 +0800] [74314] [INFO] Booting worker with pid: 74314
```

用 siege 分別對 IO bound task 

```
$ siege -c 2 -r 1 http://127.0.0.1:8888/io_task -v

{	"transactions":			           2,
	"availability":			      100.00,
	"elapsed_time":			        4.01,
	"data_transferred":		        0.00,
	"response_time":		        3.01,
	"transaction_rate":		        0.50,
	"throughput":			        0.00,
	"concurrency":			        1.50,
	"successful_transactions":	           2,
	"failed_transactions":		           0,
	"longest_transaction":		        4.01,
	"shortest_transaction":		        2.01
}
```



CPU bound task 發出 2 個請求

```
$ gunicorn -w 1 -k sync flask_app:app -b 0.0.0.0:8888
[2020-08-22 11:35:29 +0800] [74443] [INFO] Starting gunicorn 20.0.4
[2020-08-22 11:35:29 +0800] [74443] [INFO] Listening at: http://0.0.0.0:8888 (74443)
[2020-08-22 11:35:29 +0800] [74443] [INFO] Using worker: sync
[2020-08-22 11:35:29 +0800] [74460] [INFO] Booting worker with pid: 74460
[2020-08-22 11:40:37 +0800] [74443] [CRITICAL] WORKER TIMEOUT (pid:74460)
[2020-08-22 11:40:37 +0800] [74460] [INFO] Worker exiting (pid: 74460)
[2020-08-22 11:40:37 +0800] [75388] [INFO] Booting worker with pid: 75388
[2020-08-22 11:41:08 +0800] [74443] [CRITICAL] WORKER TIMEOUT (pid:75388)
[2020-08-22 11:41:08 +0800] [75388] [INFO] Worker exiting (pid: 75388)
[2020-08-22 11:41:08 +0800] [75421] [INFO] Booting worker with pid: 75421
```

```
$ siege -c 2 -r 1 http://127.0.0.1:8888/cpu_task -v

{	"transactions":			           0,
	"availability":			        0.00,
	"elapsed_time":			       61.91,
	"data_transferred":		        0.00,
	"response_time":		        0.00,
	"transaction_rate":		        0.00,
	"throughput":			        0.00,
	"concurrency":			        0.00,
	"successful_transactions":	           0,
	"failed_transactions":		           2,
	"longest_transaction":		        0.00,
	"shortest_transaction":		        0.00
}
```







## gthread

gthread 則是每個請求都由一個 thread 處理。

當 gunicorn worker type 用 gthread 時，可額外加參數 --thread 指定每個 process 能開的 thread 數量，此時 concurrency 的上限為 worker 數量乘以給個 worker 能開的 thread 數量。

這種類型的 worker 好處是 concurrency 理論上限會比 process 高，壞處依然是 thread 數量，OS 中 thread 數量是有限的，過多的 thread 依然會造成系統負擔



Gunicorn 还允许每个 worker 拥有多个线程。在这种场景下，Python 应用程序每个 worker 都会加载一次，同一个 worker 生成的每个线程共享相同的内存空间。

最大的并发请求数就是 `worker * 线程`，也就是10

为了在 Gunicorn 中使用多线程。我们使用了 `threads` 模式。每一次我们使用 `threads` 模式，worker 的类就会是 `gthread`：

```
gunicorn --workers=5 --threads=2 main:app
```

等同于：

```
gunicorn --workers=5 --threads=2 --worker-class=gthread main:app
```









io_task

```
$ gunicorn -w 1 -k gthread --thread=2 flask_app:app -b 0.0.0.0:8888
[2020-08-22 11:58:33 +0800] [76463] [INFO] Starting gunicorn 20.0.4
[2020-08-22 11:58:33 +0800] [76463] [INFO] Listening at: http://0.0.0.0:8888 (76463)
[2020-08-22 11:58:33 +0800] [76463] [INFO] Using worker: gthread
[2020-08-22 11:58:33 +0800] [76480] [INFO] Booting worker with pid: 76480
```

```
$ siege -c 2 -r 1 http://127.0.0.1:8888/io_task -v

{	"transactions":			           2,
	"availability":			      100.00,
	"elapsed_time":			        2.01,
	"data_transferred":		        0.00,
	"response_time":		        2.01,
	"transaction_rate":		        1.00,
	"throughput":			        0.00,
	"concurrency":			        2.00,
	"successful_transactions":	           2,
	"failed_transactions":		           0,
	"longest_transaction":		        2.01,
	"shortest_transaction":		        2.01
}
```



cpu_task

```
$ gunicorn -w 1 -k gthread --thread=2 flask_app:app -b 0.0.0.0:8888
[2020-08-22 11:58:33 +0800] [76463] [INFO] Starting gunicorn 20.0.4
[2020-08-22 11:58:33 +0800] [76463] [INFO] Listening at: http://0.0.0.0:8888 (76463)
[2020-08-22 11:58:33 +0800] [76463] [INFO] Using worker: gthread
[2020-08-22 11:58:33 +0800] [76480] [INFO] Booting worker with pid: 76480
```

```
$ siege -c 2 -r 1 http://127.0.0.1:8888/cpu_task -v

{	"transactions":			           2,
	"availability":			      100.00,
	"elapsed_time":			      198.00,
	"data_transferred":		        0.00,
	"response_time":		      197.99,
	"transaction_rate":		        0.01,
	"throughput":			        0.00,
	"concurrency":			        2.00,
	"successful_transactions":	           2,
	"failed_transactions":		           0,
	"longest_transaction":		      198.00,
	"shortest_transaction":		      197.99
}
```





## eventlet/gevent/tarnado

當 gunicorn worker type 用 eventlet、gevent、tarnado 等類型時，每個請求都由同一個 process 處理，而當遇到 IO 時該 process 不會等 IO 回應，會繼續處理下個請求直到該 IO 完成，理論上 concurrency 無上限。

使用非同步類型的 worker 好處和壞處非常明顯，對 IO bound task 的高效能，但在 CPU bound task 會不如 thread。



`(2*CPU)+1` 仍然是建议的`workers` 数量。因为我们仅有一核，我们将会使用 3 个worker。

在这种情况下，最大的并发请求数量是 3000。（3 个 worker * 1000 个连接/worker）



io_task

```
$ gunicorn -w 1 -k gevent flask_app:app -b 0.0.0.0:8888
[2020-08-23 01:58:18 +0800] [15822] [INFO] Starting gunicorn 20.0.4
[2020-08-23 01:58:18 +0800] [15822] [INFO] Listening at: http://0.0.0.0:8888 (15822)
[2020-08-23 01:58:18 +0800] [15822] [INFO] Using worker: gevent
[2020-08-23 01:58:18 +0800] [15847] [INFO] Booting worker with pid: 15847
```

```
$ siege -c 2 -r 1 http://127.0.0.1:8888/io_task -v

{	"transactions":			           2,
	"availability":			      100.00,
	"elapsed_time":			        2.01,
	"data_transferred":		        0.00,
	"response_time":		        2.01,
	"transaction_rate":		        1.00,
	"throughput":			        0.00,
	"concurrency":			        2.00,
	"successful_transactions":	           2,
	"failed_transactions":		           0,
	"longest_transaction":		        2.01,
	"shortest_transaction":		        2.01
}
```





cpu_task

```
$ gunicorn -w 1 -k gevent flask_app:app -b 0.0.0.0:8888
[2020-08-23 01:58:18 +0800] [15822] [INFO] Starting gunicorn 20.0.4
[2020-08-23 01:58:18 +0800] [15822] [INFO] Listening at: http://0.0.0.0:8888 (15822)
[2020-08-23 01:58:18 +0800] [15822] [INFO] Using worker: gevent
[2020-08-23 01:58:18 +0800] [15847] [INFO] Booting worker with pid: 15847
[2020-08-23 01:58:57 +0800] [15822] [CRITICAL] WORKER TIMEOUT (pid:15847)
[2020-08-23 01:58:58 +0800] [16433] [INFO] Booting worker with pid: 16433
```

```
$ siege -c 2 -r 1 http://127.0.0.1:8888/cpu_task -v

{	"transactions":			           0,
	"availability":			        0.00,
	"elapsed_time":			       31.01,
	"data_transferred":		        0.00,
	"response_time":		        0.00,
	"transaction_rate":		        0.00,
	"throughput":			        0.00,
	"concurrency":			        0.00,
	"successful_transactions":	           0,
	"failed_transactions":		           2,
	"longest_transaction":		        0.00,
	"shortest_transaction":		        0.00
}
```





### gevent

Async workers like Gevent create new greenlets (lightweight pseudo threads). Every time a new request comes they are handled by greenlets spawned by the worker threads. 

The core idea is when there is IO bound action in your code the greenlet will yield and give a handle to another greenlet to use the CPU. So when many requests are coming to your app, it can handle them concurrently, the IO-bound calls are not going to block the CPU and throughput will improve.



The most of these examples don’t talk about the issues you would face when using it in the actual production-grade app.

- Using Gevent needs *all of your code to be co-operative in nature or all libraries you use should be monkey-patchable*. Now this line may sound simple but it’s a big ask. This means either ensuring all the DB drivers, clients, 3rd party libraries used all of these are either pure python, which would ensure they can be monkey-patched OR they are written specifically with Gevent support. If any of your library or part of code does not yield while doing IO then all your greenlets in that worker are blocked and this will result in long halts. You will need to find any such code and fix it.
- Using greenlets, your connections to your backend services will explode and needs to be handled. You need to have connection pools that can be reused and at the same time ensure that your app and backend service can create and maintain that many socket connections.
- CPU blocking operations are going to stop all of your greenlets so if your requests have some CPU bound task it’s not going to help much. Of course, you can explicitly yield in between these tasks but you will need to ensure that part of code is greenlet safe.





# example



## gunicorn_config

```
# file of deployment/gunicorn_config.py

worker_class = "gthread"

workers = 2
threads = 4

max_requests = 0
max_requests_jitter = 0

timeout = 120
graceful_timeout = 120
keepalive = 600
```

> **worker_class**: use sync if your service does not support gthread, if your service process is not thread-safe, you should use sync
>
> **workers & threads**: the total workers (workers x threads) should not be too high
>
> **max_requests & jitter**: set max_requests to 0 means we do not restart workers. 
>
> **timeout & graceful_timeout**:  better use 120 by default







