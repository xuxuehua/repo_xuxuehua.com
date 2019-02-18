---
title: "locate_parameters 元素定位"
date: 2018-07-19 18:17
---

[TOC]

# 元素定位

## Python selenium定位方法

Selenium提供了8种定位方式。

- id
- name
- class name
- tag name
- link text
- partial link text
- xpath
- css selector

这8种定位方式在Python selenium中所对应的方法为：

- find_element_by_id()
- find_element_by_name()
- find_element_by_class_name()
- find_element_by_tag_name()
- find_element_by_link_text()
- find_element_by_partial_link_text()
- find_element_by_xpath()
- find_element_by_css_selector()



### 定位方法的用法

假如我们有一个Web页面，通过前端工具（如，Firebug）查看到一个元素的属性是这样的。

```
<html>
  <head>
  <body link="#0000cc">
    <a id="result_logo" href="/" onmousedown="return c({'fm':'tab','tab':'logo'})">
    <form id="form" class="fm" name="f" action="/s">
      <span class="soutu-btn"></span>
        <input id="kw" class="s_ipt" name="wd" value="" maxlength="255" autocomplete="off">
```

我们的目的是要定位input标签的输入框。

#### 通过id定位:

  ```
dr.find_element_by_id("kw")
dr.find_element(BY.ID, "kw") 
  ```

#### 通过name定位:

```
dr.find_element_by_name("wd")
dr.find_element(By.NAME, "wd")
```
#### 通过class name定位:

  ```
dr.find_element_by_class_name("s_ipt")
dr.find_element(By.CLASS_NAME, "s_ipt")
  ```

#### 通过tag name定位:

  ```
  dr.find_element_by_tag_name("input")
  dr.find_element(By.TAG_NAME, "input")
  ```

#### 通过xpath定位

  ```
  dr.find_element_by_xpath("//*[@id='kw']")
  dr.find_element(By.XPATH, "//*[@id='kw']")
  dr.find_element_by_xpath("//*[@name='wd']")
  dr.find_element_by_xpath("//input[@class='s_ipt']")
  dr.find_element_by_xpath("/html/body/form/span/input")
  dr.find_element_by_xpath("//span[@class='soutu-btn']/input")
  dr.find_element_by_xpath("//form[@id='form']/span/input")
  dr.find_element_by_xpath("//input[@id='kw' and @name='wd']")
  ```

#### 通过css定位

  ```
  from selenium.webdriver.common.by import By
  dr.find_element(By.CSS_SELECTOR, "#kw")
  dr.find_element_by_css_selector("#kw")
  dr.find_element_by_css_selector("[name=wd]")
  dr.find_element_by_css_selector(".s_ipt")
  dr.find_element_by_css_selector("html > body > form > span > input")
  dr.find_element_by_css_selector("span.soutu-btn> input#kw")
  dr.find_element_by_css_selector("form#form > span > input")
  ```

接下来，我们的页面上有一组文本链接。

```
<a class="mnav" href="http://news.baidu.com" name="tj_trnews">新闻</a>
<a class="mnav" href="http://www.hao123.com" name="tj_trhao123">hao123</a>
```

#### 通过link text定位:

  ```
from selenium.webdriver.common.by import By
dr.find_element(By.LINK_TEXT, "hao123")
dr.find_element_by_link_text("新闻")
dr.find_element_by_link_text("hao123")
dr.find_element_by_partial_link_text("新")
dr.find_element_by_partial_link_text("hao")
dr.find_element_by_partial_link_text("123")
  ```

