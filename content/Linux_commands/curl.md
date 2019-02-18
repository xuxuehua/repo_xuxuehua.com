---
title: "curl"
date: 2018-11-07 00:06
---


[TOC]


# curl

cURL ，Command line URL viewer

一种命令行工具，作用是发出网络请求，然后得到和提取数据，显示在"标准输出"（stdout）上面



## 语法

```
curl [option] [url]
```



## 选项 options



### -a/--append  附加到文件

上传文件时，附加到目标文件 





### -A/--user-agent  代理设定

设置用户代理发送给服务器

```
curl --user-agent "[User Agent]" [URL]
```

有些网站需要使用特定的浏览器去访问他们，有些还需要使用某些特定的版本。curl内置option:-A可以让我们指定浏览器去访问网站

```
curl -A "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.0)" http://www.linux.com
```



### -b/--cookie 使用cookie

```
-b/--cookie <name=string/file>    
```

cookie字符串或文件读取位置



很多网站都是通过监视你的cookie信息来判断你是否按规矩访问他们的网站的，因此我们需要使用保存的cookie信息。内置option: -b

```
curl -b cookiec.txt http://www.linux.com
```



### -B/--use-ascii  ASCII文本传输

使用ASCII /文本传输





### -c/--cookie-jar 保存cookie

操作结束后把cookie写入到这个文件中 ， 这里与-D的cookie不一样



* 模拟表单信息，模拟登录，保存cookie信息

```
curl -c ./cookie_c.txt -F log=aaaa -F pwd=****** http://blog.51yip.com/wp-login.php  
```



### -C/--continue-at 断点续转

```
-C/--continue-at <offset>  
```

断点续转  

如果在下载dodo1.JPG的过程中突然掉线了，可以使用以下的方式续传

```
curl -C -O http://www.linux.com/dodo1.JPG
```



### -D/--dump-header 保存头信息

把header信息写入到该文件中 ， 这里与-c的cookie不一样



* 模拟表单信息，模拟登录，保存头信息

```
curl -D ./cookie_D.txt -F log=aaaa -F pwd=****** http://blog.51yip.com/wp-login.php  
```





### -d/--data POST 数据

HTTP POST方式传送数据  

```
-d/--data <data>   
```



### -e/--referer 告知来源地址



很多服务器会检查http访问的referer从而来控制访问。比如：你是先访问首页，然后再访问首页中的邮箱页面，这里访问邮箱的referer地址就是访问首页成功后的页面地址，如果服务器发现对邮箱页面访问的referer地址不是首页的地址，就断定那是个盗连了
curl中内置option：-e可以让我们设定referer

```
curl -e "www.linux.com" http://mail.linux.com
```



### -E/--cert 客户端证书文件

客户端证书文件和密码 (SSL) 

```
-E/--cert <cert[:passwd]> 
```

```
curl -E mycert.pem https://url
```



### -F/--form 表单提交

```
-F/--form <name=content> 
```

模拟http表单提交数据  

```
curl --form upload=@localfilename --form press=OK [URL]
```



### -f/--fail 忽略错误

 连接失败时不显示http错误 

​      



```
# curl -f http://xuxuehua.com/a.thml
curl: (22) The requested URL returned error: 404 Not Found
```

   



### -h/--help 帮助

帮助

  



### -H/--header 自定义头信息

自定义头信息传递给服务器

有时需要在http request之中，自行增加一个头信息。`--header`参数就可以起到这个作用

```
curl --header "Content-Type:application/json" http://example.com
```



分段下载

```
curl  --header "Range: bytes=0-20000" http://s0.cyberciti.org/images/misc/static/2012/11/ifdata-welcome-0.png -o part1
curl  --header "Range: bytes=20001-36907" http://s0.cyberciti.org/images/misc/static/2012/11/ifdata-welcome-0.png -o part2
cat part1 part2 >> test1.png
gnome-open test1.png
```

> 获取Range结果使用 `curl I + url`



```
curl -H "Authorization: token OAUTH-TOKEN" https://api.github.com
```



### -i/--include  显示头信息 及页面

 

可以显示http response的头信息，连同网页代码一起。

```
curl -i www.sina.com
```





