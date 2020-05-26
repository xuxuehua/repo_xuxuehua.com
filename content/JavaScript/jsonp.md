---
title: "jsonp"
date: 2020-05-24 20:34
---
[toc]

# jsonp

本质是利用了标签(link,img,script,这里使用script)具有可跨域的特性，由服务端返回预先定义好的javascript函数的调用，并且将服务端数据以该函数参数的形式传递过来





## jsonp 优点

完美解决在测试或者开发中获取不同域下的数据

用户传递一个callback参数给服务端，然后服务端返回数据时会将这个callback参数作为函数名来包裹住JSON数据，这样客户端就可以随意定制自己的函数来自动处理返回数据了



## jsonp 缺点

jsonp只支持get请求而不支持post请求

用session来判断当前用户的登录状态，跨域时会出现问题

jsonp存在安全性问题







# 同源策略

浏览器的一种安全策略，所谓同源，指的是域名、协议、端口号完全相同

限制：cookie、localStorage和IndexDB无法读取；无法操作跨域的iframe里的dom元素；Ajax请求不能发送

目的：保护用户信息安全



## 跨域

即不同源

```
http://api.example.com/detail.html  				不同源 域名不同  
https//www.example.com/detail.html   				不同源 协议不同  
http://www.example.com:8080/detail.html     不同源    端口不同  
http://api.example.com:8080/detail.html    	不同源    域名、端口不同  
https://api.example.com/detail.html    			不同源    协议、域名不同  
https://www.example.com:8080/detail.html    不同源    端口、协议不同  
http://www.example.com/detail/index.html    同源    只是目录不同 
```











