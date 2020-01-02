---

title: "socket"
date: 2019-07-15 11:42

---

[TOC]



# Socket

利用file descriptors 与其他程序沟通的一种方式，是内核封装与应用程序之间的一个接口

在一切皆文件的Linux中，socket本质也是抽象出一个fd

同时也是网络操作系统中IO的部分延伸，可以使用socket在进程与进程之间进行通信，也可以在网络中抽象出来的接口或者方法与远端的socket进行通信



## Datagram Sockets (SOCK_DGRAM)

无连接的数据报文，基于udp



## Stream Sockets (SOCK_STREAM)

面向连接的数据流，基于tcp



# Socket 格式

完整的套接字格式

```
{protocol,src_addr,src_port,dest_addr,dest_port}
```

这常被称为套接字的五元组。其中protocol指定了是TCP还是UDP连接，其余的分别指定了源地址、源端口、目标地址、目标端口。

# socket缓冲区

## send buffer

要通过TCP连接发送出去的数据都先拷贝到send buffer，可能是从用户空间进程的app buffer拷入的，也可能是从内核的kernel buffer拷入的，拷入的过程是通过send()函数完成的，由于也可以使用write()函数写入数据，所以也把这个过程称为写数据，相应的send buffer也就有了别称write buffer。不过send()函数比write()函数更有效率。

最终数据是通过网卡流出去的，所以send buffer中的数据需要拷贝到网卡中。由于一端是内存，一端是网卡设备，可以直接使用DMA的方式进行拷贝，无需CPU的参与。也就是说，send buffer中的数据通过DMA的方式拷贝到网卡中并通过网络传输给TCP连接的另一端：接收端。

## recv buffer

当通过TCP连接接收数据时，数据肯定是先通过网卡流入的，然后同样通过DMA的方式拷贝到recv buffer中，再通过recv()函数将数据从recv buffer拷入到用户空间进程的app buffer中。

## close() 函数

通用的close()函数可以关闭一个文件描述符，当然也包括面向连接的网络套接字描述符。当调用close()时，将会尝试发送send buffer中的所有数据。但是close()函数只是将这个套接字引用计数减1，就像rm一样，删除一个文件时只是移除一个硬链接数，只有这个套接字的所有引用计数都被删除，套接字描述符才会真的被关闭，才会开始后续的四次挥手中。对于父子进程共享套接字的并发服务程序，调用close()关闭子进程的套接字并不会真的关闭套接字，因为父进程的套接字还处于打开状态，如果父进程一直不调用close()函数，那么这个套接字将一直处于打开状态，将一直进入不了四次挥手过程。

## shutdown() 函数

shutdown()函数专门用于关闭网络套接字的连接，和close()对引用计数减一不同的是，它直接掐断套接字的所有连接，从而引发四次挥手的过程。可以指定3种关闭方式：

1.关闭写。此时将无法向send buffer中再写数据，send buffer中已有的数据会一直发送直到完毕。
2.关闭读。此时将无法从recv buffer中再读数据，recv buffer中已有的数据只能被丢弃。
3.关闭读和写。此时无法读、无法写，send buffer中已有的数据会发送直到完毕，但recv buffer中已有的数据将被丢弃。

无论是shutdown()还是close()，每次调用它们，在真正进入四次挥手的过程中，它们都会发送一个FIN。

# 分类

## 监听套接字

监听套接字是在服务进程读取配置文件时，从配置文件中解析出要监听的地址、端口，然后通过socket()函数创建的，然后再通过bind()函数将这个监听套接字绑定到对应的地址和端口上。随后，进程/线程就可以通过listen()函数来监听这个端口(严格地说是监控这个监听套接字)。

## 已连接套接字

已连接套接字是在监听到TCP连接请求并三次握手后，通过accept()函数返回的套接字，后续进程/线程就可以通过这个已连接套接字和客户端进行TCP通信。

## socket() 函数

socket()函数的作用就是生成一个用于通信的套接字文件描述符sockfd(socket() creates an endpoint for communication and returns a descriptor)。这个套接字描述符可以作为稍后bind()函数的绑定对象。

## bind() 函数

服务程序通过分析配置文件，从中解析出想要监听的地址和端口，再加上可以通过socket()函数生成的套接字sockfd，就可以使用bind()函数将这个套接字绑定到要监听的地址和端口组合"addr:port"上。绑定了端口的套接字可以作为listen()函数的监听对象。

