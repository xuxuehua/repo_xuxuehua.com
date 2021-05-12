---
title: "basic_knowledge"
date: 2018-07-19 18:03
---

[TOC]



# Selenium



Jason Huggins在2004年发起了Selenium项目，当时身处ThoughtWorks的他，为了不想让自己的时间浪费在无聊的重复性工作中，幸运的是，所有被测试的浏览器都支持Javascript。Jason和他所在的团队采用Javascript编写一种测试工具来验证浏览器页面的行为；这个JavaScript类库就是Selenium core，同时也是seleniumRC、Selenium IDE的核心组件。Selenium由此诞生。

关于Selenium的命名比较有意思，当时QTP mercury是主流的商业自化工具，是化学元素汞（俗称水银），而Selenium是开源自动化工具，是化学元素硒，硒可以对抗汞。



## Selenium IDE

Selenium IDE是嵌入到Firefox浏览器中的一个插件，实现简单的浏览器操作的录制与回放功能。



## Selenium Grid

Selenium Grid是一种自动化的测试辅助工具，Grid通过利用现有的计算机基础设施，能加快Web-App的功能测试。利用Grid可以很方便地实现在多台机器上和异构环境中运行测试用例



## WebDriver

WebDriver是通过原生浏览器支持或者浏览器扩展来直接控制浏览器。WebDriver针对各个浏览器而开发，取代了嵌入到被测Web应用中的JavaScript，与浏览器紧密集成，因此支持创建更高级的测试，避免了JavaScript安全模型导致的限制。除了来自浏览器厂商的支持之外，WebDriver还利用操作系统级的调用，模拟用户输入。











# 浏览器驱动

