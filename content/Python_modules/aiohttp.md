---
title: "aiohttp"
date: 2021-01-16 20:28
---
[toc]

# aiohttp

基于asyncio实现的http client/server框架



# example



```
import asyncio

import aiohttp

urls = [
    'https://httpbin.org/',
    'https://httpbin.org/get',
    'https://httpbin.org/ip',
    'https://httpbin.org/headers',
]


async def crawler():
    async with aiohttp.ClientSession() as session:
        futures = map(asyncio.ensure_future, map(session.get, urls))
        for i in asyncio.as_completed(futures):
            print(await i)

if __name__ == '__main__':
    loop = asyncio.get_event_loop()
    loop.run_until_complete(asyncio.ensure_future(crawler()))

>>>
<ClientResponse(https://httpbin.org/ip) [200 OK]>
<CIMultiDictProxy('Date': 'Mon, 28 Dec 2020 14:28:38 GMT', 'Content-Type': 'application/json', 'Content-Length': '33', 'Connection': 'keep-alive', 'Server': 'gunicorn/19.9.0', 'Access-Control-Allow-Origin': '*', 'Access-Control-Allow-Credentials': 'true')>

<ClientResponse(https://httpbin.org/get) [200 OK]>
<CIMultiDictProxy('Date': 'Mon, 28 Dec 2020 14:28:38 GMT', 'Content-Type': 'application/json', 'Content-Length': '310', 'Connection': 'keep-alive', 'Server': 'gunicorn/19.9.0', 'Access-Control-Allow-Origin': '*', 'Access-Control-Allow-Credentials': 'true')>

<ClientResponse(https://httpbin.org/) [200 OK]>
<CIMultiDictProxy('Date': 'Mon, 28 Dec 2020 14:28:38 GMT', 'Content-Type': 'text/html; charset=utf-8', 'Content-Length': '9593', 'Connection': 'keep-alive', 'Server': 'gunicorn/19.9.0', 'Access-Control-Allow-Origin': '*', 'Access-Control-Allow-Credentials': 'true')>

<ClientResponse(https://httpbin.org/headers) [200 OK]>
<CIMultiDictProxy('Date': 'Mon, 28 Dec 2020 14:28:38 GMT', 'Content-Type': 'application/json', 'Content-Length': '227', 'Connection': 'keep-alive', 'Server': 'gunicorn/19.9.0', 'Access-Control-Allow-Origin': '*', 'Access-Control-Allow-Credentials': 'true')>
```

> 这里启动了很多了 `session.get` 的子协程，然后用 `asyncio.ensure_future` 将其封装为 `future`， 然后调用 `as_completed` 方法监听这一堆的子任务，每当有子任务完成时，就会触发 for 循环对结果进行处理

# 