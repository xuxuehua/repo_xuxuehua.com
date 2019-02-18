---
title: "operator 操作"
date: 2018-07-19 18:21
---

[TOC]



# Selenium 操作


## 浏览器操作

### 控制浏览器窗口大小

有时候我们希望能以某种浏览器尺寸打开，让访问的页面在这种尺寸下运行。例如可以将浏览器设置成移动端大小(480* 800)，然后访问移动站点，对其样式进行评估；WebDriver提供了set_window_size()方法来设置浏览器的大小。

```
from selenium import webdriver

driver = webdriver.Firefox()
driver.get("http://m.baidu.com")

# 参数数字为像素点
print("设置浏览器宽480、高800显示")
driver.set_window_size(480, 800)
driver.quit()
```

在PC端执行自动化测试脚本大多的情况下是希望浏览器在全屏幕模式下执行，那么可以使用maximize_window()方法使打开的浏览器全屏显示，其用法与set_window_size() 相同，但它不需要参数。

### 控制浏览器后退、前进

在使用浏览器浏览网页时，浏览器提供了后退和前进按钮，可以方便地在浏览过的网页之间切换，WebDriver也提供了对应的back()和forward()方法来模拟后退和前进按钮。下面通过例子来演示这两个方法的使用。

```
from selenium import webdriver

driver = webdriver.Firefox()

#访问百度首页
first_url= 'http://www.baidu.com'
print("now access %s" %(first_url))
driver.get(first_url)

#访问新闻页面
second_url='http://news.baidu.com'
print("now access %s" %(second_url))
driver.get(second_url)

#返回（后退）到百度首页
print("back to  %s "%(first_url))
driver.back()

#前进到新闻页
print("forward to  %s"%(second_url))
driver.forward()

driver.quit()
```

为了看清脚本的执行过程，下面每操作一步都通过print()来打印当前的URL地址。



### 页面操作

#### 页面刷新

有时候需要手动刷新（F5） 页面。

```
……
driver.refresh() #刷新当前页面
……
```



#### 页面等待

##### wait

wait( condition, opt_timeout, opt_message )

wait方法一般用来等待页面上某些条件得到满足后才继续执行脚本。比如等待页面上某个弹出框出现，等某个元素可以被定位到之类。

```
// 在10s内id是foo的元素被定位到，然后点击之
var button = driver.wait(until.elementLocated(By.id('foo')), 10000);
button.click();
```

另外wait还可以将执行中的脚本暂停住一段时间，直到第1个参数中的异步操作处理完毕，如下所示

```
var started = startTestServer();
driver.wait(started, 5 * 1000, 'Server should start within 5 seconds');
driver.get(getServerUrl());
```



* 隐式等待， 调用driver.implicitly_wait 在获取不可用的元素之前，会等待10s时间

```
driver = webdriver.Chrome(executable_path=driver_path)
driver.implicitly_wait(10)
driver.get('https://www.xuxuehua.com')
```



* 显式等待，表明某个条件成立后才执行获取元素的操作，可以在等待的时候，指定一个最大时间，超过时间那么就抛出异常。
* 显式等待使用 selenium.webdriver.support.excepted_conditions & selenium.webdriver.support.ui.WebDriverWait 来配合完成

```
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

driver = webdriver.Chrome()
driver.get('https://www.baidu.com')

try:
    element = WebDriverWait(driver, 10).until(
        EC.presence_of_element_located((By.ID, 'form_email'))
    )
    print(element)
except BaseException as f:
    print(f)
finally:
    driver.quit()
```



##### sleep

sleep可以不管任何情况直接将执行中的脚本直接暂停一段时间。

```
console.log('start')
driver.findElement(By.css('.kls')).click();
// 等待3s
driver.sleep(3000)
driver.quit()
```

sleep在某些时候非常好用，但是希望大家不要乱用，因为这会拖慢脚本的执行速度。



##### until

until一般跟wait一起用，用于等待页面上的一些条件被满足



- ableToSwitchToFrame(frame): 直到selenium可以将定位上下文切换到frame里
- alertIsPresent(): 直到alert出现
- elementIsDisabled(element): 直到元素灰掉，不能被点击
- elementIsEnabled(element): 直到元素可以被点击
- elementIsNotSelected(element): 直到元素不可选
- elementIsNotVisible(element): 直到元素不可见
- elementIsSelected(element): 直到元素可选
- elementIsVisible(element): 直到元素可见
- elementLocated(locator): 最常用，直到元素可以被定位
- elementTextContains(element, substr): 直到元素的text包含substr
- elementTextIs(element, expected_text): 直到元素的text是expected_text
- elementTextMatches(element,regex): 直到元素的text满足正则表达式regex
- elementsLocated(locator): 直到一组元素被定位
- stalenessOf(element): 直到元素被dom树移除或页面刷新
- titleContains(substr): 直到页面title包含substr
- titleIs(expected_title): 直到页面的title是expected_title
- titleMatches(regex): 直到页面的title满足正则表达式regex
- urlContains(substrUrl): 直到页面url包含substrUrl
- urlIs(expected_url): 直到页面的url是expected_url
- urlMatches(regex): 直到页面的url满足正则表达式regex



