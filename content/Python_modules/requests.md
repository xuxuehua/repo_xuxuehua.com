---
title: "requests"
date: 2018-07-28 02:43
---

[TOC]



# requests



## 安装

* pip 安装

```
pip install requests
```



* 源码安装

```
git clone git://github.com/requests/requests.git
cd requests
pip install .
```



## 请求 requests

### post

默认情况下，在post方法中使用 data 关键字参数，Requests的行为会表现的像是在发送一个html表单，比如

```
>>> payload = {'key1': 'value1', 'key2': 'value2'}

>>> r = requests.post("http://httpbin.org/post", data=payload)
>>> print(r.text)
{
  ...
  "form": {
    "key2": "value2",
    "key1": "value1"
  },
  ...
}
```

如果需要为多个元素使用同一key的话，可以在data中传入元组，比如

```
>>> payload = (('key1', 'value1'), ('key1', 'value2'))
>>> r = requests.post('http://httpbin.org/post', data=payload)
>>> print(r.text)
{
  ...
  "form": {
    "key1": [
      "value1",
      "value2"
    ]
  },
  ...
}
```

如果 data 中传的是string而不是dict的话，那么字符串将会被直接发送出去

```
>>> import json

>>> url = 'https://api.github.com/some/endpoint'
>>> payload = {'some': 'data'}

>>> r = requests.post(url, data=json.dumps(payload))
```

因为现在很多api接受json字符串格式的post data，Requests为这种方式提供了更简单的写法

```
>>> url = 'https://api.github.com/some/endpoint'
>>> payload = {'some': 'data'}

>>> r = requests.post(url, json=payload)
```



#### Ajax.json 

```
import requests

data = {
    'first': 'true',
    'pn': '1',
    'kd': 'python'
}

headers = {
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Iridium/2017.11 Safari/537.36 Chrome/62.0.3202.94',
    'Referer': 'https://www.lagou.com/jobs/list_python?labelWords=&fromSearch=true&suginput='
}

url = 'https://www.lagou.com/jobs/positionAjax.json?city=%E5%8C%97%E4%BA%AC&needAddtionalResult=false'

response = requests.post(url, headers=headers, data=data)
print(response.json())
```

> headers 里面的Referer 是必须的
>
> url是Ajax.json 生成的



#### 上传文件

最简单的方式

```
>>> url = 'http://httpbin.org/post'
>>> files = {'file': open('report.xls', 'rb')}

>>> r = requests.post(url, files=files)
>>> r.text
{
  ...
  "files": {
    "file": "<censored...binary...data>"
  },
  ...
}
```

你可以显式地设置文件名，文件类型和请求头：

```
>>> url = 'http://httpbin.org/post'
>>> files = {'file': ('report.xls', open('report.xls', 'rb'), 'application/vnd.ms-excel', {'Expires': '0'})}

>>> r = requests.post(url, files=files)
>>> r.text
{
  ...
  "files": {
    "file": "<censored...binary...data>"
  },
  ...
}
```



### get

无参数的调用

```
import requests

response = requests.get('http://www.xuxuehua.com')
print(response)
```

在Requests里，我们一般使用`params`关键字参数的方式，传入dict来传递url参数。比如，假设我们想传递`key1=value1`和`key2=value2`这2个参数，我们可以用下面的代码

```
>>> payload = {'key1': 'value1', 'key2': 'value2'}
>>> r = requests.get('http://httpbin.org/get', params=payload)
```

可以通过url方法来查看requests帮我们构造的编码后的url

```
>>> print(r.url)
http://httpbin.org/get?key2=value2&key1=value1
```

我们还可以为同一个key传递一组数据，比如

```
>>> payload = {'key1': 'value1', 'key2': ['value2', 'value3']}

>>> r = requests.get('http://httpbin.org/get', params=payload)
>>> print(r.url)
http://httpbin.org/get?key1=value1&key2=value2&key2=value3
```

#### 返回值

```
import requests

response = requests.get('http://www.xuxuehua.com')
print(response.text) # 返回unicode格式数据,是str类型, 可以指定解码方式
print(response.content) # 返回字节流数据，即原生字符串，就是从网页直接抓下来的，没有经过任何解码，bytes类型
print(response.url) # 返回完整url
print(response.encoding) # 返回响应头部字节编码
print(response.status_code) # 返回响应码
```



#### 下载图片

```
def download_file(url):
    print('Downding %s' %url)
    local_filename = url.split('/')[-1]
    r = requests.get(url, stream=True)
    with open(local_filename, 'wb') as f:
        for chunk in r.iter_content(chunk_size=1024):
            if chunk:
                f.write(chunk)
                f.flush()
    return local_filename
```



#### proxy 处理

```
import requests

proxy = {
    'http': '210.73.202.121:53281'
}

url = 'http://httpbin.org/ip'

response = requests.get(url, proxies=proxy)
print(response.text)

>>>
{
  "origin": "210.73.202.121"
}
```



