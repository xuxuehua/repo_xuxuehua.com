---
title: "lock"
date: 2020-11-22 09:58
---
[toc]



# 锁

避免死锁的问题。下面以 Redis 的锁服务为例（参考 [Redis 的官方文档](https://redis.io/topics/distlock) ）。

我们通过以下命令对资源加锁。

```
SET resource_name my_random_value NX PX 30000
```

> `SET NX` 命令只会在 `key` 不存在的时候给 `key` 赋值，`PX` 命令通知 Redis 保存这个 key 30000ms。
>
> `my_random_value` 必须是全局唯一的值。这个随机数在释放锁时保证释放锁操作的安全性。
>
> PX 操作后面的参数代表的是这个 key 的存活时间，称作锁过期时间。
>
> 当资源被锁定超过这个时间时，锁将自动释放。
>
> 获得锁的客户端如果没有在这个时间窗口内完成操作，就可能会有其他客户端获得锁，引起争用问题。

这里的原理是，只有在某个 key 不存在的情况下才能设置（set）成功该 key。于是，这就可以让多个进程并发去设置同一个 key，只有一个进程能设置成功。而其它的进程因为之前有人把 key 设置成功了，而导致失败（也就是获得锁失败）。

通过下面的脚本为申请成功的锁解锁：

```
if redis.call("get",KEYS[1]) == ARGV[1] then 
    return redis.call("del",KEYS[1]) 
else 
    return 0 
end
```



如果 key 对应的 value 一致，则删除这个 key。

通过这个方式释放锁是为了避免 Client 释放了其他 Client 申请的锁。







## fence 

- 锁服务需要有一个单调递增的版本号。
- 写数据的时候，也需要带上自己的版本号。
- 数据库服务需要保存数据的版本号，然后对请求做检查。

如果使用 ZooKeeper 做锁服务的话，那么可以使用 `zxid` 或 znode 的版本号来做这个 fence 版本号。





