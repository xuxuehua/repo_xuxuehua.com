---
title: "restful_api"
date: 2020-05-07 10:16
---
[toc]



# Restful API 设计

**think about future, design with flexibility, but only implement for production**。中文大概就是说：要考虑未来的场景，设计时留有余地，但永远只实现 production 确实要用的功能

用API就得明确自己只是整个软件世界的一份子，要对上下游负责



## 100% restful

保证 API 100% RESTful。RESTful 的核心是 everything is a “resource”，所有的 action 和接口，都应该是相应 resource 上的 CRUD 操作。如果脱离这种设计模式，一定要再三考虑是不是必要？有没有其他方案可以避免





## 参数结构化

在 request 和 response 中，都应该尽可能的保持参数的结构化。

如果是一个 hash，就传一个 hash（不要传 hash.to_string）。

API 的 serialization / deserialization 会将其自动序列化成字符串。

多语言之间的 API，比如 Ruby，Java，C# 之间的调用，通常都是在 serialization / deserialization 中完成不同语言间类型的转换。





## Security

Authentication 和 Security 的考虑，应该始终放在首位。保证对特定的用户永远只 expose 相关的接口和权限。Authentication 可能是使用证书和白名单，也可能是通过用户登陆的 credentials 生成的验证 token，或者 session / cookie 等方式来处理。此外，所有的 API 层的 logging，应该保证不要 log 任何 sensitive 的信息。





## Client 一致性

API 本身应该是 client 无关的。也就是说，一个 API 对 request 的处理尽可能避免对 client 是 mobile 还是 web 等的考虑。

Client 相关的 response 格式，不应该在 API 中实现。而所有的 client 无关的计算和处理，又应该尽可能的在 server 端统一处理。以提高性能和一致性。



尽可能让 API 是 Idempotent（幂等 denoting an element of a set which is unchanged in value when multiplied or otherwise operated on by itself.）的。这有好几个不同层次的含义。举几个例子：同一个 request 发一遍和发两遍是不是能够保证相同结果？Request 失败后重发和第一次发是不是能保证相同结果？当然具体的做法还是要看应用场景。





## nonce

nonce，这是个很关键并且在网络通信中很常见的字段。

因为网络通信是不可靠的，一个信息包有可能会丢失，也有可能重复发送，在金融操作中，这两者都会造成很严重的后果。丢包的话，我们重新发送就行了；但是重复的包，我们需要去重。

虽然 TCP 在某种程度上可以保证，但为了在应用层面进一步减少错误发生的机会，Gemini 交易所要求所有的通信 payload 必须带有 nonce。

nonce 是个单调递增的整数。当某个后来的请求的 nonce，比上一个成功收到的请求的 nouce 小或者相等的时候，Gemini 便会拒绝这次请求。这样一来，重复的包就不会被执行两次了。

另一方面，这样也可以在一定程度上防止中间人攻击：

一则是因为 nonce 的加入，使得加密后的同样订单的加密文本完全混乱；

二则是因为，这会使得中间人无法通过“发送同样的包来构造重复订单“进行攻击。

```
t = datetime.datetime.now()
payload_nonce = str(int(time.mktime(t.timetuple())*1000))
payload = {   "request": "/v1/order/new",   "nonce": payload_nonce,   "symbol": "btcusd",   "amount": "5",   "price": "3633.00",   "side": "buy",   "type": "exchange limit",   "options": ["maker-or-cancel"]}
```

