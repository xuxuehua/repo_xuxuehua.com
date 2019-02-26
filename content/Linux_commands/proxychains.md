---
title: "proxychains"
date: 2018-12-06 11:37
---


[TOC]



# proxychains



## installation



### Ubuntu

```
apt-get install proxychains
```



Make a config file at `~/.proxychains/proxychains.conf` with content:

```
strict_chain
proxy_dns 
remote_dns_subnet 224
tcp_read_time_out 15000
tcp_connect_time_out 8000
localnet 127.0.0.0/255.0.0.0
quiet_mode

[ProxyList]
socks5  127.0.0.1 1080
```



### Mac OS

```
brew install proxychains-ng

vim /usr/local/etc/proxychains.conf

strict_chain
proxy_dns 
remote_dns_subnet 224
tcp_read_time_out 15000
tcp_connect_time_out 8000
localnet 127.0.0.0/255.0.0.0
quiet_mode
[ProxyList]
socks5  127.0.0.1 1080 
```

> 由于 **MacOS** 的 **SIP** 策略的影响，不能修改一些系统文件，所以直接运行 `proxychains4 curl xxx` 不会走代理。解决办法就是安装自己用户的 **proxychains4** 和 **curl**

```
brew install curl
echo 'export PATH="/usr/local/opt/curl/bin:$PATH"' >> ~/.bash_profile
echo "alias pc="proxychains4" >> ~/.bash_profile
```



## run

```
proxychains4 curl https://www.twitter.com/
proxychains4 git push origin master
```

Or just proxify bash:

```
proxychains4 bash
curl https://www.twitter.com/
git push origin master
```