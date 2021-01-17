---
title: "process"
date: 2020-12-22 22:19
---
[toc]



# 进程状态

![process](process.assets/processes-2.jpg)



## New

正在初始化

## Ready

资源已就绪，随时可被 CPU 执行

## Running

正在被 CPU 运行

## Waiting

正在等待资源或事件

## Terminated

已经执行完成（等待回收）





# 进程操作



## fork / spawn 创建进程

进程可以通过 `fork` 或 `spawn` 创建子进程，原进程称为父进程。

每一个进程都有一个唯一标识，称为 process identifier，或 PID，每一个进程内也会保存父进程的 PID，称为 PPID。（同一 pid namespace 内的 PID 是唯一的）。

一个典型的 UNIX 系统内，进程调度器一般称为 `sched`，其 PID 为 0。在系统启动时，`sched` 会启动 `init`，其 PID 为 1。然后 `init` 会按计划启动启动内的各个 daemon。

一般来说，父进程创建子进程后有两种常见行为：

1. 调用 `wait()` 等待子进程完成；
2. 和子进程并行运行

子进程一般会继承父进程的资源，不过一般会有 COW 机制。子进程相对于父进程的地址空间一般有两种情况：

- 子进程完全拷贝父进程，分享同一份 text 和 data。每个子进程都有独立的 PCB。`fork` 就是如此。
- 子进程也可以加载新的程序到新的地址空间，拥有全新的 text 和 data。windows 上的 `spawn` 和 Linux 的 `exec` 就是这样。

`fork()` 有三种返回值：

- `0`：说明当前处于子进程之中；
- `-1`：出错；
- `PPID`：当前处于父进程之中。



## exit 停止进程

进程可以通过调用 `exit(status)` 停止自己，其父进程可以通过 `wait()` 拿到 status。一般 0 表示无异常。

进程也可能被操作系统停止：

- 系统无法提供所需资源；
- 系统响应 KILL 信号；
- 父进程杀死子进程；



# 进程间通信 IPC

Inter Process Communication

Linux 内，进程是资源的最小单位，进程与进程间资源隔离，为了能够实现跨进程的通讯，所以需要实现进程间通信机制（IPC）

主要有以下几种方式：

- 共享内存：最快
- 信号量：同步
- 信号：异步
- 管道：字节流
- 消息队列：结构化
- Socket：跨主机



## 共享内存

管道和消息队列等通信方式，需要在内核和用户空间进行四次的数据拷贝， 而共享内存则只拷贝两次数据：从输入文件到共享内存区，从共享内存区到输出文件。

共享内存的方式有三种：

- mmap
- Posix 共享内存
- 系统 V 共享内存



### mmap

mmap 可以将一个普通文件映射进进程的地址空间，使得进程可以像访问普通内存一样的访问普通文件。

多个进程可以以 MAP_SHARED 的方式映射同一份文件，来实现跨进程的内存共享，共同维护同一份文件：

```c
ptr=mmap(NULL, len , PROT_READ|PROT_WRITE, MAP_SHARED , fd , 0);
```

`len` 值决定了映射多少文件内容到内存，这个值也决定了进程能够防伪的内存地址区间。

进程对共享内存的修改并不会立刻写回文件，一般在映射解除时才会写回文件，这样可以提供更高的性能。 需要手动写回时，可以调用 msync。



### 系统 V 共享内存

系统调用 `mmap()` 通过映射一个普通文件实现共享内存。 系统V则是通过映射特殊文件系统shm中的文件实现进程间的共享内存通信。

进程间需要共享的数据被放在一个叫做IPC共享内存区域的地方， 所有需要访问该共享区域的进程都要把该共享区域映射到本进程的地址空间中去。 系统V共享内存通过shmget获得或创建一个IPC共享内存区域，并返回相应的标识符。





## 信号量 semaphore

信号量与其说是一个通讯机制，不如说是一个锁，提供跨进程的资源访问控制机制，可用于资源锁或同步。

每当有进程获取信号量，都会导致信号量的取值 -1，而当有进程释放信号量后取值 +1，当取值 =0 时，无法再获取信号量。

信号量可以设定取值，当取值范围为 0、1 时，称为二值信号量，此时的信号量等同于互斥锁。

信号量的结构：

```c
struct sem {
    int semval;  // 当前取值
    int sempid;  // 最后操作的 pid
}
```

信号量的一些限制（括号内为参考值）：

- 可以一次性操作的信号量数量上限为 SEMOPM（32）；
- 信号量取值的上限 SEMVMX（32767）；
- 系统范围内信号量集的最大数量上限 SEMMNI（32000）；
- 每个信号量集内信号量的最大数目 SEMMSL（250）；



## 信号

信号时在软件层面上对**中断机制**的一种模仿，从原理上，一个进程收到一个信号和处理器收到一个中断请求是一样的。

