---
title: "scrapy"
date: 2018-10-02 18:24
---


[TOC]


# scrapy

Scrapy 将一些东西封装好，在上面写爬虫可以变的更加高效



## 架构图

![img](https://cdn.pbrd.co/images/HGzycG9.png)

- **引擎(Scrapy)**
  用来处理整个系统的数据流处理, 触发事务(框架核心)
- **调度器(Scheduler)**
  用来接受引擎发过来的请求, 压入队列中, 并在引擎再次请求的时候返回. 可以想像成一个URL（抓取网页的网址或者说是链接）的优先队列, 由它来决定下一个要抓取的网址是什么, 同时去除重复的网址
- **下载器(Downloader)**
  用于下载网页内容, 并将网页内容返回给蜘蛛(Scrapy下载器是建立在twisted这个高效的异步模型上的)
- **爬虫(Spiders)**
  爬虫是主要干活的, 用于从特定的网页中提取自己需要的信息, 即所谓的实体(Item)。用户也可以从中提取出链接,让Scrapy继续抓取下一个页面
- **项目管道(Pipeline)**
  负责处理爬虫从网页中抽取的实体，主要的功能是持久化实体、验证实体的有效性、清除不需要的信息。当页面被爬虫解析后，将被发送到项目管道，并经过几个特定的次序处理数据。
- **下载器中间件(Downloader Middlewares)**
  位于Scrapy引擎和下载器之间的框架，主要是处理Scrapy引擎与下载器之间的请求及响应。
- **爬虫中间件(Spider Middlewares)**
  介于Scrapy引擎和爬虫之间的框架，主要工作是处理蜘蛛的响应输入和请求输出。



### 运行流程

1. 引擎从调度器中取出一个链接(URL)用于接下来的抓取
2. 引擎把URL封装成一个请求(Request)传给下载器
3. 下载器把资源下载下来，并封装成应答包(Response)
4. 爬虫解析Response
5. 解析出实体（Item）,则交给实体管道进行进一步的处理
6. 解析出的是链接（URL）,则把URL交给调度器等待抓取





## 安装

```
pip install scrapy
```





## 创建项目

```
scrapy startproject PROJECT_NAME
```



会生成如下目录结构

```
my_first_scrapy/
├── my_first_scrapy
│   ├── __init__.py
│   ├── __pycache__
│   ├── items.py
│   ├── middlewares.py
│   ├── pipelines.py
│   ├── settings.py
│   └── spiders
│       ├── __init__.py
│       └── __pycache__
└── scrapy.cfg
```

> scrapy.cfg  项目的主配置信息。（真正爬虫相关的配置信息在settings.py文件中）
> items.py    设置数据存储模板，用于结构化数据，如：Django的Model
> pipelines    数据处理行为，如：一般结构化的数据持久化
> settings.py 配置文件，如：递归的层数、并发数，延迟下载等
> spiders      爬虫目录，如：创建文件，编写爬虫规则





### 前期准备

设置settings.py

关闭robotstxt

```
ROBOTSTXT_OBEY = False
```

默认请求头

```
DEFAULT_REQUEST_HEADERS = {
    'accept': '*/*',
    'accept-encoding': 'gzip, deflate, br',
    'accept-language': 'en-US,en;q=0.9,zh-CN;q=0.8,zh;q=0.7,zh-TW;q=0.6',
    'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
}
```



### 创建爬虫

```
scrapy gescrapy genspider [-t template] <name> <domain>
   - 创建爬虫应用
   如：
      scrapy gensipider xxh xuxuehua.com
      scrapy gensipider -t xmlfeed autohome autohome.com.cn
   PS:
      查看所有命令：scrapy gensipider -l
      查看模板命令：scrapy gensipider -d 模板名称
```



### 调用爬虫

```
scrapy crawl 爬虫应用名称
   - 运行单独爬虫应用
   如:
   	  scrapy crawl xxh
      scrapy crawl xxh --nolog
```

> 这里运行的应用名称不包含.py 后缀





#### start.py 调用

```
# encoding: utf-8

from scrapy import cmdline

cmdline.execute(['scrapy', 'crawl', 'name'])  # 这里的name指spider目录下的项目名称，不含.py
```





### example

#### qiushibaike.com

qsbk_spider.py

```
# -*- coding: utf-8 -*-
import scrapy
from qsbk.items import QsbkItem


class QsbkSpiderSpider(scrapy.Spider):
    name = 'qsbk_spider'
    allowed_domains = ['qiushibaike.com']
    start_urls = ['https://www.qiushibaike.com/text/page/1/']
    base_domain = "https://www.qiushibaike.com"

    def parse(self, response):
        duanzi_divs = response.xpath("//div[@id='content-left']/div")
        for duanzi_div in duanzi_divs:
            author = duanzi_div.xpath(".//h2/text()").get().strip()
            content = duanzi_div.xpath(".//div[@class='content']//text()").getall()
            content = ''.join(content).strip()
            item = QsbkItem(author=author, content=content)
            yield item
        next_url = response.xpath("//ul[@class='pagination']/li[last()]/a/@href").get()
        if not next_url:
            return
        else:
            yield scrapy.Request(self.base_domain+next_url, callback=self.parse)
```



items.py

```
# -*- coding: utf-8 -*-

# Define here the models for your scraped items
#
# See documentation in:
# https://doc.scrapy.org/en/latest/topics/items.html

import scrapy


class QsbkItem(scrapy.Item):
    author = scrapy.Field()
    content = scrapy.Field()
```



pipelines.py

```
# -*- coding: utf-8 -*-

import json
from scrapy.exporters import JsonLinesItemExporter


class QsbkPipeline(object):

    def __init__(self):
        self.fp = open('duanzi.json', 'wb')
        self.exporter = JsonLinesItemExporter(self.fp, ensure_ascii=False, encoding='utf-8')

    def open_spider(self, spider):
        print('Start crawling...')

    def process_item(self, item, spider):
        self.exporter.export_item(item)
        return item

    def close_spider(self, spider):
        self.fp.close()
        print('End crawling...')
```



settings.py

```
# -*- coding: utf-8 -*-



BOT_NAME = 'qsbk'

SPIDER_MODULES = ['qsbk.spiders']
NEWSPIDER_MODULE = 'qsbk.spiders'



# Obey robots.txt rules
ROBOTSTXT_OBEY = False


DOWNLOAD_DELAY = 1

DEFAULT_REQUEST_HEADERS = {
    'accept': '*/*',
    'accept-encoding': 'gzip, deflate, br',
    'accept-language': 'en-US,en;q=0.9,zh-CN;q=0.8,zh;q=0.7,zh-TW;q=0.6',
    'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
}

ITEM_PIPELINES = {
   'qsbk.pipelines.QsbkPipeline': 300,
}

```



start.py

```
# encoding: utf-8

from scrapy import cmdline

cmdline.execute(['scrapy', 'crawl', 'qsbk_spider'])
```





## CrawlSpider

继承自Spider，可有指定规则，满足一定的URL就可以进行爬取， 无需手动 yield Request

LinkExtractor 和 Rule 决定爬虫的具体走向



### 创建爬虫

```
scrapy genspider -c crawl [爬虫名字] [域名]
```



### LinkExtractors 链接提取器 

可以自动在所爬的页面上找到符合规则的url，实现自动爬取



### example

#### wxapp-union.com

CMD

```
scrapy startproject wxapp
cd wxapp/
scrapy genspider -t crawl wxapp_spider "wxapp-union.com"
```



wxapp_spider.py

```
# -*- coding: utf-8 -*-
import scrapy
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule
from wxapp.items import WxappItem


class WxappSpiderSpider(CrawlSpider):
    name = 'wxapp_spider'
    allowed_domains = ['wxapp-union.com']
    start_urls = ['http://www.wxapp-union.com/portal.php?mod=list&catid=2&page=1']

    rules = (
        Rule(LinkExtractor(allow=r'.+mod=list&catid=2&page=\d'), follow=True),
        Rule(LinkExtractor(allow=r'.+article-.+\.html'), callback="parse_detail", follow=False),
    )

    def parse_detail(self, response):
        title = response.xpath("//h1[@class='ph']/text()").get()
        author_p = response.xpath("//p[@class='authors']")
        author = author_p.xpath(".//a/text()").get()
        public_time = author_p.xpath(".//span/text()").get()
        article_content = response.xpath("//td[@id='article_content']//text()").getall()
        article_content = "".join(article_content).strip()
        item = WxappItem(title=title, author=author, public_time=public_time, article_content=article_content)
        yield item
```



items.py

```
# -*- coding: utf-8 -*-

import scrapy


class WxappItem(scrapy.Item):
    title = scrapy.Field()
    author = scrapy.Field()
    public_time = scrapy.Field()
    article_content = scrapy.Field()
```



pipelines.py

```
# -*- coding: utf-8 -*-

# Define your item pipelines here
#
# Don't forget to add your pipeline to the ITEM_PIPELINES setting
# See: https://doc.scrapy.org/en/latest/topics/item-pipeline.html

from scrapy.exporters import JsonLinesItemExporter


class WxappPipeline(object):

    def __init__(self):
        self.fp = open('wxjc.json', 'wb')
        self.exporter = JsonLinesItemExporter(self.fp, ensure_ascii=False, encoding='utf-8')

    def process_item(self, item, spider):
        self.exporter.export_item(item)
        return item

    def close_spider(self):
        self.fp.close()
```



settings.py

```
BOT_NAME = 'wxapp'
SPIDER_MODULES = ['wxapp.spiders']
NEWSPIDER_MODULE = 'wxapp.spiders'
ROBOTSTXT_OBEY = False
DOWNLOAD_DELAY = 1
DEFAULT_REQUEST_HEADERS = {
    'accept': '*/*',
    'accept-encoding': 'gzip, deflate, br',
    'accept-language': 'en-US,en;q=0.9,zh-CN;q=0.8,zh;q=0.7,zh-TW;q=0.6',
    'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
}
ITEM_PIPELINES = {
   'wxapp.pipelines.WxappPipeline': 300,
}
```



start.py

```
#encoding: utf-8

from scrapy import cmdline

cmdline.execute("scrapy crawl wxapp_spider".split())
```







## Scrape Shell

用于测试规则是否正确

需要在scrapy所在环境中执行



```
$ scrapy shell http://www.wxapp-union.com/article-4525-1.html


[s] Available Scrapy objects:
[s]   scrapy     scrapy module (contains scrapy.Request, scrapy.Selector, etc)
[s]   crawler    <scrapy.crawler.Crawler object at 0x1038dd048>
[s]   item       {}
[s]   request    <GET http://www.wxapp-union.com/article-4525-1.html>
[s]   response   <200 http://www.wxapp-union.com/article-4525-1.html>
[s]   settings   <scrapy.settings.Settings object at 0x105f4ae10>
[s]   spider     <WxappSpiderSpider 'wxapp_spider' at 0x107204cc0>
[s] Useful shortcuts:
[s]   fetch(url[, redirect=True]) Fetch URL and update local objects (by default, redirects are followed)
[s]   fetch(req)                  Fetch a scrapy.Request and update local objects
[s]   shelp()           Shell help (print this help)
[s]   view(response)    View response in a browser
In [1]: response.xpath("//h1[@class='ph']/text()").get()
Out[1]: '小程序挖坑之路 '

In [5]: from bs4 import BeautifulSoup

In [6]: soup = BeautifulSoup(response.text, 'lxml')

In [7]: soup.find('h1', attrs={"class": 'ph'})
Out[7]: <h1 class="ph">小程序挖坑之路 </h1>


```





## Request

### scrapy.FormRequest 

可发送post请求，指定表单数据



### Start_requests

如需要在爬虫开始时发送post 请求，应该重写'start_requests'方法，在这个方法中，发送post请求



### example

#### renren.com (login page)

cmd

```
$ scrapy startproject renren_login
$ cd renren_login/
$ scrapy genspider renren 'renren.com'
```



renren.py

```
# -*- coding: utf-8 -*-
import scrapy


class RenrenSpider(scrapy.Spider):
    name = 'renren'
    allowed_domains = ['renren.com']
    start_urls = ['http://renren.com/']

    def start_requests(self):
        """模拟登录"""
        url = 'http://www.renren.com/PLogin.do'
        data = {'email': '970138074@qq.com', 'password': 'pythonspider'}
        request = scrapy.FormRequest(url, formdata=data, callback=self.parse_page)
        yield request

    def parse_page(self, response):
        '''查看登录后，某人的主页'''
        request = scrapy.Request(url='http://www.renren.com/880151247/profile', callback=self.parse_profile)
        yield request

    def parse_profile(self, response):
        """获取个人主页内容"""
        with open('dp.html', 'w', encoding='utf-8') as fp:
            fp.write(response.text)
```



start.py

```
#encoding: utf-8

from scrapy import cmdline

cmdline.execute("scrapy crawl renren".split())
```



setting.py

```
ROBOTSTXT_OBEY = False
DOWNLOAD_DELAY = 1
DEFAULT_REQUEST_HEADERS = {
    'accept': '*/*',
    'accept-encoding': 'gzip, deflate, br',
    'accept-language': 'en-US,en;q=0.9,zh-CN;q=0.8,zh;q=0.7,zh-TW;q=0.6',
    'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
}
```





#### douban.com 

cmd

```
$ scrapy startproject douban_login
$ cd douban_login/
$ scrapy genspider douban 'douban.com'
```



douban.py

```
# -*- coding: utf-8 -*-
import scrapy
from urllib import request
from PIL import Image


class DoubanSpider(scrapy.Spider):
    name = 'douban'
    allowed_domains = ['douban.com']
    start_urls = ['https://accounts.douban.com/login']
    login_url = 'https://accounts.douban.com/login'
    profile_url = 'https://www.douban.com/people/50290734/'
    editsignature_url = 'https://www.douban.com/j/people/50290734/edit_signature'

    def parse(self, response):
        formdata = {
            'source': 'None',
            'redir': 'https://www.douban.com',
            'form_email': ' ',
            'form_password': ' ',
            'remember': 'on',
            'login': '登录'
        }
        captcha_url = response.css('img#captcha_image::attr(src)').get()
        if captcha_url:
            captcha = self.recognize_captcha(captcha_url)
            formdata['captcha-solution'] = captcha
            captcha_id = response.xpath("//input[@name='captcha-id']/@value").get()
            formdata['captcha-id'] = captcha_id

        yield scrapy.FormRequest(url=self.login_url, formdata=formdata, callback=self.parse_after_login)

    def parse_after_login(self, response):
        if response.url == 'https://www.douban.com':
            yield scrapy.Request(self.profile_url, callback=self.parse_profile)
            print('Login successful')
        else:
            print("Login Failed")

    def parse_profile(self, response):
        if response.url == self.profile_url:
            ck = response.xpath("//input[@name='ck']/@value").get()
            formdata = {
                'ck': ck,
                'signature': 'This is python signature.'
            }
            yield scrapy.FormRequest(self.editsignature_url, formdata=formdata, callback=self.parse_none)
        else:
            print('Profile signature unsuccessful')

    def parse_none(self, response):
        """默认没有callback会再次调用parse方法"""
        pass

    def recognize_captcha(self, image_url):
        request.urlretrieve(image_url, 'captcha.png')
        image = Image.open('captcha.png')
        image.show()
        captcha = input('Input captcha: ')
        return captcha
```



start.py

```
#encoding: utf-8

from scrapy import cmdline

cmdline.execute("scrapy crawl douban".split())
```



setting.py

```
ROBOTSTXT_OBEY = False
DOWNLOAD_DELAY = 1
DEFAULT_REQUEST_HEADERS = {
    'accept': '*/*',
    'accept-encoding': 'gzip, deflate, br',
    'accept-language': 'en-US,en;q=0.9,zh-CN;q=0.8,zh;q=0.7,zh-TW;q=0.6',
    'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
}
```



## Pipeline

### media pipeline

包括Files Pipeline 或者Images Pipeline

可以避免下载重复的文件

生成的图片时通用格式，方便检测图片信息

异步下载效率非常高



#### Files Pipeline

1. 定义一个item，然后再item中定义file_urls & files, file_urls是用来存储需要下载的链接，需要一个列表
2. 文件下载完成后，会讲相关信息存储到item中files属性中，比如下载路径，文件校验码
3. settings.py中FILES_STORE 时配置文件到下载路径
4. 需要在ITEM_PIPELINES中设置scrapy.pipelines.files.FilesPipeline:1



#### Images Pipeline

1. 定义一个item，然后再item中定义image_urls & images, image_urls是用来存储需要下载的链接，需要一个列表
2. 文件下载完成后，会讲相关信息存储到item中files属性中，比如下载路径，文件校验码
3. settings.py中IMAGES_STORE 时配置图片到下载路径
4. 需要在ITEM_PIPELINES中设置scrapy.pipelines.images.ImagesPipeline:1
5. 下载到指定文件夹，需要重写pipeline



### example

#### autohome.com.cn

CMD

```
$ scrapy startproject bmw
$ cd bmw/
$ scrapy genspider bmw5 'car.autohome.com.cn'
```



settings.py

```
import os
BOT_NAME = 'bmw'
SPIDER_MODULES = ['bmw.spiders']
NEWSPIDER_MODULE = 'bmw.spiders'
ROBOTSTXT_OBEY = False
DOWNLOAD_DELAY = 1
DEFAULT_REQUEST_HEADERS = {
    'accept': '*/*',
    'accept-encoding': 'gzip, deflate, br',
    'accept-language': 'en-US,en;q=0.9,zh-CN;q=0.8,zh;q=0.7,zh-TW;q=0.6',
    'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
}
ITEM_PIPELINES = {
   # 'bmw.pipelines.BmwPipeline': 300,
   # 'scrapy.pipelines.images.ImagesPipeline': 1  # 默认的下载方法
    'bmw.pipelines.BMWImagesPipeline': 1
}
IMAGES_STORE = os.path.join(os.path.dirname(os.path.dirname(__file__)), 'images')
```



items.py

```
# -*- coding: utf-8 -*-

# Define here the models for your scraped items
#
# See documentation in:
# https://doc.scrapy.org/en/latest/topics/items.html

import scrapy


class BmwItem(scrapy.Item):
    category = scrapy.Field()
    image_urls = scrapy.Field()
    images = scrapy.Field()
```



bmw5.py

```
# -*- coding: utf-8 -*-
import scrapy
from bmw.items import BmwItem


class Bmw5Spider(scrapy.Spider):
    name = 'bmw5'
    allowed_domains = ['car.autohome.com.cn']
    start_urls = ['https://car.autohome.com.cn/pic/series/65.html']

    def parse(self, response):
        uiboxs = response.xpath("//div[@class='uibox']")[1:]
        for uibox in uiboxs:
            category = uibox.xpath("./div[@class='uibox-title']/a/text()").get()
            urls = uibox.xpath(".//ul/li/a/img/@src").getall()
            urls = list(map(lambda url: response.urljoin(url), urls))
            item = BmwItem(category=category, image_urls=urls)
            yield item
```



pipelines.py

```
# -*- coding: utf-8 -*-

# Define your item pipelines here
#
# Don't forget to add your pipeline to the ITEM_PIPELINES setting
# See: https://doc.scrapy.org/en/latest/topics/item-pipeline.html

import os
from urllib import request
from scrapy.pipelines.images import ImagesPipeline
from bmw import settings


class BmwPipeline(object):
    """需要打开settings里面的ITEM_PIPELINES"""

    def __init__(self):
        self.path = os.path.join(os.path.dirname(os.path.dirname(__file__)), 'images')
        if not os.path.exists(self.path):
            os.mkdir(self.path)

    def process_item(self, item, spider):
        category = item['category']
        urls = item['urls']

        category_path = os.path.join(self.path, category)
        if not os.path.exists(category_path):
            os.mkdir(category_path)

        for url in urls:
            image_name = url.split('_')[-1]
            request.urlretrieve(url, os.path.join(category_path, image_name))

        return item


class BMWImagesPipeline(ImagesPipeline):

    def get_media_requests(self, item, info):
        """在发送下载请求前调用， 其本身就是去发送下载请求"""
        request_objs = super(BMWImagesPipeline, self).get_media_requests(item, info)
        for request_obj in request_objs:
            request_obj.item = item
        return request_objs

    def file_path(self, request, response=None, info=None):
        """重写这个方法，图片将要被存储的时候调用，来获取这个图片存储的路径"""
        path = super(BMWImagesPipeline, self).file_path(request, response, info)
        category = request.item.get('category')
        images_store = settings.IMAGES_STORE
        category_path = os.path.join(images_store, category)
        if not os.path.exists(category_path):
            os.mkdir(category_path)
        image_name = path.replace("full/", "")
        image_path = os.path.join(category_path, image_name)
        return image_path
```





## 下载器中间件 

Downloader Middlewares

中间件可以设置代理IP，随机请求头。

下载器实现两个方法



### process_request(self, request, spider)

* 返回None，scrapy将继续处理该request，执行其他中间件中的相应方法，直到合适的下载器处理函数被调用

* 返回Response对象， 将不会调用其他的process_request方法，将直接返回response对象。已经激活的process_response方法会在每个response返回时调用
* 返回Request对象，不会使用之前的request对象去下载数据，而是根据现在返回的request对象返回数据
* 抛出异常，调用process_exception



### Process_response(self, request, response, spider)

* 返回Response，将新的response传给其他中间件，最终传递给引擎爬虫
* 返回Request，下载器链接切断，返回request会被重新被下载器调度
* 抛出异常，调用errback



### 随机请求头中间件

### 网络上的请求头

http://useragentstring.com/pages/useragentstring.php?typ=Browser



### example

#### httpbin.org

CMD

```
$ scrapy startproject useragent_demo
$ cd useragent_demo/
$ scrapy genspider httpbin 'httpbin.org'
```



httpbin.py

```
# -*- coding: utf-8 -*-
import scrapy
import json


class HttpbinSpider(scrapy.Spider):
    name = 'httpbin'
    allowed_domains = ['httpbin.org']
    start_urls = ['http://httpbin.org/user-agent']

    def parse(self, response):
        user_agent = json.loads(response.text)['user-agent']
        print('-'*88)
        print(user_agent)
        print('-'*88)
        yield scrapy.Request(self.start_urls[0], dont_filter=True)  # 关闭去重功能
```



middlewares.py

```
# -*- coding: utf-8 -*-

# Define here the models for your spider middleware
#
# See documentation in:
# https://doc.scrapy.org/en/latest/topics/spider-middleware.html

from scrapy import signals
import random



class UserAgentDownloadMiddleware(object):
    USER_AGENTS = [
        'Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2228.0 Safari/537.36',
        'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2227.1 Safari/537.36',
        'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2227.0 Safari/537.36',
        'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2227.0 Safari/537.36',
        'Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2226.0 Safari/537.36',
        'Mozilla/5.0 (Windows NT 6.4; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2225.0 Safari/537.36',
        'Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2225.0 Safari/537.36'
    ]

    def process_request(self, request, spider):
        user_agent = random.choice(self.USER_AGENTS)
        request.headers['User-Agent'] = user_agent
```



settings.py

```
BOT_NAME = 'useragent_demo'
SPIDER_MODULES = ['useragent_demo.spiders']
NEWSPIDER_MODULE = 'useragent_demo.spiders'
ROBOTSTXT_OBEY = False
DOWNLOAD_DELAY = 1
DEFAULT_REQUEST_HEADERS = {
    'accept': '*/*',
    'accept-encoding': 'gzip, deflate, br',
    'accept-language': 'en-US,en;q=0.9,zh-CN;q=0.8,zh;q=0.7,zh-TW;q=0.6',
    'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
}
DOWNLOADER_MIDDLEWARES = {
   # 'useragent_demo.middlewares.UseragentDemoDownloaderMiddleware': 543,
    'useragent_demo.middlewares.UserAgentDownloadMiddleware': 543,
}
```



start.py

```
from scrapy import cmdline
cmdline.execute('scrapy crawl httpbin'.split())
```





### 代理中间件

cmd

```
$ scrapy genspider ipproxy 'httpbin.org'
```



ipproxy.py

```
# -*- coding: utf-8 -*-
import scrapy
import json


class IpproxySpider(scrapy.Spider):
    name = 'ipproxy'
    allowed_domains = ['httpbin.org']
    start_urls = ['https://httpbin.org/ip']
    # start_urls = ['https://ip.cn']

    def parse(self, response):
        # value = response.xpath("//div[@class='well']/p/code/text()")[0]
        user_agent = json.loads(response.text)['user-agent']
        print('-' * 88)
        print(user_agent)
        print('-' * 88)
        yield scrapy.Request(self.start_urls[0], dont_filter=True)  # 关闭去重功能
```



middlewares.py

共享代理

```
class IPProxyDownloadMiddleware(object):
    """kuaidaili.com"""
    PROXIES = [
        '60.208.32.201:80',
        '117.30.196.140:58171',
        '182.18.6.9:53281',
        '121.232.199.76:9000',
        '117.90.7.242:9000',
        '114.234.81.212:9000',
        '183.158.205.140:9000',
        '118.24.185.22:80',
        '180.118.86.82:9000',
        '119.51.246.215:8060',
        '180.118.73.110:9000',
        '106.14.197.219:8118'
    ]

    def process_request(self, request, spider):
        proxy = random.choice(self.PROXIES)
        request.meta['proxy'] = proxy
```

独享代理

```
class IPProxyDownloadMiddleware(object):
    def process_request(self, request, spider):
        proxy = '121.191.6.124:16816'
        user_password = 'username:password'
        request.meta['proxy'] = proxy
        b64_user_password = base64.b64encode(user_password.encode('utf-8'))
        request.headers['Proxy-Authorization'] = 'Basic ' + b64_user_password.decode('utf-8')
        
```





settings.py

```
BOT_NAME = 'multiproxy'
SPIDER_MODULES = ['multiproxy.spiders']
NEWSPIDER_MODULE = 'multiproxy.spiders'
ROBOTSTXT_OBEY = False
DOWNLOAD_DELAY = 1
DEFAULT_REQUEST_HEADERS = {
    'accept': '*/*',
    'accept-encoding': 'gzip, deflate, br',
    'accept-language': 'en-US,en;q=0.9,zh-CN;q=0.8,zh;q=0.7,zh-TW;q=0.6',
    'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
}
DOWNLOADER_MIDDLEWARES = {
   # 'multiproxy.middlewares.MultiproxyDownloaderMiddleware': 543,
    'multiproxy.middlewares.IPProxyDownloadMiddleware': 100,
}BOT_NAME = 'multiproxy'
SPIDER_MODULES = ['multiproxy.spiders']
NEWSPIDER_MODULE = 'multiproxy.spiders'
ROBOTSTXT_OBEY = False
DOWNLOAD_DELAY = 1
DEFAULT_REQUEST_HEADERS = {
    'accept': '*/*',
    'accept-encoding': 'gzip, deflate, br',
    'accept-language': 'en-US,en;q=0.9,zh-CN;q=0.8,zh;q=0.7,zh-TW;q=0.6',
    'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
}
DOWNLOADER_MIDDLEWARES = {
   # 'multiproxy.middlewares.MultiproxyDownloaderMiddleware': 543,
    'multiproxy.middlewares.IPProxyDownloadMiddleware': 100,
}
```



start.py

```
from scrapy import cmdline

cmdline.execute("scrapy crawl ipproxy".split())
```



#### example

##### zhipin.com

cmd

```
$ scrapy startproject boss
$ cd boss
$ scrapy genspider -t crawl zhipin 'zhipin.com'
```



middleware.py

这里的代理中间件需要测试

```
# -*- coding: utf-8 -*-

# Define here the models for your spider middleware
#
# See documentation in:
# https://doc.scrapy.org/en/latest/topics/spider-middleware.html
import random
import requests
import json
import datetime
from lxml import etree


class UserAgentDownloadMiddleware(object):
    USER_AGENTS = [
        'Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2228.0 Safari/537.36',
        'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2227.1 Safari/537.36',
        'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2227.0 Safari/537.36',
        'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2227.0 Safari/537.36',
        'Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2226.0 Safari/537.36',
        'Mozilla/5.0 (Windows NT 6.4; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2225.0 Safari/537.36',
        'Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2225.0 Safari/537.36'
    ]

    def process_request(self, request, spider):
        user_agent = random.choice(self.USER_AGENTS)
        request.headers['User-Agent'] = user_agent


class IPProxyDownloadMiddleware(object):

    def __init__(self):
        self.proxy_url = None
        self.current_date = None
        self.proxy_list = None
        self.formated_proxy_list = None
        self.current_proxy = None
        self.blacked = False

    def process_request(self, request, spider):
        print('-'*88)
        print('request.meta', request.meta)
        print('-'*88)
        if 'proxy' not in request.meta:
            request.meta['proxy'] = self.update_proxy()

    def process_response(self, request, response, spider):
        if response.status != 200:
            if not self.current_proxy.blacked:
                self.current_proxy.blacked = True
            self.update_proxy()  # 执行到这里，代表请求被block了
            print('%s proxy have been blacked' % self.current_proxy.ip)
            return request
        return response  # 正常请求到，需要返回response

    def update_proxy(self):
        if not self.current_proxy or self.current_proxy.blacked:
            temp = datetime.datetime.now()
            self.current_date = temp.strftime('%Y/%m/%d/%H')
            self.proxy_url = 'https://ip.ihuan.me/today/{}.html'.format(self.current_date)
            response = requests.get(self.proxy_url)
            text = response.text
            html = etree.HTML(text)
            self.proxy_list = html.xpath("//p[@class='text-left']/text()")
            self.formated_proxy_list = []
            for i in range(len(self.proxy_list)):
                self.formated_proxy_list.append('http://' + self.proxy_list[i].split('@')[0].strip())
            self.current_proxy = random.choice(self.formated_proxy_list)
            return self.current_proxy
```



zhipin.py

```
# -*- coding: utf-8 -*-
import scrapy
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule
from boss.items import BossItem


class ZhipinSpider(CrawlSpider):
    name = 'zhipin'
    allowed_domains = ['zhipin.com']
    start_urls = ['https://www.zhipin.com/c101010100/h_100010000/?query=python&page=1']

    rules = (
        # list info
        Rule(LinkExtractor(allow=r'.+\?query=python&page=\d'), follow=True),
        # detail info
        Rule(LinkExtractor(allow=r'.+job_detail/.+.html'), callback="parse_job", follow=False),
    )

    def parse_job(self, response):
        title = response.xpath("//div[@class='name']/h1/text()").get()
        salary = response.xpath("//span[@class='badge']/text()").get().strip()
        job_info = response.xpath("//div[@class='job-primary detail-box']/div[@class='info-primary']/p/text()").getall()
        city = job_info[0]
        work_years = job_info[1]
        education = job_info[2]
        company = response.xpath("//div[@class='info-company']//a/text()").get()

        item = BossItem(title=title, salary=salary, city=city, work_years=work_years, education=education, company=company)
        yield item
```



items.py

```
import scrapy


class BossItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    title = scrapy.Field()
    salary = scrapy.Field()
    city = scrapy.Field()
    work_years = scrapy.Field()
    education = scrapy.Field()
    company = scrapy.Field()
```



settings.py

```
BOT_NAME = 'boss'
SPIDER_MODULES = ['boss.spiders']
NEWSPIDER_MODULE = 'boss.spiders'
ROBOTSTXT_OBEY = False
DOWNLOAD_DELAY = 1
DEFAULT_REQUEST_HEADERS = {
    'accept': '*/*',
    'accept-encoding': 'gzip, deflate, br',
    'accept-language': 'en-US,en;q=0.9,zh-CN;q=0.8,zh;q=0.7,zh-TW;q=0.6',
    'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36'
}
DOWNLOADER_MIDDLEWARES = {
   # 'boss.middlewares.BossDownloaderMiddleware': 543,
    'boss.middlewares.UserAgentDownloadMiddleware': 100,
    # 'boss.middlewares.IPProxyDownloadMiddleware': 200,
}
ITEM_PIPELINES = {
   'boss.pipelines.BossPipeline': 300,
}
```





##### jianshu.com

```
$ scrapy startproject jianshu_spider
cd jianshu_spider/
$ scrapy genspider -t crawl js "jianshu.com"
```



start.py

```
from scrapy import cmdline

cmdline.execute('scrapy crawl js'.split())
```



settings.py

```

```

