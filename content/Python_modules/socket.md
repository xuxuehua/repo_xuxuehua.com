---
title: "socket"
date: 2018-09-10 12:14
---


[TOC]


# socket

socket(family, type[,protocal]) 使用给定的套接族，套接字类型，协议编号（默认为0）来创建套接字



## Socket Families(地址簇)

协议类型称为地址簇

```
socket.AF_UNIX unix本机进程间通信 
socket.AF_INET　IPV4　
socket.AF_INET6  IPV6
```



## Socket 类型

| socket 类型           | 描述                                                         |
| --------------------- | ------------------------------------------------------------ |
| socket.AF_UNIX        | 用于同一台机器上的进程通信（既本机通信）                     |
| socket.AF_INET        | 用于服务器与服务器之间的网络通信                             |
| socket.AF_INET6       | 基于IPV6方式的服务器与服务器之间的网络通信                   |
| socket.SOCK_STREAM    | 基于TCP的流式socket通信                                      |
| socket.SOCK_DGRAM     | 基于UDP的数据报式socket通信                                  |
| socket.SOCK_RAW       | 原始套接字，普通的套接字无法处理ICMP、IGMP等网络报文，而SOCK_RAW可以；其次SOCK_RAW也可以处理特殊的IPV4报文；此外，利用原始套接字，可以通过IP_HDRINCL套接字选项由用户构造IP头 |
| socket.SOCK_SEQPACKET | 可靠的连续数据包服务.  #废弃了                               |



## 创建 Socket



### tcp

```
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
```



### udp

```
sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
```





## Socket 函数



### 服务器端

| Socket 函数       | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| s.bind(address)   | 将套接字绑定到地址，在AF_INET下，以tuple(host, port)的方式传入，如s.bind((host, port)) |
| s.listen(backlog) | 开始监听TCP传入连接，backlog指定在拒绝链接前，操作系统可以挂起的最大连接数，该值最少为1，大部分应用程序设为5就够用了 |
| s.accept()        | 接受TCP链接并返回（conn, address），其中conn是新的套接字对象，可以用来接收和发送数据，address是链接客户端的地址。 |



### 客户端

| Socket 函数           | 描述                                                         |
| --------------------- | ------------------------------------------------------------ |
| s.connect(address)    | 链接到address处的套接字，一般address的格式为tuple(host, port)，如果链接出错，则返回socket.error错误 |
| s.connect_ex(address) | 功能与s.connect(address)相同，但成功返回0，失败返回errno的值 |



### 公用的

| Socket 函数                            | 描述                                                         |
| -------------------------------------- | ------------------------------------------------------------ |
| s.recv(bufsize[, flag])                | 接受TCP套接字的数据，数据以字符串形式返回，buffsize指定要接受的最大数据量，flag提供有关消息的其他信息，通常可以忽略 |
| s.send(string[, flag])                 | 发送TCP数据，将字符串中的数据发送到链接的套接字，返回值是要发送的字节数量，该数量可能小于string的字节大小 |
| s.sendall(string[, flag])              | 完整发送TCP数据，将字符串中的数据发送到链接的套接字，但在返回之前尝试发送所有数据。成功返回None，失败则抛出异常 |
| s.recvfrom(bufsize[, flag])            | 接受UDP套接字的数据u，与recv()类似，但返回值是tuple(data, address)。其中data是包含接受数据的字符串，address是发送数据的套接字地址 |
| s.sendto(string[, flag], address)      | 发送UDP数据，将数据发送到套接字，address形式为tuple(ipaddr, port)，指定远程地址发送，返回值是发送的字节数 |
| s.close()                              | 关闭套接字                                                   |
| s.getpeername()                        | 返回套接字的远程地址，返回值通常是一个tuple(ipaddr, port)    |
| s.getsockname()                        | 返回套接字自己的地址，返回值通常是一个tuple(ipaddr, port)    |
| s.setsockopt(level, optname, value)    | 设置给定套接字选项的值                                       |
| s.getsockopt(level, optname[, buflen]) | 返回套接字选项的值                                           |
| s.settimeout(timeout)                  | 设置套接字操作的超时时间，timeout是一个浮点数，单位是秒，值为None则表示永远不会超时。一般超时期应在刚创建套接字时设置，因为他们可能用于连接的操作，如s.connect() |
| s.gettimeout()                         | 返回当前超时值，单位是秒，如果没有设置超时则返回None         |
| s.fileno()                             | 返回套接字的文件描述                                         |
| s.setblocking(flag)                    | 如果flag为0，则将套接字设置为非阻塞模式，否则将套接字设置为阻塞模式（默认值）。非阻塞模式下，如果调用recv()没有发现任何数据，或send()调用无法立即发送数据，那么将引起socket.error异常。 |
| s.makefile()                           | 创建一个与该套接字相关的文件                                 |



## Socket 编程流程

### TCP 服务器

```
1、创建套接字，绑定套接字到本地IP与端口
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.bind()

2、开始监听链接
s.listen()

3、进入循环，不断接受客户端的链接请求
While True:
    s.accept()

4、接收客户端传来的数据，并且发送给对方发送数据
s.recv()
s.sendall()

5、传输完毕后，关闭套接字
s.close()
```



### TCP 客户端 

```
1、创建套接字并链接至远端地址
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect()


2、链接后发送数据和接收数据
s.sendall()
s.recv()

3、传输完毕后，关闭套接字
```



# 实现TCP持续的socket通信

## Server 

```
import socket

HOST = '127.0.0.1'
PORT = 8001

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.bind((HOST, PORT))
s.listen(5)

print('Start at %s:%s' % (HOST, PORT))
print('Waiting for connection')

while True:
    conn, addr = s.accept()
    print('Connected by ', addr)

    while True:
        data = conn.recv(1024)
        print(data.decode('utf-8'))

        conn.send('Server have received your message.'.encode('utf-8'))
```



## Client

```
import socket

HOST = '127.0.0.1'
PORT = 8001

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((HOST, PORT))

while True:
    cmd = input('Input your message : ')
    s.send(cmd.encode('utf-8'))
    data = s.recv(1024)
    print(data)
```



# 实现UDP持续的socket通信

## Server

```
import socket

HOST = '127.0.0.1'
PORT = 9999

s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

s.bind((HOST, PORT))

print('Bind UDP on %s' % PORT)

while True:
    data, addr = s.recvfrom(1024)
    print('Received from %s:%s' % addr)
    s.sendto(b'Hello, %s' % data, addr)
```



## Client 

```
import socket

HOST = '127.0.0.1'
PORT = 9999

s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

while True:
    cmd = input('UDP Data: ')
    s.sendto(cmd.encode('utf-8'), (HOST, PORT))
    print(s.recv(1024).decode('utf-8'))
```



# TCP 多线程聊天室

## server 

```
import socket
import threading

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind(('0.0.0.0', 8000))
server.listen()


def handle_sock(sock, addr):
    while True:
        data = sock.recv(1024)
        print(data.decode("utf-8"))
        re_data = input()
        sock.send(re_data.encode("utf-8"))


while True:
    sock, addr = server.accept()
    client_thread = threading.Thread(target=handle_sock, args=(sock, addr))
    client_thread.start()
```



## client 

```
import socket
client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client.connect(("127.0.0.1", 8000))

while True:
    re_data = input()
    client.send(re_data.encode("utf-8"))
    data = client.recv(1024)
    print(data.decode("utf-8"))
```



# socketserver

先设置socket类型，然后依次调用bind(), listen(), accept()， 最后用while循环让服务器不断接收请求