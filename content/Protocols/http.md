---
title: "http"
date: 2018-09-16 13:07
---


[TOC]


# HTTP

[https://tools.ietf.org/html](https://tools.ietf.org/html)

HTTP 协议是无状态的，任何两次请求之间都没有依赖关系



# 请求类

```
GET / HTTP/1.1  #请求行
Host: 127.0.0.1		# 下面都是请求头
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Iridium/2019.04 Safari/537.36 Chrome/73.0.0.0
DNT: 1
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
```







## 请求行

```
GET / HTTP/1.1  #请求行
```

> GET: 请求方法
>
> / : 资源路径
>
> HTTP/1.1 : 协议及版本号



## 请求头

key value格式，尾部追加\r\n换行



### User-Agent

请求发出者，兼容性以及定制化需求，如针对手机类的浏览器返回定制类的结果

```
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) 
```



### Referer

表明档期这个请求是从哪个url过来的，常用于反爬虫机制。如果不是从指定页面过来的，就不会响应



### Cookie

cookie用来标识相同的请求



### Connection

使用keepalive，一个连接可以发多个请求

```
Connection: keep-alive
```



## 请求体

请求体是可选的

GET请求默认没有请求体





# 响应类

```
HTTP/1.1 200 OK // 状态行
Bdpagetype: 2 // 响应头
Bdqid: 0x9376a04b0015f045
Cache-Control: private
Connection: keep-alive
Content-Encoding: gzip
Content-Type: text/html;charset=utf-8
Date: Fri, 31 Jan 2020 09:13:00 GMT
Expires: Fri, 31 Jan 2020 09:12:59 GMT
Server: BWS/1.1
Set-Cookie: BDSVRTM=372; path=/
Set-Cookie: BD_HOME=1; path=/
Set-Cookie: H_PS_PSSID=1464_21098_18560_30472_30479; path=/; domain=.baidu.com
Strict-Transport-Security: max-age=172800
Traceid: 1580461980055062605810625856614811693125
X-Ua-Compatible: IE=Edge,chrome=1
Transfer-Encoding: chunked 

<!doctype html>  // 响应体
<html>
<head>
		<title>Example</title>
```



## 状态行

```
HTTP/1.1 200 OK
```

> HTTP/1.1: 协议版本
>
> 200 : 状态码
>
> OK : 原因
>
> \r\n : 结尾换行





## 响应头

key value格式，尾部追加\r\n换行

```
Bdpagetype: 2 // 响应头
Bdqid: 0x9376a04b0015f045
Cache-Control: private
Connection: keep-alive
Content-Encoding: gzip
Content-Type: text/html;charset=utf-8
Date: Fri, 31 Jan 2020 09:13:00 GMT
Expires: Fri, 31 Jan 2020 09:12:59 GMT
Server: BWS/1.1
Set-Cookie: BDSVRTM=372; path=/
Set-Cookie: BD_HOME=1; path=/
Set-Cookie: H_PS_PSSID=1464_21098_18560_30472_30479; path=/; domain=.baidu.com
Strict-Transport-Security: max-age=172800
Traceid: 1580461980055062605810625856614811693125
X-Ua-Compatible: IE=Edge,chrome=1
Transfer-Encoding: chunked 
```



### Server

Web服务器端使用什么程序响应的



### Content-Type

内容类型

常用 html， 音频，视频等



### Content-Length

内容长度







## 响应体

与响应头空行隔开

可以是文本或者是二进制格式，大小由Content-Length头指定





# HTTP 响应状态码

详细查看 [https://tools.ietf.org/html/rfc7231](https://tools.ietf.org/html/rfc7231)





## 1XX 纯信息



## 2XX 成功类

### 200

请求被正常处理



### 201

请求被正常处理，并创建一个新的资源



### 204

请求处理成功，无内容返回



### 206

partial content 客户发送了一个带有Range头的GET请求，服务器完成了它。



## 3XX 重定向类

### 301

永久重定向

Moved Permanently 所请求的页面已经转移至新的url



### 302

临时重定向。

如访问一个需要登录页面，没有登录，会重定向到登录页面





### 303

See Other 所请求的页面可在别的url下被找到。





### 304

Not modified 未按预期修改文档。客户端有缓冲的文档并发出了一个条件性的请求（一般是提供If-Modified-Since头表示客户只想比指定日期更新的文档）。服务器告诉客户，原来缓冲的文档还可以继续使用。即重定向到缓存的资源





## 4XX 客户端错误类





### 400

请求到URL在服务器上找不到，也就是请求URL 错误， 请求参数错误

Bad Request 服务器未能理解请求。

`**400 Bad Request**` response status code indicates that the server cannot or will not process the request due to something that is perceived to be a client error (e.g., malformed request syntax, invalid request message framing, or deceptive request routing).



### 401

未授权请求，需要获取授权信息





### 403

服务器拒绝访问，权限不够



### 404

Not Found 服务器无法找到被请求的页面。



### 405 

若只允许指定的http方法如GET方法。POST方法会放回405， Method not allowed，请求方法不允许





## 5XX 服务器端错误类





### 500

Internet Server Error 请求未完成。服务器遇到不可预知的情况，服务器内部发生错误





### 501

Not Implemented 请求未完成。服务器不支持所请求的功能。	