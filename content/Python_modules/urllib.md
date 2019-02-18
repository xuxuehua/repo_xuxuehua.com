---
title: "urllib"
date: 2018-09-16 16:20
---


[TOC]


# urllib



## urlopen

网络请求方法

返回值是一个http.client.HTTPResponse 对象，对象是一个类文件句柄对象，有read(size), readline, readlines, getcode 方法

```
from urllib import request

resp = request.urlopen('http://www.baidu.com')
print(resp.read())
```



## urlretrieve

将网页文件保存到本地

```
from urllib import request

request.urlretrieve('http://www.baidu.com', 'baidu.html')
```



可用于保存图片

```
from urllib import request

request.urlretrieve('http://www.sanguosha.com/static/pc/dist/img/bg3.jpg', 'sanguosha.jpg')
```



## urlencode 编码

将请求中的字符进行编码

```
from urllib import parse

data = {'name': '许学生', 'greet': 'Hello World'}
qs = parse.urlencode(data)
print(qs)

>>>
name=%E8%AE%B8%E5%AD%A6%E7%94%9F&greet=Hello+World
```



## parse_qs 解码

将编码后的url参数解码

```
from urllib import parse

qs = "name=%E8%AE%B8%E5%AD%A6%E7%94%9F&greet=Hello+World"
print(parse.parse_qs(qs))

>>>
{'name': ['许学生'], 'greet': ['Hello World']}
```





## urlparse 

对url组成部分进行分割

```
from urllib import request, parse

url = 'http://www.xuxuehua.com/s?username=xuxuehua'

result = parse.urlparse(url)
print(result)

>>>
ParseResult(scheme='http', netloc='www.xuxuehua.com', path='/s', params='', query='username=xuxuehua', fragment='')
```





## urlsplit

对url组成部分进行分割

```
from urllib import request, parse

url = 'http://www.xuxuehua.com/s?username=xuxuehua'

result = parse.urlsplit(url)
print(result)

>>>
SplitResult(scheme='http', netloc='www.xuxuehua.com', path='/s', query='username=xuxuehua', fragment='')
```



## ProxyHandler 代理设置

通过代理访问目标网址



```
from urllib import request

url = 'http://httpbin.org/ip'

handler = request.ProxyHandler({'http': '116.192.175.124:41844'})

opener = request.build_opener(handler)
req = request.Request(url)
resp = opener.open(req)
print(resp.read())

>>>
b'{\n  "origin": "116.192.175.124"\n}\n'
```







### 常用代理

**代理IP价格对比** （2017.7.1）

