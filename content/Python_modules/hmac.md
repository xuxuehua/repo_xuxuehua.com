---
title: "hmac"
date: 2018-08-26 19:23
---

[TOC]

# hmac

python 还有一个 hmac 模块，它内部对我们创建 key 和 内容 再进行处理然后再加密

散列消息鉴别码，简称HMAC，是一种基于消息鉴别码MAC（Message Authentication Code）的鉴别机制。使用HMAC时,消息通讯的双方，通过验证消息中加入的鉴别密钥K来鉴别消息的真伪；

一般用于网络通信中消息加密，前提是双方先要约定好key,就像接头暗号一样，然后消息发送把用key把消息加密，接收方用key ＋ 消息明文再加密，拿加密后的值 跟 发送者的相对比是否相等，这样就能验证消息的真实性，及发送者的合法性了。

```
In [31]: import hmac

In [39]: h = hmac.new(b'Hello World')

In [40]: h.digest()
Out[40]: b'\x17\xef\x0b\x13D\xd4\xa6gT[7\x8a\xfb"\xa1+'

In [41]: h.hexdigest()
Out[41]: '17ef0b1344d4a667545b378afb22a12b'
```