绑定了地址和端口的套接字就有了源地址和源端口(对服务器自身来说是源)，再加上通过配置文件中指定的协议类型，五元组中就有了其中3个元组。即：

```
{protocal,src_addr,src_port}
```

但是，常见到有些服务程序可以配置监听多个地址、端口实现多实例。这实际上就是通过多次socket()+bind()系统调用生成并绑定多个套接字实现的。

## accept() 函数

accpet()函数的作用是读取已完成连接队列中的第一项(读完就从队列中移除)，**并对此项生成一个用于后续连接的套接字描述符**，假设使用connfd来表示。有了新的连接套接字，工作进程/线程(称其为工作者)就可以通过这个连接套接字和客户端进行数据传输， 而监听套接字(sockfd)则仍然被监听者监听

当服务端收到了ack消息后，就表示三次握手完成了，表示和客户端的这个tcp连接已经建立好了。连接建立好的一开始，这个tcp连接会放在listen()打开的established queue队列中等待accept()的消费。

当established queue中的tcp连接被accept()消费后，这个tcp连接就会关联accept()所指定的套接字，并分配一个新的文件描述符。也就是说，经过accept()之后，这个连接和listen套接字已经没有任何关系了。

## connect() 函数

connect()函数则用于向某个已监听的套接字发起连接请求，也就是发起TCP的三次握手过程。从这里可以看出，连接请求方(如客户端)才会使用connect()函数，当然，在发起connect()之前，连接发起方也需要生成一个sockfd，且使用的很可能是绑定了随机端口的套接字。既然connect()函数是向某个套接字发起连接的，自然在使用connect()函数时需要带上连接的目的地，即目标地址和目标端口，这正是服务端的监听套接字上绑定的地址和端口。同时，它还要带上自己的地址和端口，对于服务端来说，这就是连接请求的源地址和源端口。于是，TCP连接的两端的套接字都已经成了五元组的完整格式。

## listen() 函数

listen()函数就是监听已经通过bind()绑定了addr+port的套接字的。监听之后，套接字就从CLOSE状态转变为LISTEN状态，于是这个套接字就可以对外提供TCP连接的窗口了。

如果监听了多个地址+端口，即需要监听多个套接字，那么此刻负责监听的进程/线程会采用select()、poll()的方式去轮询这些套接字(当然，也可以使用epoll()模式)，其实只监控一个套接字时，也是使用这些模式去轮询的，只不过select()或poll()所感兴趣的套接字描述符只有一个而已。

**listen()函数还维护了两个队列：连接未完成队列(syn queue)和连接已完成队列(accept queue)**。

### 处理过程

在进程/线程(监听者)监听的过程中，它阻塞在select()或poll()上。直到有数据(SYN信息)写入到它所监听的sockfd中(即recv buffer)，内核被唤醒(注意不是app进程被唤醒，因为TCP三次握手和四次挥手是在内核空间由内核完成的，不涉及用户空间)并将SYN数据拷贝到kernel buffer中进行一番处理(比如判断SYN是否合理)，

并准备SYN+ACK数据，这个数据需要从kernel buffer中拷入send buffer中，再拷入网卡传送出去。这时会在连接未完成队列(syn queue)中为这个连接创建一个新项目，并设置为SYN_RECV状态。

然后再次使用select()/poll()方式监控着套接字listenfd，直到再次有数据写入这个listenfd中，内核再次被唤醒，如果这次写入的数据是ACK信息，表示是某个客户端对服务端内核发送的SYN的回应，于是将数据拷入到kernel buffer中进行一番处理后，把连接未完成队列中对应的项目移入连接已完成队列(accept queue/established queue)，并设置为ESTABLISHED状态，

如果这次接收的不是ACK，则肯定是SYN，也就是新的连接请求，于是和上面的处理过程一样，放入连接未完成队列。

对于已经放入已完成队列中的连接，将等待内核通过accept()函数进行消费(由用户空间进程发起accept()系统调用，由内核完成消费操作)，只要经过accept()过的连接，连接将从已完成队列中移除，也就表示TCP已经建立完成了，两端的用户空间进程可以通过这个连接进行真正的数据传输了，直到使用close()或shutdown()关闭连接时的4次挥手，中间再也不需要内核的参与。这就是监听者处理整个TCP连接的循环过程

