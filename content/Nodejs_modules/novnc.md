---
title: "novnc"
date: 2018-11-20 23:45
---


[TOC]


# novnc

## Installation

```
git clone https://github.com/novnc/noVNC.git
```





## 创建安全连接

VNC的默认会话不是安全的，我们需要创建一个安全的VNC连接。

```
To encrypt the traffic using the WebSocket ‘wss://’ URI scheme you need to generate a certificate for the proxy to load. By default the proxy loads a certificate file name self.pem but the –cert=CERT option can override the file name. You can generate a self-signed certificate using openssl. When asked for the common name, use the hostname of the server where the proxy will be running:
```





```
openssl req -new -x509 -days 365 -nodes -out self.pem -keyout self.pem
```

> 只填写了Common Name (e.g. server FQDN or YOUR name) []:这个字段，填写的内容是本机的hostname，一路回车完成创建，so easy. 
>
> 创建完毕的证书self.pem需要放置到noVNC/utils目录下，当启动noVNC时，websockify将自动装载证书



### Launch

```
./utils/launch.sh --vnc localhost:5901
```

