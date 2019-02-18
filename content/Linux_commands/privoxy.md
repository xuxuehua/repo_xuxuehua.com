---
title: "privoxy"
date: 2019-01-14 17:22
---


[TOC]



# privoxy



## Installation

```
sudo apt install privoxy
```



## Configuration

```
listen-address  127.0.0.1:8118
listen-address  [::1]:8118

forward-socks5t   /               127.0.0.1:1080 .
```



`.bash_profile`

```
function proxy_off(){
    unset http_proxy
    unset https_proxy
    echo -e "proxy_off"
}

function proxy_on() {
    /usr/local/sbin/privoxy /usr/local/etc/privoxy/config
    export no_proxy="localhost,127.0.0.1,localaddress,.localdomain.com"
    export http_proxy="http://127.0.0.1:8118"
    export https_proxy=$http_proxy
    echo -e "proxy_on"
}
```

