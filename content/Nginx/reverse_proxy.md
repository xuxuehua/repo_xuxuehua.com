---
title: "reverse_proxy"
date: 2018-12-27 13:14
---


[TOC]



# Reverse Proxy



## 变更为上游服务器

添加127.0.0.1	

```
   server {
        listen       127.0.0.1:8090;
        }
```



重启nginx

```
nginx -s stop
nginx
```



## openresty

```
https://openresty.org/download/openresty-1.13.6.2.tar.gz
tar
./configure && make && make install	
```



## reverse.conf

```
upstream local {			# local是一组的命名
    server 127.0.0.1:8090;	# 这里可以放一组机器， 不同的用户访问相同的URL，但返回的是不同的结果，需要使用反向代理
}

        location / {
            #root   html;
            #index  index.html index.htm;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

			#proxy_cache my_cache;	#
            
            #proxy_cache_key $host$uri$is_args$args;
            #proxy_cache_valid	200	304	302	ld;
            proxy_pass http://local;	# local指上游配置的组名
        }
```



## Cache 配置

```
http {
    proxy_cache_path /tmp/nginxcache levels=1:2 keys_zone=my_cache:10m max_size=10g inactive=60m use_temp_path=off;
    
    #激活缓存
    server {
        location / {
            proxy_cache my_cache;	# 这里的my_cache就是上面的配置
            
            proxy_cache_key $host$uri$is_args$args; #这里的所有作为cache的key
            proxy_cache_valid	200	304	302	ld;	#这里不返回ø
        }
    }
}
```

