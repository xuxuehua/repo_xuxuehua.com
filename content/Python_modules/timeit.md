---
title: "timeit"
date: 2020-10-13 08:38
---
[toc]







# timeit



## timeit



### 获取延迟

```
import requests
import timeit
def get_orderbook(): 
    orderbook = requests.get("https://api.gemini.com/v1/book/btcusd").json()
        
n = 10
latency = timeit.timeit('get_orderbook()', setup='from __main__ import get_orderbook', number=n) * 1.0/n
    
print('Latency is {} ms'.format(latency * 1000))

>>>
Latency is 1272.5125689001288 ms
```