信号是进程间通信机制中唯一的**异步通信机制**。

信号的类别可以有两种分类方法，区分为两类信号：

- 可靠性
    - 不可靠信号 (信号值小于 SIGRTMIN 的信号)
    - 可靠信号 (信号值位于 SIGRTMIN 和 SIGRTMAX 之间的信号都是可靠信号。这批信号都支持排队，不会丢失。)
- 时效性
    - 非实时信号 (都是不可靠信号)
    - 实时信号 (都是可靠信号)



### 进程对信号的处理

进程有三种方式来响应一个信号：

- 忽略信号（SIGKILL 和 SIGSTOP 无法被忽略）；
- 捕捉信号（自定义信号处理函数）；
- 执行默认操作（实时信号的默认操作都是进程终止）。



### 发送信号

发送信号的主要函数有：

- `kill()`：向指定 pid（进程或进程组） 发送信号；

- `raise()`：向进程自身发送信号；

- `sigqueue()`：只能向一个进程发信号，可以带上信号参数，传递更多的数据；

- `alarm()`：又称为闹钟，指定时间后向进程自身发送 SIGALRM 信号；

- ```
    setitimer()
    ```

    ：加强班的 alarm，支持三种定时机制

    - `ITIMER_REAL`：设定绝对时间；
    - `ITIMER_VIRTUAL`：设定程序执行时间，经过这段时间后发送信号；
    - `ITIMER_PROF`：设定相对时间为进程执行以及内核因本进程而消耗的时间和。

- `abort()`：向进程自身发送 SIGABORT 信号



### 监听信号

主要有两个函数：

- `signal`：旧方法，只能绑定信号和处理函数；
- `sigaction`：新方法，还可以传递信号参数



### 信号的流程

一个标准的信号生命周期为：

信号产生（Generation） –> 信号未决（Pending） –> 信号递达（Delivery）

进程在接受到信号以前，可以选择阻塞（Block）某类信号，让这些信号保持在未决状态。 进程在收到信号后也可以选择是执行处理函数，或者忽略信号。

如果在进程解除某个信号前，该信号产生了多次，那么对于非实时信号，多次产生只记为一次；而对于实时信号，会创建一个队列，保存所有信号。

主要有三个函数：

- sigprocmask：指定阻塞哪些信号或信号集；
- sigpending：获取所有被阻塞的信号；
- sigsuspend：暂停进程，等待某个信号。





## 管道

管道是最初的 Unix IPC 形式之一，具有以下特点：

- 半双工，数据只能单向流动，头部和尾部在创建时就确定了；
- 只能用于父子进程或兄弟进程；
- 只存在于内存之中，自成一种文件系统；
- 写入的内容添加在管道缓冲区的尾部，读取内容是从缓冲区头部读取。

使用时的注意点：

- 数据写入不保证原子性，只要有空闲空间就会写入，如果空间不足，写操作就会阻塞，直到缓冲区的数据被读走
- 如果读断已被关闭，写操作将会触发 SIGPIPE
- 匿名，仅能用于有亲属关系的进程间

现在使用场景很小了，基本只有 shell 里 `a | b` 这种时候还会用用。



### 命名管道

又名为 named pipe 或 FIFO，以未见的形式存在于文件系统中，任何进程都可以通过文件路径来访问该管道。

使用时严格符合先入先出规则，不能使用 lseek 移动定位。一些读写规则如下：

- 当写入数据量小于 PIPE_BUF 时，Linux将保证写入的原子性

因为管道传输的是字节流，所以交流双方需要自己约定传输的协议。



## 消息队列

信号和管道都只作用于相应进程的生命周期之内，所以又被称为 process-persistent。

而消息队列是随内核持续的，只有在内核重启或者显式的删除时，才会被真正的删除。

消息队列就是由内核所维护的一个链表，每一个消息队列都有一个系统唯一的 key。其中的每一个消息，都是一个结构体：

```c
struct msgbuf {
    long mtype;
    char mtext[1];
};
```

主要的 API 有三个：

- msgget：创建一个消息队列，并返回该队列的描述字；
- msgrcv：向指定消息队列发送消息；
- msgsnd：由指定消息队列获取消息；





## socket 套接字

这个大家都很熟悉了。

一般流程：

```c
// 创建套接字
int socket( int domain, int type, int ptotocol);

// 绑定地址
int bind( int sockfd, const struct sockaddr * my_addr, socklen_t my_addr_len)

// 建立连接
int connect( int sockfd, const struct sockaddr * servaddr, socklen_t addrlen)

// 接受请求
int accept( int sockfd, struct sockaddr * cliaddr, socklen_t * addrlen)

// 通信
// recv & send 时面向连接的
recv
recvfrom
recvmsg
send
sendto
sendmsg

// 关闭
close()
```

