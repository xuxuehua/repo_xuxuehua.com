---
title: "osx"
date: 2018-12-05 22:06
---


[TOC]



# OSX



## Homebrew

### Installation 

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```



Proxy setup

```
/usr/bin/ruby -e "$(curl --proxy socks5://127.0.0.1:1080 -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```



## xcode

```
xcode-select --install
```





## CMD proxy

### privoxy

```
brew install privoxy
```

```
cd /usr/local/etc/privoxy/
echo 'listen-address 0.0.0.0:8118\nforward-socks5 / localhost:1080 .' >> config
```

> 注意：`0.0.0.0` 可以让其他设备访问到，若不需要，请修改成用 `127.0.0.1`；`8118`是HTTP代理的默认端口；localhost:1080 是 SOCKS5（shadowsocks） 默认的地址，可根据需要自行修改，且注意不要忘了最后有一个空格和点号。



```
/usr/local/sbin/privoxy /usr/local/etc/privoxy/config
```

查看是否启动成功

```
netstat -na | grep 8118
```



### .bash_profile

```
vi ~/.bash_profile	# 编辑配置文件
alias setproxy="export ALL_PROXY=socks5://127.0.0.1:1086"
alias unsetproxy="unset ALL_PROXY"
# :wq保存后
source ~/.bash_profile	# 立即生效
setproxy	# 开启代理
unsetproxy	# 关闭代理
```