### -I/--head 只显示头信息 (大写) 

只显示http response的头信息

用于查看资源的HTTP 头部

```
curl -I http://s0.cyberciti.org/images/misc/static/2012/11/ifdata-welcome-0.png
```

```
HTTP/1.1 301 Moved Permanently
Date: Sat, 29 Dec 2018 10:37:49 GMT
Connection: keep-alive
Cache-Control: max-age=3600
Expires: Sat, 29 Dec 2018 11:37:49 GMT
Location: https://s0.cyberciti.org/images/misc/static/2012/11/ifdata-welcome-0.png
X-Content-Type-Options: nosniff
Server: cloudflare
CF-RAY: 490ba22e118b2e15-NRT
```



#### 断点续传

当请求资源时，如果服务器返回如下响应头

```
accept-ranges=bytes 
```

表明服务器支持bytes类型得数据断点续传。**如果为空或者none，可能不支持断点续传**。



### -k 忽略证书 (https)

允许不使用证书到SSL站点

```
-k/--insecure 
```



### -L 自动跳转

有的网址是自动跳转的。使用`-L`参数，curl就会跳转到新的网址。

```
curl -L www.sina.com
```



### -o/--output  保存页面

把输出写到该文件中  

* 抓取页面内容到一个文件中

```
curl -o home.html  http://blog.51yip.com 
```



* 使用linux的重定向功能保存

```
curl http://www.linux.com >> linux.html
```



* 测试网页返回值

```
curl -o /dev/null -s -w %{http_code} www.linux.com
```

### -O/--remote-name 保留文件名(大写)

把输出写到该文件中，保留远程文件的文件名 



* 还可以用正则来抓取东西

```
curl -O http://blog.51yip.com/wp-content/uploads/2010/09/compare_varnish.jpg  
curl -O http://blog.51yip.com/wp-content/uploads/2010/[0-9][0-9]/aaaaa.jpg 
```

下载重命名

```
curl -O http://www.linux.com/{hello,bb}/dodo[1-5].JPG
```

由于下载的hello与bb中的文件名都是dodo1，dodo2，dodo3，dodo4，dodo5。因此第二次下载的会把第一次下载的覆盖，这样就需要对文件进行重命名。

```
curl -o #1_#2.JPG http://www.linux.com/{hello,bb}/dodo[1-5].JPG
```



### -r/--range 分段处理

检索来自HTTP/1.1或FTP服务器字节范围 



有时候下载的东西会比较大，这个时候我们可以分段下载。使用内置option：-r

```
# curl -r 0-100 -o dodo1_part1.JPG http://www.linux.com/dodo1.JPG
# curl -r 100-200 -o dodo1_part2.JPG http://www.linux.com/dodo1.JPG
# curl -r 200- -o dodo1_part3.JPG http://www.linux.com/dodo1.JPG
# cat dodo1_part* > dodo1.JPG
```



### -s/--silent 不显示下载进度



```
curl -s -o aaa.jpg  http://blog.51yip.com/wp-content/uploads/2010/09/compare_varnish.jpg  
```



### -T/--upload-file  上传

上传文件  

```
curl -T test.sql ftp://用户名:密码@ip:port/demo/curtain/bbstudy_files/
```



curl不仅仅可以下载文件，还可以上传文件。通过内置option:-T来实现

```
curl -T dodo1.JPG -u 用户名:密码 ftp://www.linux.com/img/
```

### -u/--user 用户密码认证

```
-u/--user <user[:password]>设置服务器的用户和密码  
```



```
curl -u "username" https://api.github.com
```



* 通过ftp下载文件

```
curl -u 用户名:密码 -O http://blog.51yip.com/demo/curtain/bbstudy_files/style.css  
 % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current  
 Dload  Upload   Total   Spent    Left  Speed  
101  1934  101  1934    0     0   3184      0 --:--:-- --:--:-- --:--:--  7136  
```



### -v/--verbose  verbose

可以显示一次http通信的整个过程，包括端口连接和http request头信息

```
curl -v www.sina.com
```





### -x/--proxy  代理

在给定的端口上使用代理 



```
curl --proxy socks5://127.0.0.1:1080 ip-api.com
curl -x socks5://127.0.0.1:1080 ip-api.com
```





