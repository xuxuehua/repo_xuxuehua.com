---
title: "pgbouncer"
date: 2021-07-02 16:20
---



[toc]





# PgBouncer

PgBouncer is a lightweight connection pooler for PostgreSQL.

The information that used to be on this wiki page is contained on the [PgBouncer website](https://www.pgbouncer.org/)



PgBouncer作为PostgreSQL数据库的连接池中间件。与其他存在于PostgreSQL的连接池中间件不同，PgBouncer仅作为一个连接池和代理层为PostgreSQL和应用之间提供服务。

Pgbouncer具备例如连接池模式、连接类型、端口重用，应用场景以及用户认证、网络认证等多种重要特性

相对与pgpool，pgbouncer对资源的需求更小，如果你仅仅只需要一个连接池的功能，选择pgbouncer是正确的。但是如果你还需要一下故障切换，负载均衡，同步的功能，pgpool更适合



## 特点

- 高性能，因为Pgbouncer自身不需要查看整个数据包，所以在网络开销上仅为2k（默认情况），对系统的内存要求小。
- 部署灵活：Pgbouncer没有绑定到一台后端服务器。目标数据库可以驻留在不同的主机上。
- 可维护性强：支持大多数配置项的的在线重新配置；并且支持在线重启/升级，而不会断开客户端连接。
- 认证灵活：用户认证支持基于文件的验证方式外，还提供了数据库查询验证；网络连接认证与Postgresql数据库一致，支持多种模式验证。
- 灵活连接数：支持全局、数据库、用户和客户端连接数组合形式设置。



# 数据库连接池

在Pgbouncer中包括会话连接池、事务连接池、语句连接池三种方式



PostgreSQL服务器可以处理来自客户端的多个并发连接。为此，它为每个连接启动（“fork”）新进程，从那时起，客户端和新的服务器进程进行通信，而无需原始postgres进程进行干预。因此，主服务器进程始终在运行，等待客户端连接，而客户端及关联的服务器进程来来往往。”但是，这意味着每个新连接都会分叉一个新进程，保留在内存中，并可能在多个会话中变得过分繁忙。在业务量较小的情况下，这种方式基本可以满足要求，但是当业务量迅速激增，我们可能就需要不断去更改max_connections来满足客户端的需求。当时同样也带来了很大的问题，如频繁的关闭和创建连接造成的内存开销，管理已产生的大量连接等等，最终导致服务器响应缓慢而无法对外提供数据库服务。

在这样一个背景下，数据库连接池就被提出来了，对于使用Postgresql数据库来说，一般分为客户端连接池，比如c3p0、druid等等；另外一种则是服务器端连接池，例如pgbouncer、odyssey、pgpoolII等。



接池的作用：

- 接受和管理来自客户端的连接
- 建立和维护与服务器的连接
- 将服务器连接分配给客户端连接

特点：

- 单个服务器连接可处理来自不同客户端的会话，事务和语句
- 单个客户端会话的事务和/或语句可在不同的服务器连接上运行







## 会话连接池

官方解释为最有礼貌的方法。当客户端连接时，服务器连接将在其保持连接的整个过程中分配给它。当客户端断开连接时，服务器连接将重新放入池中。此模式支持所有PostgeSQL功能。



分配给客户端的服务器连接在客户端连接的整个生命周期内持续。这看起来好像根本不使用连接池一样，但是有一个重要的区别：当分配的客户端断开连接时，服务器连接不会被破坏。当客户端断开连接时，池管理器将：

- 清除客户端所做的任何会话状态更改。
- 将服务器连接返回到池中，以供其他客户端使用。

![image-20210706154706315](/Users/rxu/coding/github/repo_xuxuehua.com/content/Postgresql/pgbouncer.assets/image-20210706154706315.png)









## 事务连接池 （常用）

服务器连接仅在事务期间分配给客户端。当PgBouncer发现事务已结束时，服务器连接将被放回池中。该模式破坏了PostgreSQL的一些基于会话的功能。仅当应用程序通过协作使用不中断功能时，才可以使用它。有关不兼容的功能。



数据库客户端很少在不间断的情况下执行连续的事务。而是通常在事务之间执行非数据库工作。这意味着服务器连接在等待新工作到达时会花费大量时间空闲。

事务池模式试图减少服务器连接的空闲时间：

![image-20210706154315917](/Users/rxu/coding/github/repo_xuxuehua.com/content/Postgresql/pgbouncer.assets/image-20210706154315917.png)

- 池程序在开始事务时将服务器连接分配给客户端。
- 客户端的事务完成后，池程序将释放连接分配。



注意事项：

- 如果客户端运行多个事务，则每个事务可以在不同的服务器连接上执行。
- 单个服务器连接可以在其生命周期内运行由不同客户端发出的事务。



与服务器所允许的连接相比，允许活动客户端的数量要多得多。尽管取决于给定的工作负载，但经常会看到10倍或更多的活动客户端连接与服务器连接比率。

这确实带来了一个重要的警告：客户端不再期望对数据库会话状态所做的更改在同一客户端进行的连续事务中继续存在，因为这些事务可能在不同的服务器连接上运行。此外，如果客户端进行会话状态更改，它们可能并且很可能会影响其他客户端。





### 事务池示例

以下是一些使用上面的事务池示例

- 如果客户端1在T1中的第一个服务器连接上将会话设置为只读，而客户端2的T3是写事务，则T3将失败，因为它在现在的只读服务器连接上运行。
- 如果客户端1运行PREPARE a1 AS ...在T1上运行EXECUTE a1 ...，在T2上，则T2将失败，因为预编译语句对于运行T1的服务器连接是本地的。
- 如果客户端2在T3中创建了一个临时表并尝试在T4中使用它，则T4将失败，因为该临时表对于运行T3的服务器连接是本地的。

有关使用事务池时不支持的会话状态功能和操作的完整列表，请参见PgBouncer的列表



## 语句连接池

官方解释为最激进的方法。不允许多语句事务。本质上为了在客户端上强制执行“自动提交”模式，主要针对PL/Proxy。

在此，服务器连接分配仅在单个语句的持续时间内持续。这具有与事务池模式相同的会话状态限制，同时还破坏了事务语义

![image-20210706154738684](/Users/rxu/coding/github/repo_xuxuehua.com/content/Postgresql/pgbouncer.assets/image-20210706154738684.png)





## 连接池分析

![image](/Users/rxu/coding/github/repo_xuxuehua.com/content/Postgresql/pgbouncer.assets/bVcPaCA.png)

从上述对比情况来看，在连接池的选择上，需要依据业务环境特点来进行选择，默认情况下推荐使用事务连接池，它兼顾了执行事务的特性，尤其多语句的支持，并且不会像会话连接池那样，尝尝处于等待状态。当然事务模式并不支持预编译语句。而根据具体业务场景的特殊需要，有些时候需要客户端与服务器端保持连接，或者支持预编译语句，这样只能选择会话池模式。还有一些特例情况，某些业务场景只是单语句执行，那么语句池模式可能更适合。因此对比这三种模式，可以发现从对客户端操作的支持程度来讲，会话池支持度最高，其次是事务池，最后是语句池模式。但是从支持的连接数来讲，可能刚好是相反的顺序。





### 示例场景

- 有些只运行快速查询，因此在没有事务的情况下可以共享一个会话来处理上百个并发查询。
- 一些角色成员对于会话级并发是安全的，并且总是使用事务。因此，他们可以安全地共享数百个并发事务的多个会话。
- 有些角色过于复杂，无法与其他人共享会话。因此，您对它们使用会话池模式可以避免当所有“插槽”都已占用时连接错误。
- 不要使用它代替HAProxy或其他负载均衡器。尽管pgbouncer具有一些可配置的功能来解决负载均衡器要解决的问题，例如dns_max_ttl，并且可以为其设置DNS配置，但是大多数产品环境都使用HAProxy或其他用于HA的负载均衡器。这是因为HAProxy确实擅长以循环方式在服务器之间实现负载平衡，而不是pgbouncer。尽管pgbouncer对于postgres连接池更好，但最好使用一个小型守护程序来完美地执行一项任务，而不是使用较大的守护程序来完成两项任务，那样效果更糟。

在对于连接数的建议值来讲，上文也给出了一个大致的结果，就是一般情况下设置为CPU核数的3-4倍左右，当然这个不是绝对值，应该是在与业务场景类似的硬件环境中充分进行测试后，才能够得出具体的数值。

还有一点需要注意的是连接Pgbouncer的连接方式，网络连接和unix socket连接方式，较网络连接，unix socket方式可能更加节省网络通信的开销，因此如果pgbouncer和数据库在一台机器部署，可以优选该方式；如果处于不同服务器上，则选择网络连接。

# Installation



## amazon linux 2 

```
yum install -y https://download-ib01.fedoraproject.org/pub/epel/7/x86_64/Packages/p/pgbouncer-1.14.0-2.el7.x86_64.rpm
```





## ubuntu 20.04

```
apt update -y 
apt install pgbouncer
```



# 部署形式

包括单应用场景、多应用场景、集群场景还有多实例场景，这些方式都是依据不同的业务场景，没有孰优孰劣，符合的才是对的。



## 单应用场景



![image](/Users/rxu/coding/github/repo_xuxuehua.com/content/Postgresql/pgbouncer.assets/20210304113130810.png)



单应用场景主要具体为短连接较多的场景，频繁进行数据库的连接操作，但操作时间较短，均为短连接，所以将pgbouncer于应用服务器部署在同一台服务器，减少应用服务器和pgbouncer之间的开销。



配置文件



```
[databases]
test1 =
test =
[pgbouncer]
listen_port = 6688
listen_addr = 192.168.165.3
auth_type = md5
auth_file = /home/postgres/pgbouncer/bin/userlist.txt
logfile = /home/postgres/pgbouncer/pgbouncer1.log
pidfile =/home/postgres/pgbouncer/pgbouncer1.pid
unix_socket_dir = /tmp
;;unix_socket_mode = 0777
admin_users = wzb
stats_users = wzb
pool_mode = session
max_client_conn=1000
default_pool_size=30
```



导出数据库中用户名及密码到userslist.txt

userslist.txt，格式为用户名 密码



```
'testuser' 'md54d15115d8bebd3188c1ae09c4a9848af'
'testuser1' 'md5f8386abbae413786661ee5a5cfb5593c'
'rxu' 'md53d57c4bc9a647385e6916efd0b44db46'
```



启动Pgbouncer

pgbouncer -d pgbouncer.ini



客户端连接方式

psql -dtest1 -Utestuser1 -p6688





## 多应用场景



![在这里插入图片描述](/Users/rxu/coding/github/repo_xuxuehua.com/content/Postgresql/pgbouncer.assets/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NjE5OTgxNw==,size_16,color_FFFFFF,t_70.png)



多应用场景，一般指多个应用服务器连接数据库，因此可以选择将pgbouncer与数据库服务部署在同一台服务器上，减少pgbouncer和数据库之间的开销。



配置PgBouncer.ini文件

```
[databases]
a1 = host=127.0.0.1 port=5432 dbname=test
a2 = host=127.0.0.1 port=5432 dbname=test1
[pgbouncer]
listen_port = 6688
listen_addr = *
auth_type = md5
auth_file = /home/postgres/pgbouncer/bin/userlist.txt
logfile = /home/postgres/pgbouncer/pgbouncer.log
pidfile =/home/postgres/pgbouncer/pgbouncer.pid
admin_users = postgres
stats_users = rxu, postgres
pool_mode = session
max_client_conn=1000
default_pool_size=30
```



导出数据库中用户名及密码到userslist.txt

userslist.txt，格式为用户名 密码



```
'testuser' 'md54d15115d8bebd3188c1ae09c4a9848af'
'testuser1' 'md5f8386abbae413786661ee5a5cfb5593c'
'wzb' 'md53d57c4bc9a647385e6916efd0b44db46'
```



启动Pgbouncer

```
pgbouncer -d pgbouncer.ini
```



连接后端数据库

```
$ psql -p 6688 -U testuser a1

$ psql -p 6688 -U testuser1 a2
```

连接pgbouncer数据库

```
psql -p 6688 pgbouncer -U wzb

```





## 集群场景（读写分离）

读写分离场景下pgbouncer的配置与前面配置基本一致，主要区别于要针对读和写进行分别部署pgbouncer，因为pgbouncer本身只是数据库连接池，不具备负载均衡，或高可用，IP漂移等特性，需要结合其他成熟产品进行组合使用。





![在这里插入图片描述](/Users/rxu/coding/github/repo_xuxuehua.com/content/Postgresql/pgbouncer.assets/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NjE5OTgxNw==,size_16,color_FFFFFF,t_70-20210706155356625.png)



多实例场景主要利用linux系统端口重用技术，这个特性依靠Linux内核上的支持（Linux3.6以上版本），并结合pgbouncer自身支持（设置so_reuseport=1）结合起来形成多实例场景下的pgbouncer使用，可以认为是pgbouncer的高可靠或者高可用，在某一个实例进程故障的情况下，其他实例集成仍然可以处理来自外部的数据库连接请求。从操作系统层面来看，属于多进程共享同一个端口。



实例配置1



```
[databases]
a2 = host=127.0.0.1 port=5432 dbname=test1 pool_size=50
;;a1 = host=127.0.0.1 port=5432 dbname=test pool_size=30
[pgbouncer]
listen_port = 6688
listen_addr = 192.168.165.3
auth_type = md5
auth_file = /home/postgres/pgbouncer/bin/userlist.txt
logfile = /home/postgres/pgbouncer/pgbouncer1.log
pidfile =/home/postgres/pgbouncer/pgbouncer1.pid
unix_socket_dir = /tmp/pg1
#unix_socket_mode = 0777
admin_users = wzb
stats_users = wzb
pool_mode = session
max_client_conn=1000
default_pool_size=30
so_reuseport = 1
```



实例配置2

```
[databases]
a2 = host=127.0.0.1 port=5432 dbname=test1 pool_size=50
;;a1 = host=127.0.0.1 port=5432 dbname=test pool_size=30
[pgbouncer]
listen_port = 6688
listen_addr = 192.168.165.3
auth_type = md5
auth_file = /home/postgres/pgbouncer/bin/userlist.txt
logfile = /home/postgres/pgbouncer/pgbouncer2.log
pidfile =/home/postgres/pgbouncer/pgbouncer2.pid
unix_socket_dir = /tmp/pg2
#unix_socket_mode = 0777
admin_users = wzb
stats_users = wzb
pool_mode = session
max_client_conn=1000
default_pool_size=30
so_reuseport = 1
```



导出数据库中用户名及密码到userslist.txt

userslist.txt，格式为用户名 密码



```
'testuser' 'md54d15115d8bebd3188c1ae09c4a9848af'
'testuser1' 'md5f8386abbae413786661ee5a5cfb5593c'
'wzb' 'md53d57c4bc9a647385e6916efd0b44db46'
```



启动多实例

```
./pgbouncer pgbouncer.ini

./pgbouncer pgbouncer1.ini
```



# configuration



## pgbouncer.ini

```
[databases]
sbtest = host=127.0.0.1 port=5432 dbname=sbtest
 
[pgbouncer]
listen_port = 6543
listen_addr = 127.0.0.1
auth_type = md5
auth_file = userslist.txt
logfile = pgbouncer.log
pidfile = pgbouncer.pid
admin_users = postgres
pool_mode = transaction
default_pool_size=56
max_client_conn=600
```

> pool_mode: 连接池类型
>
> default_pool_size：每个用户/数据库对允许多少个服务器连接
>
> max_client_conn：允许的最大客户端连接数
>
> userslist.txt通过指定文件AUTH_FILE只包含用于连接到PostgreSQL的用户和口令的信息;该文件中的密码可以是纯文本密码，也可以是使用MD5或SCRAM加密的密码，具体取决于要使用的身份验证方法。











# Appendix

https://www.pgfans.cn/a?id=962