| **代理网站**                                                 | **网址**                                                     | **包天** | **包周** | **包月** | **包年** | **代理****IP****展示** | **备注** | **推荐指数** |
| ------------------------------------------------------------ | ------------------------------------------------------------ | -------- | -------- | -------- | -------- | ---------------------- | -------- | ------------ |
| [**米扑代理**](http://proxy.mimvp.com/price.php)             | [proxy.mimvp.com](https://proxy.mimvp.com/)                  | **5**    | **20**   | **60**   | **500**  | Y                      |          | 五星         |
| [**快代理**](http://www.kuaidaili.com/pricing/)              | [www.kuaidaili.com](http://www.kuaidaili.com/)               | 10       | 30       | 100      | 1000     | Y                      |          | 四星         |
| [**大象代理**](http://www.daxiangdaili.com/) [**西刺代理**](http://www.xicidaili.com/) | [www.daxiangdaili.com](http://www.daxiangdaili.com/) [www.xicidaili.com](http://www.xicidaili.com/) | 9        | 63       | 98       | 1176     | Y                      |          | 三星         |
| [**站大爷**](http://ip.zdaye.com/buy.html)                   | [ip.zdaye.com](http://ip.zdaye.com/)                         | 8        | 56       | 80       | 720      | Y                      |          | 四星         |
| [**讯代理**](http://www.xdaili.cn/priceproxy.html)           | [www.xdaili.cn](http://www.xdaili.cn/)                       | 9        | 63       | 210      | 2100     | Y                      |          | 三星         |
| [**阿布云**](http://www.abuyun.com/%23pricing)               | [www.abuyun.com](http://www.abuyun.com/)                     | 20       | 128      | 499      | 5988     |                        |          | 三星         |
| [**蚂蚁代理**](http://www.mayidaili.com/dynamic%23p6)        | [www.mayidaili.com](http://www.mayidaili.com/)               | 3        | 21       | 90       | 1095     | Y                      |          | 一星         |
| [**360****代理**](http://www.swei360.com/pricing/)           | [www.swei360.com](http://www.swei360.com/)                   | 10       | 30       | 100      | 1000     | Y                      | 同快代理 | 三星         |
| [**云代理**](http://www.ip3366.net/pricing/)                 | [www.ip3366.net](http://www.ip3366.net/)                     | 6        | 30       | 80       | 399      | Y                      | 同快代理 | 三星         |
| [**代理云**](http://www.dailiyun.com/price.html)             | [www.dailiyun.com](http://www.dailiyun.com/)                 | 10       | 70       | 600      | 3650     |                        |          | 二星         |
| [**流年代理**](http://www.89ip.cn/)                          | [www.89ip.cn](http://www.89ip.cn/)                           | **0**    | **0**    | **0**    | **0**    | Y                      | 同云代理 | 三星         |
| [**无忧代理**](http://www.data5u.com/buy/dynamic.html)       | [www.data5u.com](http://www.data5u.com/)                     | 10       | 60       | 160      | 1250     | Y                      |          | 三星         |
| [**全网代理**](http://www.goubanjia.com/buy/index.shtml)     | [www.goubanjia.com](http://www.goubanjia.com/)               | 6        | 30       | 65       | 410      | Y                      |          | 三星         |
| [**芝麻代理**](http://ip.mengdie.com/pay/)                   | [ip.mengdie.com](http://ip.mengdie.com/)                     | 6        | 42       | 180      | 1554     |                        |          | 二星         |
| [**IPRENT**](http://www.iprent.cn/)                          | [www.iprent.cn](http://www.iprent.cn/)                       | 19       | 133      | 570      | 1140     |                        |          | 一星         |
| [**虎头代理**](http://www.hutoudaili.com/)                   | [www.hutoudaili.com](http://www.hutoudaili.com/)             | 20       | 140      | 200      | 2400     |                        |          | 三星         |
| [**AWMProxy**](http://awmproxy.net/modules/socks/)           | [awmproxy.net](http://awmproxy.net/)                         | 15       | 102      | 646      | 71175    | Y                      |          | 四星         |
| [**ProxyKey**](https://www.proxykey.com/buy)                 | [www.proxykey.com](https://www.proxykey.com/)                | 4        | 28       | 115      | 1380     |                        |          | 二星         |
| [**HideMy**](http://hidemy.name/)                            | [hidemy.name](http://www.apple.com/)                         | **6**    | **42**   | **55**   | **224**  | Y                      |          | 二星         |
| [**HideMyAss**](http://www.hidemyass.com/)                   | [www.hidemyass.com](http://www.hidemyass.com/)               | 2.6      | 18       | 78       | 535      | Y                      |          | 二星         |



## cookie

用于二次访问目标网站，调用其数据判断当前用户是谁



### 格式

```
Set-Cookie: NAME=VALUE: Expires/Max-age=DATE: Path=PATH: Domain=DOMAIN_NAME: SECURE
```

> SECURE: 是否只在https协议下起作用



### 保存cookie 到本地

```
from urllib import request
from http.cookiejar import MozillaCookieJar

cookiejar = MozillaCookieJar('cookie.txt')
handler = request.HTTPCookieProcessor(cookiejar)
opener = request.build_opener(handler)

headers = {
    "user-agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Iridium/2017.11 Safari/537.36 Chrome/62.0.3202.94"
}

req = request.Request('http://httpbin.org/cookies/set?course-abc', headers=headers)
resp = opener.open(req)
print(resp.read())
cookiejar.save(ignore_discard=True, ignore_expires=True)
```

### 读取本地cookie

```
from urllib import request
from http.cookiejar import MozillaCookieJar

cookiejar = MozillaCookieJar('cookie.txt')
cookiejar.load(ignore_discard=True)
handler = request.HTTPCookieProcessor(cookiejar)
opener = request.build_opener(handler)

headers = {
    "user-agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Iridium/2017.11 Safari/537.36 Chrome/62.0.3202.94"
}

req = request.Request('http://httpbin.org/cookies/set?course-abc', headers=headers)
resp = opener.open(req)
print(resp.read())
```



### HTTPCookieProcessor

通过手动输入cookie 访问目标网站

```
from urllib import request

headers = {
    "user-agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Iridium/2017.11 Safari/537.36 Chrome/62.0.3202.94",
    'cookie': '__utmt=1; _ga=GA1.2.417365795.1537089875; _gat=1; csrftoken=bzizAWyaMDl8vfW6sjILtBgIGXTcP61p; auth_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6Imdob3N0bHltYW4iLCJkZXZpY2UiOjAsImlzX3N0YWZmIjpmYWxzZSwiaWQiOjMxNjY2MTk4LCJleHAiOjE1Mzc5NTQ0MDF9.QeQ8APR_xDJ-Jqo3AEWaeyJqtlZ-cna6tHiIaXRCExA; sessionid=".eJyrVopPLC3JiC8tTi2KT0pMzk7NS1GyUkrOz83Nz9MDS0FFi_V885Myc1J98tMz85ygKnWQtWcCdRobmpmZGVpa1AIA3GYf6A:1g1TQf:FaI9o6QbO8SG0to634ZnUiN3NVM"; __utma=183787513.417365795.1537089875.1537089875.1537089875.1; __utmb=183787513.5.10.1537089875; __utmc=183787513; __utmz=183787513.1537089875.1.1.utmcsr=(direct)|utmccn=(direct)|utmcmd=(none); userid=31666198'
}

url = 'https://www.shanbay.com/web/users/mine/zone'

req = request.Request(url, headers=headers)
resp = request.urlopen(req)
print(resp.read().decode('utf-8'))
```



### http.cookiejar



#### CookieJar

管理HTTP cookie 值，存储HTTP请求生存的cookie，像传出的HTTP请求添加cookie的对象。整个cookie 都存储在内存中，对CookieJar实例进行垃圾回收后cookie也将丢失



* Below is diswork.

```
from urllib import request, parse
from http.cookiejar import CookieJar


headers = {
    "user-agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Iridium/2017.11 Safari/537.36 Chrome/62.0.3202.94"
}


def get_opener():
    cookiejar = CookieJar()
    handler = request.HTTPCookieProcessor(cookiejar)
    opener = request.build_opener(handler)
    return opener


def login_shanbay(opener):
    data = {
        'username': '',
        'password': ''
    }

    login_url = 'https://web.shanbay.com/web/account/login'

    req = request.Request(login_url, data=parse.urlencode(data).encode('utf-8'), headers=headers)
    request.urlopen(req)


def person_profile(opener):

    personal_url = 'https://web.shanbay.com/web/users/31666198/zone'

    req = request.Request(personal_url, headers=headers)
    resp = opener.open(req)

    with open('shanbay.html', 'w', encoding='utf-8') as f:
        f.write(resp.read().decode('utf-8'))


if __name__ == '__main__':
    opener = get_opener()
    login_shanbay(opener)
    person_profile(opener)

```





#### FileCookieJar

FileCookieJar(filename, delayload=None, policy=None)

哟哦你过来创建FileCookieJar实例，检索cookie信息并将cookie存储到文件中。

filename是存储cookie的文件名

delayload为True时，支持延迟访问文件，只有在需要的时候才读取



#### MozillaCookieJar

MozillaCookieJar(filename, delayload=None, policy=None)

创建于Mozilla兼容的cookie.txt



#### LWPCookieJar

LWPCookieJar(filename, delayload=None, policy=None)

创建于libwww-perl标准的Set-Cookie3文件格式兼容的FileCookieJar实例





