---
title: "basic_knowledge"
date: 2020-02-29 21:24
---
[toc]





# Redis

开源的kv 存储系统 

原子性操作如push/pop add/remove 

数据存于内存中，但是不同于memcache，会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，在此基础上实现主从同步

配合关系型数据库做高速缓存



redis采取单线程+多路IO复用的机制

多路复用指一个线程来检查多个socket的就绪状态，得到就绪状态后可以再同一个线程里面执行







## 组件

```
redis-benchmark 性能测试工具
redis-server	服务器启动命令
redis-cli	客户端，操作入口
```



## 配置

```
vim /etc/redis.conf

daemonize no 改成yes，会让服务从后台启动
```



### 关闭

```
# redis-cli shutdown

OR

# redis-cli -p 6379 shutdown
```



## 测试

```
# redis-cli
127.0.0.1:6379> ping
PONG
```





# installation

## centos

```
yum -y install epel-release && yum -y install redis
```



## Amazon linux

compiled 

```
sudo yum -y install gcc make 
cd /tmp
sudo wget http://download.redis.io/redis-stable.tar.gz
sudo tar xvzf redis-stable.tar.gz
sudo rm -f redis-stable.tar.gz
cd redis-stable
sudo yum groupinstall "Development Tools"
sudo make distclean
sudo make
sudo yum install -y tcl
sudo make test
sudo cp src/redis-server /usr/local/bin/
sudo cp src/redis-cli /usr/local/bin/

sudo mkdir /etc/redis
sudo cp /tmp/redis-stable/redis.conf /etc/redis
```

```
vim /etc/redis/redis.conf
# change to systemd
supervised systemd

# find the dir directory. This option specifies the directory that Redis will use to dump persistent data. We need to pick a location that Redis will have write permission and that isn’t viewable by normal users.
dir /var/lib/redis
```



```
groupadd redis && useradd redis -g redis
sudo mkdir /var/lib/redis
sudo chown redis:redis /var/lib/redis
sudo chmod 770 /var/lib/redis
```



```
cat > /etc/systemd/system/redis.service <<EOF
[Unit]
Description=Redis In-Memory Data Store
After=network.target

[Service]
User=redis
Group=redis
ExecStart=/usr/local/bin/redis-server /etc/redis/redis.conf
ExecStop=/usr/local/bin/redis-cli shutdown
Restart=always

[Install]
WantedBy=multi-user.target
EOF
```



# 数据类型



## string

最基本类型，一个key 对应一个value

string是二进制安全的，即string可以包含任何数据，如序列化的对象

value最多可以是512M



## set

与list类似的列表功能，但可以去重 

set是string 类型的无序集合，底层是一个value为null的hash表，所以增删查的复杂度为O(1)

```
sadd <key> <v1> <v2> 将一个或者多个member元素加入到集合key中，已存在于集合的member元素将被忽略

smembers <key> 取出该集合的所有值

sismember <key> <value> 判断集合<key> 是否含有该<value>值，有返回1，没有返回0

scard <key> 返回该集合的元素个数

srem <key> <v1> <v2> 删除集合中的某个元素

spop <key> 随机从该集合中吐出一个值

srandmember <key> <n> 随机从该集合中取出n个值，但不会从集合中删除

sinter <k1> <k2> 返回两个集合交集元素

sunion <k1> <k2> 返回两个集合并集元素

sdiff <k1> <k2>  返回两个集合差集元素

```







## list

Redis 列表是简单的字符串列表，按照插入顺序排序

底层实现是双向链表，对两端的操作性能很高



```
lpush/rpush <key> <v1> <v2> <v3> ... 从左边/右边插入一个或者多个值

lpop/rpop <key> 从左边/右边弹出值，若值不在，键也不在了

rpop|push <k1> <k2> 从k1列表右边弹出一个值，插入到k2列表的左边

lrange <key> <start> <stop> 按照索引下标获得元素，从左到右

lindex <key> <index> 按照索引下标获得元素，从左到右

llen <key> 获得列表长度

linsert <key> before <value> <new_value> 在value前面插入 new_value

lrem <key> <n> <value> 	从左边删除n个value，从左到右
```



## hash

hash 是一个string类型的field和value的映射表，适合存储对象，类似Java中的 Map<String, Object> 



```
hset <key> <field> <value> 给key集合中的field键赋值value

hget <key> <field> 从key集合field取出value

hmset <key> <field1> <v1> <field2> <v2> ...	批量设置hash 值

hexists <key> <field> 查看hash表中，给定域field 是否存在

hkeys <key> 列出该hash集合的所有field

hvals <key> 列出该hash集合的所有value

hincrby <key> <field> <increment> 为hash 表中key中域field的值加上增量increment

hsetnx <key> <field> <value> 将hash表key中域field的值设置为value，只有在域field不存在的时候
```



## zset 

sorted set

这个有序集合关联一个评分 score，或者次序position来获取一个范围的元素



```
zadd <key> <score1> <value1> <score2> <value2> 将一个或者多个元素以及其score值加入到有序集key中

zrange <key> <start> <stop> [withscores] 返回有序集<key> 汇总，下标在<start> <stop> 之间的元素，加上withscores可以让分数一起和值返回到结果集

zrangebyscore key min max [withscores] [limit offset count] 返回有序集key中，所有的score值介于min和max之间的成员（包括等于min和max），有序集成员按照score值递增从小到大排序

zrevrangebyscore key max min [withscores] [limit offset count]  返回有序集key中，所有的score值介于min和max之间的成员（包括等于min和max），有序集成员按照score值递增从大到小排序

zincrby <key> <increment> <value> 为元素score加上增量

zrem <key> <value> 删除该集合下指定的元素

zcount <key> <min> <max> 统计该集合分数区间内的元素个数

zrank <key> <value> 返回该值在集合中的排名，从0开始
```









