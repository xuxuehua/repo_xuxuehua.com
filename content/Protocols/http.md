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





# RESTFUL API 设计

https://github.com/godruoyi/restful-api-specification



## HATEOAS

API 的使用者未必知道，URL 是怎么设计的。一个解决方法就是，在回应中，给出相关链接，便于下一步操作。这样的话，用户只要记住一个 URL，就可以发现其他的 URL。这种方法叫做 HATEOAS。

```
{
  ...
  "feeds_url": "https://api.github.com/feeds",
  "followers_url": "https://api.github.com/user/followers",
  "following_url": "https://api.github.com/user/following{/target}",
  "gists_url": "https://api.github.com/gists{/gist_id}",
  "hub_url": "https://api.github.com/hub",
  ...
}
```









# HTTP 响应状态码

详细查看 [https://tools.ietf.org/html/rfc7231](https://tools.ietf.org/html/rfc7231)





## 1XX 信息相关

API 不需要`1xx`状态码



## 2XX 成功类

### 200 

请求被正常处理

OK. The request has successfully executed. Response depends upon the verb invoked.



### 201 

请求被正常处理，并创建一个新的资源

Created. The request has successfully executed and a new resource has been created in the process. The response body is either empty or contains a representation containing URIs for the resource created. The Location header in the response should point to the URI as well.



### 202 

Accepted. The request was valid and has been accepted but has not yet been processed. The response should include a URI to poll for status updates on the request. This allows asynchronous REST requests



### 204

请求处理成功，无内容返回

No Content. The request was successfully processed but the server did not have any response. The client should not update its display.



### 206

partial content 客户发送了一个带有Range头的GET请求，服务器完成了它。



## 3XX 重定向类

### 301

永久重定向，所请求的页面已经转移至新的url

Moved Permanently. The requested resource is no longer located at the specified URL. The new Location should be returned in the response header. Only GET or HEAD requests should redirect to the new location. The client should update its bookmark if possible.



### 302

临时重定向。

如访问一个需要登录页面，没有登录，会重定向到登录页面

Found. The requested resource has temporarily been found somewhere else. The temporary Location should be returned in the response header. Only GET or HEAD requests should redirect to the new location. The client need not update its bookmark as the resource may return to this URL.



### 303

所请求的页面可在别的url下被找到。

`303`用于`POST`、`PUT`和`DELETE`请求

收到`303`以后，浏览器不会自动跳转，而会让用户自己决定下一步怎么办。下面是一个例子。

```http
HTTP/1.1 303 See Other
Location: /api/orders/12345
```



See Other. This response code has been reinterpreted by the W3C Technical Architecture Group (TAG) as a way of responding to a valid request for a non-network addressable resource. This is an important concept in the Semantic Web when we give URIs to people, concepts, organizations, etc. There is a distinction between resources that can be found on the Web and those that cannot. Clients can tell this difference if they get a 303 instead of 200. The redirected location will be reflected in the Location header of the response. This header will contain a reference to a document about the resource or perhaps some metadata about it.



### 304

Not modified 未按预期修改文档。客户端有缓冲的文档并发出了一个条件性的请求（一般是提供If-Modified-Since头表示客户只想比指定日期更新的文档）。服务器告诉客户，原来缓冲的文档还可以继续使用。即重定向到缓存的资源





### 307

`302`和`307`的含义一样，也是"暂时重定向"，区别在于`302`和`307`用于`GET`请求





## 4XX 客户端错误类





### 400

请求到URL在服务器上找不到，也就是客户端请求URL 错误， 请求参数错误，不做任何处理

Bad Request 服务器未能理解请求。

400 Bad Request response status code indicates that the server cannot or will not process the request due to something that is perceived to be a client error (e.g., malformed request syntax, invalid request message framing, or deceptive request routing).



### 401

Unauthorized 未授权请求，需要获取授权信息





### 403

Forbidden， 服务器拒绝访问，权限不够

但是不具有访问资源所需的权限



### 404

Not Found 服务器无法找到被请求的页面。



### 405 

 Method not allowed，若只允许指定的http方法如GET方法。POST方法会放回405，用户已经通过了认证，但是请求方法不允许



### 406

Not Acceptable



### 410

Gone. 所请求的资源已从这个地址转移，不再可用。



### 411

Length Required.



### 412

Precondition Failed.



### 413

Entity Too Large.



### 414

URI Too Long.



### 415

Unsupported Media Type.

客户端要求的返回格式不支持。比如，API 只能返回 JSON 格式，但是客户端要求返回 XML 格式。



### 417

Expectation Failed.



### 422

Unprocessable Entity， 客户端上传的附件无法处理，导致请求失败。



### 429

Too Many Requests， 客户端的请求次数超过限额。





## 5XX 服务器端错误类



### 500

Internet Server Error 请求未完成。客户端请求有效，但服务器遇到不可预知的情况，服务器内部发生错误





### 501

Not Implemented 请求未完成。服务器不支持所请求的功能。	



### 502 

连接超时 我们向服务器发送请求 由于服务器当前链接太多，导致服务器方面无法给于正常的响应,产生此类报错

The 502 Bad Gateway error is an HTTP status code that means that ELB received an invalid response from the EC2 Instance.



解决办法：

1.提高 Web 服务器的响应速度，也即减少内部的调用关系，可以把需要的页面、素材或数据，缓存在内存中，可以是专门的缓存服务器 ，也可以Web服务器自身的缓存，提高响应速度；

2.网络带宽的问题，则对传输的数据包进行压缩处理，或者向IDC申请增加带宽；

3.属于内部网络的故障或设置问题，也即内部网络拥塞，可能内部存在大量的数据调用或交互造成的，则需要优化内部网络传输或协议；

4.数据库的数据读取造成前端服务器 ，响应用户的请求变慢，那么必须提高数据库的处理能力，若是只读业务可以增加数据缓存的模式 或者增加数据库备机，分散读压力；



HTTP 502: Bad gateway

Possible causes:

- The load balancer received a TCP RST from the target when attempting to establish a connection.
- The load balancer received an unexpected response from the target, such as "ICMP Destination unreachable (Host unreachable)", when attempting to establish a connection. Check whether traffic is allowed from the load balancer subnets to the targets on the target port.
- The target closed the connection with a TCP RST or a TCP FIN while the load balancer had an outstanding request to the target. Check whether the keep-alive duration of the target is shorter than the idle timeout value of the load balancer.
- The target response is malformed or contains HTTP headers that are not valid.
- The load balancer encountered an SSL handshake error or SSL handshake timeout (10 seconds) when connecting to a target.
- The deregistration delay period elapsed for a request being handled by a target that was deregistered. Increase the delay period so that lengthy operations can complete.
- The target is a Lambda function and the response body exceeds 1 MB.
- The target is a Lambda function that did not respond before its configured timeout was reached.



当目标类型为 `ip` 时，负载均衡器可支持针对每个唯一目标（IP 地址和端口）的 55000 个并发连接或每分钟约 55000 个连接。如果连接数超过该值，则会增大出现端口分配错误的几率。如果您收到端口分配错误，请将多个目标添加到目标组。
Network Load Balancer不支持 `lambda` 目标类型，仅 Application Load Balancer 支持 `lambda` 目标类型。有关详细信息，请参阅 [Lambda功能作为目标](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/lambda-functions.html) 在 *Application Load Balancer 用户指南*.



### 503

Service Unavailable.

服务器无法处理请求，一般用于网站维护状态。



### 504

Gateway timeout

the proxy server or gateway did not receive a response from the origin server within a specified timeout. Consider for example that an Elastic Load Balancer is sitting between your origin server, and the ELB timed out trying to receiving the response from your server.

Possible causes:

- The load balancer failed to establish a connection to the target before the connection timeout expired (10 seconds).
- The load balancer established a connection to the target but the target did not respond before the idle timeout period elapsed.
- The network ACL for the subnet did not allow traffic from the targets to the load balancer nodes on the ephemeral ports (1024-65535).
- The target returns a content-length header that is larger than the entity body. The load balancer timed out waiting for the missing bytes.
- The target is a Lambda function and the Lambda service did not respond before the connection timeout expired.

