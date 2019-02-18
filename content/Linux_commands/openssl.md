---
title: "openssl"
date: 2018-10-22 03:16
---


[TOC]


# openssl

OpenSSL command line tool



## SYNOPSIS 摘要

```
$ openssl command [ command_opts ][ command_args ]

$ openssl [ list-standard-commands | list-message-digest-commands | list-cipher-commands | list-cipher-algorithms | list-message-digest-algorithms | list-public-key-algorithms]

$ openssl no-XXX [ arbitrary options ]
```





## commands

### s_client  

This implements a generic SSL/TLS client which can establish a transparent connection to a remote server speaking SSL/TLS. It's intended for testing purposes only and provides only rudimentary interface functionality but internally uses mostly all functionality of the OpenSSL ssl library.



#### 证书导出

Tested

```
echo | openssl s_client -connect server:port 2>&1 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > cert.pem
```



Untested

```
openssl s_client -showcerts -connect www.example.com:443 </dev/null
openssl s_client -showcerts -servername www.example.com -connect www.example.com:443 </dev/null
```





### s_server  

This implements a generic SSL/TLS server which accepts connections from remote clients speaking SSL/TLS. It's intended for testing purposes only and
​             provides only rudimentary interface functionality but internally uses mostly all functionality of the OpenSSL ssl library.  It provides both an own command
​             line oriented protocol for testing SSL functions and a simple HTTP response facility to emulate an SSL/TLS-aware webserver.



## rand 随机数

随机字符串

```
$ openssl rand -base64 4
GsOFIA==
```

随机数字

```
openssl rand -base64 4 | cksum | cut -c 1-8
15404016
```