### -X/--request  指定请求

指定什么命令  



#### POST

* POST application/x-www-form-urlencoded

`application/x-www-form-urlencoded` is the default:

```
curl -d "param1=value1&param2=value2" -X POST http://localhost:3000/data

```

explicit:

```
curl -d "param1=value1&param2=value2" -H "Content-Type: application/x-www-form-urlencoded" -X POST http://localhost:3000/data

```

with a data file

```
curl -d "@data.txt" -X POST http://localhost:3000/data

```

- 数据没有经过表单编码，还可以让curl为你编码，参数是`--data-urlencode`。

```
curl -X POST --data-urlencode "date=April 1" example.com/form.cgi
```







* json format 

 `-d '{"key1":"value1", "key2":"value2"}'` or `-d @data.json`

```
curl -d '{"key1":"value1", "key2":"value2"}' -H "Content-Type: application/json" -X POST http://localhost:3000/data

```

with a data file

```
curl -d "@data.json" -X POST http://localhost:3000/data
```

* POST方法必须把数据和网址分开，curl就要用到--data参数。

```
curl -X POST --data "data=xxx" example.com/form.cgi
```







#### DELETE

```
curl -X DELETE www.example.com
```



### --limit-rate  限速

```
--limit-rate <rate> 
```

设置传输速度

```
curl URL --limit-rate 50k
```



### --max-filesize 最大文件大小

设置最大下载的文件总量 

```
curl URL --max-filesize bytes
```



### --socks4 

```
--socks4 <host[:port]> 
```

用socks4代理给定主机和端口  



### --socks5

DNS解析是在本地上进行的

```
--socks5 <host[:port]> 
```

用socks5代理给定主机和端口 

```
 curl --socks5 127.0.0.1:1080  ifconfig.co
```



### --socks5-hostname

DNS解析是在proxy上进行的

```
curl --socks5-hostname 127.0.0.1:1080 https://ip.cn
```





### --trace 详细通信

```
--trace <file>  
```

对指定文件进行debug 

```
curl --trace output.txt www.sina.com
```



### --trace-ascii 详细信息保存到文件

```
--trace-ascii <file> 
```

跟踪但没有hex输出  

```
curl --trace-ascii output.txt www.sina.com
```



### --trace-time 详细信息带时间戳

```
--trace-time    
```

跟踪/详细输出时，添加时间戳  

```
curl --trace-time output.txt www.sina.com
```







### -#/--progress 进度条



用进度条显示当前的传送状态  

```
curl -# -O  http://blog.51yip.com/wp-content/uploads/2010/09/compare_varnish.jpg  
######################################################################## 100.0%  
```



### 其他

