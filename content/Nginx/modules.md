---
title: "modules"
date: 2019-01-03 13:28
---


[TOC]



# 模块

![img](https://cdn.pbrd.co/images/HUGM64R.png)





## 查看模块是否配置

```
objs/ngx_modules.c 
```

数组`ngx_module_t *ngx_modules[]` 中会有模块信息





## 模块分类

每一个模块必须具备的数据结构

```
nginx_module_t
```

而数据结构里面的type，表示模块种类



cat src/http/ngx_http.c

```
ngx_module_t  ngx_http_module = {
    NGX_MODULE_V1,
    &ngx_http_module_ctx,                  /* module context */
    ngx_http_commands,                     /* module directives */
    NGX_CORE_MODULE,                       /* module type */
    NULL,                                  /* init master */
    NULL,                                  /* init module */
    NULL,                                  /* init process */
    NULL,                                  /* init thread */
    NULL,                                  /* exit thread */
    NULL,                                  /* exit process */
    NULL,                                  /* exit master */
    NGX_MODULE_V1_PADDING
};
```



### NGX_CORE_MODUELE

核心模块，本身会定义出新的子类模块

```
events
http
mail
stream 
```



#### events 子类模块

所有事件的处理的通用方法

```
NGX_EVENT_MODULE
```



```
epoll
event_core
```



#### http 子类模块

```
NGX_HTTP_MODULE
```





#### mail 子类模块

```
NGX_MAIL_MODULE
```



#### stream 子类模块

```
NGX_STREAM_MODULE
```







### NGX_CONF_MODULE

只有一个模块，负责解析nginx的conf 文件

```
ngx_conf_module
```



