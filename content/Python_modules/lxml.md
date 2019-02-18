---
title: "lxml"
date: 2018-09-23 17:29
---


[TOC]

# lxml

lxml 是 一个HTML/XML的解析器，主要的功能是如何解析和提取 HTML/XML 数据
lxml和正则一样，也是用 C 实现的，是一款高性能的 Python HTML/XML 解析器



## 安装	

http://lxml.de

```
pip install lxml
```



## 解析html字符串

我们可以利用他来解析HTML代码，并且在解析HTML代码的时候，如果HTML代码不规范，他会自动的进行补全。

```
from lxml import etree

text = '''
<div>
    <ul>
         <li class="item-0"><a href="link1.html">first item</a></li>
         <li class="item-1"><a href="link2.html">second item</a></li>
         <li class="item-inactive"><a href="link3.html">third item</a></li>
         <li class="item-1"><a href="link4.html">fourth item</a></li>
         <li class="item-0"><a href="link5.html">fifth item</a> # 注意，此处缺少一个 </li> 闭合标签
     </ul>
 </div>
'''

#利用etree.HTML，将字符串解析为HTML文档
html = etree.HTML(text)

# 按字符串序列化HTML文档
result = (etree.tostring(html, encoding='utf-8').decode('utf-8'))

print(result)

>>>
<html><body><div>
    <ul>
         <li class="item-0"><a href="link1.html">first item</a></li>
         <li class="item-1"><a href="link2.html">second item</a></li>
         <li class="item-inactive"><a href="link3.html">third item</a></li>
         <li class="item-1"><a href="link4.html">fourth item</a></li>
         <li class="item-0"><a href="link5.html">fifth item</a> # 注意，此处缺少一个 </li> 闭合标签
     </ul>
 </div>
</body></html>
```



## 解析html文件

除了直接使用字符串进行解析，lxml还支持从文件中读取内容。我们新建一个hello.html文件：

```
<!-- hello.html -->
<div>
    <ul>
         <li class="item-0"><a href="link1.html">first item</a></li>
         <li class="item-1"><a href="link2.html">second item</a></li>
         <li class="item-inactive"><a href="link3.html"><span class="bold">third item</span></a></li>
         <li class="item-1"><a href="link4.html">fourth item</a></li>
         <li class="item-0"><a href="link5.html">fifth item</a></li>
     </ul>
 </div>
```

```
from lxml import etree

html = etree.HTML('hello.html')
result = etree.tostring(html, pretty_print=True, encoding='utf-8').decode('utf-8')
print(result)

>>>
<!-- hello.html -->
<div>
    <ul>
         <li class="item-0"><a href="link1.html">first item</a></li>
         <li class="item-1"><a href="link2.html">second item</a></li>
         <li class="item-inactive"><a href="link3.html"><span class="bold">third item</span></a></li>
         <li class="item-1"><a href="link4.html">fourth item</a></li>
         <li class="item-0"><a href="link5.html">fifth item</a></li>
     </ul>
 </div>
```



### 标签不正常返回正常

创建HTML解析器解析

```
<!-- hello.html -->
<div>
    <ul>
         <li class="item-0"><a href="link1.html">first item</a></li>
         <li class="item-1"><a href="link2.html">second item</a>
     </ul>
 </div>
```

```
from lxml import etree

parser = etree.HTMLParser(encoding='utf-8')
htmlElement = etree.parse('hello.html', parser=parser)
result = etree.tostring(htmlElement, pretty_print=True, encoding='utf-8').decode('utf-8')
print(result)

>>>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN" "http://www.w3.org/TR/REC-html40/loose.dtd">
<!-- hello.html -->
<html>
  <body>
    <div>
    <ul>
         <li class="item-0"><a href="link1.html">first item</a></li>
         <li class="item-1"><a href="link2.html">second item</a>
     </li></ul>
 </div>
  </body>
</html>
```



## Example 

### douban 电影