```
-anyauth   可以使用“任何”身份验证方法  
-basic 使用HTTP基本验证  
--data-ascii <data>  以ascii的方式post数据  
--data-binary <data> 以二进制的方式post数据  
--negotiate     使用HTTP身份验证  
--digest        使用数字身份验证  
--disable-eprt  禁止使用EPRT或LPRT  
--disable-epsv  禁止使用EPSV  
--egd-file <file> 为随机数据(SSL)设置EGD socket路径  
--tcp-nodelay   使用TCP_NODELAY选项  
--cert-type <type> 证书文件类型 (DER/PEM/ENG) (SSL)  
--key <key>     私钥文件名 (SSL)  
--key-type <type> 私钥文件类型 (DER/PEM/ENG) (SSL)  
--pass  <pass>  私钥密码 (SSL)  
--engine <eng>  加密引擎使用 (SSL). "--engine list" for list  
--cacert <file> CA证书 (SSL)  
--capath <directory> CA目录 (made using c_rehash) to verify peer against (SSL)  
--ciphers <list>  SSL密码  
--compressed    要求返回是压缩的形势 (using deflate or gzip)  
--connect-timeout <seconds> 设置最大请求时间  
--create-dirs   建立本地目录的目录层次结构  
--crlf          上传是把LF转变成CRLF  
--ftp-create-dirs 如果远程目录不存在，创建远程目录  
--ftp-method [multicwd/nocwd/singlecwd] 控制CWD的使用  
--ftp-pasv      使用 PASV/EPSV 代替端口  
--ftp-skip-pasv-ip 使用PASV的时候,忽略该IP地址  
--ftp-ssl       尝试用 SSL/TLS 来进行ftp数据传输  
--ftp-ssl-reqd  要求用 SSL/TLS 来进行ftp数据传输  

-form-string <name=string> 模拟http表单提交数据  
-g/--globoff 禁用网址序列和范围使用{}和[]  
-G/--get 以get的方式来发送数据    
--ignore-content-length  忽略的HTTP头信息的长度  
 
  
从文件中读取-j/--junk-session-cookies忽略会话Cookie  
-界面<interface>指定网络接口/地址使用  
-krb4 <级别>启用与指定的安全级别krb4  
-j/--junk-session-cookies 读取文件进忽略session cookie  
--interface <interface> 使用指定网络接口/地址  
--krb4 <level>  使用指定安全级别的krb4  
-K/--config  指定的配置文件读取  
-l/--list-only 列出ftp目录下的文件名称  
--local-port<NUM> 强制使用本地端口号  
-m/--max-time <seconds> 设置最大传输时间  
--max-redirs <num> 设置最大读取的目录数  
 
-M/--manual  显示全手动  
-n/--netrc 从netrc文件中读取用户名和密码  
--netrc-optional 使用 .netrc 或者 URL来覆盖-n  
--ntlm          使用 HTTP NTLM 身份验证  
-N/--no-buffer 禁用缓冲输出  
-p/--proxytunnel   使用HTTP代理  
--proxy-anyauth 选择任一代理身份验证方法  
--proxy-basic   在代理上使用基本身份验证  
--proxy-digest  在代理上使用数字身份验证  
--proxy-ntlm    在代理上使用ntlm身份验证  
-P/--ftp-port <address> 使用端口地址，而不是使用PASV  
-Q/--quote <cmd>文件传输前，发送命令到服务器  
--range-file 读取（SSL）的随机文件  
-R/--remote-time   在本地生成文件时，保留远程文件时间  
--retry <num>   传输出现问题时，重试的次数  
--retry-delay <seconds>  传输出现问题时，设置重试间隔时间  
--retry-max-time <seconds> 传输出现问题时，设置最大重试时间  
 
-S/--show-error   显示错误  
 
--stderr <file>  
-t/--telnet-option <OPT=val> Telnet选项设置  


--url <URL>     Spet URL to work with  

-U/--proxy-user <user[:password]>设置代理用户名和密码  
-V/--version 显示版本信息  
-w/--write-out [format]什么输出完成后  
 

-y/--speed-time 放弃限速所要的时间。默认为30  
-Y/--speed-limit 停止传输速度的限制，速度时间'秒  
-z/--time-cond  传送时间设置  
-0/--http1.0  使用HTTP 1.0  
-1/--tlsv1  使用TLSv1（SSL）  
-2/--sslv2 使用SSLv2的（SSL）  
-3/--sslv3         使用的SSLv3（SSL）  
--3p-quote      like -Q for the source URL for 3rd party transfer  
--3p-url        使用url，进行第三方传送  
--3p-user       使用用户名和密码，进行第三方传送  
-4/--ipv4   使用IP4  
-6/--ipv6   使用IP6  
```





## curlrc

```
#this is a sample .curlrc file

# store the trace in curl_trace.txt file. beware that multiple executions of the curl command will overwrite this file
--trace curl_trace.txt

# store the header info in curl_headers.txt file. beware that multiple executions of the curl command will overwrite this file
--dump-header curl_headers.txt

#change the below referrer URL or comment it out entirely
-e "https://www.google.com"

#change the below useragent string. get your/other UA strings from http://www.useragentstring.com/
-A "Mozilla/5.0 (Windows; U; Windows NT 6.0; en-US) AppleWebKit/525.13 (KHTML, like Gecko) Chrome/0.2.149.27 Safari/525.13"

#some headers
-H  "Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8"
-H  "Upgrade-Insecure-Requests: 1"
-H  "Accept-Encoding: gzip, deflate, sdch"
-H  "Accept-Language: en-US,en;q=0.8"



# socks5
socks5 = "127.0.0.1:1080"
```