until还允许我们自定义条件。我们只需要传入1个回调，该回调返回真值(true)就代表等待的条件被满足。

在javascript，真值表示所有**不是**null, undefined, false, 0, 或 空字符串的值。

```
driver.wait(function() {
  return driver.getTitle().then(function(title) {
    return title === '测试教程网';
  });
}, 1000);
```



#### 页面切换

switch_to_window 进行切换，具体切换到哪个页面从driver.windows_handles 中找到

```
from selenium import webdriver

driver = webdriver.Chrome()
driver.get('https://www.baidu.com')
driver.execute_script("window.open('http://xuxuehua.com')")
print(driver.window_handles)
driver.switch_to_window(driver.window_handles[1])
print(driver.current_url)
```



#### 页面源码 page_source

```
contents = brower.page_source
```





### 关闭浏览器

close() 关闭单个窗口

quit() 关闭所有窗口



### 代理IP设置

```
from selenium import webdriver

options = webdriver.ChromeOptions()
options.add_argument("--proxy-server=http://112.115.163.76:53281")
driver = webdriver.Chrome(chrome_options=options)
driver.get('http://httpbin.org/ip')

print(driver.page_source)
driver.quit()
```






## WebDriver 操作

### 点击和输入

前面我们已经学习了定位元素， 定位只是第一步， 定位之后需要对这个元素进行操作， 或单击（按钮） 或输入（输入框） ， 下面就来认识 WebDriver 中最常用的几个方法：

- clear()： 清除文本。
- send_keys (value)： 模拟按键输入。
- click()： 单击元素。

```
from selenium import webdriver

driver = webdriver.Chrome()
driver.get("https://www.baidu.com")

driver.find_element_by_id("kw").clear()
driver.find_element_by_id("kw").send_keys("selenium")
driver.find_element_by_id("su").click()

driver.quit()
```







### 提交

- submit()

submit()方法用于提交表单。 例如， 在搜索框输入关键字之后的“回车” 操作， 就可以通过该方法模拟。

```
from selenium import webdriver

driver = webdriver.Chrome()
driver.get("https://www.baidu.com")

search_text = driver.find_element_by_id('kw')
search_text.send_keys('selenium')
search_text.submit()

driver.quit()
```

有时候 submit()可以与 click()方法互换来使用， submit()同样可以提交一个按钮， 但 submit()的应用范围远不及 click()广泛。

### 其他常用方法

- size： 返回元素的尺寸。
- text： 获取元素的文本。
- get_attribute(name)： 获得属性值。
- is_displayed()： 设置该元素是否用户可见。

```
from selenium import webdriver

driver = webdriver.Chrome()
driver.get("http://www.baidu.com")

# 获得输入框的尺寸
size = driver.find_element_by_id('kw').size
print(size)

# 返回百度页面底部备案信息
text = driver.find_element_by_id("cp").text
print(text)

# 返回元素的属性值， 可以是 id、 name、 type 或其他任意属性
attribute = driver.find_element_by_id("kw").get_attribute('type')
print(attribute)

# 返回元素的结果是否可见， 返回结果为 True 或 False
result = driver.find_element_by_id("kw").is_displayed()
print(result)

driver.quit()
```

输出结果：

```
{'width': 500, 'height': 22}
©2015 Baidu 使用百度前必读 意见反馈 京 ICP 证 030173 号
text
True
```

执行上面的程序并查看结果： size 方法用于获取百度输入框的宽、 高， text 方法用于获得百度底部的备案信息， get_attribute()用于获得百度输入的 type 属性的值， is_displayed()用于返回一个元素是否可见， 如果可见则返回 True， 否则返回 False。





## 鼠标事件 行为链

在 WebDriver 中， 将这些关于鼠标操作的方法封装在 ActionChains 类提供。

ActionChains 类提供了鼠标操作的常用方法：

- perform()： 执行所有 ActionChains 中存储的行为；
- context_click()： 右击；
- double_click()： 双击；
- drag_and_drop()： 拖动；
- move_to_element()： 鼠标悬停。

### 鼠标悬停操作