```
import requests
from lxml import etree

headers = {
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.92 Safari/537.36',
    'Referer': 'https://movie.douban.com/'
}

url = 'https://movie.douban.com/cinema/nowplaying/beijing/'
response = requests.get(url, headers=headers)
text = response.text

html = etree.HTML(text)

ul = html.xpath("//ul[@class='lists']")[0]
# print(etree.tostring(ul, encoding='utf-8').decode('utf-8')) # 所有正在上映电影的信息

movies = []
lis = ul.xpath("./li")
for li in lis:
    # print(etree.tostring(li, encoding='utf-8').decode('utf-8'))
    title = li.xpath("@data-title")[0]
    score = li.xpath("@data-score")[0]
    duration = li.xpath("@data-duration")[0]
    region = li.xpath("@data-region")[0]
    director = li.xpath("@data-director")[0]
    actors = li.xpath("@data-actors")[0]
    thumbnail = li.xpath(".//img/@src")
    movie = {
        "title": title,
        "score": score,
        "duration": duration,
        "region": region,
        "director": director,
        "actors": actors,
        "thumbnail": thumbnail
    }
    movies.append(movie)

print(movies)
```



### dytt8.net

```
from lxml import etree
import requests


HEADERS = {
    "user-agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Iridium/2017.11 Safari/537.36 Chrome/62.0.3202.94"
}

BASE_DOMAIN = 'http://dytt8.net'


def get_detail_urls(url):
    response = requests.get(url, headers=HEADERS)
    # text = (response.content.decode('gbk'))  # page source 查看编码为gbk2312, 第三页有问题，无法解码
    text = response.text

    html = etree.HTML(text)

    detail_urls = html.xpath("//table[@class='tbspan']//a/@href")
    detail_urls = map(lambda url:BASE_DOMAIN+url, detail_urls)
    return detail_urls


def parse_detail_page(url):
    movie = {}
    response = requests.get(url, headers=HEADERS)
    text = response.content.decode('gbk')
    html = etree.HTML(text)

    title = html.xpath("//div[@class='title_all']//font[@color='#07519a']/text()")[0]
    movie['title'] = title

    zoomElement = html.xpath("//div[@id='Zoom']")[0]
    images = zoomElement.xpath(".//img/@src")
    cover = images[0]
    screenshot = images[1]
    movie['cover'] = cover
    movie['screenshot'] = screenshot

    def parse_info(info, rule):
        return info.replace(rule, "").strip()

    infos = zoomElement.xpath(".//text()")
    for index, info in enumerate(infos):
        if info.startswith('◎年　　代'):
            info = parse_info(info, "◎年　　代")
            movie['year'] = info
        elif info.startswith('◎产　　地'):
            info = parse_info(info, "◎产　　地")
            movie['country'] = info
        elif info.startswith('◎豆瓣评分'):
            info = parse_info(info, "◎豆瓣评分")
            movie['douban_rating'] = info
        elif info.startswith('◎导　　演'):
            info = parse_info(info, "◎导　　演")
            movie['director'] = info
        elif info.startswith('◎主　　演'):
            info = parse_info(info, "◎主　　演")
            actors = [info]
            for x in range(index+1, len(infos)):
                actor = infos[x].strip()
                if actor.startswith('◎'):
                    break
                actors.append(actor)
            movie['actors'] = actors
        elif info.startswith('◎简　　介'):
            info = parse_info(info, '◎简　　介')
            for x in range(index+1, len(infos)):
                profile = infos[x].strip()
                movie['profile'] = profile

    download_url = html.xpath("//td[@bgcolor='#fdfddf']/a/@href")[0]
    movie['download_url'] = download_url
    return movie


def spider():
    base_url = 'http://dytt8.net/html/gndy/dyzz/list_23_{}.html'
    movies = []
    for x in range(1, 8):
        url = base_url.format(x)
        detail_urls = get_detail_urls(url)
        for detail_url in detail_urls:
            movie = parse_detail_page(detail_url)
            movies.append(movie)
            print(movies)


if __name__ == '__main__':
    spider()
```