端口号的一些约定：

- 0-1023：IANA 分配的专用端口，绑定时需要 sudo 权限；
- 1024-49151：可以向 IANA 申请注册的端口；
- 49152-65535：临时端口。



### I/O 复用

当 CPU 需要处理多个套接字时，I/O 复用可以由 select 管理多个套接字， 然后让 CPU 阻塞在 select 上，当有任何套接字准备好时，select 就会将相应的套接字交给 CPU 处理。



### Unix 通信域

除了使用 PF_INET 进行网络通信外，还可以使用 PF_LOCAL 进行单机内的 IPC 通讯。

# 僵尸进程 Zombie

僵尸进程指的就是运行结束后，没有被父进程回收的进程。该进程释放了运行时的大部分资源，但是保留在 process table 中，是对系统资源的无谓浪费。

子进程退出时，会向父进程抛出 `SIGCHLD` 信号，并且将结束状态存储到 process table 中。如果父进程不去读取这些信息，子进程就会一直处在 zombie state。通过 `wait` 或 `poll` 读取后，这些信息就会从 process table 中删除。



## top

linux中，可以使用top命令查看

```
top - 14:33:04 up 1 day, 20:29,  1 user,  load average: 0.07, 0.02, 0.00
Tasks: 117 total,   1 running, 116 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  3530384 total,  2835168 free,   176128 used,   519088 buff/cache
KiB Swap:  3670012 total,  3670012 free,        0 used.  3070132 avail Mem 
```



# 孤儿进程 Orphaned

孤儿进程指的是父进程已经退出，但是自己依然在运行。一般来说，orphaned 会被 init 接管。

1. 交给同 thread group 的其他线程
2. 递归的查找祖先进程，交给第一个设置了 subreaper 的进程
3. 交给 init（PID 1）



# 守护进程 Daemon

脱离于终端，长时间的运行与后台的一种独立的特殊进程。其本质上也是一种孤儿进程。

适用于需要 7*24 小时运行的服务器，主要通过信号等 IPC 机制来控制。而如果进程与终端关联，就会受到终端信号的干扰（比如终端断开、用户操作），所以为了保证守护进程不受终端影响，所以我们需要创建一种完全脱离终端的进程。

创建守护进程的步骤：

1. fork 新进程，创建新 session（group leader 不得调用 setsid，所以需要先 fork）
2. 重置 umask，放开权限
3. 再次 fork，确保 pid 不等于 gid 和 sid
4. 关闭 stdin、stdout、stderr
5. 忽略子进程退出信号 SIGCHLD



# 内存

每一个进程都使用一套独立的虚拟内存地址，进程的内存从低位到高位可以分为四部分：

1. text：代码
2. data：global 和 static 变量
3. heap：堆，用于动态分配的内存
4. stack：栈，用于存储变量

当进程被交换出 CPU 的时候，还需要保存额外的程序计数器（program counter）和寄存器变量（registers），用于恢复



# PCB 进程信息

在内核中，会有一个结构体保存着每一个进程的信息，称为 PCB（Process Control Block），其内容包括：

- Process State: 进程状态
- Process ID: PID
- CPU registers and Program Counter: 用户上下文切换的恢复
- CPU-Scheduling information: 保存优先级等信息
- Accounting information: 计数器，包括运行时间等
- I/O Status information: 设备、文件句柄表



在 Linux 中，PCB 存储在 `task_struct` 中。



# Queue 

所有的进程都被存储在 job queue 中（这个 queue 也被用来计算 average load）

就绪的进程存储在 `ready queue` 中，等待设备就绪的进程会存储在 `device queue` 中，一般每一个设备都会有一个独立的队列。



# Schedulers

从应用场景上，可以把调度器分为三类：

- long-term scheduler（job scheduler）
- short-term scheduler（CPU sheduler）
- medium-term scheduler

## long-term scheduler

运行频率较低，用于加载新的任务进入队列，可以执行一些智能判断的算法。



## short-term scheduler

执行频率极高（100ms），用于在 CPU 中切换任务。



## medium-term scheduler

非典型的调度器，当系统的 load 偏高时，该调度器可以把一些任务暂时挂起，让一些耗时较短的任务尽快完成，以缩短队列长度。



# 上下文切换 Context Switch

上下文切换的几个场景：

- 当接收到中断信号后，CPU 对当前进程进行 `state-save`，然后切换到内核态处理信号，完毕后再对被中断的进程执行 `state-restore`；
- 当一个进程因时钟而被切换时，和上述过程相同，因为时钟就是 `timer interrupt`；

`state` 包括进程所有的寄存器和计数器等，也就是 PCB 的全部内容。

上下文切换发生的非常频繁，一般硬件都会对此做特殊优化。