![img](http://orru5lls3.bkt.clouddn.com/xuanting.jpg)

```
from selenium import webdriver
# 引入 ActionChains 类
from selenium.webdriver.common.action_chains import ActionChains

driver = webdriver.Chrome()
driver.get("https://www.baidu.cn")

# 定位到要悬停的元素
above = driver.find_element_by_link_text("设置")
# 对定位到的元素执行鼠标悬停操作
ActionChains(driver).move_to_element(above).perform()

……
```



```
from selenium import webdriver

driver = webdriver.Chrome()

inputTag = driver.find_element_by_id('kw')
submitTag = driver.find_element_by_id('su')

actions = ActionChains(driver)
actions.move_to_element(inputTag)
actions.send_keys_to_element(inputTag, 'python')
actions.move_to_element(submitTag)
actions.click(submitTag)
actions.perform()
```

>  click_and_hold(element) 点击但不松开鼠标
>
> move_to_element(above)
>
> context_click() 方法用于模拟鼠标右键操作， 在调用时需要指定元素定位。
>
> perform() 执行所有 ActionChains 中存储的行为， 可以理解成是对整个操作的提交动作。







## 键盘事件



### send_keys (Python)



Keys()类提供了键盘上几乎所有按键的方法。 前面了解到， send_keys()方法可以用来模拟键盘输入， 除此 之外， 我们还可以用它来输入键盘上的按键， 甚至是组合键， 如 Ctrl+A、 Ctrl+C 等。

```
from selenium import webdriver
# 引入 Keys 模块
from selenium.webdriver.common.keys import Keys

driver = webdriver.Chrome()
driver.get("http://www.baidu.com")

# 输入框输入内容
driver.find_element_by_id("kw").send_keys("seleniumm")

# 删除多输入的一个 m
driver.find_element_by_id("kw").send_keys(Keys.BACK_SPACE)


# 输入空格键+“教程”
driver.find_element_by_id("kw").send_keys(Keys.SPACE)
driver.find_element_by_id("kw").send_keys("教程")

# ctrl+a 全选输入框内容
driver.find_element_by_id("kw").send_keys(Keys.CONTROL, 'a')

# ctrl+x 剪切输入框内容
driver.find_element_by_id("kw").send_keys(Keys.CONTROL, 'x')

# ctrl+v 粘贴内容到输入框
driver.find_element_by_id("kw").send_keys(Keys.CONTROL, 'v')

# 通过回车键来代替单击操作
driver.find_element_by_id("su").send_keys(Keys.ENTER)
driver.quit()
```

需要说明的是， 上面的脚本没有什么实际意义， 仅向我们展示模拟键盘各种按键与组合键的用法。

- from selenium.webdriver.common.keys import Keys

在使用键盘按键方法前需要先导入 keys 类。

以下为常用的键盘操作：

- send_keys(Keys.BACK_SPACE) 删除键（BackSpace）
- send_keys(Keys.SPACE) 空格键(Space)
- send_keys(Keys.TAB) 制表键(Tab)
- send_keys(Keys.ESCAPE) 回退键（Esc）
- send_keys(Keys.ENTER) 回车键（Enter）
- send_keys(Keys.CONTROL,‘a’) 全选（Ctrl+A）
- send_keys(Keys.CONTROL,‘c’) 复制（Ctrl+C）
- send_keys(Keys.CONTROL,‘x’) 剪切（Ctrl+X）
- send_keys(Keys.CONTROL,‘v’) 粘贴（Ctrl+V）
- send_keys(Keys.F1) 键盘 F1
- ……
- send_keys(Keys.F12) 键盘 F12



### sendKeys(nodejs)



sendKeys()方法可以用来模拟用户按下键盘，组合键比如`ctrl+c`等也可以模拟。

打开一个页面，然后一下往下拉，直到滚动条拉到最底部, 用模拟按空格键的方式实现。



#### SPACE 空格

```
var webdriver = require('selenium-webdriver'),
  By = webdriver.By;

var Key = webdriver.Key;

var dr = new webdriver.Builder().forBrowser('chrome').build();
dr.get('http://www.xuxuehua.com');

// 把页面的body找到，在body上模拟按钮，这是整页面模拟按键事件的小技巧
var body = dr.findElement(By.css("body"));

// 每隔1.5s按一次空格键
// setTimeout在js binding中相当于其他binding中的sleep功能
for (var i = 0; i < 10; i++) {
  setTimeout(function() {
    body.sendKeys(Key.SPACE);
  }, i * 1500);
}
```





## 组合事件

当页面的交互比较复杂的时候，我们可能会使用到组合事件。比如先把鼠标移动到某个元素上，然后按住鼠标，将该元素拖到另一个地方。为了完成这种操作，我们就需要使用组合事件[ActionSequence](http://seleniumhq.github.io/selenium/docs/api/javascript/module/selenium-webdriver/index_exports_ActionSequence.html)

组合事件中，我们可以组合鼠标和键盘的操作，这些操作将根据我们组合的顺序依次执行。



### ActionSequence

####  基本用法

- 直接使用`WebDriver.actions()`进行调用，不需要额外的初始化工作;
- 只有显示调用了`perform()`方法才让所有动作真正得到执行；简而言之，一定要调用`perform()`;

```
driver.actions().
     keyDown(Key.SHIFT).
     click(element1).
     click(element2).
     dragAndDrop(element3, element4).
     keyUp(Key.SHIFT).
     perform();
```



#### 实例方法

- click( opt_elementOrButton, opt_button ): 单击，相当于把鼠标移动到元素的中心，然后点击左键
- doubleClick( opt_elementOrButton, opt_button ): 双击, 相当于把鼠标移动到元素的中心，然后双击左键
- this.dragAndDrop( element, location ): 拖拽，第1个参数是拖谁(WebElement)，第2个参数是拽到哪里，可以是WebElement，也可以是坐标`{x: number, y: number}`
- this.keyDown( key ): 按下一个键，必须是{ALT, CONTROL, SHIFT, COMMAND, META}中的一个， 会一直按着，除非调用keyUp()进行松开
- this.keyUp( key ): 松开一个键，必须是{ALT, CONTROL, SHIFT, COMMAND, META}中的一个
- this.mouseDown( opt_elementOrButton, opt_button ): 按下鼠标，除非mouseUp，否则不松开
- this.mouseMove( location, opt_offset ): 把鼠标移动到元素的中心或者具体位置，当然，第2个参数可以也可以增加1个偏移量, `{x: number, y: number}`
- this.mouseUp( opt_elementOrButton, opt_button ): 松开鼠标
- this.sendKeys( …var_args ): 除了具体的Key以外，该方法也可以接受Array，这样模拟组合键就容易多了



## 其他操作



### 获取断言信息

不管是在做功能测试还是自动化测试，最后一步需要拿实际结果与预期进行比较。这个比较的称之为**断言**。

我们通常可以通过获取title 、URL和text等信息进行断言。text方法在前面已经讲过，它用于获取标签对之间的文本信息。 下面同样以百度为例，介绍如何获取这些信息。

```
from selenium import webdriver
from time import sleep


driver = webdriver.Firefox()
driver.get("https://www.baidu.com")

print('Before search================')

# 打印当前页面title
title = driver.title
print(title)

# 打印当前页面URL
now_url = driver.current_url
print(now_url)

driver.find_element_by_id("kw").send_keys("selenium")
driver.find_element_by_id("su").click()
sleep(1)

print('After search================')

# 再次打印当前页面title
title = driver.title
print(title)

# 打印当前页面URL
now_url = driver.current_url
print(now_url)

# 获取结果数目
user = driver.find_element_by_class_name('nums').text
print(user)

driver.quit()
```

脚本运行结果如下：

```
Before search================
百度一下，你就知道
https://www.baidu.com/
After search================
selenium_百度搜索
https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=0&rsv_idx...
搜索工具
百度为您找到相关结果约5,380,000个
```

- title：用于获得当前页面的标题。
- current_url：用户获得当前页面的URL。
- text：获取搜索条目的文本信息。



### 断言 (nodejs)

```
var webdriver = require('selenium-webdriver'),
  By = webdriver.By;

var assert = require('selenium-webdriver/testing/assert');

var dr = new webdriver.Builder().forBrowser('chrome').build();
dr.get('http://www.testclass.net/');

dr.getTitle().then(function(title) {
  assert(title).contains('测试教程网');
});

dr.getCurrentUrl().then(function(url) {
  assert(url).contains('testclass');
});

dr.quit()
```



上面断言全部通过的时候看不到任何的结果，大家可以试着修改一下断言，让断言失败，这时候就应该可以看到类似下面的信息

```
AssertionError: 测试教程网 · 测试教程网.indexOf(测试教程网12443) !== -1
```












### 多表单切换(Python)
在Web应用中经常会遇到frame/iframe表单嵌套页面的应用，WebDriver只能在一个页面上对元素识别与定位，对于frame/iframe表单内嵌页面上的元素无法直接定位。这时就需要通过switch_to.frame()方法将当前定位的主体切换为frame/iframe表单的内嵌页面中。

```
<html>
  <body>
    ...
    <iframe id="x-URS-iframe" ...>
      <html>
         <body>
           ...
           <input name="email" >
```

126邮箱登录框的结构大概是这样子的，想要操作登录框必须要先切换到iframe表单。

```
from selenium import webdriver

driver = webdriver.Chrome()
driver.get("http://www.126.com")

driver.switch_to.frame('x-URS-iframe')
driver.find_element_by_name("email").clear()
driver.find_element_by_name("email").send_keys("username")
driver.find_element_by_name("password").clear()
driver.find_element_by_name("password").send_keys("password")
driver.find_element_by_id("dologin").click()
driver.switch_to.default_content()

driver.quit()
```

switch_to.frame() 默认可以直接取表单的id 或name属性。如果iframe没有可用的id和name属性，则可以通过下面的方式进行定位。

```
……
#先通过xpth定位到iframe
xf = driver.find_element_by_xpath('//*[@id="x-URS-iframe"]')

#再将定位对象传给switch_to.frame()方法
driver.switch_to.frame(xf)
……
driver.switch_to.parent_frame()
```

除此之外，在进入多级表单的情况下，还可以通过switch_to.default_content()跳回最外层的页面。



### 多表单切换(nodejs)

* switchTo().frame(id)

id可以是1个数字，从0开始。switchTo().frame(0)表示切换到页面上的第1个frame，依此类推;
id也可以是1个WebElement对象，也就是需要切换到的frame;
id可以是null，等于是调用了switchTo().defaultContent()方法;

* switchTo().defaultContent()

切换回到主页面，调用这个方法会将定位的上下文切回主页面，此后就可以继续定位主页面上的元素了。



```
var webdriver = require('selenium-webdriver'),
  By = webdriver.By;

var dr = new webdriver.Builder().forBrowser('chrome').build();
dr.get('some url');

// 切换到第2个frame进行元素定位
dr.switchTo().frame(1).then(function() {
  dr.findElement(By.id('this-element-is-in-frame'));
});

// 切换到第id==example-frame的frame中进行元素定位
dr.findElement(By.id('example-frame')).then(function(iframe) {
  dr.switchTo().frame(iframe).then(function() {
    dr.findElement(By.id('this-element-is-in-frame'));
  });
});

// 切换回主页面
dr.switchTo().defaultContent();
```





### 多窗口切换(Python)

在页面操作过程中有时候点击某个链接会弹出新的窗口，这时就需要主机切换到新打开的窗口上进行操作。WebDriver提供了switch_to.window()方法，可以实现在不同的窗口之间切换。 以百度首页和百度注册页为例，在两个窗口之间的切换如下图。![img](http://orru5lls3.bkt.clouddn.com/more_windows_n.png)

```
from selenium import webdriver
import time

driver = webdriver.Firefox()
driver.implicitly_wait(10)
driver.get("http://www.baidu.com")

# 获得百度搜索窗口句柄
sreach_windows = driver.current_window_handle

driver.find_element_by_link_text('登录').click()
driver.find_element_by_link_text("立即注册").click()

# 获得当前所有打开的窗口的句柄
all_handles = driver.window_handles

# 进入注册窗口
for handle in all_handles:
    if handle != sreach_windows:
        driver.switch_to.window(handle)
        print('now register window!')
        driver.find_element_by_name("account").send_keys('username')
        driver.find_element_by_name('password').send_keys('password')
        time.sleep(2)
        # ……


driver.quit()
```

在本例中所涉及的新方法如下：

- current_window_handle：获得当前窗口句柄。
- window_handles：返回所有窗口的句柄到当前会话。
- switch_to.window()：用于切换到相应的窗口，与上一节的switch_to.frame()类似，前者用于不同窗口的切换，后者用于不同表单之间的切换。



### 多窗口切换(nodejs)

有时候我们点击链接会弹出新窗口，我们需要去新窗口继续定位和操作元素，这时候就需要用到切换窗口的操作了。

switchTo().window(name_or_handle)方法可以切换到目标窗口。

一般来说，不建议大家直接使用上面的方法去切换，更明智的做法是获取要打开的窗口的链接，然后直接用get访问该链接，这样就不需要写切换窗口的代码了。




* switchTo().window(name_or_handle)

窗口的name，这是为了兼容以前的实现，至于窗口的name是什么，我不太清楚
窗口句柄，使用driver.getAllWindowHandles()句柄就可以返回浏览器中所有的打开的标签句柄了



```
dr.getAllWindowHandles().then(function(handles) }{
  for (var i = 0; i < handles.length; i++) {
    dr.switchTo().window(handles[i]);
  }
});
```









### 警告框处理

在WebDriver中处理JavaScript所生成的alert、confirm以及prompt十分简单，具体做法是使用 switch_to.alert 方法定位到 alert/confirm/prompt，然后使用text/accept/dismiss/ send_keys等方法进行操作。

- text：返回 alert/confirm/prompt 中的文字信息。
- accept()：接受现有警告框。
- dismiss()：解散现有警告框。
- send_keys(keysToSend)：发送文本至警告框。keysToSend：将文本发送至警告框。

如下图，百度搜索设置弹出的窗口是不能通过前端工具对其进行定位的，这个时候就可以通过switch_to_alert()方法接受这个弹窗。![img](http://orru5lls3.bkt.clouddn.com/alert_windows.png)

```
from selenium import webdriver
from selenium.webdriver.common.action_chains import ActionChains
import time

driver = webdriver.Firefox()
driver.implicitly_wait(10)
driver.get('http://www.baidu.com')

# 鼠标悬停至“设置”链接
link = driver.find_element_by_link_text('设置')
ActionChains(driver).move_to_element(link).perform()

# 打开搜索设置
driver.find_element_by_link_text("搜索设置").click()

# 保存设置
driver.find_element_by_class_name("prefpanelgo").click()
time.sleep(2)

# 接受警告框
driver.switch_to.alert.accept()

driver.quit()
```

通过switch_to_alert()方法获取当前页面上的警告框，并使用accept()方法接受警告框。



### 下拉框选择(Python)

有时我们会碰到下拉框，WebDriver提供了Select类来处理下拉框。 如百度搜索设置的下拉框，如下图：![img](http://orru5lls3.bkt.clouddn.com/select.png)

```
from selenium import webdriver
from selenium.webdriver.support.select import Select
# 可能是另一种导入方式 from selenium.webdriver.support.ui import Select
from time import sleep

driver = webdriver.Chrome()
driver.implicitly_wait(10)
driver.get('http://www.baidu.com')

# 鼠标悬停至“设置”链接
driver.find_element_by_link_text('设置').click()
sleep(1)
# 打开搜索设置
driver.find_element_by_link_text("搜索设置").click()
sleep(2)

# 搜索结果显示条数
sel = driver.find_element_by_xpath("//select[@id='nr']")
Select(sel).select_by_value('50')  # 显示50条
# ……

driver.quit()
```

>  Select类用于定位select标签。 select_by_value() 方法用于定位下接选项中的value值。
>
> select_by_index()
>
> select_by_visible_text()
>
> deselect_all()



### 下拉框选择(nodejs)

下拉列表的标签是select，如上所示，其中

- select表示下拉列表
- option表示列表里的子项

html里，下拉列表中一共有3项，分别是Option One, Option Two和Option Three。

```
<select id="select">
  <optgroup label="Option Group">
    <option>Option One</option>
    <option>Option Two</option>
    <option>Option Three</option>
  </optgroup>
</select>
```



```
var path = require('path');
var webdriver = require('selenium-webdriver'),
  By = webdriver.By;

var testFile = "file://" + path.join(__dirname,  "index.html")

var dr = new webdriver.Builder().forBrowser('chrome').build();
dr.get(testFile)

dr.findElement(By.id('select')).then(function(select) {
  dr.findElements(By.css('option')).then(function(options) {
    options[options.length - 1].click();
  });
});
```

### 运行结果

![img](http://wx4.sinaimg.cn/mw1024/726a2979gy1fhp1kxp8qdj20f606kaa6.jpg)



### 文件上传

对于通过input标签实现的上传功能，可以将其看作是一个输入框，即通过send_keys()指定本地文件路径的方式实现文件上传。

创建upfile.html文件，代码如下：

```
<html>
<head>
<meta http-equiv="content-type" content="text/html;charset=utf-8" />
<title>upload_file</title>
<link href="http://cdn.bootcss.com/bootstrap/3.3.0/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
  <div class="row-fluid">
	<div class="span6 well">
	<h3>upload_file</h3>
	  <input type="file" name="file" />
	</div>
  </div>
</body>
<script src="http://cdn.bootcss.com/bootstrap/3.3.0/css/bootstrap.min.js"></scrip>
</html>
```

通过浏览器打开upfile.html文件，功能如下图。![img](http://orru5lls3.bkt.clouddn.com/upfile.png)

接下来通过send_keys()方法来实现文件上传。

```
from selenium import webdriver
import os

driver = webdriver.Firefox()
file_path = 'file:///' + os.path.abspath('upfile.html')
driver.get(file_path)

# 定位上传按钮，添加本地文件
driver.find_element_by_name("file").send_keys('D:\\upload_file.txt')

driver.quit()
```



### cookie操作

有时候我们需要验证浏览器中cookie是否正确，因为基于真实cookie的测试是无法通过白盒和集成测试进行的。WebDriver提供了操作Cookie的相关方法，可以读取、添加和删除cookie信息。

WebDriver操作cookie的方法：

- get_cookies()： 获得所有cookie信息。
- get_cookie(name)： 返回字典的key为“name”的cookie信息。
- add_cookie(cookie_dict) ： 添加cookie。“cookie_dict”指字典对象，必须有name 和value 值。
- delete_cookie(name,optionsString)：删除cookie信息。“name”是要删除的cookie的名称，“optionsString”是该cookie的选项，目前支持的选项包括“路径”，“域”。
- delete_all_cookies()： 删除所有cookie信息。

下面通过get_cookies()来获取当前浏览器的cookie信息。

```
from selenium import webdriver

driver = webdriver.Firefox()
driver.get("http://www.youdao.com")

# 获得cookie信息
cookie= driver.get_cookies()
# 将获得cookie的信息打印
print(cookie)

driver.quit()
```

从执行结果可以看出，cookie数据是以字典的形式进行存放的。知道了cookie的存放形式，接下来我们就可以按照这种形式向浏览器中写入cookie信息。

```
from selenium import webdriver

driver = webdriver.Firefox()
driver.get("http://www.youdao.com")

# 向cookie的name 和value中添加会话信息
driver.add_cookie({'name': 'key-aaaaaaa', 'value': 'value-bbbbbb'})

# 遍历cookies中的name 和value信息并打印，当然还有上面添加的信息
for cookie in driver.get_cookies():
    print("%s -> %s" % (cookie['name'], cookie['value']))

driver.quit()


输出结果：
======================== RESTART: =========================
YOUDAO_MOBILE_ACCESS_TYPE -> 1
_PREF_ANONYUSER__MYTH -> aGFzbG9nZ2VkPXRydWU=
OUTFOX_SEARCH_USER_ID -> -1046383847@218.17.158.115
JSESSIONID -> abc7qSE_SBGsVgnVLBvcu
key-aaaaaaa -> value-bbbbbb
```

从执行结果可以看到，最后一条cookie信息是在脚本执行过程中通过add_cookie()方法添加的。通过遍历得到所有的cookie信息，从而找到key为“name”和“value”的特定cookie的value。



### 调用JavaScript代码

虽然WebDriver提供了操作浏览器的前进和后退方法，但对于浏览器滚动条并没有提供相应的操作方法。在这种情况下，就可以借助JavaScript来控制浏览器的滚动条。WebDriver提供了execute_script()方法来执行JavaScript代码。

用于调整浏览器滚动条位置的JavaScript代码如下：

```
<!-- window.scrollTo(左边距,上边距); -->
window.scrollTo(0,450);
```

window.scrollTo()方法用于设置浏览器窗口滚动条的水平和垂直位置。方法的第一个参数表示水平的左间距，第二个参数表示垂直的上边距。其代码如下：

```
from selenium import webdriver
from time import sleep

# 访问百度
driver=webdriver.Firefox()
driver.get("http://www.baidu.com")

# 设置浏览器窗口大小
driver.set_window_size(500, 500)

# 搜索
driver.find_element_by_id("kw").send_keys("selenium")
driver.find_element_by_id("su").click()
sleep(2)

# 通过javascript设置浏览器窗口的滚动条位置
js="window.scrollTo(100,450);"
driver.execute_script(js)
sleep(3)

driver.quit()
```

通过浏览器打开百度进行搜索，并且提前通过set_window_size()方法将浏览器窗口设置为固定宽高显示，目的是让窗口出现水平和垂直滚动条。然后通过execute_script()方法执行JavaScripts代码来移动滚动条的位置。







## 截图



### 全屏截图(Python

```
driver.save_screenshot('screen.png')
```



### 窗口截图(Python)

自动化用例是由程序去执行的，因此有时候打印的错误信息并不十分明确。如果在脚本执行出错的时候能对当前窗口截图保存，那么通过图片就可以非常直观地看出出错的原因。WebDriver提供了截图函数get_screenshot_as_file()来截取当前窗口。

```
from selenium import webdriver
from time import sleep

driver = webdriver.Firefox()
driver.get('http://www.baidu.com')

driver.find_element_by_id('kw').send_keys('selenium')
driver.find_element_by_id('su').click()
sleep(2)

# 截取当前窗口，并指定截图图片的保存位置
driver.get_screenshot_as_file("D:\\baidu_img.jpg")

driver.quit()
```

脚本运行完成后打开D盘，就可以找到baidu_img.jpg图片文件了。



###窗口截图(nodejs)

```
var webdriver = require('selenium-webdriver'),
  By = webdriver.By;

var Key = webdriver.Key;

var dr = new webdriver.Builder().forBrowser('chrome').build();
dr.get('http://www.testclass.net/selenium_javascript/');
dr.takeScreenshot().then(function(data) {
  require('fs').writeFile('pic.png', data, 'base64');
  dr.quit();
})
```





### 元素截图

`screenshot_as_png`

```
code = self.driver.find_element_by_xpath("//img[@alt='CAPTCHA']")
        img = code.screenshot_as_png
        img_name = "./code/code{}.png".format(time.strftime('%Y-%m-%d_%H%M%S', time.localtime(time.time())))
        with open(img_name, 'wb') as f:
            f.write(img)
        rec_code = self.code_recog(img_name)
```









## 执行javascript

使用driver.executeScript(js)方法会在当前的定位上下文里执行相应的js脚本。

### 传参

executeScript方法支持向脚本中传入参数，所有参数的参数都可以在脚本中通过`arguments`对象来引用。我们可以传入下列类型的参数:

- boolean
- number
- string
- WebElement:也就是我们定位到的页面对象
- Array或者是Object: 只要里面的每一项都是上面的类型就好了

### 返回值

同样的，我们可以接收js脚本的返回值，返回值有下面一些情况

- 如果js返回HTML元素，那么该元素会自动被封装成WebElement
- null 或者 undefined会被转成null
- boolean, number, string 不做转换
- function会被转成相应的字符串表示
- Array和Object中的每一项都按照上面的规则转换

 

```
var path = require('path');
var webdriver = require('selenium-webdriver'),
  By = webdriver.By;

var url = "http://www.baidu.com/";

var dr = new webdriver.Builder().forBrowser('chrome').build();
dr.get(url)

// 先隐藏掉百度一下按钮
// 通过arguments[0]传入百度一下按钮
var hideBtn = "arguments[0].style.display='none';"

dr.executeScript(hideBtn, dr.findElement(By.id('su')));


// 然后返回页面上所有导航链接的文本
// 通过return返回需要的结果
var linkTexts = "return $('.mnav').text()"
dr.executeScript(linkTexts).then(function(texts) {
  console.log(texts);
});

// 最后把页面背景变成黑色

dr.executeScript("document.body.style.backgroundColor='black'");
```



## 处理javascript弹出框

### 一般的处理方式

当alert弹出之后，我们可以通过类似下面的代码去处理alert

```
driver.switchTo().alert().dismiss();
driver.switchTo().alert().accept();
```

切换到alert/confirm/prompt之后，我们可以进行如下的后续动作

- accept(): 接受,点ok
- dismiss(): 点取消
- getText(): 拿到提示文本
- sendKeys( text ): 如果是prompt的话，可以用该方法输入一些内容
- authenticateAs( username, password ): 如果是[basic authentication](https://en.wikipedia.org/wiki/Basic_access_authentication)的话，可以通过该方法来输入用户名和密码

### 一劳永逸的处理方式

如果我们不在乎alert上提示的内容，只想页面上不弹出alert/confirm/prompt的话，可以通过js来覆盖这些方法的原生实现，从而达到**禁用**弹出框的效果，比如下面的代码就演示了如何禁用alert。

```
var banAlert = 'window.alert = function(msg){}'
driver.executeScript(banAlert);
```

这样在测试过程中，所有的alert都不会弹出。







# Example



## lagou.com

```
from selenium import webdriver
from lxml import etree
import re
import requests
import time
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By


class LagouSpider(object):

    def __init__(self):
        self.driver = webdriver.Chrome()
        self.url = 'https://www.lagou.com/jobs/list_python?city=%E5%8C%97%E4%BA%AC&cl=false&fromSearch=true&labelWords=&suginput='
        self.positions = []

    def run(self):
        self.driver.get(self.url)
        while True:
            source = self.driver.page_source
            WebDriverWait(driver=self.driver, timeout=10).until(
                EC.presence_of_element_located((By.XPATH, "//div[@class='pager_container']/span[last()]"))
            )
            self.parse_list_page(source)
            time.sleep(2)
            try:
                next_button = self.driver.find_element_by_xpath("//div[@class='pager_container']/span[last()]")
                if 'pager_next_disabled' in next_button.get_attribute("class"):  # 最后一页就点不了了
                    break
                else:
                    next_button.click()
            except:
                print(source)
            time.sleep(1)

    def parse_list_page(self, source):
        html = etree.HTML(source)
        links = html.xpath("//a[@class='position_link']/@href")
        for link in links:
            self.request_detail_page(link)
            time.sleep(1)

    def request_detail_page(self, url):
        self.driver.execute_script("window.open('%s')" % url)
        self.driver.switch_to.window(self.driver.window_handles[1])
        WebDriverWait(self.driver, timeout=10).until(
            EC.presence_of_element_located((By.XPATH, "//span[@class='name']"))
        )
        source = self.driver.page_source
        self.parse_detail_page(source)
        self.driver.close()  # 关闭当前页面
        self.driver.switch_to.window(self.driver.window_handles[0])

    def parse_detail_page(self, source):
        html = etree.HTML(source)
        position_name = html.xpath("//span[@class='name']/text()")[0]
        job_request_spans = html.xpath("//dd[@class='job_request']//span")
        salary = job_request_spans[0].xpath('.//text()')[0].strip()
        city = job_request_spans[1].xpath('./text()')[0].strip()
        city = re.sub(r'[\s/]', '', city)  # 替换斜杠
        work_years = job_request_spans[2].xpath('.//text()')[0].strip()
        work_years = re.sub(r'[\s/]', '', work_years)
        education = job_request_spans[3].xpath('.//text()')[0].strip()
        education = re.sub(r'[\s/]', '', education)
        job_desc = ''.join(html.xpath("//dd[@class='job_bt']//text()")).strip()
        company_name = html.xpath("//h2[@class='fl']/text()")[0].strip()
        position = {
            'name': position_name,
            'company_name': company_name,
            'salary': salary,
            'work_years': work_years,
            'education': education,
            'job_desc': job_desc
        }
        self.positions.append(position)
        print(self.positions)
        print('='*66)


if __name__ == '__main__':
    spider = LagouSpider()
    spider.run()
```