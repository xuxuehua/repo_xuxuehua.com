---
title: "websocket"
date: 2020-10-13 08:44
---
[toc]



# websocket

WebSocket 是一种在单个 TCP/TLS 连接上，进行全双工、双向通信的协议。WebSocket 可以让客户端与服务器之间的数据交换变得更加简单高效，服务端也可以主动向客户端推送数据。

在 WebSocket API 中，浏览器和服务器只需要完成一次握手，两者之间就可以直接创建持久性的连接，并进行双向数据传输。



## 缺点

websocket基于tcp的，虽然协议上有纠错，重传和等待的机制，但一些特殊的情况还是可能会有丢包的情况，比如同时有超过服务器负载的客户端在请求数据。

TCP 协议保证了所有的包按顺序抵达（即使是乱序抵达，在前面的包收到之前，TCP 协议下的底层程序也会讲先到达的靠后的包缓存，直到前面的包抵达，才送给上层的应用程序），但是并不能保证不可恢复的错误发生的时候



## installation

```
pip install websocket_client
```



# hello world

```
import websocket
import thread

# 在接收到服务器发送消息时调用
def on_message(ws, message):
    print('Received: ' + message)

# 在和服务器建立完成连接时调用   
def on_open(ws):
    # 线程运行函数
    def gao():
        # 往服务器依次发送0-4，每次发送完休息0.01秒
        for i in range(5):
            time.sleep(0.01)
            msg="{0}".format(i)
            ws.send(msg)
            print('Sent: ' + msg)
        # 休息1秒用于接收服务器回复的消息
        time.sleep(1)
        
        # 关闭Websocket的连接
        ws.close()
        print("Websocket closed")
    
    # 在另一个线程运行gao()函数
    thread.start_new_thread(gao, ())


if __name__ == "__main__":
    ws = websocket.WebSocketApp("ws://echo.websocket.org/",
                              on_message = on_message,
                              on_open = on_open)
    
    ws.run_forever()


Sent: 0
Sent: 1
Received: 0
Sent: 2
Received: 1
Sent: 3
Received: 2
Sent: 4
Received: 3
Received: 4
Websocket closed
```





相对于 REST 来说，Websocket 是一种更加实时、高效的数据交换方式。当然缺点也很明显：因为请求和回复是异步的，这让我们程序的状态控制逻辑更加复杂。

```
import ssl
import websocket
import json

# 全局计数器
count = 5

def on_message(ws, message):
    global count
    print(message)
    count -= 1
    # 接收了5次消息之后关闭websocket连接
    if count == 0:
        ws.close()

if __name__ == "__main__":
    ws = websocket.WebSocketApp(
        "wss://api.gemini.com/v1/marketdata/btcusd?top_of_book=true&offers=true",
        on_message=on_message)
    ws.run_forever(sslopt={"cert_reqs": ssl.CERT_NONE})

>>>
{"type":"update","eventId":7275473603,"socket_sequence":0,"events":[{"type":"change","reason":"initial","price":"11386.12","delta":"1.307","remaining":"1.307","side":"ask"}]}
{"type":"update","eventId":7275475120,"timestamp":1562380981,"timestampms":1562380981991,"socket_sequence":1,"events":[{"type":"change","side":"ask","price":"11386.62","remaining":"1","reason":"top-of-book"}]}
{"type":"update","eventId":7275475271,"timestamp":1562380982,"timestampms":1562380982387,"socket_sequence":2,"events":[{"type":"change","side":"ask","price":"11386.12","remaining":"1.3148","reason":"top-of-book"}]}
{"type":"update","eventId":7275475838,"timestamp":1562380986,"timestampms":1562380986270,"socket_sequence":3,"events":[{"type":"change","side":"ask","price":"11387.16","remaining":"0.072949","reason":"top-of-book"}]}
{"type":"update","eventId":7275475935,"timestamp":1562380986,"timestampms":1562380986767,"socket_sequence":4,"events":[{"type":"change","side":"ask","price":"11389.22","remaining":"0.06204196","reason":"top-of-book"}]}
```



## orderbook