#### 不信任的SSL证书

```
resp = requests.get(url, verify=False)
```







### 重定向

默认情况下，除了HEAD请求, Requests 会自动处理所有重定向。

如果你使用的是GET、OPTIONS、POST、PUT、PATCH 或者 DELETE，那么你可以通过 allow_redirects 参数禁用重定向处理：

```
>>> r = requests.get('http://github.com', allow_redirects=False)
>>> r.status_code
301
>>> r.history
[]
```

可以使用`history`方法来追踪重定向

```
>>> r = requests.get('http://github.com')

>>> r.url
'https://github.com/'

>>> r.status_code
200

>>> r.history
[<Response [301]>]
```

如果你使用了 HEAD 方法，你也可以启用重定向：

```
>>> r = requests.head('http://github.com', allow_redirects=True)
>>> r.url
'https://github.com/'
>>> r.history
[<Response [301]>]
```







### 自定义请求的headers

有时候我们需要自定义一些请求的headers，比如我们可能需要将jwt的token放到headers里以完成鉴权。

```
>>> url = 'https://api.github.com/some/endpoint'
>>> headers = {'user-agent': 'my-app/0.0.1'}

>>> r = requests.get(url, headers=headers)
```

 

### timeout 超时时间

使用timeout 参数可以设定等待连接的秒数，如果等待超时，Requests会抛出异常

```
>>> requests.get('http://github.com', timeout=0.001)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
requests.exceptions.Timeout: HTTPConnectionPool(host='github.com', port=80): Request timed out. (timeout=0.001)
```

timeout 仅对连接过程有效，与响应体的下载无关。 timeout 并不是整个下载响应的时间限制，而是如果服务器在 timeout 秒内没有应答，将会引发一个异常（更精确地说，是在 timeout 秒内没有从基础套接字上接收到任何字节的数据时)。



## headers

```
headers = {
    "user-agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Iridium/2017.11 Safari/537.36 Chrome/62.0.3202.94"
}
```





## 解析响应

### 获取响应的状态码

使用`status_code` 就可以了。

```
>>> r = requests.get('http://httpbin.org/get')
>>> r.status_code
200
```

为方便引用，Requests还附带了一个内置的状态码查询对象：

```
>>> r.status_code == requests.codes.ok
True
```

如果发送了一个错误请求(一个 4XX 客户端错误，或者 5XX 服务器错误响应)，我们可以通过 Response.raise_for_status() 来抛出异常：

```
>>> bad_r = requests.get('http://httpbin.org/status/404')
>>> bad_r.status_code
404

>>> bad_r.raise_for_status()
Traceback (most recent call last):
  File "requests/models.py", line 832, in raise_for_status
    raise http_error
requests.exceptions.HTTPError: 404 Client Error
```

如果在测试用例中直接使用`raise_for_status()`方法，如果有异常抛出的话，用例会自动失败



### json

可以把json字符串转成python的dict数据类型

```
In [2]: import requests

In [3]: r = requests.get('https://api.github.com/events')

In [4]: r.json()
Out[4]:
[{'actor': {'avatar_url': 'https://avatars.githubusercontent.com/u/37750057?',
   'display_login': 'lazar-eric2',
   'gravatar_id': '',
   'id': 37750057,
   .....
   ......
```

> 需要引起注意的是r.json()解析成功了并不代表请求是成功的。很多服务器在响应失败的时候也会返回json字符串，如果要判断响应的状态的话，建议使用`r.raise_for_status()` 或者使用`check r.status_code`来判断返回值是否符合预期



### jsonp 格式

```
import re
import json

spot_instance_prices_url = "https://website.spot.ec2.aws.a2z.com/spot.js"

r.text = _jsonp

def loads_jsonp(_jsonp):
    try:
        return json.loads(re.match(".*?({.*}).*",_jsonp,re.S).group(1))
    except:
	raise ValueError('Invalid Input')
```









### text

`text` 方法会拿到请求的响应内容，比如以再github的timeline接口为例

```
>>> import requests

>>> r = requests.get('https://api.github.com/events')
>>> r.text
u'[{"repository":{"open_issues":0,"url":"https://github.com/...
```

Requests会自动去解码server端返回的内容，大部分的unicode字符集都会被无缝解码。

当你发送请求的时候，Requests会根据HTTP headers来推断编码，并在`r.text`调用的时候使用。我们可以查看和修改Requests的编码，比如

```
>>> r.encoding
'utf-8'
>>> r.encoding = 'ISO-8859-1'
```



### 二进制

有时候服务器返回的数据不是文本的，而是二进制的，这时候我们就需要处理二进制的内容。

Requests中可以使用content方法来返回二进制的响应内容，比如

```
>>> r.content
b'[{"repository":{"open_issues":0,"url":"https://github.com/...
```

如果响应是gzip压缩过的，Requests会自动解压。