Firefox浏览器驱动：[geckodriver](https://github.com/mozilla/geckodriver/releases)

Chrome浏览器驱动：[chromedriver](https://sites.google.com/a/chromium.org/chromedriver/home)     [taobao备用地址](https://npm.taobao.org/mirrors/chromedriver)    [chrome_all_version_list](http://chromedriver.storage.googleapis.com/index.html)

IE浏览器驱动：[IEDriverServer](http://selenium-release.storage.googleapis.com/index.html)

Edge浏览器驱动：[MicrosoftWebDriver](https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver)

Opera浏览器驱动：[operadriver](https://github.com/operasoftware/operachromiumdriver/releases)

PhantomJS浏览器驱动：[phantomjs](http://phantomjs.org/)



# Installation



## Pip

```
pip install selenium
```





## Install PhantomJS in Centos

```
sudo mv $PHANTOM_JS.tar.bz2 /usr/local/share/
cd /usr/local/share/
sudo tar xvjf phantomjs-1.9.2-linux-x86_64.tar.bz2
sudo ln -sf /usr/local/share/$PHANTOM_JS/bin/phantomjs /usr/local/share/phantomjs
sudo ln -sf /usr/local/share/$PHANTOM_JS/bin/phantomjs /usr/local/bin/phantomjs
sudo ln -sf /usr/local/share/$PHANTOM_JS/bin/phantomjs /usr/bin/phantomjs
```





## 测试

打开一款Python编辑器，默认Python自带的IDLE也行。创建 baidu.py文件，输入以下内容：

```
from selenium import webdriver


driver = webdriver.Chrome()
driver.get('https://www.baidu.com')

print(driver.title)

driver.quit()
```

 

# 驱动调用

```
from selenium import webdriver


driver = webdriver.Firefox()   # Firefox浏览器

driver = webdriver.Chrome()    # Chrome浏览器

driver = webdriver.Ie()        # Internet Explorer浏览器

driver = webdriver.Edge()      # Edge浏览器

driver = webdriver.Opera()     # Opera浏览器

driver = webdriver.PhantomJS()   # PhantomJS
```



## chromedriver

Install chromedriver in debian 

```
apt install chromedriver
```



```
wget http://chromedriver.storage.googleapis.com/89.0.4389.23/chromedriver_linux64.zip
```

> Please find your specific chrome version

```
unzip chromedriver_linux64.zip
sudo cp chromedriver /usr/bin/chromedriver
sudo chown root:root /usr/bin/chromedriver
sudo chmod +x /usr/bin/chromedriver

sudo cp chromedriver /usr/local/bin/chromedriver
sudo chown root:root /usr/local/bin/chromedriver
sudo chmod +x /usr/local/bin/chromedriver
```





### ChromeOptions

```
from selenium import webdriver

chrome_options = webdriver.ChromeOptions()
chrome_options.add_argument('--no-sandbox')
chrome_options.add_argument('--window-size=1420,1080')
# chrome_options.add_argument('--user-agent=iphone')
# chrome_options.add_argument('--ignore-certificate-errors')
chrome_options.add_argument('--headless')
chrome_options.add_argument('--disable-gpu')
driver = webdriver.Chrome(chrome_options=chrome_options)
driver.maximize_window()
driver.get(url)
```





for specific profile

```
vim /opt/google/chrome/google-chrome

# Added at bottom
exec -a "$0" "$HERE/chrome" "$@" --user-data-dir 
```



```
from selenium import webdriver

chrome_options = webdriver.ChromeOptions()
chrome_options.add_argument('--user-data-dir=/home/vnc/.config/chrome-remote-desktop/chrome-config/google-chrome/Profile 2')
chrome_options.add_argument("--remote-debugging-port=9222")
driver = webdriver.Chrome(options=chrome_options, executable_path="/usr/bin/chromedriver",
                          service_args=['--verbose', '--log-path=/tmp/chromedriver.log'])
driver.get("https://xurick.com")

```

> To find path to your chrome profile data you need to type `chrome://version/` into address bar 







浏览器启动时安装crx扩展

```
#coding=utf-8
from selenium import webdriver
option = webdriver.ChromeOptions()
option.add_extension('d:\crx\AdBlock_v2.17.crx') #自己下载的crx路径
driver = webdriver.Chrome(chrome_options=option)
driver.get('http://www.xurick.com/')

可以去https://sites.google.com/a/chromium.org/chromedriver/capabilities查看更多
```



```text
from selenium import webdriver
option = webdriver.ChromeOptions()

# 添加UA
options.add_argument('user-agent="MQQBrowser/26 Mozilla/5.0 (Linux; U; Android 2.3.7; zh-cn; MB200 Build/GRJ22; CyanogenMod-7) AppleWebKit/533.1 (KHTML, like Gecko) Version/4.0 Mobile Safari/533.1"')

# 指定浏览器分辨率
options.add_argument('window-size=1920x3000') 

# 谷歌文档提到需要加上这个属性来规避bug
chrome_options.add_argument('--disable-gpu') 

 # 隐藏滚动条, 应对一些特殊页面
options.add_argument('--hide-scrollbars')

# 不加载图片, 提升速度
options.add_argument('blink-settings=imagesEnabled=false') 

# 浏览器不提供可视化页面. linux下如果系统不支持可视化不加这条会启动失败
options.add_argument('--headless') 

# 以最高权限运行
options.add_argument('--no-sandbox')

# 手动指定使用的浏览器位置
options.binary_location = r"C:\Program Files (x86)\Google\Chrome\Application\chrome.exe" 

#添加crx插件
option.add_extension('d:\crx\AdBlock_v2.17.crx') 

# 禁用JavaScript
option.add_argument("--disable-javascript") 

# 设置开发者模式启动，该模式下webdriver属性为正常值
options.add_experimental_option('excludeSwitches', ['enable-automation']) 

# 禁用浏览器弹窗
prefs = {  
    'profile.default_content_setting_values' :  {  
        'notifications' : 2  
     }  
}  
options.add_experimental_option('prefs',prefs)


driver=webdriver.Chrome(chrome_options=chrome_options)
```







### chrome info

在Chrome的浏览器地址栏中输入以下命令，就会返回相应的结果。这些命令包括查看内存状态，浏览器状态，网络状态，DNS服务器状态，插件缓存等等。

```
　　about:version - 显示当前版本

　　about:memory - 显示本机浏览器内存使用状况

　　about:plugins - 显示已安装插件

　　about:histograms - 显示历史记录

　　about:dns - 显示DNS状态

　　about:cache - 显示缓存页面

　　about:gpu -是否有硬件加速

　　about:flags -开启一些插件 //使用后弹出这么些东西：“请小心，这些实验可能有风险”，不知会不会搞乱俺的配置啊！

　　chrome://extensions/ - 查看已经安装的扩展
```











## nodejs 下 安装

```
mkdir se
cd se
npm install --save selenium-webdriver@2.44.0
```



### PhantomJS

```
npm install phantomjs -g 
```



###chrome driver

```
npm install --save chromedriver@2.12.0
```



### 简单的环境验证测试



新建文件start.js，并键入下面的内容。

```
// start.js
var webdriver = require('selenium-webdriver');

var driver = new webdriver.Builder().forBrowser('chrome').build();

driver.get('https://www.google.com');

console.log('quit driver');
driver.quit();
```





# Appendix

https://zhuanlan.zhihu.com/p/60852696

https://developpaper.com/python-3-selenium-runs-on-centos-server/