关于xpaht和css的定位比较复杂，请参考： [xpath语法](http://www.w3school.com.cn/xpath/xpath_syntax.asp)、 [css选择器](http://www.w3school.com.cn/cssref/css_selectors.asp)



#### 定位一组元素

WebDriver还提供了8种用于定位一组元素的方法。

```
find_elements_by_id()
find_elements_by_name()
find_elements_by_class_name()
find_elements_by_tag_name()
find_elements_by_link_text()
find_elements_by_partial_link_text()
find_elements_by_xpath()
find_elements_by_css_selector()
```

定位一组元素的方法与定位单个元素的方法类似，唯一的区别是在单词element后面多了一个s表示复数。

接下来通过例子演示定位一组元素的使用：

```
from selenium import webdriver
from time import sleep

driver = webdriver.Chrome()
driver.get("https://www.baidu.com")

driver.find_element_by_id("kw").send_keys("selenium")
driver.find_element_by_id("su").click()
sleep(1)

# 定位一组元素
texts = driver.find_elements_by_xpath('//div/h3/a')

# 循环遍历出每一条搜索结果的标题
for t in texts:
    print(t.text)

driver.quit()
```

程序运行结果：

```
Selenium - Web Browser Automation
官网
功能自动化测试工具——Selenium篇
selenium + python自动化测试环境搭建 - 虫师 - 博客园
selenium是什么?_百度知道
怎样开始用selenium进行自动化测试(个人总结)_百度经验
Selenium_百度百科
selenium_百度翻译
Selenium官网教程_selenium自动化测试实践_Selenium_领测软件测试网
Selenium(浏览器自动化测试框架)_百度百科
自动化基础普及之selenium是啥? - 虫师 - 博客园
python十大主流开源框架 「菜鸟必看」
```



## nodejs selenium 定位方法

### By.className( name )

selenium**不支持**复合的class属性。比如`class="col-md-1 col-sm-2"`，我们只能通过`col-md-1`或者是`col-sm-2`来定位，不能同时使用这2个class进行定位。



```
<p>
  <label for="input__text3" class="error">Error</label>
  <input id="input__text3" class="is-error" type="text" placeholder="Text Input">
</p>
<p>
  <label for="input__text4" class="valid">Valid</label>
  <input id="input__text4" class="is-valid" type="text" placeholder="Text Input">
</p>
```

```
var path = require('path');
var webdriver = require('selenium-webdriver'),
  By = webdriver.By;

var testFile = "file://" + path.join(__dirname,  "index.html")

var dr = new webdriver.Builder().forBrowser('chrome').build();
dr.get(testFile)

dr.findElement(By.className('is-error')).sendKeys('should be error');
dr.findElement(By.className('is-valid')).sendKeys('should be valid');
```



### By.css( selector )

```
<p>
  <label for="input__search">Search</label>
  <input id="input__search" type="search" placeholder="Enter Search Term">
</p>
<p>
  <label for="input__text2">Number Input</label>
  <input id="input__text2" type="number" placeholder="Enter a Number">
</p>
<p>
  <label for="input__text3" class="error">Error</label>
  <input id="input__text3" class="is-error" type="text" placeholder="Text Input">
</p>
<p>
  <label for="input__text4" class="valid">Valid</label>
  <input id="input__text4" class="is-valid" type="text" placeholder="Text Input">
</p>
```



```
var path = require('path');
var webdriver = require('selenium-webdriver'),
  By = webdriver.By;

var testFile = "file://" + path.join(__dirname,  "index.html");

var dr = new webdriver.Builder().forBrowser('chrome').build();
dr.get(testFile);

// id selector
dr.findElement(By.css('#input__search')).sendKeys('测试教程网');
// attribute selector
dr.findElement(By.css('input[type="number"]')).sendKeys('66666');
// class selector
dr.findElement(By.css('.is-error')).sendKeys('should be error');
// class selector
dr.findElement(By.css('.is-valid')).sendKeys('should be valid');
```





### By.id( id )

```
var path = require('path');
var webdriver = require('selenium-webdriver'),
  By = webdriver.By;

var testFile = "file://" + path.join(__dirname,  "index.html")

var dr = new webdriver.Builder().forBrowser('chrome').build();
dr.get(testFile)

dr.findElement(By.id('input__text')).sendKeys('测试教程网');
dr.findElement(By.id('input__password')).sendKeys('password');
dr.findElement(By.id('input__webaddress')).sendKeys('http://www.itest.info');
dr.findElement(By.id('input__emailaddress')).sendKeys('service@itest.info');
dr.findElement(By.id('input__phone')).sendKeys('13888888888');
dr.findElement(By.id('input__search')).sendKeys('keywords');
dr.findElement(By.id('input__text2')).sendKeys('6666666');
dr.findElement(By.id('input__text3')).sendKeys('should be error');
dr.findElement(By.id('input__text4')).sendKeys('should be valid');
```



### By.js( script, …var_args )



### By.linkText( text )

链接可以使用By.linkText和By.partialLinkText的方式去定位。

链接的html标签是`<a></a>`。

看这个html代码`<a href="http://www.itet.info">这就是linkText<a>`，这里**[这就是linkText]** 便是这个链接的linkText，partialLinkText指的是部分的链接文本，如果链接的文本过长的时候，使用partialLinkText会起到简化代码的作用。

```
<ul>
  <li><a href="#text__headings">Headings</a></li>
  <li><a href="#text__paragraphs">Paragraphs</a></li>
  <li><a href="#text__blockquotes">Blockquotes</a></li>
  <li><a href="#text__lists">Lists</a></li>
  <li><a href="#text__hr">Horizontal rules</a></li>
  <li><a href="#text__tables">Tabular data</a></li>
  <li><a href="#text__code">Code</a></li>
  <li><a href="#text__inline">Inline elements</a></li>
</ul>
```



```
var path = require('path');
var webdriver = require('selenium-webdriver'),
  By = webdriver.By;

var testFile = "file://" + path.join(__dirname,  "index.html");

var dr = new webdriver.Builder().forBrowser('chrome').build();
dr.get(testFile);

dr.findElement(By.linkText('Headings')).getAttribute('href').then(function(href) {
  console.log(href);
});

dr.findElement(By.linkText('Paragraphs')).getAttribute('href').then(function(href) {
  console.log(href);
});

dr.findElement(By.linkText('Blockquotes')).getAttribute('href').then(function(href) {
  console.log(href);
});

dr.findElement(By.linkText('Lists')).getAttribute('href').then(function(href) {
  console.log(href);
});

dr.findElement(By.linkText('Horizontal rules')).getAttribute('href').then(function(href) {
  console.log(href);
});

dr.findElement(By.linkText('Tabular data')).getAttribute('href').then(function(href) {
  console.log(href);
});

dr.findElement(By.linkText('Code')).getAttribute('href').then(function(href) {
  console.log(href);
});

dr.findElement(By.partialLinkText('Inline')).getAttribute('href').then(function(href) {
  console.log(href);
});

dr.quit();

>>>
file://xxxx/index.html#text__headings
file://xxxx/index.html#text__paragraphs
file://xxxx/index.html#text__blockquotes
file://xxxx/index.html#text__lists
file://xxxx/index.html#text__hr
file://xxxx/index.html#text__tables
file://xxxx/index.html#text__code
file://xxxx/index.html#text__inline
```



### By.name( name )

```
// <input name="username" />
dr.findElement(By.name('username')).sendKeys('测试教程网');


// <input name="password" type="password" />
dr.findElement(By.name('password')).sendKeys('就不告诉你');
```



### By.partialLinkText( text )



### By.xpath( xpath )

#### xpath语法要点

- `nodename` 选取此节点的所有子节点。
- `/` 从根节点选取。
- `//` 从匹配选择的当前节点选择文档中的节点，而不考虑它们的位置。
- `.` 选取当前节点。
- `..` 选取当前节点的父节点。
- `@` 选取属性。



```
// <input id="input__text2" type="number" placeholder="Enter a Number">
dr.findElement(By.xpath('//input[@type="number"]')).sendKeys('66666');
```



### 定位一组元素

定位一组元素一般有如下的作用

- 找到一组属性部分相同的元素，遍历元素，做一些批量操作
- 找到一组属性部分相同的元素，遍历元素，返回**1个或几个**特定的元素，做更精确的定位

findElements方法可以定位一组元素。该方法的参数跟findElement方法一致。

 

```
var webdriver = require('selenium-webdriver'),
  By = webdriver.By;

var dr = new webdriver.Builder().forBrowser('chrome').build();
dr.get('http://www.testclass.net/selenium_javascript/');

dr.findElements(By.css('.post-stub a')).then(function(links){
  for (var i = 0; i < links.length; i++) {
    links[i].getAttribute('href').then(function(href) {
      console.log(href);
    });
  }
});
dr.quit();
```





