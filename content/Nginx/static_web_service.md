---
title: "static_web_service"
date: 2018-11-25 15:05
---


[TOC]


# static_web_service



## nginx.conf

```
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;  # 打开gzip压缩

    server {
        listen       8080;            # 指定端口
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {                  # 请求路径
	    alias project-html-website/;  # 这里与url的路径一样的
            #root   html;             # 这里需要写绝对路径
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

    }
```

  

## 文件路径

```
server {
    location / {
        alias dlib/; 通常使用alias
    }
}
```



## gzip 压缩

```
http {
    gzip	on;
    gzip_min_length	1; #长度一字节
    gzip_comp_level 2;
    gzip_types	text/plain	application/x-javascript	text/css	application/xml	text/javascript	application/x-httpd-php	image/jpeg	image/gif	image/png;
}
```



## autoindex 遍历目录内容

```
server {
    location / {
        alias dlib/;
        autoindex	on; 
        set $limit_rate	1k;  # 限制访问速度，每秒1K字节
        
    }
}
```



## log_format 日志记录

指定日志格式

```
http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  logs/access.log  main;
}
```



开启日志

```
    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        access_log  logs/host.access.log  main;
    }
```

