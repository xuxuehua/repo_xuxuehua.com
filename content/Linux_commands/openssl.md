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





# commands

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





## speed  测速

```
$ openssl speed -evp aes-256-gcm
Doing aes-256-gcm for 3s on 16 size blocks: 107897710 aes-256-gcm's in 3.00s
Doing aes-256-gcm for 3s on 64 size blocks: 68627661 aes-256-gcm's in 3.00s
Doing aes-256-gcm for 3s on 256 size blocks: 24038659 aes-256-gcm's in 3.00s
Doing aes-256-gcm for 3s on 1024 size blocks: 6093305 aes-256-gcm's in 2.99s
Doing aes-256-gcm for 3s on 8192 size blocks: 774747 aes-256-gcm's in 2.99s
LibreSSL 2.8.3
built on: date not available
options:bn(64,64) rc4(16x,int) des(idx,cisc,16,int) aes(partial) blowfish(idx)
compiler: information not available
The 'numbers' are in 1000s of bytes per second processed.
type             16 bytes     64 bytes    256 bytes   1024 bytes   8192 bytes
aes-256-gcm     575878.68k  1464113.38k  2052296.32k  2084521.60k  2122290.74k
```



```
$ openssl speed -evp chacha20-poly1305
chacha20-poly1305 is an unknown cipher or digest
```





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