下面这个例子演示了如何从服务器返回的二进制内容创建图片。

```
>>> from PIL import Image
>>> from io import BytesIO

>>> i = Image.open(BytesIO(r.content))
```



### header

`r.headers` 可以拿到Python 字典形式展示的服务器响应头：

```
>>> r.headers
{
    'content-encoding': 'gzip',
    'transfer-encoding': 'chunked',
    'connection': 'close',
    'server': 'nginx/1.0.4',
    'x-runtime': '148ms',
    'etag': '"e1ca502697e5c9317743dc078f67693f"',
    'content-type': 'application/json'
}
```

但是这个字典比较特殊：它是仅为 HTTP 头部而生的。根据[RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)， HTTP 头部是大小写不敏感的。

因此，我们可以使用任意大写形式来访问这些响应头字段：

```
>>> r.headers['Content-Type']
'application/json'

>>> r.headers.get('content-type')
'application/json'
```



#### user-agent (爬虫需更改)

```
headers = {
    'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36'
}

requests.get(url, headers=headers).text
```









### cookie

如果某个响应中包含一些 cookie，你可以快速访问它们：

```
>>> url = 'http://example.com/some/cookie/setting/url'
>>> r = requests.get(url)

>>> r.cookies['example_cookie_name']
'example_cookie_value'
```

要想发送你的cookies到服务器，可以使用 cookies 参数：

```
>>> url = 'http://httpbin.org/cookies'
>>> cookies = dict(cookies_are='working')

>>> r = requests.get(url, cookies=cookies)
>>> r.text
'{"cookies": {"cookies_are": "working"}}'
```

Cookie 的返回对象为 [RequestsCookieJar](http://docs.python-requests.org/zh_CN/latest/api.html#requests.cookies.RequestsCookieJar)，它的行为和字典类似，但界面更为完整，适合跨域名跨路径使用。你还可以把 Cookie Jar 传到 Requests 中：

```
>>> jar = requests.cookies.RequestsCookieJar()
>>> jar.set('tasty_cookie', 'yum', domain='httpbin.org', path='/cookies')
>>> jar.set('gross_cookie', 'blech', domain='httpbin.org', path='/elsewhere')
>>> url = 'http://httpbin.org/cookies'
>>> r = requests.get(url, cookies=jar)
>>> r.text
'{"cookies": {"tasty_cookie": "yum"}}'
```



#### 获取cookie value

```
import requests

url = 'http://www.baidu.com/'

response = requests.get(url)
print(response.cookies.get_dict())
```

 

#### session

共享cookie免登录

```
import requests

url = 'https://www.shanbay.com/api/v1/account/login/web/'
zone_url = 'https://www.shanbay.com/checkin/user/xxxxx/'

headers = {
    'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Iridium/2017.11 Safari/537.36 Chrome/62.0.3202.94'
}

data = {"username":"xxx","password":"xxx"}

session = requests.Session()
session.put(url, headers=headers, data=data)

response = session.get(zone_url)

with open('shanbay.html', 'w', encoding='utf-8') as f:
    f.write(response.text)

```







# exception 处理

All exceptions that Requests explicitly raises inherit from 

```
requests.exceptions.RequestException
```



 catch the base-class exception, which will handle all cases:

```py
try:
    r = requests.get(url, params={'s': thing})
except requests.exceptions.RequestException as e:  # This is the correct syntax
    raise SystemExit(e)
```

 can catch them separately and do different things.

```py
try:
    r = requests.get(url, params={'s': thing})
except requests.exceptions.Timeout:
    # Maybe set up for a retry, or continue in a retry loop
except requests.exceptions.TooManyRedirects:
    # Tell the user their URL was bad and try a different one
except requests.exceptions.RequestException as e:
    # catastrophic error. bail.
    raise SystemExit(e)
```



```
url='http://www.google.com/blahblah'

try:
    r = requests.get(url,timeout=3)
    r.raise_for_status()
except requests.exceptions.HTTPError as errh:
    print ("Http Error:",errh)
except requests.exceptions.ConnectionError as errc:
    print ("Error Connecting:",errc)
except requests.exceptions.Timeout as errt:
    print ("Timeout Error:",errt)
except requests.exceptions.RequestException as err:
    print ("OOps: Something Else",err)

Http Error: 404 Client Error: Not Found for url: http://www.google.com/blahblah
```



## 401

If you want http errors (e.g. 401 Unauthorized) to raise exceptions, you can call [`Response.raise_for_status`](https://requests.readthedocs.io/en/latest/api/#requests.Response.raise_for_status). That will raise an `HTTPError`, if the response was an http error.

```
try:
    r = requests.get('http://www.google.com/nothere')
    r.raise_for_status()
except requests.exceptions.HTTPError as err:
    raise SystemExit(err)
    
>>>
SystemExit: 404 Client Error: Not Found for url: http://www.google.com/nothere
```