### Syn flood

如果监听者发送SYN+ACK后，迟迟收不到客户端返回的ACK消息，监听者将被select()/poll()设置的超时时间唤醒，并对该客户端重新发送SYN+ACK消息，防止这个消息遗失在茫茫网络中。但是，这一重发就出问题了，如果客户端调用connect()时伪造源地址，那么监听者回复的SYN+ACK消息是一定到不了对方的主机的，也就是说，监听者会迟迟收不到ACK消息，于是重新发送SYN+ACK。但无论是监听者因为select()/poll()设置的超时时间一次次地被唤醒，还是一次次地将数据拷入send buffer，这期间都是需要CPU参与的，而且send buffer中的SYN+ACK还要再拷入网卡(这次是DMA拷贝，不需要CPU)。如果，这个客户端是个攻击者，源源不断地发送了数以千、万计的SYN，监听者几乎直接就崩溃了，网卡也会被阻塞的很严重。这就是所谓的syn flood攻击。

解决syn flood的方法有多种，例如，缩小listen()维护的两个队列的最大长度，减少重发syn+ack的次数，增大重发的时间间隔，减少收到ack的等待超时时间，使用syncookie等，但直接修改tcp选项的任何一种方法都不能很好兼顾性能和效率。所以在连接到达监听者线程之前对数据包进行过滤是极其重要的手段。

## send() 函数

send()函数是将数据从app buffer复制到send buffer中(当然，也可能直接从内核的kernel buffer中复制)

## recv() 函数

recv()函数则是将recv buffer中的数据复制到app buffer中

# netstat / ss相关

## Recv-Q / Send-Q

netstat命令的Send-Q和Recv-Q列表示的就是socket buffer相关的内容

```
Recv-Q
    Established: The count of bytes not copied by the user program connected to this socket.  Listening: Since Kernel 2.6.18 this  column  contains the current syn backlog.

Send-Q
    Established:  The count of bytes not acknowledged by the remote host.  Listening: Since Kernel 2.6.18 this column contains the maximum size of the syn backlog.
```

```
# netstat -tnl
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN     
tcp6       0      0 :::80                   :::*                    LISTEN     
tcp6       0      0 :::22                   :::*                    LISTEN     
tcp6       0      0 ::1:25                  :::*                    LISTEN

# ss -tnl
State      Recv-Q Send-Q                    Local Address:Port      Peer Address:Port
LISTEN     0      128                                   *:22                   *:*   
LISTEN     0      100                           127.0.0.1:25                   *:*   
LISTEN     0      128                                  :::80                  :::*   
LISTEN     0      128                                  :::22                  :::*   
LISTEN     0      100                                 ::1:25                  :::*
```

> Listen状态下的套接字，netstat的Send-Q和ss命令的Send-Q列的值不一样，因为netstat根本就没写上未完成队列的最大长度。因此，判断队列中是否还有空闲位置接收新的tcp连接请求时，应该尽可能地使用ss命令而不是netstat。

# 地址/端口重用技术

正常情况下，**一个addr+port只能被一个套接字绑定，换句话说，addr+port不能被重用，不同套接字只能绑定到不同的addr+port上**。

在现在的Linux内核中，已经有支持地址重用的socket选项SO_REUSEADDR和支持端口重用的socket选项SO_REUSEPORT。设置了端口重用选项后，再去绑定套接字，就不会再有错误了。而且，一个实例绑定了两个addr+port之后(可以绑定多个，此处以两个为例)，就可以同一时刻使用两个监听进程/线程分别去监听它们，客户端发来的连接也就可以通过round-robin的均衡算法轮流地被接待。

该技术似乎感觉上去，性能很好，不仅减轻了监听资格(互斥锁)的争抢，避免"饥饿问题"，还能更高效地监听，并因为可以负载均衡，从而可以减轻监听线程的压力。但实际上，每个监听线程的监听过程都是需要消耗CPU的，如果只有一核CPU，即使重用了也体现不出重用的优势，反而因为切换监听线程而降低性能。因此，要使用端口重用，必须考虑是否已将各监听进程/线程隔离在各自的cpu中，也就是说是否重用、重用几次都需考虑cpu的核数以及是否将进程与cpu相互绑定。