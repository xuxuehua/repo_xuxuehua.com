---
title: "ssl 开启ssl"
date: 2018-12-31 22:52
---


[TOC]



# Ssl 

## 版本

```
1995	SSL3.0
1999	TLS1.0
2006	TLS1.1
2008	TLS1.2
2018	TLS1.3
```



## 包分析

```
TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
```

> ECDHE	密钥交换
>
> RSA		身份认证
>
> AES		算法
>
> 128		强度
>
> GCM	模式（多核CPU提高性能）
>
> SHA256	MAC或PRF （定长输出）





## 证书

### DV Domain Validated

只会验证域名的归属是否正确，时间很快



### OV Organization Validated

验证申请的机构是否正确，价格较高，时间较长



### EV Extended Validation

更严格的验证，会在浏览器地址栏显示企业名称



### 证书链

由根证书，二级证书，子证书组成

nginx发送证书的时候，会发送两个证书：先发送站点的主证书，然后发送二级证书



## TLS 通讯过程

![img](https://cdn.pbrd.co/images/HUig3UN.png)





## Nginx 加密性能

小文件是考验非对称加密的性能

大文件是考验对称加密的性能

![img](https://cdn.pbrd.co/images/HUieoK5.png)











## Let's encrypt 配置

### centos

```
yum -y install python2-certbot-nginx
```

### Ubuntu

```
apt install python3-certbot-nginx
apt install python-certbot-nginx
```



### ngnix.conf

```
server {
    server_name Name.domain.com;
    listen 80;
    location / {
        alias html/your_directory/;
    }
}
```



```
certbot --nginx --nginx-server-root=/usr/local/openresty/nginx/conf/ -d us3.domain .com
```





# ssl certificate checker

https://www.digicert.com/help/