### doutula.com (多线程处理)

```
import requests
from lxml import etree
from urllib import request
import os
import re
from queue import Queue
import threading


class Producer(threading.Thread):

    headers = {
        'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Iridium/2017.11 Safari/537.36 Chrome/62.0.3202.94'
    }

    def __init__(self, page_queue, img_queue, *args, **kwargs):
        super(Producer, self).__init__(*args, **kwargs)
        self.page_queue = page_queue
        self.img_queue = img_queue

    def run(self):
        while True:
            if self.page_queue.empty():
                break
            url = self.page_queue.get()
            self.parse_page(url)

    def parse_page(self, url):
        response = requests.get(url, headers=self.headers)
        text = response.text
        html = etree.HTML(text)
        imgs = html.xpath("//div[@class='page-content text-center']//img[@class!='gif']")
        for img in imgs:
            img_url = img.get('data-original')
            alt = img.get('alt')
            alt = re.sub(r'[\?？\.。,，!！*]', '', alt) # 替换不可能用作命名的特殊字符
            suffix = os.path.splitext(img_url)[1]
            filename = alt + suffix
            self.img_queue.put((img_url, filename))


class Consumer(threading.Thread):

    def __init__(self, page_queue, img_queue, *args, **kwargs):
        super(Consumer, self).__init__(*args, **kwargs)
        self.page_queue = page_queue
        self.img_queue = img_queue

    def run(self):
        while True:
            if self.img_queue.empty() and self.page_queue.empty():
                break
            img_url, filename = self.img_queue.get()
            request.urlretrieve(img_url, 'images/' + filename)
            print(filename + ' has downloaded.')


def main():
    page_queue = Queue(100)
    img_queue = Queue(500)

    for i in range(1, 101):
        url = 'http://www.doutula.com/photo/list/?page=%d' % i
        page_queue.put(url)

    for i in range(5):
        t = Producer(page_queue, img_queue)
        t.start()

    for i in range(5):
        t = Consumer(page_queue, img_queue)
        t.start()


if __name__ == '__main__':
    main()
```



### lagou.com (Deprecated)

```
import requests
from lxml import etree
import time
import re


headers = {
    "Referer": "https://www.lagou.com/jobs/list_python?labelWords=&fromSearch=true&suginput=",
    "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Iridium/2017.11 Safari/537.36 Chrome/62.0.3202.94"
}


def request_list_page():
    url = 'https://www.lagou.com/jobs/positionAjax.json?city=%E5%8C%97%E4%BA%AC&needAddtionalResult=false'
    data = {
        "first": "true",
        "pn": 1,
        "kd": "python"
    }

    for x in range(1, 10):
        data['pn'] = x
        response = requests.post(url, headers=headers, data=data)
        result = response.json()
        positions = result['content']['positionResult']['result']
        for position in positions:
            positionId = position['positionId']
            position_url = 'https://www.lagou.com/jobs/%s.html' % positionId
            parse_position_detail(position_url)
            break
        break


def parse_position_detail(url):
    response = requests.get(url, headers=headers)
    text = response.text
    html = etree.HTML(text)
    position_name = html.xpath("//span[@class='name']/text()")[0]
    job_request_spans = html.xpath("//dd[@class='job_request']//span")
    salary = job_request_spans[0].xpath('.//text()')[0].strip()
    city = job_request_spans[1].xpath('./text()')[0].strip()
    city = re.sub(r'[\s/]', '', city) # 替换斜杠
    work_years = job_request_spans[2].xpath('.//text()')[0].strip()
    work_years = re.sub(r'[\s/]', '', work_years)
    education = job_request_spans[3].xpath('.//text()')[0].strip()
    education = re.sub(r'[\s/]', '', education)

    job_desc = ''.join(html.xpath("//dd[@class='job_bt']//text()")).strip()


def main():
    request_list_page()


if __name__ == '__main__':
    main()
```