# 操作

```
keys * 查看当前库中所有的键

exists <key>	判断某个键是否存在

type <key>	查看键类型

del <key>  删除键

expire <key> <seconds> 设置键过期时间，单位为秒

ttl <key>	查看还有多少秒过期  -1永不过期， -2已经过期

dbsize 查看当前数据库中key的数量

flushdb		清空当前库

flushall	清空所有库

get <key> 查询对应的键值

set <key> <value> 添加键值对

append <key> <value> 将value追加到原值的末尾

strlen <key> 获得键长度

setnx <key> <value>	只有在key不存在时设置key的值

incr <key> 将key中存储的数据值增1， 只能对数字操作

decr <key> 将key中存储的数据值减1， 只能对数字操作

incrby/decrby <key> <range> 将key中存储的数字增减，自定义步长

mset <k1> <v1> <k2> <v2> 设置多个kv

mget <k1> <v1> <k2> <v2> 获取多个kv

msetnx <k1> <v1> <k2> <v2> 若不存在，设置多个kv

getrange <key> <start_at> <end_at> 获得值的范围

setrange <key> <start_at> <value> 用value覆写<key>所存储的字符串，从start_at开始

setex <key> <expire_time> <value> 设置键值的同时，设置过期时间，单位为s

getset <key> <value>  设置新的值，同时获得旧的值
```





## select

默认16个数据库，从下标0开始，默认使用0

使用统一的密码管理，即所有库都是相同密码

```
select <db_id>
```









# redis 事务

单独的隔离操作，事务中所有命令都会被徐离婚，按照顺序执行。

事务中所有的命令都会被序列化，按顺序执行，不会被其他客户端发来的命令请求打断

用于串联多个命令，防止别的命令插队

队列中命令没有提交之前都不会实际的被执行

事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚





## 过程

从输入Multi 命令开始，输入的命令都会依次进入命令队列中，但不会被执行，知道输入Exec后

组队过程中可以使用discard来放弃组队

队列中某个命令出错，整个队列都会被取消

如果某阶段的某个命令出错，只有报错的命令不会被执行，其他命令都会执行，不会回滚



```
127.0.0.1:6379> multi 
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k4
(error) ERR wrong number of arguments for 'set' command
127.0.0.1:6379> exec
(error) EXECABORT Transaction discarded because of previous errors.
```



```
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> set k4 v4
QUEUED
127.0.0.1:6379> incr k4
QUEUED
127.0.0.1:6379> exec
1) OK
2) OK
3) (error) ERR value is not an integer or out of range
```







## 锁

Redis的事务会出现锁冲突



### 悲观锁

Pessimistic Lock 

每次去拿数据的时候都认为别人会修改，所以每次都上锁

类似于创痛关系型数据库里面的行锁，表锁，读锁，写锁等



### 乐观锁 (redis)

Optimistic Lock

每次拿数据的时候，都认为别人不会修改，所以不会上锁

可以使用版本号机制，判断此期间是否有别人更新这个数据

适用于多读的应用类型，提高了吞吐量

Redis利用这种check and set机制实现事务



### watch 

在multi之前，先执行`watch key1 [key2] `可以监听一个或者多个key，如果在事务执行之前，这个key被其他命令锁改动，那么事务将会被打断

```
127.0.0.1:6379> set balance 0
OK
127.0.0.1:6379> watch balance
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> decrby balance 10
QUEUED
127.0.0.1:6379> incrby debt 10
QUEUED
127.0.0.1:6379> exec
1) (integer) -10
2) (integer) 10
```



### unwatch

取消watch对所有key的监视 

若watch之后，先执行了exec或者discard，就无需unwatch了





# Redis 持久化



## RDB

Redis Database

在指定时间间隔内，将内存中的数据集快照写入磁盘形成快照，恢复的时候将快照文件写入内存中

退出redis-cli，会在当前目录下生成rdb文件，可以修改redis.conf文件进行修改

```
-rw-r--r--. 1 root root   77 Mar  1 14:02 dump.rdb
```



### 保存

可以使用save命令手动保存

```
save 900 1		# 900s内至少一个key发生变化，会持久化一次
save 300 10		# 300s内至少10个key发生变化，会持久化一次
save 60 10000	# 60s内至少10000个key发生变化，会持久化一次
```



save命令只负责保存，其他不管会全部阻塞，所以需要使用bgsave命令操作



### 备份

Redis会单独fork一个子进程来进行持久化，会将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件

整个过程主进程不进行任何IO操作

RDB方式比AOF方式更加高效

缺点是最后一次持久化后的数据可能丢失



先查看config中dir的路径，将rdb文件复制到别的地方即可完成备份操作



### 恢复

关闭redis

把备份文件复制到工作目录下

启动redis即可完成恢复



## AOF

Append Of File

以日志的形式来记录每个写操作，将Redis执行过的所有写指令记录下来，在Redis启动初会去根据该文件执行一次完成数据恢复



默认不开启，需要手动在配置文件中设置

保存路径与RDB一致

```
appendonly yes #默认为no
appendfilename "appendonly.aof"
```



遇到AOF文件损坏，使用下面命令恢复

```
redis-check-aof --fix appendonly.aof
```



### 同步

配置同步参数

```
appendfsync always		#始终同步，每次redis都会立刻写入日志
appendfsync everysec	#每秒同步，每秒记录日志
appendfsync no 				#不主动进行同步，把同步时机交给操作系统
```





# Redis 主从复制

```
vim redis.conf

include /root/myredis/redis.conf
pidfile /var/run/redis_6379.pid
port 6379
dbfilename dump6379.rdb
```



```
info replication 
f
```