```python
import copy
import json
import ssl
import time
import websocket


class OrderBook(object):

    BIDS = 'bid'
    ASKS = 'ask'

    def __init__(self, limit=20):

        self.limit = limit

        # (price, amount)
        self.bids = {}
        self.asks = {}

        self.bids_sorted = []
        self.asks_sorted = []

    def insert(self, price, amount, direction):
        if direction == self.BIDS:
            if amount == 0:
                if price in self.bids:
                    del self.bids[price]
            else:
                self.bids[price] = amount
        elif direction == self.ASKS:
            if amount == 0:
                if price in self.asks:
                    del self.asks[price]
            else:
                self.asks[price] = amount
        else:
            print('WARNING: unknown direction {}'.format(direction))

    def sort_and_truncate(self):
        # sort
        self.bids_sorted = sorted([(price, amount) for price, amount in self.bids.items()], reverse=True)
        self.asks_sorted = sorted([(price, amount) for price, amount in self.asks.items()])

        # truncate
        self.bids_sorted = self.bids_sorted[:self.limit]
        self.asks_sorted = self.asks_sorted[:self.limit]

        # copy back to bids and asks
        self.bids = dict(self.bids_sorted)
        self.asks = dict(self.asks_sorted)

    def get_copy_of_bids_and_asks(self):
        return copy.deepcopy(self.bids_sorted), copy.deepcopy(self.asks_sorted)


class Crawler:
    def __init__(self, symbol, output_file):
        self.orderbook = OrderBook(limit=10)
        self.output_file = output_file

        self.ws = websocket.WebSocketApp('wss://api.gemini.com/v1/marketdata/{}'.format(symbol),
                                         on_message = lambda ws, message: self.on_message(message))
        self.ws.run_forever(sslopt={'cert_reqs': ssl.CERT_NONE})

    def on_message(self, message):
        # 对收到的信息进行处理，然后送给 orderbook
        data = json.loads(message)
        for event in data['events']:
            price, amount, direction = float(event['price']), float(event['remaining']), event['side']
            self.orderbook.insert(price, amount, direction)

        # 整理 orderbook，排序，只选取我们需要的前几个
        self.orderbook.sort_and_truncate()

        # 输出到文件
        with open(self.output_file, 'a+') as f:
            bids, asks = self.orderbook.get_copy_of_bids_and_asks()
            output = {
                'bids': bids,
                'asks': asks,
                'ts': int(time.time() * 1000)
            }
            f.write(json.dumps(output) + '\n')


if __name__ == '__main__':
    crawler = Crawler(symbol='BTCUSD', output_file='BTCUSD.txt')

>>>
{"bids": [[11398.73, 0.96304843], [11398.72, 0.98914437], [11397.32, 1.0], [11396.13, 2.0], [11395.95, 2.0], [11395.87, 1.0], [11394.09, 0.11803397], [11394.08, 1.0], [11393.59, 0.1612581], [11392.96, 1.0]], "asks": [[11407.42, 1.30814001], [11407.92, 1.0], [11409.48, 2.0], [11409.66, 2.0], [11412.15, 0.525], [11412.42, 1.0], [11413.77, 0.11803397], [11413.99, 0.5], [11414.28, 1.0], [11414.72, 1.0]], "ts": 1562558996535}
{"bids": [[11398.73, 0.96304843], [11398.72, 0.98914437], [11397.32, 1.0], [11396.13, 2.0], [11395.95, 2.0], [11395.87, 1.0], [11394.09, 0.11803397], [11394.08, 1.0], [11393.59, 0.1612581], [11392.96, 1.0]], "asks": [[11407.42, 1.30814001], [11407.92, 1.0], [11409.48, 2.0], [11409.66, 2.0], [11412.15, 0.525], [11412.42, 1.0], [11413.77, 0.11803397], [11413.99, 0.5], [11414.28, 1.0], [11414.72, 1.0]], "ts": 1562558997377}
{"bids": [[11398.73, 0.96304843], [11398.72, 0.98914437], [11397.32, 1.0], [11396.13, 2.0], [11395.95, 2.0], [11395.87, 1.0], [11394.09, 0.11803397], [11394.08, 1.0], [11393.59, 0.1612581], [11392.96, 1.0]], "asks": [[11407.42, 1.30814001], [11409.48, 2.0], [11409.66, 2.0], [11412.15, 0.525], [11412.42, 1.0], [11413.77, 0.11803397], [11413.99, 0.5], [11414.28, 1.0], [11414.72, 1.0]], "ts": 1562558997765}
{"bids": [[11398.73, 0.96304843], [11398.72, 0.98914437], [11397.32, 1.0], [11396.13, 2.0], [11395.95, 2.0], [11395.87, 1.0], [11394.09, 0.11803397], [11394.08, 1.0], [11393.59, 0.1612581], [11392.96, 1.0]], "asks": [[11407.42, 1.30814001], [11409.48, 2.0], [11409.66, 2.0], [11412.15, 0.525], [11413.77, 0.11803397], [11413.99, 0.5], [11414.28, 1.0], [11414.72, 1.0]], "ts": 1562558998638}
{"bids": [[11398.73, 0.97131753], [11398.72, 0.98914437], [11397.32, 1.0], [11396.13, 2.0], [11395.95, 2.0], [11395.87, 1.0], [11394.09, 0.11803397], [11394.08, 1.0], [11393.59, 0.1612581], [11392.96, 1.0]], "asks": [[11407.42, 1.30814001], [11409.48, 2.0], [11409.66, 2.0], [11412.15, 0.525], [11413.77, 0.11803397], [11413.99, 0.5], [11414.28, 1.0], [11414.72, 1.0]], "ts": 1562558998645}
{"bids": [[11398.73, 0.97131753], [11398.72, 0.98914437], [11397.32, 1.0], [11396.13, 2.0], [11395.87, 1.0], [11394.09, 0.11803397], [11394.08, 1.0], [11393.59, 0.1612581], [11392.96, 1.0]], "asks": [[11407.42, 1.30814001], [11409.48, 2.0], [11409.66, 2.0], [11412.15, 0.525], [11413.77, 0.11803397], [11413.99, 0.5], [11414.28, 1.0], [11414.72, 1.0]], "ts": 1562558998748}
```



WebSocket 会丢包吗？如果丢包的话， Orderbook 爬虫又会发生什么？这一点应该如何避免呢？

1. websocket基于tcp的，虽然协议上有纠错，重传和等待的机制，但一些特殊的情况还是可能会有丢包的情况，比如同时有超过服务器负载的客户端在请求数据。
2.如果丢包的情况发生时，类似开大会会场人人都发微信图片，看着WiFi信号满格，却发不出去，差不多一样的道理爬虫也是收不到数据的。
3.查了下websocket的WebSocketApp的函数，有个参数on_error，是websocket发生错误的时候触发的，那么我们可以编写这个对应的回调函数来让服务器重发或者其他有效的处理。