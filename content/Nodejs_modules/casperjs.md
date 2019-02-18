---
title: "casperjs"
date: 2018-08-15 16:30
---

[TOC]



# casperjs



## 安装

### 依赖

[PhantomJS](https://github.com/RaHsu/casperjs-document-chinese/blob/master/phantomjs.org) 1.9.1 或更高版本。具体请查阅[PhantomJS的安装说明](https://github.com/RaHsu/casperjs-document-chinese/blob/master/phantomjs.org/download.html)

从1.1.0版本之后，不管你使用哪种安装方法安装CasperJS，你都必须先安装一个引擎（PhantomJS，SlimerJS），它们是你使用CasperJS的先决条件。



### OSX 

首先可不要忘了你的更新命令：

```
    $ brew update
```

安装1.1以上版本（推荐）：

```
$ brew install casperjs
```

如果你已经安装了casperjs并想获取它的最新版本（稳定版|开发版），使用`upgrade`命令：

```
$ brew upgrade casperjs
```



### npm安装

你可以通过npm安装CasperJS：

- 常用：

```
$ npm install -g casperjs
```

> 提示

-g 命令安装后，你可以在系统全局中使用CasperJS。



- 安装某个特定版本：
  - beta3：`npm install -g casperjs@1.1.0-beta3`
  - beta2：`$ npm install -g casperjs@1.1.0-beta2`
- 如果你想使用npm安装主干（master）版本：

```
$ npm install -g git https://github.com/casperjs/casperjs.git
```


> 警告

虽然CasperJS通过npm安装，但它并不是一个NodeJS模块，也不可以仅通过NodeJS执行。所以你不可以使用`require(‘casperjs’)`来加载CasperJS。请注意，CasperJS无法使用绝大多数NodeJS模块。



### git安装

你也可以通过git安装，CasperJS的代码主要是托管在github上的。

* 安装主干分支

```
$ git clone git://github.com/casperjs/casperjs.git
$ cd casperjs
$ ln -sf `pwd`/bin/casperjs /usr/local/bin/casperjs
```

如果PhantomJS和CasperJS已经安装成功，输入以下命令你应该得到相应的结果：

```
$ phantomjs --version
1.9.2
$ casperjs
CasperJS version 1.1.0-beta4 at /Users/niko/Sites/casperjs, using phantomjs version 1.9.2
# ...
```

如果你喜欢使用PhantomJS：

```
$ slimerjs --version
Innophi SlimerJS 0.8pre, Copyright 2012-2013 Laurent Jouanneau & Innophi
$ casperjs
CasperJS version 1.1.0 at /Users/niko/Sites/casperjs, using slimerjs version 0.8.0
```

 



## Hello World

创建一个名为`sample.js`的文件，写入以下代码：

```
var casper = require('casper').create();

casper.start('http://casperjs.org/', function() {
    this.echo(this.getTitle());
});

casper.thenOpen('http://phantomjs.org', function() {
    this.echo(this.getTitle());
});

casper.run();
```

运行脚本（使用python）：

```
$ casperjs sample.js
```

运行脚本（使用node）：[这里](https://github.com/casperjs/casperjs/issues/1864)有关于node的详细信息

```
$ node casperjs.js sample.js
```

运行脚本（使用PhantomJS）

```
$ phantomjs casperjs.js sample.js
```

得到如下的结果：

```
$ casperjs sample.js
CasperJS, a navigation scripting and testing utility for PhantomJS
PhantomJS: Headless WebKit with JavaScript API
```



### 测试

CasperJS同时也是一个测试框架，测试脚本与抓取脚本略有不同，尽管它们共享了大部分的API。

一个简单的测试脚本：

```
// hello-test.js
casper.test.begin("Hello, Test!", 1, function(test) {
  test.assert(true);
  test.done();
});
```

使用CasperJS的测试子命令来运行它：

```
$ casperjs test hello-test.js
Test file: hello-test.js
# Hello, Test!
PASS Subject is strictly true
PASS 1 test executed in 0.023s, 1 passed, 0 failed, 0 dubious, 0 skipped.
```



## 命令行及参数

CasperJS附带了位于cli模块中的PhantomJS解析器之上的内置命令行解析器。它将传递的参数公开为**位置**和**命名选项**。

Casper实例总是包含一个可以使用的cli属性来方便访问这些参数，因此您不用担心操作cli模块解析API。

我们来看看这个简单的casper脚本：

```
var casper = require("casper").create();

casper.echo("Casper CLI passed args:");
require("utils").dump(casper.cli.args);

casper.echo("Casper CLI passed options:");
require("utils").dump(casper.cli.options);

casper.exit();
```

> ##### 提示

请注意这两个casper-path和cli选项，这些选项通过casperjs Python可执行文件传递给casper脚本。

运行结果：

```
$ casperjs test.js arg1 arg2 arg3 --foo=bar --plop anotherarg
Casper CLI passed args: [
    "arg1",
    "arg2",
    "arg3",
    "anotherarg"
]
Casper CLI passed options: {
    "casper-path": "/Users/niko/Sites/casperjs",
    "cli": true,
    "foo": "bar",
    "plop": true
}
```

获取，查询或删除参数：

```
var casper = require("casper").create();
casper.echo(casper.cli.has(0));
casper.echo(casper.cli.get(0));
casper.echo(casper.cli.has(3));
casper.echo(casper.cli.get(3));
casper.echo(casper.cli.has("foo"));
casper.echo(casper.cli.get("foo"));
casper.cli.drop("foo");
casper.echo(casper.cli.has("foo"));
casper.echo(casper.cli.get("foo"));
casper.exit();
```

运行结果：

```
$ casperjs test.js arg1 arg2 arg3 --foo=bar --plop anotherarg
true
arg1
true
anotherarg
true
bar
false
undefined
```

> ##### 提示

您可能需要在Windows中包裹含有转义双引号的空格的选项。如：`-foo = \“space bar \”`。

> ##### 提示

如果你想检查是否有任何参数或选项传递到你的脚本怎么办？ 你可以这样：

```
// removing default options passed by the Python executable
casper.cli.drop("cli");
casper.cli.drop("casper-path");

if (casper.cli.args.length === 0 && Object.keys(casper.cli.options).length === 0) {
    casper.echo("No arg nor option passed").exit();
}
```

### casperjs 本地选项

*版本1.1新增* casperjs命令行有3个可用参数：

- `--direct`：将log信息输出在控制台中。
- `--log-level=[debug|info|warning|error]` 设置log类别
- `--engine=[phantomjs|slimerjs]` 选择你想使用的浏览器内核。CasperJS支持 PhantomJS（默认）使用的Webkit内核， 和 SlimerJS 使用的Gecko内核。

> 警告 *在版本1.1及以后版本弃用* `--direct`选项已经重命名为`--verbose`，尽管`--direct`仍然可以使用，但我们建议你放弃使用它从而使用重命名之后的选项。

示例：

```
$ casperjs --verbose --log-level=debug myscript.js
```

最后提醒，你仍然可以像运行PhantomJS的脚本一样使用PhantomJS的标准CLI选项来运行脚本。

```
$ casperjs --web-security=no --cookies-file=/tmp/mycookies.txt myscript.js
```

> ##### 提示

如果你想知道本地PhantomJS cli拥有哪些可用的选项，你可以运行phantomjs的`--help`命令， SlimerJS和PhantomJS的命令基本上相同。

### 原始参数值

*版本1.0新增* 在默认情况下，cli对象将处理每个传递的参数并将它们转换到适当的检测类型，看一个例子：

```
var casper = require('casper').create();
var utils = require('utils');

utils.dump(casper.cli.get('foo'));

casper.exit();
```

如你所见，01234567的值被转换成了Nember。

如果你使用参数的原始值，可以在cli对象中使用raw属性，它保存了传入参数的原始值：

```
var casper = require('casper').create();
var utils = require('utils');

utils.dump(casper.cli.get('foo'));
utils.dump(casper.cli.raw.get('foo'));

casper.exit();
```

例子的用法：

```
var casper = require('casper').create();
var utils = require('utils');

utils.dump(casper.cli.get('foo'));
utils.dump(casper.cli.raw.get('foo'));

casper.exit();
```

由于ECMA脚本对数字的限制，对于非常长的数字（超过17位），CasperJS会使用参数的原始值。[更多信息](https://github.com/casperjs/casperjs/issues/1134)





## Selector 选择器

CasperJS大量使用选择器来处理DOM，并且可以透明地使用CSS3或XPath表达式。

```
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <title>My page</title>
</head>
<body>
    <h1 class="page-title">Hello</h1>
    <ul>
        <li>one</li>
        <li>two</li>
        <li>three</li>
    </ul>
    <footer><p>©2012 myself</p></footer>
</body>
</html>
```



### CSS3

在默认情况下，CasperJS接受CSS3的选择器字符串来检查元素。

如果你想检查`<h1 class="page-title">`元素是否存在，你可以这样写：

```
var casper = require('casper').create();

casper.start('http://domain.tld/page.html', function() {
    if (this.exists('h1.page-title')) {
        this.echo('the heading exists');
    }
});

casper.run();
```

如果你使用的是测试框架，你应该这样写：

```
casper.test.begin('The heading exists', 1, function suite(test) {
    casper.start('http://domain.tld/page.html', function() {
        test.assertExists('h1.page-title');
    }).run(function() {
        test.done();
    });
});
```

其他方便的测试方法都依赖于选择器：

```
casper.test.begin('Page content tests', 3, function suite(test) {
    casper.start('http://domain.tld/page.html', function() {
        test.assertExists('h1.page-title');
        test.assertSelectorHasText('h1.page-title', 'Hello');
        test.assertVisible('footer');
    }).run(function() {
        test.done();
    });
});
```



### XPath

你可以使用[XPath表达式](http://en.wikipedia.org/wiki/XPath)作为选择器。

```
casper.start('http://domain.tld/page.html', function() {
    this.test.assertExists({
        type: 'xpath',
        path: '//*[@class="plop"]'
    }, 'the element exists');
});
```

为了简化XPath表达式的阅读，CasperJS提供了一个selectXPath助手模块来帮助你：

```
var x = require('casper').selectXPath;

casper.start('http://domain.tld/page.html', function() {
    this.test.assertExists(x('//*[@id="plop"]'), 'the element exists');
});
```

CasperJS中唯一对XPath的限制是当你想在`casper.fill()`方法中填写文件字段时；PhantomJS本身只允许在其`uploadFile`方法中使用CSS3选择器，这加强了这个限制。



```
<a class="tab" href="Packages.aspx" style="display:inline-block;color:#666666;height:40px;width:120px;"><br>Packages</a>
```

```
casper.then(function () {
    this.click('a[href^="Packages.aspx"]');
});
```





## 测试

CasperJS拥有自己的测试框架，并提供一些工具来减轻你测试的负担。

> 警告 *在版本1.1后改变* 测试框架的所有API只能在使用casperjs test子命令时使用：

- 如果你想在CasperJS的测试环境之外使用casper.test属性，你将会得到一个错误。
- 对于1.1-bata3版本，您不能在此测试环境中覆盖预配置的casper实例。在[常见问题](http://docs.casperjs.org/en/latest/faq.html#faq-test-casper-instance)模块可以了解更详细的信息。

### 单元测试

假如我们想要测试一个“沉默的牛”对象：

```
function Cow() {
    this.mowed = false;
    this.moo = function moo() {
        this.mowed = true; // 让沉默牛能够发出叫声的方法
        return 'moo!';
    };
}
```

让我们为它编写一个小小的测试套件：

```
// cow-test.js
casper.test.begin('Cow can moo', 2, function suite(test) {
    var cow = new Cow();
    test.assertEquals(cow.moo(), 'moo!');
    test.assert(cow.mowed);
    test.done();
});
```

在casperjs的测试子命令中运行这个脚本：

```
$ casperjs test cow-test.js
```

理论上你应该得到这样的结果： [![img](https://camo.githubusercontent.com/b0122b75b136fc243e4b1d6dca835d350df54058/687474703a2f2f646f63732e6361737065726a732e6f72672f656e2f6c61746573742f5f696d616765732f636f772d746573742d6f6b2e706e67)](https://camo.githubusercontent.com/b0122b75b136fc243e4b1d6dca835d350df54058/687474703a2f2f646f63732e6361737065726a732e6f72672f656e2f6c61746573742f5f696d616765732f636f772d746573742d6f6b2e706e67)

如果你想要测试失败：

```
casper.test.begin('Cow can moo', 2, function suite(test) {
    var cow = new Cow();
    test.assertEquals(cow.moo(), 'BAZINGA!');
    test.assert(cow.mowed);
    test.done();
});
```

你就会得到这样的结果： [![img](https://camo.githubusercontent.com/1ab5d8ed3b500283395bc831b744fd831a5ad356/687474703a2f2f646f63732e6361737065726a732e6f72672f656e2f6c61746573742f5f696d616765732f636f772d746573742d6b6f2e706e67)](https://camo.githubusercontent.com/1ab5d8ed3b500283395bc831b744fd831a5ad356/687474703a2f2f646f63732e6361737065726a732e6f72672f656e2f6c61746573742f5f696d616765732f636f772d746573742d6b6f2e706e67)

> 提示 测试框架的API文档在[这里](http://docs.casperjs.org/en/latest/modules/tester.html)哦。

### 浏览器测试

我们来为google搜索来写一个测试套件吧：

```
// googletesting.js
casper.test.begin('Google search retrieves 10 or more results', 5, function suite(test) {
    casper.start("http://www.google.fr/", function() {
        test.assertTitle("Google", "google homepage title is the one expected");
        test.assertExists('form[action="/search"]', "main form is found");
        this.fill('form[action="/search"]', {
            q: "casperjs"
        }, true);
    });

    casper.then(function() {
        test.assertTitle("casperjs - Recherche Google", "google title is ok");
        test.assertUrlMatch(/q=casperjs/, "search term has been submitted");
        test.assertEval(function() {
            return __utils__.findAll("h3.r").length >= 10;
        }, "google search for \"casperjs\" retrieves 10 or more results");
    });

    casper.run(function() {
        test.done();
    });
});
```

运行这个测试套件：

```
$ casperjs test googletesting.js
```

你大概会得到这样的结果： ！[img](http://docs.casperjs.org/en/latest/_images/testsuiteok.png)

### 在测试环境中设置Casper选项

由于您必须在测试环境中使用预配置的casper实例，所以你可以通过这种方式来更新其选项：

```
casper.options.optionName = optionValue; // where optionName is obviously the desired option name

casper.options.clientScripts.push("new-script.js");
```

### 高级技巧

`Tester#begin()`接受函数或对象来描述一个套件，对象选项允许设置setUp（）和tearDown（）函数。

```
// cow-test.js
casper.test.begin('Cow can moo', 2, {
    setUp: function(test) {
        this.cow = new Cow();
    },

    tearDown: function(test) {
        this.cow.destroy();
    },

    test: function(test) {
        test.assertEquals(this.cow.moo(), 'moo!');
        test.assert(this.cow.mowed);
        test.done();
    }
});
```

### 测试命令参数和选项

#### 参数

casperjs test命令将把每个传递的参数视为包含测试的文件或目录路径，它将递归扫描任何传递的目录，以搜索* .js或* .coffee文件，并将它们添加到堆栈中。

> 警告 在写测试代码时你需要考虑下面两种情况：

- 你**不能**在一个测试文件中创建两个Casper实例。
- 当套件（或文件）中包含的所有测试都已被执行时，您**必须**调用Tester.done()。

#### 选项

选项之前一般都包含一个双横线前缀（--）：

- `--xunit=<filename>` 可以将测试套件的结果输出在一个XUnit XML文件中。
- `--direct` 或 `--verbose` 会直接将log消息输出在控制台中。
- `--log-level=<logLevel>` 可以设置log消息的级别（详情在[这里](http://docs.casperjs.org/en/latest/logging.html))
- 当所有测试都执行完毕后`--auto-exit=no` 可以避免直接退出测试进程，这通常允许执行补充操作，但意味着退出casper手动监听和退出测试器事件：

```
// $ casperjs test --auto-exit=no
casper.test.on("exit", function() {
  someTediousAsyncProcess(function() {
    casper.exit();
  });
});
```

*版本1.0新增*

- `--includes=foo.js,bar.js`将在执行每个测试文件之前包含foo.js和bar.js文件。
- `--pre = pre-test.js`将在执行整个测试套件**之前**添加pre-test.js中包含的测试。
- `--post = post-test.js`将在执行整个测试套件**之后**添加post-test.js中包含的测试。
- `--fail-fast`在遇到第一个错误时会立即终止当前测试套件的运行。
- `--concise`将创建一个更简洁的测试套件的输出。
- `--no-colors`将从casperjs创建一个没有（美丽）颜色的输出。

示例自定义命令：

```
$ casperjs test --includes=foo.js,bar.js \
                --pre=pre-test.js \
                --post=post-test.js \
                --direct \
                --log-level=debug \
                --fail-fast \
                test1.js test2.js /path/to/some/test/dir
```

> 警告 *版本1.1后弃用* `--direct`选项已经被重命名为`--verbose`，虽然`--direct`仍然可以使用，但是它已近被考虑将在未来版本中废弃。

> 提示 [这里](https://gist.github.com/3813361)有一个demo便于你开始使用涉及某些选项的示例套件。

### 将结果以XUnit格式输出

CasperJS支持将测试套件的结果导出到XUnit XML文件中，该文件与Jenkins等持续集成工具兼容。要保存测试套件的XUnit日志，请使用`--xunit`选项：

```
$ casperjs test googletesting.js --xunit=log.xml
```

你可以通过传递一个对象到`casper.test.fail()`函数自定义`name`属性的值，比如：

```
casper.test.fail('google search for "casperjs" retrieves 10 or more results', {name: 'result count is 10+'});
```

```
<?xml version="1.0" encoding="UTF-8"?>
<testsuites duration="1.249">
    <testsuite errors="0" failures="0" name="Google search retrieves 10 or more results" package="googletesting" tests="5" time="1.249" timestamp="2012-12-30T21:27:26.320Z">
        <testcase classname="googletesting" name="google homepage title is the one expected" time="0.813"/>
        <testcase classname="googletesting" name="main form is found" time="0.002"/>
        <testcase classname="googletesting" name="google title is ok" time="0.416"/>
        <testcase classname="googletesting" name="search term has been submitted" time="0.017"/>
        <testcase classname="googletesting" name="results count is 10+" time="0.001"/>
            <failure type="fail">google search for "casperjs" retrieves 10 or more results</failure>
        <system-out/>
    </testsuite>
</testsuites>
```

### CasperJS自检测

CasperJS拥有自己的功能测试套件，位于`test`子文件夹中，你可以这样运行它：

```
$ casperjs selftest
```

> 提示 使用测试套件是一个检测你平台bug的一个好方法，如果发生错误，你可以向我们[发送错误报告](https://github.com/casperjs/casperjs/issues/new)或向我们[发送邮件](https://groups.google.com/forum/#!forum/casperjs)来提问。

### 扩展Casper进行测试

下面的命令：

```
$ casperjs test [path]
```

是

```
$ casperjs /path/to/casperjs/tests/run.js [path]
```

的简写版本，所以如果你想为你的测试扩展Casper的功能，最好选择是编写你自己的程序并从这里扩展casper对象的示例。

> 提示 你可以在[run.js](https://github.com/casperjs/casperjs/blob/master/tests/run.js)中查看runner的源码



## casper模块

### casper类

获取casper实例最简单的方法是使用模块的`create()`方法：

```
var casper = require('casper').create();
```

您也可以自己检索主要功能并实例化：

```
var casper = new require('casper').Casper();
```

> 提示 在这里了解[如何扩展Casper](http://docs.casperjs.org/en/latest/extending.html)。

Casper构造器和`create()`函数都接受一个标准的js对象作为选项参数：

```
var casper = require('casper').create({
    verbose: true,
    logLevel: "debug"
});
```

### casper.options

选项对象可以被传递到Casper构造器中：

```
var casper = require('casper').create({
    clientScripts:  [
        'includes/jquery.js',      // These two scripts will be injected in remote
        'includes/underscore.js'   // DOM on every request
    ],
    pageSettings: {
        loadImages:  false,        // The WebPage instance used by Casper will
        loadPlugins: false         // use these settings
    },
    logLevel: "info",              // Only "info" level messages will be logged
    verbose: true                  // log messages will be printed out to the console
});
```

您还可以在运行时更改选项:

```
var casper = require('casper').create();
casper.options.waitTimeout = 1000;
```

下面是所有选项的详细信息：

#### clientScripts

**类型**：Array

**默认值**：[ ]

包含在加载的每个页面中脚本文件路径的集合。

#### exitOnError

**类型**：Boolean

**默认值**：true

设置当脚本抛出未捕获的错误时CasperJS是否必须退出。

#### httpStatusHandlers

**类型**：Object

**默认值**：{ }

设置当脚本抛出未捕获的错误时CasperJS是否必须退出。

#### logLevel

**类型**：string

**默认值**："error"

设置log等级。

#### onAlert

**类型**：Function

**默认值**：null

**调用方式** ：onAlert(Object Casper,String message)

当JavaScript alert()函数执行时调用的函数。

#### onDie

**类型**：Function

**默认值**：null

**调用方式** ：onDie(Object Casper,String message,String status)

当Casper#die()函数执行时调用的函数。

#### onError

**类型**：Function

**默认值**：null

**调用方式** ：onError(Object Casper,String msg,Array backtrace)

发生“错误”级别事件时调用的函数。

#### onLoadError

**类型**：Function

**默认值**：null

**调用方式** ：onLoadError(Object Casper,String requestUrl,String status)

当请求的资源不能被加载时调用的函数。

#### onPageInitialized

**类型**：Function

**默认值**：null

**调用方式** ：onPageInitialized(Object page)

WebPage实例初始化后调用的函数。

#### onResourceReceived

**类型**：Function

**默认值**：null

**调用方式** ：onResourceReceived(Object Casper, Object resource)

PhantomJS'WebPage＃onResourceReceived（）回调的代理方法，但当前的Casper实例作为第一个参数传递。

#### onResourceRequested

**类型**：Function

**默认值**：null

**调用方式** ：onResourceRequested(Object Casper, Object resource)

PhantomJS的WebPage代理方法＃onResourceRequested（）回调，但当前的Casper实例作为第一个参数传递。

#### onStepComplete

**类型**：Function

**默认值**：null

**调用方式** ：onStepComplete(Object Casper, stepResult)

当step函数执行完毕后执行的函数。

#### onStepTimeout

**类型**：Function

**默认值**：Function

**调用方式** ：onStepTimeout(Integer timeout, Integer stepNum)

当step函数执行时间超过stepTimeout选项的值（如果已设置）时执行的函数。 默认情况下，在超时时，脚本将退出显示错误，而在测试环境中，只会将错误添加到套件结果中。

#### onTimeout

**类型**：Function

**默认值**： Function

**调用方式** ：onTimeout(Integer timeout)

当脚本执行时间超过超时选项的值时执行的功能（如果有的话）。 默认情况下，在超时时，脚本将退出显示错误，而在测试环境中，只会将错误添加到套件结果中。

#### onWaitTimeout

**类型**：Function

**默认值**：Function

**调用方式** ：onWaitTimeout(Integer timeout)

当waitFor *函数执行时间超过waitTimeout选项的值（如果有）已被设置时执行的函数 。 默认情况下，在超时时，脚本将退出显示错误，而在测试环境中，只会将错误添加到套件结果中。

#### page

**类型**：WebPage

**默认值**：null

一个PhanthomJS网页实例。

> 修改`page`的属性可能会导致一些Casper的特性无法工作。比如，修改`onUrlChanged`属性会导致`waitForUrl`特性无法工作。

#### pageSettings

**类型**： Object

**默认值**：{ }

PhantomJS的WebPage选项对象。可用的设置有：

- `javascriptEnabled`定义是否在页面中执行脚本（默认为true）
- `loadImages`定义是否加载内联图像
- `localToRemoteUrlAccessEnabled`定义本地资源（例如从文件）是否可以访问远程URL（默认为false）
- `userAgent`定义当网页请求资源时发送到服务器的用户代理
- `userName`设置用于HTTP认证的用户名
- `password`设置用于HTTP认证的密码
- `resourceTimeout`（以毫秒为单位）定义超时时间，所请求的资源将停止尝试并继续执行页面的其他部分。`onResourceTimeout`回调将在超时时被调用。`undefined`（默认值）表示默认gecko参数。

PhantomJS具体设置：

- `XSSAuditingEnabled`定义是否应监视跨站点脚本尝试的加载请求（默认为false）
- `webSecurityEnabled`定义是否应启用Web安全性（默认为true）

SlimerJS具体设置 :

- `allowMedia false`可以取消加载媒体（音频/视频）。默认值：true。 （仅限SlimerJS）
- `maxAuthAttempts`指示HTTP身份验证的最大尝试次数。 （SlimerJS 0.9）
- `plainTextAllContent true`表示`pages.plainText`返回所有内容，甚至脚本元素的内容，不可见元素等。默认值：false。

#### remoteScripts

**类型**： Array

**默认值**：[ ]

*版本1.0新增*

包含在加载的每个页面中远程脚本URL的集合。

#### safeLogs

**类型**： Boolean

**默认值**：true

*版本1.0新增*

当此选项设置为true时，默认情况下，在`<input type =“password”>`中输入的任何密码信息将在日志消息中被模糊化。将safeLog设置为false将以透明文本披露密码（不推荐）。

#### SilentErrors

**类型**： Boolean

**默认值**：false

启用此选项时，不会抛出捕获的步骤错误（尽管相关事件仍然发生）。在测试环境中大部分内部使用。

#### stepTimeout

**类型**： Number

**默认值**：null

`Max step timeout`的单位为毫秒，当设置时，每个定义的step函数都将在达到此超时值之前执行。

您可以定义`onStepTimeout( )`回调来捕获这种情况。默认情况下，脚本将会`die( )`并显示错误消息。

#### timeout

**类型**： Number

**默认值**：null

最大超时数（毫秒）。

#### verbose

**类型**： Boolean

**默认值**：false

实时输出`log`消息。

#### viewportSize

**类型**： Object

**默认值**：null

视口大小，例如{width：800，height：600}。

> 提示 PhantomJS附带的默认视口为400x300，CasperJS默认不会覆盖它。

#### retryTimeout

**类型**： Number

**默认值**：100

尝试之间的默认延迟，是与wait系函数配套的函数。

#### waitTimeout

**类型**： Number

**默认值**：100

默认等待超时的时间，是与wait系函数配套的函数。

### Casper 原型

#### back( )

**调用方式**：back( )

返回浏览器历史的上一页面。

```
casper.start('http://foo.bar/1')
casper.thenOpen('http://foo.bar/2');
casper.thenOpen('http://foo.bar/3');
casper.back();
casper.run(function() {
    console.log(this.getCurrentUrl()); // 'http://foo.bar/2'
});
```

建议你一起看看[forward()](http://docs.casperjs.org/en/latest/modules/casper.html#forward)函数。

#### base64encode()

**调用方式**：base64encode(String url [, String method, Object data])

使用客户端XMLHttpRequest同步使用base64算法对资源进行编码。

> 提示 我们不能使用window.btoa( )，因为它在PhantomJS使用的WebKit版本中不能运行。

示例：检索base64编码的Google logo 图像

```
var base64logo = null;
casper.start('http://www.google.fr/', function() {
    base64logo = this.base64encode('http://www.google.fr/images/srpr/logo3w.png');
});

casper.run(function() {
    this.echo(base64logo).exit();
});
```

您还可以执行HTTP POST请求来检索要编码的内容:

```
var base64contents = null;
casper.start('http://domain.tld/download.html', function() {
    base64contents = this.base64encode('http://domain.tld/', 'POST', {
        param1: 'foo',
        param2: 'bar'
    });
});

casper.run(function() {
    this.echo(base64contents).exit();
});
```

#### bypass( )

**调用方式**：bypass(Numbr nb)

*1.1版本新增* 绕过一定数量的导航步骤：

```
casper.start();
casper.then(function() {
    // This step will be executed
});
casper.then(function() {
    this.bypass(2);
});
casper.then(function() {
    // This test won't be executed
});
casper.then(function() {
    // Nor this one
});
casper.run();
```

#### click( )

**调用方法**：click(String selector, [Number|String X, Number|String Y])

点击由选择器表达式匹配出来的元素，该方法依次尝试两种策略：

1. 使用JavaScript触发点击事件。
2. 如果第一种尝试失败，则使用原生QtWebKit事件。

例如：

```
casper.start('http://google.fr/');

casper.thenEvaluate(function(term) {
    document.querySelector('input[name="q"]').setAttribute('value', term);
    document.querySelector('form[name="f"]').submit();
}, 'CasperJS');

casper.then(function() {
    // Click on 1st result link
    this.click('h3.r a');
});

casper.then(function() {
    // Click on 1st result link
    this.click('h3.r a',10,10);
});

casper.then(function() {
    // Click on 1st result link
    this.click('h3.r a',"50%","50%");
});


casper.then(function() {
    console.log('clicked ok, new location is ' + this.getCurrentUrl());
});

casper.run();
```

#### clickLabel( )

**调用方法**： clickLabel(String label[, String tag])

*0.6.1版本新增*

找到包含标签文本的第一个DOM元素，并确保元素节点名称是标签（可选）：

```
// <a href="...">My link is beautiful</a>
casper.then(function() {
    this.clickLabel('My link is beautiful', 'a');
});

// <button type="submit">But my button is sexier</button>
casper.then(function() {
    this.clickLabel('But my button is sexier', 'button');
});
```

#### capture( )

**调用方法** : capture(String targetFilepath, [Object clipRect, Object imgOptions])

PhantomJS的WebPage＃render的代理方法，添加一个截屏参数，用于自动设置页面截屏设置，并在完成后将其还原：

```
casper.start('http://www.google.fr/', function() {
    this.capture('google.png', {
        top: 100,
        left: 100,
        width: 500,
        height: 400
    });
});

casper.run();
```

*版本1.1新增*

imgOptions对象允许指定两个选项:

- `format`可以手动设置图像格式，避免依赖文件名。
- `quality`可以设置图像质量，数值从1到100。

示例：

```
casper.start('http://foo', function() {
    this.capture('foo', undefined, {
        format: 'jpg',
        quality: 75
    });
});
```

#### captureBase64()

**调用方式**：captureBase64(String format[, Mixed area])

以给定的格式计算当前页面或页面内的区域的二进制图像捕获的Base64表示.

支持的图像格式有：bmp,jpg,jpeg,png,ppm,tiff,xbm,xpm。

`area`参数可以是以下几种类型：

- String：一个CSS3选择器字符串，比如：`div#plop form[name="form"] input[type="submit"]`。
- cilpRect: 一个cilpRect对象，比如：`{"top":0,"left":0,"width":320,"height":200}`
- Object: 一个选择器对象，比如：一个XPath选择器。

例子：

```
casper.start('http://google.com', function() {
    // selector capture
    console.log(this.captureBase64('png', '#lga'));
    // clipRect capture
    console.log(this.captureBase64('png', {
        top: 0,
        left: 0,
        width: 320,
        height: 200
    }));
    // whole page capture
    console.log(this.captureBase64('png'));
});

casper.run();
```

#### captureSelector()

**调用方法**： captureSelector(String targetFile, String selector [, Object imgOptions])

捕获包含提供的选择器的页面区域并将其保存到`targetFile`：

```
casper.start('http://www.weather.com/', function() {
    this.captureSelector('weather.png', '#LookingAhead');
});

casper.run();
```

*版本1.1新增*

`imgOptions`对象允许指定两个选项：

- `format`允许指定保存文件的格式，而不依赖于文件名。
- `quality`可以设置图像质量，数值从1到100。

#### clear( )

**调用方法**：clear( )

*版本0.6.5新增* 清除当前页面执行环境上下文，避免先前加载的DOM内容仍然活动。

它是在远程DOM环境中阻止JavaScript执行的一种方法：

```
casper.start('http://www.google.fr/', function() {
    this.clear(); // 本页面的JavaScript将停止执行
});

casper.then(function() {
    // ...
});

casper.run();
```

#### clearCache()

**调用方法**：clearCache( )

*版本1.1.5新增*

用newPage()将新页面对象替换为当前页面，并使用clearMemoryCache()清除内存缓存。例子：

```
casper.start('http://www.google.fr/', function() {
    this.clearCache(); // cleared the memory cache and replaced page object with newPage().
});

casper.then(function() {
    // ...
});

casper.run();
```

#### clearMemoryCache()

**调用方法**： clearMemoryCache()

*版本1.1.5新增*

使用引擎的`page.clearMemoryCache()`方法来清除内存缓存。例子：

```
casper.start('http://www.google.fr/', function() {
    this.clearMemoryCache(); // cleared the memory cache.
});

casper.then(function() {
    // ...
});

casper.run();
```

#### debugHTML()

**调用方法**：debugHTML([String selector, Boolean outer])

在控制台直接输出`getHTML()`的结果，它的参数与`getHTML()`相同。

#### debugPage()

**调用方法**：debugPage()

将当前页面的文本内容直接记录到标准输出，以进行调试：

```
casper.start('http://www.google.fr/', function() {
    this.debugPage();
});

casper.run();
```

#### die( )

**调用方法**：die(String message[, int status])

退出phantom并返回一条错误信息和一个状态码（可选）。

```
casper.start('http://www.google.fr/', function() {
    this.die("Fail.", 1);
});

casper.run();
```

#### download( )

**调用方法**：download(String url, String target[, String method, Object data])

将远程资源保存到本地。你还可以设置`method`指定http的传输方式，并通过data对象传递请求参数。

```
casper.start('http://www.google.fr/', function() {
    var url = 'http://www.google.fr/intl/fr/about/corporate/company/';
    this.download(url, 'google_company.html');
});

casper.run(function() {
    this.echo('Done.').exit();
});
```

> ##### 提示

如果你在下载文件时遇到了一些问题，把[web security](http://docs.casperjs.org/en/latest/faq.html#faq-web-security)关掉再试试。

#### each( )

**调用方法**：each(Array array, Function fn) 遍历一个给定的数组并对每一项执行一个回调函数。

```
var links = [
    'http://google.com/',
    'http://yahoo.com/',
    'http://bing.com/'
];

casper.start().each(links, function(self, link) {
    self.thenOpen(link, function() {
        this.echo(this.getTitle());
    });
});

casper.run();
```

> ##### 提示

看一下具体是如何使用这个案例的[googlematch.js](https://github.com/casperjs/casperjs/blob/master/samples/googlematch.js)示例脚本。

#### eachThen( )

**调用方式**：eachThen(Array array, Function then)

*版本1.1新增*

迭代提供的数组项目，并向堆栈添加一个与当前数据相连的步骤。

```
casper.start().eachThen([1, 2, 3], function(response) {
    this.echo(response.data);
}).run();
```

这里有一个打开一个url数组的例子：

```
var casper = require('casper').create();
var urls = ['http://google.com/', 'http://yahoo.com/'];

casper.start().eachThen(urls, function(response) {
  this.thenOpen(response.data, function(response) {
    console.log('Opened', response.url);
  });
});

casper.run();
```

> ##### 提示

当前的数组项目会被存储在`respense.data`中。

#### echo( )

**调用方式**：echo(String message[, String style])

在标准输出流中输出字符，还可以给字符添加漂亮的颜色哦！（看[这里](http://docs.casperjs.org/en/latest/modules/colorizer.html#colorizer-module)了解更多。

```
casper.start('http://www.google.fr/', function() {
    this.echo('Page title is: ' + this.evaluate(function() {
        return document.title;
    }), 'INFO'); // 会在控制台输出绿色的字符哦
});

casper.run();
```

#### evaluate( )

**调用方式**：evaluate(Function fn[, arg1[, arg2[, …]]]) 与 PhantomJS的WebPage#evaluate方法基本相同，即执行一段浏览器原生dom语句。

```
casper.evaluate(function(username, password) {
    document.querySelector('#username').value = username;
    document.querySelector('#password').value = password;
    document.querySelector('#submit').click();
}, 'sheldon.cooper', 'b4z1ng4');
```

> ##### 提示

如果你想填充表单，最好还是使用`fill()`方法。

------

> #### 理解`evaluate( )`

这个方法背后的概念应该是我们开发CasperJS时遇到的最难理解的一个，你可以把这个方法当作CasperJS环境通往你打开页面的大门，每当你向`evaluate()`传递一个关闭命令时，你就相当于在打开页面的控制台中执行代码。 这是一个快速起草的图，大概解释了分离问题： [![img](https://camo.githubusercontent.com/9ae77887c57304d9458b80fac4c4dbfffb6597f0/687474703a2f2f646f63732e6361737065726a732e6f72672f656e2f6c61746573742f5f696d616765732f6576616c756174652d6469616772616d2e706e67)](https://camo.githubusercontent.com/9ae77887c57304d9458b80fac4c4dbfffb6597f0/687474703a2f2f646f63732e6361737065726a732e6f72672f656e2f6c61746573742f5f696d616765732f6576616c756174652d6469616772616d2e706e67)

#### evaluateOrDie()

**调用方式**：evaluateOrDie(Function fn[, String message, int status])

在当前页面检测某个dom表达式是否存在，如果返回的结果为true则执行die()函数。

```
casper.start('http://foo.bar/home', function() {
    this.evaluateOrDie(function() {
        return /logged in/.match(document.title);
    }, 'not authenticated');
});

casper.run();
```

#### exit( )

**调用方式**：exit([int status])

使用状态代码（可选）退出PhantomJS。

你不能依赖这个方法立即退出你的脚本，因为这个函数是异步工作的，也就是说当你异步调用了这个方法后，你的脚本仍然在运行（而不是立即结束）。在[这里](https://github.com/casperjs/casperjs/issues/193)查看更多详情。

#### exists()

**调用方式**：exists(String selector)

检查dom中是否有与提供的选择器匹配的元素：

```
casper.start('http://foo.bar/home', function() {
    if (this.exists('#my_super_id')) {
        this.echo('found #my_super_id', 'INFO');
    } else {
        this.echo('#my_super_id not found', 'ERROR');
    }
});

casper.run();
```

#### fetchText( )

**调用方式**：fetchText(String selector)

检索与给定选择器表达式匹配的文本内容。如果您提供的表达式匹配到多个元素，它们的文本内容将被连接：

```
casper.start('http://google.com/search?q=foo', function() {
    this.echo(this.fetchText('h3'));
}).run();
```

#### forward()

**调用方式**：forward()

返回浏览器历史中当前页面的下一页：

```
casper.start('http://foo.bar/1')
casper.thenOpen('http://foo.bar/2');
casper.thenOpen('http://foo.bar/3');
casper.back();    // http://foo.bar/2
casper.back();    // http://foo.bar/1
casper.forward(); // http://foo.bar/2
casper.run();
```

#### log( )

**调用方式**： log(String message[, String level, String space])

在某个可选空间中输出可选级别的消息。可选的级别有：`debug`,`info`,`warning`,`error`。空间指的是你可以过滤log消息的某种命名空间。CasperJS默认在两个不同的空间中输出消息：`phantom`和`remote`，要区分PhantomJS环境中远程发生的情况，你可以这样写：

```
casper.start('http://www.google.fr/', function() {
    this.log("I'm logging an error", "error");
});

casper.run();
```

#### fill()

**调用方式**：fill(String selector, Object values[, Boolean submit])

填充文本域并决定是否要提交表单（可选），要填充的文本域是通过它们的name属性来辨别的。

*在版本1.1中改变*：如果你要使用CSS3和XPath表达式，请使用fillSelectors()和fillXPath()方法。

示例html代码：

```
<form action="/contact" id="contact-form" enctype="multipart/form-data">
    <input type="text" name="subject"/>
    <textearea name="content"></textearea>
    <input type="radio" name="civility" value="Mr"/> Mr
    <input type="radio" name="civility" value="Mrs"/> Mrs
    <input type="text" name="name"/>
    <input type="email" name="email"/>
    <input type="file" name="attachment"/>
    <input type="checkbox" name="cc"/> Receive a copy
    <input type="submit"/>
</form>
```

填充并提交表单：

```
casper.start('http://some.tld/contact.form', function() {
    this.fill('form#contact-form', {
        'subject':    'I am watching you',
        'content':    'So be careful.',
        'civility':   'Mr',
        'name':       'Chuck Norris',
        'email':      'chuck@norris.com',
        'cc':         true,
        'attachment': '/Users/chuck/roundhousekick.doc'
    }, true);
});

casper.then(function() {
    this.evaluateOrDie(function() {
        return /message sent/.test(document.body.innerText);
    }, 'sending message failed');
});

casper.run(function() {
    this.echo('message sent').exit();
});
```

fill()方法支持支持填充单选框，用法与填充文本域一样，对于多选框，你可以使用与多选值对应的数组：

```
<form action="/contact" id="contact-form" enctype="multipart/form-data">
    <select multiple name="category">
    <option value="0">Friends</option>
    <option value="1">Family</option>
    <option value="2">Acquitances</option>
    <option value="3">Colleagues</option>
    </select>
</form>
```

填充多选域的脚本：

```
casper.then(function() {
   this.fill('form#contact-form', {
       'categories': ['0', '1'] // Friends and Family
   });
});
```

> ##### 警告

`fill()`方法填充文本域**并不支持XPath选择器**，PhantomJS原生`uploadFile()`方法只支持CSS3选择器，加强了这个限制。

#### fillSelectors()

**调用方式**：fillSelectors(String selector, Object values[, Boolean submit])

*版本1.1新增* 这个方法可以填充表单并提交表单（可选），本方法配适css3的选择器：

```
casper.start('http://some.tld/contact.form', function() {
    this.fillSelectors('form#contact-form', {
        'input[name="subject"]':    'I am watching you',
        'input[name="content"]':    'So be careful.',
        'input[name="civility"]':   'Mr',
        'input[name="name"]':       'Chuck Norris',
        'input[name="email"]':      'chuck@norris.com',
        'input[name="cc"]':         true,
        'input[name="attachment"]': '/Users/chuck/roundhousekick.doc'
    }, true);
});
```

#### fillLabels( )

**调用方式**：fillLabels(String selector, Object values[, Boolean submit])

*版本1.1新增*

根据`label`标签提供的字段名来填写表单：

```
casper.start('http://some.tld/contact.form', function() {
    this.fillLabels('form#contact-form', {
        Email:         'chuck@norris.com',
        Password:      'chuck',
        Content:       'Am watching thou',
        Check:         true,
        No:            true,
        Topic:         'bar',
        Multitopic:    ['bar', 'car'],
        File:          fpath,
        "1":           true,
        "3":           true,
        Strange:       "very"
    }, true);
});
```

#### fillXPath( )

**调用方式**： fillXPath(String selector, Object values[, Boolean submit])

*版本1.1新增*

使用XPath选择器选择字段填写表单并（可选）提交表单：

```
casper.start('http://some.tld/contact.form', function() {
    this.fillXPath('form#contact-form', {
        '//input[@name="subject"]':    'I am watching you',
        '//input[@name="content"]':    'So be careful.',
        '//input[@name="civility"]':   'Mr',
        '//input[@name="name"]':       'Chuck Norris',
        '//input[@name="email"]':      'chuck@norris.com',
        '//input[@name="cc"]':         true,
    }, true);
});
```

> 警告 使用XPath选择器目前还不能填充文件域，是因为PhantomJS原生函数`uploadFile( )`不支持XPath选择器。

#### getCurrentUrl( )

**调用方式**：getCurrentUrl()

检索当前页面的URL，请注意，url将被url解码：

```
casper.start('http://www.google.fr/', function() {
    this.echo(this.getCurrentUrl()); // "http://www.google.fr/"
});

casper.run();
```

#### getElementAttribute( )

**调用方式**：getElementAttribute(String selector, String attribute)

*版本1.0新增*

检索匹配你提供的选择器的元素的属性值：

```
var casper = require('casper').create();

casper.start('http://www.google.fr/', function() {
    require('utils').dump(this.getElementsAttribute('div[title="Google"]', 'title')); // "['Google']"
});

casper.run();
```

#### getElementBounds()

**调用方式**： getElementBounds(String selector, Boolean page)

检索与提供的选择器匹配的DOM元素的边界。如果页面中含有frame/iframe元素，请将`page`参数设置为`true`.

这个方法会返回一个拥有4个属性的对象，分别是：`top`,`left`,`width`,`height`，当元素不存在时返回`null`：

```
var casper = require('casper').create();

casper.start('http://www.google.fr/', function() {
    require('utils').dump(this.getElementBounds('div[title="Google"]'));
});

casper.run();
```

你会得到像这样的输出：

```
{
    "height": 95,
    "left": 352,
    "top": 16,
    "width": 275
}
```

#### getElementsBounds()

**调用方式**：getElementsBounds(String selector)

*版本1.0新增*

返回匹配到的所有元素的位置信息。

这个方法会返回一个对象数组，对象结构与`getElementBounds()`相同。

#### getElementInfo( )

**调用方式**：getElementInfo(String selector)

*版本1.0新增*

获取选择器匹配到的第一个元素的信息：

```
casper.start('http://google.fr/', function() {
    require('utils').dump(this.getElementInfo('#hplogo'));
});
```

你会得到这样的结果：

```
{
    "attributes": {
        "align": "left",
        "dir": "ltr",
        "id": "hplogo",
        "onload": "window.lol&&lol()",
        "style": "height:110px;width:276px;background:url(/images/srpr/logo1w.png) no-repeat",
        "title": "Google"
    },
    "height": 110,
    "html": "<div nowrap=\"nowrap\" style=\"color:#777;font-size:16px;font-weight:bold;position:relative;left:214px;top:70px\">France</div>",
    "nodeName": "div",
    "tag": "<div dir=\"ltr\" title=\"Google\" align=\"left\" id=\"hplogo\" onload=\"window.lol&amp;&amp;lol()\" style=\"height:110px;width:276px;background:url(/images/srpr/logo1w.png) no-repeat\"><div nowrap=\"nowrap\" style=\"color:#777;font-size:16px;font-weight:bold;position:relative;left:214px;top:70px\">France</div></div>",
    "text": "France\n",
    "visible": true,
    "width": 276,
    "x": 62,
    "y": 76
}
```

#### getElementsInfo()

**调用方式**：getElementsInfo(String selector)

*版本1.1新增*

获取选择器匹配到的所有元素的信息：

```
casper.start('http://google.fr/', function() {
    require('utils').dump(this.getElementsInfo('#hplogo'));
});
```

你会得到类似这样的结果：

```
[
    {
        "attributes": {
            "align": "left",
            "dir": "ltr",
            "id": "hplogo",
            "onload": "window.lol&&lol()",
            "style": "height:110px;width:276px;background:url(/images/srpr/logo1w.png) no-repeat",
            "title": "Google"
        },
        "height": 110,
        "html": "<div nowrap=\"nowrap\" style=\"color:#777;font-size:16px;font-weight:bold;position:relative;left:214px;top:70px\">France</div>",
        "nodeName": "div",
        "tag": "<div dir=\"ltr\" title=\"Google\" align=\"left\" id=\"hplogo\" onload=\"window.lol&amp;&amp;lol()\" style=\"height:110px;width:276px;background:url(/images/srpr/logo1w.png) no-repeat\"><div nowrap=\"nowrap\" style=\"color:#777;font-size:16px;font-weight:bold;position:relative;left:214px;top:70px\">France</div></div>",
        "text": "France\n",
        "visible": true,
        "width": 276,
        "x": 62,
        "y": 76
    }
]
```

> ##### 提示

这个方法返回的并不是元素集，而仅仅是一个包含对象属性的简单数组。这是因为CasperJS环境并没有直接取得页面元素的权限。

#### getFormValues()

**调用方式**：getFormValues(String selector)

*版本1.1新增*

获取一个表单中所有填充域的值：

```
casper.start('http://www.google.fr/', function() {
    this.fill('form', {q: 'plop'}, false);
    this.echo(this.getFormValues('form').q); // 'plop'
});

casper.run();
```

#### getGlobal()

**调用方式**：getGlobal(String name)

获取页面全局对象的值，一般来说，`getGlobal('foo')`可以获取window.foo对象的值。

```
casper.start('http://www.google.fr/', function() {
    this.echo(this.getGlobal('innerWidth')); // 1024
});

casper.run();
```

#### getHTML()

**调用方式**：getHTML([String selector, Boolean outer])

*版本1.0新增*

返回页面的html代码，默认输出整个页面的HTML内容：

```
casper.start('http://www.google.fr/', function() {
    this.echo(this.getHTML());
});

casper.run();
```

getHTML()方法还可以指定获取HTML代码还是内容，看例子：

```
<html>
    <body>
        <h1 id="foobar">Plop</h1>
    </body>
</html>
```

只获取匹配到标签中的文本内容，你可以：

```
casper.start('http://www.site.tld/', function() {
    this.echo(this.getHTML('h1#foobar')); // => 'Plop'
});
```

如果要获取所有HTML（含标签）内容：

```
casper.start('http://www.site.tld/', function() {
    this.echo(this.getHTML('h1#foobar', true)); // => '<h1 id="foobar">Plop</h1>'
});
```

#### getPageContent( )

**调用方法**：getPageContent()

*版本1.0新增*

返回当前页面内容，这个方法主要用于处理非HTML内容的页面：

```
var casper = require('casper').create();

casper.start().then(function() {
    this.open('http://search.twitter.com/search.json?q=casperjs', {
        method: 'get',
        headers: {
            'Accept': 'application/json'
        }
    });
});

casper.run(function() {
    require('utils').dump(JSON.parse(this.getPageContent()));
    this.exit();
});
```

#### getTitle()

**调用方法**：getTitle() 返回当前页面的标题：

```
casper.start('http://www.google.fr/', function() {
    this.echo(this.getTitle()); // "Google"
});

casper.run();
```

#### mouseEvent()

**调用方法**： mouseEvent(String type, String selector, [Number|String X, Number|String Y])

*版本0.6.9新增*

在第一个匹配到的元素上触发鼠标事件。

支持的事件类型有:`mouseup`,`mousedown`,`click`,`dblclick`,`mousemove`,`mouseover`, `mouseout`,如果你的phantomJS版本大于1.9.8，还支持`mouseenter`,`mouseleave`和`contextmenu`事件。

> ##### 警告

支持的事件类型取决于你使用的引擎，老版本的引擎只提供部分事件的支持，所以，为了提供最好的支持，你最好使用最新版本的引擎：

```
casper.start('http://www.google.fr/', function() {
    this.mouseEvent('click', 'h2 a', "20%", "50%");
});

casper.run();
```

#### newPage()

**调用方法**： newPage()

*版本1.1新增*

**只有1.1.0版本后的版本才支持这个函数。**

创建一个新的页面：

```
casper.start('http://google.com', function() {
    // ...
});

casper.then(function() {
    casper.page = casper.newPage();
    casper.open('http://yahoo.com').then( function() {
        // ....
    });
});

casper.run();
```

#### open()

**调用方法**： open(String location, Object Settings)

执行打开给定位置的HTTP请求。您可以伪造GET，POST，PUT，DELETE和HEAD请求。

GET请求的例子

```
casper.start();

casper.open('http://www.google.com/').then(function() {
    this.echo('GOT it.');
});

casper.run();
```

POST请求的例子：

```
casper.start();

casper.open('http://some.testserver.com/post.php', {
    method: 'post',
    data:   {
        'title': 'Plop',
        'body':  'Wow.'
    }
});

casper.then(function() {
    this.echo('POSTED it.');
});

casper.run();
```

传递嵌套的参数数组：

```
casper.start();

casper.open('http://some.testserver.com/post.php', {
    method: 'post',
    data:   {
        'title': 'Plop',
        'body':  'Wow.'
    }
});

casper.then(function() {
    this.echo('POSTED it.');
});

casper.run();
```

*版本1.0新增*

以utf-8编码方式发送数据：

```
casper.open('http://some.testserver.com/post.php', {
       method: 'post',
       headers: {
           'Content-Type': 'application/json; charset=utf-8'
       },
       encoding: 'utf8', // not enforced by default
       data: {
            'table_flip': '(╯°□°）╯︵ ┻━┻ ',
       }
});
```

*版本1.0新增*

您还可以在执行传出请求时设置要发送的自定义请求标头，比如：

```
casper.open('http://some.testserver.com/post.php', {
    method: 'post',
    data:   {
        'title': 'Plop',
        'body':  'Wow.'
    },
    headers: {
        'Accept-Language': 'fr,fr-fr;q=0.8,en-us;q=0.5,en;q=0.3'
    }
});
```

#### reload( )

**调用方式**： reload([Function then])

*版本1.0新增*

重新加载当前页面：

```
casper.start('http://google.com', function() {
    this.echo("loaded");
    this.reload(function() {
        this.echo("loaded again");
    });
});

casper.run();
```

#### repeat()

**调用方式**： repeat(int times, Function then)

将一个行为重复执行指定的次数。

```
casper.start().repeat(3, function() {
    this.echo("Badger");
});

casper.run();
```

#### resourceExists()

**调用方式**：resourceExists(String|Function|RegExp test)

检查某个资源是否被加载了，这个方法的参数可以是function，string和RegExp实例。

```
casper.start('http://www.google.com/', function() {
    if (this.resourceExists('logo3w.png')) {
        this.echo('Google logo loaded');
    } else {
        this.echo('Google logo not loaded', 'ERROR');
    }
});

casper.run();
```

> ##### 提示

如果你想等到资源加载完毕，可以使用`waitForResource()`函数。

#### run()

**调用方式**：run(fn onComplete[, int time])

运行整套步骤，并且可以在完成后执行回调。显然，为了运行Casper导航套件，调用此方法是必需的。

这样写的话CasperJS套件是不会运行的哦：

```
casper.start('http://foo.bar/home', function() {
    // ...
});

// hey, it's missing .run() here!
```

这样CasperJS套件才会运行：

```
casper.start('http://foo.bar/home', function() {
    // ...
});

casper.run();
```

`Casper.run()`方法同样接受一个`onComplete`的回调方法，可以将其视为在执行所有其他步骤时执行的自定义最终步骤，只是在定义了这个函数后，要记得使用`exit()`方法退出Casper。

```
casper.start('http://foo.bar/home', function() {
    // ...
});

casper.then(function() {
    // ...
});

casper.run(function() {
    this.echo('So the whole suite ended.');
    this.exit(); // <--- don't forget me!
});
```

将回调绑定到complete.error将在onComplete回调失败时触发。

#### scrollTo()

**调用方式**：scrollTo(Number x, Number y)

*版本1.1-beta3新增*

滚动页面到指定的坐标(x和y坐标)。

```
casper.start('http://foo.bar/home', function() {
    this.scrollTo(500, 300);
});
```

> ##### 提示

此操作是同步的。

#### scrollToBottom()

**调用方式**：scrollToBottom()

*版本1.1-beta3新增*

滚动页面到最底部。

```
casper.start('http://foo.bar/home', function() {
    this.scrollToBottom();
});
```

> ##### 提示

此操作是同步的。

#### sendKeys()

**调用方式**：sendKeys(Selector selector, String keys[, Object options])

*版本1.0新增*

将本地键盘事件发送到与提供的选择器匹配的元素：

```
casper.then(function() {
    this.sendKeys('form.contact input#name', 'Duke');
    this.sendKeys('form.contact textarea#message', "Damn, I'm looking good.");
    this.click('form.contact input[type="submit"]');
});
```

*版本1.1新增*

目前`sendKeys()`方法支持的元素不仅包括`<input>`和`<textarea>`元素，还包括所有 `contenteditable`属性为`true`的元素。



*  自定义属性

(Boolean) reset:

*版本1.1-beta3新增*

当reset的值为true时，该方法会首先将文本域清空，当值为false时，该方法仅仅会将字符串添加在文本域已有的文字之后。

- (Boolean) keepFocus:

默认情况下，`sendKeys()`会清除文本域的自动聚焦，通常意味着关闭了自动完成的部件。如果你想保留聚焦，就使用`keepFocus`属性。例如，在jQuery-UI中，你可以选择自动补全功能中的提示输入：

```
casper.then(function() {
  this.sendKeys('form.contact input#name', 'action', {keepFocus: true});
  this.click('form.contact ul.ui-autocomplete li.ui-menu-item:first-  child a');
```
});

```
- (String) modifiers:

sendKeys（）接受一个修饰符选项来支持键修饰符。它的选项是一个表示要使用的修饰符的组合的字符串，由+字符分隔：
​```js
casper.then(function() {
    this.sendKeys('document', 's', {modifiers: 'ctrl+alt+shift'});
});
```

可用的修饰符有：

- ctrl
- alt
- shift
- meta
- keypad

#### setHttpAuth()

**调用方式**：setHttpAuth(String username, String password)

为基于HTTP的身份验证系统设置HTTP_AUTH_USER和HTTP_AUTH_PW值：

```
casper.start();

casper.setHttpAuth('sheldon.cooper', 'b4z1ng4');

casper.thenOpen('http://password-protected.domain.tld/', function() {
    this.echo("I'm in. Bazinga.");
})
casper.run();
```

当然你可以直接传递url中的auth字符串来打开：

```
var url = 'http://sheldon.cooper:b4z1ng4@password-protected.domain.tld/';

casper.start(url, function() {
    this.echo("I'm in. Bazinga.");
})

casper.run();
```

#### setMaxListeners()

**调用方式**：setMaxListeners(Integer maxListeners)

设置可以为每种类型的监听器添加的监听的最大数量：

```
casper.setMaxListeners(12);
```

> #### 提示

您的casper脚本中的监听器注册不正确可能会导致一条警告消息，指示已检测到可能的EventEmitter泄漏。确保您以要求的方式添加监听器。

如果您需要一个将被所有脚本处理的监听器，那么请确保它只被注册一次。例如，如果您需要为每个测试套件添加一个监听器，请确保在setUp期间添加监听器，并在tearDown期间删除该监听器。有关setUp和tearDown结构，请参阅[Tester＃begin()](http://docs.casperjs.org/en/latest/modules/tester.html#tester-begin-configuration)。

> #### 警告

我们不建议更改最大监听器的数量，只有当你添加的监听事件超过最大限制时，才去增加它的最大数量，增加此限制会增加所有监听器类型，并且可能导致事件未发现的事件泄漏。如果您必须增加此限制，请以小增量增加该限制。

#### start()

**调用方式**： start(String url[, Function then])

配置并启动Casper，然后打开提供的URL，并执行`step`数组提供的步骤(可选)：

```
casper.start('http://google.fr/', function() {
    this.echo("I'm loaded.");
});

casper.run();
```

另一种书写方法：

```
casper.start('http://google.fr/');

casper.then(function() {
    this.echo("I'm loaded.");
});

casper.run();
```

还可以这样写：

```
casper.start('http://google.fr/');

casper.then(function() {
    casper.echo("I'm loaded.");
});

casper.run();
```

选择哪种书写方式全凭你的喜好。

> ##### 提示

您必须调用start（）方法才能添加导航步骤并运行该套件。如果你不这样做，你会收到一条错误消息提示你这样做。

#### status()

**调用方式**： status(Boolean asString)

*版本1.0新增*

返回当前实例的状态：

```
casper.start('http://google.fr/', function() {
    this.echo(this.status(true));
});

casper.run();
```

#### switchToFrame()

**调用方式**： switchToFrame(String|Number frameInfo)

*版本1.1.5新增*

将主页切换到具有与传递参数相匹配的名称或帧索引号的frame中。将本地脚本，远程脚本和客户端工具注入此frame。

#### switchToMainFrame()

**调用方式**： switchToMainFrame()

*版本1.1.5新增*

将主页面切换到当前活动页面的父frame。

#### switchToParentFrame()

**调用方式**： switchToParentFrame()

*版本1.1.5新增*

将主页面切换到主框架。

#### then()

**调用方式**： then(Function then)

这个方法是将一个新步骤添加到栈的标准方法，只需要一个简单的函数：

```
casper.start('http://google.fr/');

casper.then(function() {
    this.echo("I'm in your google.");
});

casper.then(function() {
    this.echo('Now, let me write something');
});

casper.then(function() {
    this.echo('Oh well.');
});

casper.run();
```

你想添加多少步骤都可以。注意目前的`casper`实例将会自动绑定`this`指针。

想要运行你所有定义的步骤，调用`run()`方法，然后结果就会出现了。

> ##### 提示

你需要先`start()`一个Casper实例之后才可以使用`then()`方法。

> #### 访问当前的HTTP响应

*版本1.0新增*

你可以使用回调函数的第一个参数来访问当前的HTTP响应：

```
casper.start('http://www.google.fr/', function(response) {
    require('utils').dump(response);
});
```

将显示的结果：

```
$ casperjs dump-headers.js
{
    "contentType": "text/html; charset=UTF-8",
    "headers": [
        {
            "name": "Date",
            "value": "Thu, 18 Oct 2012 08:17:29 GMT"
        },
        {
            "name": "Expires",
            "value": "-1"
        },
        // ... lots of other headers
    ],
    "id": 1,
    "redirectURL": null,
    "stage": "end",
    "status": 200,
    "statusText": "OK",
    "time": "2012-10-18T08:17:37.068Z",
    "url": "http://www.google.fr/"
}
```

你也可以访问某一个具体的头信息：

```
casper.start('http://www.google.fr/', function(response) {
    this.echo(response.headers.get('Date'));
});
```

结果：

```
$ casperjs dump-headers.js
Thu, 18 Oct 2012 08:26:34 GMT
```

> ##### 警告

`then()`函数会在以下两种情况后被执行：

1. 当上一个步骤被执行之后。
2. 当上一个HTTP请求被处理并加载页面后。

但是要知道，我们对于‘页面加载’的时间点没有一个明确的定义，是DOMReady事件被触发之后吗？还是'当所有的请求都完成了'？，还是‘所有的应用逻辑都已完成’？还是’所有的元素都渲染完成了‘？答案永远取决于上下文。所以我们强烈建议你使用'waitFor()'家族的方法来明确控制逻辑是你期望的那样。

通常的做法是使用`waitForSelector()`：

```
casper.start('http://my.website.com/');

casper.waitForSelector("#plop", function() {
    this.echo("I'm sure #plop is available in the DOM");
});

casper.run();
```

#### thenBypass()

**调用方式**： thenBypass(Number nb)

*版本1.1新增*

跳过指定数量的步骤：

```
casper.start('http://foo.bar/');
casper.thenBypass(2);
casper.then(function() {
    // This test won't be executed
});
casper.then(function() {
    // Nor this one
});
casper.then(function() {
    // While this one will
});
casper.run();
```

#### thenBypassIf()

**调用方式**：thenBypassIf(Mixed condition, Number nb)

*版本1.1新增* 当参数为true或回调函数的返回值为true时，跳过指定数量的步骤：

```
var universe = {
    answer: 42
};
casper.start('http://foo.bar/');
casper.thenBypassIf(function() {
    return universe && universe.answer === 42;
}, 2);
casper.then(function() {
    // This step won't be executed as universe.answer is 42
});
casper.then(function() {
    // Nor this one
});
casper.then(function() {
    // While this one will
});
casper.run();
```

#### thenBypassUnless()

**调用方式**：thenBypassIf(Mixed condition, Number nb)

*版本1.1新增*

thenBypassIf()的反函数。

#### thenClick()

**调用方式**：thenClick(String selector[, Function then])

点击一个选择器指定的元素并在点击后执行回调函数（可选）：

```
// Click the first link in the casperJS page
casper.start('http://casperjs.org/').thenClick('a', function() {
    this.echo("I clicked on first link found, the page is now loaded.");
});

casper.run();
```

这个方法可以看作是`then()`和`cilck()`方法的结合。

#### thenClick()

**调用方式**：thenEvaluate(Function fn[, arg1[, arg2[, …]]])

在当前检索的页面DOM中执行代码评估：

```
// Querying for "Chuck Norris" on Google
casper.start('http://google.fr/').thenEvaluate(function(term) {
    document.querySelector('input[name="q"]').setAttribute('value', term);
    document.querySelector('form[name="f"]').submit();
}, 'Chuck Norris');

casper.run()
```

这个方法可以看作是`then()`和`evaluate()`方法的结合。

#### thenOpen()

**调用方式**：thenOpen(String location[, mixed options])

打开一个新页面，并在之后执行一些步骤（可选）：

```
casper.start('http://google.fr/').then(function() {
    this.echo("I'm in your google.");
});

casper.thenOpen('http://yahoo.fr/', function() {
    this.echo("Now I'm in your yahoo.")
});

casper.run();
```

*版本1.0新增*

你还可以在第二个参数中设置具体的访问选项：

```
casper.start().thenOpen('http://url.to/some/uri', {
    method: "post",
    data: {
        username: 'chuck',
        password: 'n0rr15'
    }
}, function() {
    this.echo("POST request has been sent.")
});

casper.run();
```

#### thenOpenAndEvaluate()

**调用方式**：thenOpenAndEvaluate(String location[, Function then[, arg1[, arg2[, …]]])

一个打开新页面并在远程dom环境中执行代码评估的简短版本：

```
casper.start('http://google.fr/').then(function() {
    this.echo("I'm in your google.");
});

casper.thenOpenAndEvaluate('http://yahoo.fr/', function() {
    var f = document.querySelector('form');
    f.querySelector('input[name=q]').value = 'chuck norris';
    f.submit();
});

casper.run(function() {
    this.debugPage();
    this.exit();
});
```

#### toString()

**调用方式**：toString()

*版本1.0新增*

返回一个Casper实例的字符串表示：

```
casper.start('http://google.fr/', function() {
    this.echo(this); // [object Casper], currently at http://google.fr/
});

casper.run();
```

#### unwait()

**调用方式**： unwait()

*版本1.1新增*

中止所有当前的等待进程。

#### userAgent()

**调用方式**： userAgent(String agent)

在发送请求时在请求头中设置userAgent字符串：

```
casper.start();

casper.userAgent('Mozilla/5.0 (Macintosh; Intel Mac OS X)');

casper.thenOpen('http://google.com/', function() {
    this.echo("I'm a Mac.");
    this.userAgent('Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)');
});

casper.thenOpen('http://google.com/', function() {
    this.echo("I'm a PC.");
});

casper.run();
```

#### viewport()

**调用方式**： viewport(Number width, Number height[, Function then])

改变当前视口的尺寸：

```
casper.viewport(1024, 768);
```

为了确保页面回流发生，你必须异步使用这个函数：

```
casper.viewport(1024, 768).then(function() {
    // new view port is now effective
});
```

*版本1.1新增*

在1.1版本之后你可以直接将`then`步骤函数传入`viewport()`中：

```
casper.viewport(1024, 768, function() {
    // new view port is effective
});
```

> ##### 提示

PhantomJS 的默认视口大小为400*300，CasperJS无法重写它的默认值。

#### visible()

**调用方式**： visible(String selector)

检测选择器指定的元素在页面上是否可见：

```
casper.start('http://google.com/', function() {
    if (this.visible('#hplogo')) {
        this.echo("I can see the logo");
    } else {
        this.echo("I can't see the logo");
    }
});
```

#### wait()

**调用方式**： wait(Number timeout[, Function then])

使某一步骤等待指定是的时间后执行指定的回调函数（可选）：

```
casper.start('http://yoursite.tld/', function() {
    this.wait(1000, function() {
        this.echo("I've waited for a second.");
    });
});

casper.run();
```

你也可以这样写：

```
casper.start('http://yoursite.tld/');

casper.wait(1000, function() {
    this.echo("I've waited for a second.");
});

casper.run();
```

#### waitFor()

**调用方式**： waitFor(Function testFx[, Function then, Function onTimeout, Number timeout, Object details])

等待在函数返回true之后执行指定函数。

你还可以使用onTimeout参数设置超时回调，并使用timeout设置超时时间（以毫秒为单位）。默认超时时间为5000毫秒：

```
casper.start('http://yoursite.tld/');

casper.waitFor(function check() {
    return this.evaluate(function() {
        return document.querySelectorAll('ul.your-list li').length > 2;
    });
}, function then() {
    this.captureSelector('yoursitelist.png', 'ul.your-list');
});

casper.run();
```

使用onTimeout的例子：

```
casper.start('http://yoursite.tld/');

casper.waitFor(function check() {
    return this.evaluate(function() {
        return document.querySelectorAll('ul.your-list li').length > 2;
    });
}, function then() {    // step to execute when check() is ok
    this.captureSelector('yoursitelist.png', 'ul.your-list');
}, function timeout() { // step to execute if check has failed
    this.echo("I can't haz my screenshot.").exit();
});

casper.run();
```

当timeout事件被触发时，包含的信息集的details属性将会被传递到`waitFor.timeout`事件中，这可以用于提供更好的错误消息或有条件地忽略一些超时事件。

> ##### 提示

所有的`waitFor`方法都不是可链接的，不过你可以把它们包裹在`casper.then`方法中来达成这个功能。

*版本1.1.5新增*

从1.1.5开始，如果check函数仍然为false，则在超时后最后一次运行check函数。

#### waitForAlert()

**调用方式**： waitForAlert(Function then[, Function onTimeout, Number timeout])

*版本1.1-beta4新增*

等待JavaScript alert事件被触发，你可以在回调函数中的`response.data`参数中获得alert的内容。

```
casper.waitForAlert(function(response) {
    this.echo("Alert received: " + response.data);
});
```

#### waitForExec()

**调用方式**： waitForExec(command, parameters[, Function then, Function onTimeout, timeout])

*版本1.1.5新增*

等待直到command运行并退出。command, parameters, pid, stdout, stderr, elapsedTime和exitCode将会包含在response.data属性中。command必须为一个字符串，parameters必须为一个数组，command可以为一个可执行的字符串或一个可执行的字符串及其其由空格分隔的参数，如果命令是伪造的或不是字符串，则使用系统shell（环境变量SHELL或ComSpec）。由空格分隔的参数与要发送到可执行文件的parameters数组相连接。如果参数是伪造的或不是数组，则使用空数组。timeout可以是一个数字或两个数字的数组，第一个是wait*系列函数的超时，第二个是超时是TERM和KILL信号之间的超时。如果没有声明，它假定第一个元素的值相同或者等于wait家族函数的默认超时值：

```
// merge captured PDFs with default system shell (bash on Linux) calling /usr/bin/gs, and runs a small script to remove files
casper.waitForExec(null, ['-c','{ /usr/bin/gs -dPDFSETTINGS=/ebook -dBATCH -dNOPAUSE -q -sDEVICE=pdfwrite -sOutputFile=/my_merged_captures.pdf  /my_captures*.pdf && /bin/rm /my_captures*.pdf; } || { /bin/rm /my_merged_captures.pdf && exit 1; }'],
    function(response) {
        this.echo("Program finished by itself:" + JSON.stringify(response.data));
    }, function(timeout, response) {
        this.echo("Program finished by casper:" + JSON.stringify(response.data));
});

 // merge captured PDFs with bash calling /usr/bin/gs, and runs a small script to remove files
casper.waitForExec('/bin/bash -c', ['{ /usr/bin/gs -dPDFSETTINGS=/ebook -dBATCH -dNOPAUSE -q -sDEVICE=pdfwrite -sOutputFile=/my_merged_captures.pdf  /my_captures*.pdf && /bin/rm /my_captures*.pdf; } || { /bin/rm /my_merged_captures.pdf && exit 1; }'],
    function(response) {
        this.echo("Program finished by itself:" + JSON.stringify(response.data));
    }, function(timeout, response) {
        this.echo("Program finished by casper:" + JSON.stringify(response.data));
});


// merge captured PDFs calling /usr/bin/gs
casper.waitForExec('/usr/bin/gs',['-dPDFSETTINGS=/ebook','-dBATCH','-dNOPAUSE','-q','-sDEVICE=pdfwrite','-sOutputFile=/my_merged_captures_pdfs.pdf','/my_captures_1.pdf','/my_captures_2.pdf','/my_captures_3.pdf'],
    function(response) {
        this.echo("Program finished by itself:" + JSON.stringify(response.data));
    }, function(timeout, response) {
        this.echo("Program finished by casper:" + JSON.stringify(response.data));
});

// merge captured PDFs calling /usr/bin/gs
casper.waitForExec('/usr/bin/gs -dPDFSETTINGS=/ebook -dBATCH -dNOPAUSE -q -sDEVICE=pdfwrite -sOutputFile=/my_merged_captures_pdfs.pdf /my_captures_1.pdf /my_captures_2.pdf /my_captures_3.pdf', null,
    function(response) {
        this.echo("Program finished by itself:" + JSON.stringify(response.data));
    }, function(timeout, response) {
        this.echo("Program finished by casper:" + JSON.stringify(response.data));
});
```

> ##### 提示

waitForExec（）仅在超时时终止被调用的程序，如果被调用的程序调用其他进程，则waitForExec（）超时时该进程不会被杀死。

#### waitForPopup()

**调用方式**： waitForPopup(String|RegExp|Object urlPattern[, Function then, Function onTimeout, Number timeout])

*版本1.0新增*

等待一个与提供的模式匹配的弹窗打开。

当前加载的弹出窗口在Casper.popups数组类属性中可用：

```
casper.start('http://foo.bar/').then(function() {
    this.test.assertTitle('Main page title');
    this.clickLabel('Open me a popup');
});

// this will wait for the popup to be opened and loaded
casper.waitForPopup(/popup\.html$/, function() {
    this.test.assertEquals(this.popups.length, 1);
});

// this will wait for the first popup to be opened and loaded
casper.waitForPopup(0, function() {
    this.test.assertEquals(this.popups.length, 1);
});

// this will wait for the popup's named to be opened and loaded
casper.waitForPopup({windowName: "mainPopup"}, function() {
    this.test.assertEquals(this.popups.length, 1);
});

// this will wait for the popup's title to be opened and loaded
casper.waitForPopup({title: "Popup Title"}, function() {
    this.test.assertEquals(this.popups.length, 1);
});

// this will wait for the popup's url to be opened and loaded
casper.waitForPopup({url: 'http://foo.bar/'}, function() {
    this.test.assertEquals(this.popups.length, 1);
});

// this will set the popup DOM as the main active one only for time the
// step closure being executed
casper.withPopup(/popup\.html$/, function() {
    this.test.assertTitle('Popup title');
});

// next step will automatically revert the current page to the initial one
casper.then(function() {
    this.test.assertTitle('Main page title');
});
```

#### waitForResource()

**调用方式**： waitForResource(String|Function|RegExp testFx[, Function then, Function onTimeout, Number timeout])

等待由`testFx`指定的资源加载完毕并准备好进行下一步。

`testFx`参数可以是一个字符串，一个函数或是一个正则表达式实例：

```
casper.waitForResource("foobar.png", function() {
    this.echo('foobar.png has been loaded.');
});
```

使用正则表达式：

```
casper.waitForResource(/foo(bar|baz)\.png$/, function() {
    this.echo('foobar.png or foobaz.png has been loaded.');
});
```

使用函数：

```
casper.waitForResource(function testResource(resource) {
    return resource.url.indexOf("https") === 0;
}, function onReceived() {
    this.echo('a secure resource has been loaded.');
});
```

#### waitForUrl()

**调用方式**： waitForUrl(String|RegExp url[, Function then, Function onTimeout, Number timeout])

*版本1.1新增*

等待直到当前页面的url符合提供的模式（可以是字符串或正则表达式）：

```
casper.start('http://foo/').waitForUrl(/login\.html$/, function() {
    this.echo('redirected to login.html');
});

casper.run();
```

#### waitForSelector()

**调用方式**： waitForSelector(String selector[, Function then, Function onTimeout, Number timeout])

等待与提供的选择器匹配的元素存在并执行下一步骤：

```
casper.start('https://twitter.com/#!/n1k0');

casper.waitForSelector('.tweet-row', function() {
    this.captureSelector('twitter.png', 'html');
});

casper.run();
```

```
var xLoadMoreBtn = {type:'xpath',path:'//a[contains(text(), "Load more")]'};

function addStep() {
    casper.waitForSelector(xLoadMoreBtn, function() {
        casper.capture('ok.png');
        casper.click(xLoadMoreBtn);
        addStep();
    })
}

addStep();
casper.run();
```





#### waitWhileSelector()

**调用方式**： waitWhileSelector(String selector[, Function then, Function onTimeout, Number timeout])

在执行下一步骤之前等待与提供的选择器匹配的元素的内容改变：

```
casper.start('http://foo.bar/');

casper.waitForSelectorTextChange('.selector', function() {
    this.echo('The text on .selector has been changed.');
});

casper.run();
```

#### waitForText()

**调用方式**： waitForText(String text[, Function then, Function onTimeout, Number timeout])

*版本1.0新增*

等待直到指定的文本在当前页面出现：

```
casper.start('http://why.univer.se/').waitForText("42", function() {
    this.echo('Found the answer.');
});

casper.run();
```

#### waitUntilVisible()

**调用方式**： waitUntilVisible(String selector[, Function then, Function onTimeout, Number timeout])

等待直到与提供的选择器匹配的元素在dom中可见。

#### waitWhileVisible()

**调用方式**： waitWhileVisible(String selecto[, Function then, Function onTimeout, Number timeout])

等待与提供选择器匹配的元素由可见变为不可见并执行下一步骤：

```
var casper = require('casper').create();

casper.start('https://www.example.com/').thenClick('html body div p a', function () {
    this.waitWhileVisible('body > div:nth-child(1) > p:nth-child(2)', function () {
        this.echo("The selected element existed in previous page but doesn't exist in this page.");
    })
}).run();
```

#### warn()

**调用方式**： warn(String message)

在标准输出中输出一条警告信息：

```
casper.warn("I'm a warning message.");
```

> ##### 提示

调用`warn()`函数会触发warn事件。

#### withFrame()

**调用方式**：withFrame(String|Number frameInfo, Function then)

*版本1.0新增*

将主页切换到具有与传递参数相匹配的名称或frame索引号的frame，并处理一个步骤。

页面上下文切换仅持续到步骤执行完成：

```
casper.start('tests/site/frames.html', function() {
    this.test.assertTitle('FRAMESET TITLE');
});

casper.withFrame('frame1', function() {
    this.test.assertTitle('FRAME TITLE');
});

casper.withFrame(0, function() {
    this.test.assertTitle('FRAME TITLE');
});

casper.then(function() {
    this.test.assertTitle('FRAMESET TITLE');
});
```

#### withPopup()

**调用方式**：withPopup(Mixed popupInfo, Function then)

*版本1.0新增*

将主页面切换到匹配作为参数传递的信息的弹出窗口，并处理一个步骤。页面上下文切换仅持续到步骤执行完成：

```
casper.start('http://foo.bar/').then(function() {
    this.test.assertTitle('Main page title');
    this.clickLabel('Open me a popup');
});

// this will wait for the popup to be opened and loaded
casper.waitForPopup(/popup\.html$/, function() {
    this.test.assertEquals(this.popups.length, 1);
});

// this will set the popup DOM as the main active one only for time the
// step closure being executed
casper.withPopup(/popup\.html$/, function() {
    this.test.assertTitle('Popup title');
});

// this will set the popup DOM as the main active one only for time the
// step closure being executed
casper.withPopup(0, function() {
    this.test.assertTitle('Popup title');
});

// this will set the popup DOM as the main active one only for time the
// step closure being executed
casper.withPopup({windowName: "mainPopup", title:'Popup title', url:'http://foo.bar/'}, function() {
    this.test.assertTitle('Popup title');
});

// next step will automatically revert the current page to the initial one
casper.then(function() {
    this.test.assertTitle('Main page title');
});
```

> ##### 提示

当前加载的弹出窗口在Casper.popups数组类属性中可用。

#### withSelectorScope()

**调用方式**： withSelectorScope(String selector, Function then)

*版本1.1.5新增*

将主DOM范围切换到特定范围，作为参数传递的信息，并处理一个步骤。它的范围上下文切换只能持续到步执行完成：

```
.. index:: Zoom
```

#### zoom()

**调用方式**: zoom(Number factor)

*版本1.0新增*

设置当前页面缩放系数:

```
var casper = require('casper').create();

casper.start().zoom(2).thenOpen('http://google.com', function() {
    this.capture('big-google.png');
});

casper.run();
```







## clientutils 模块

Casper附带了一些注入远程DOM环境的客户端实用程序，并可通过clientUilils模块的ClientUtils类的__utils__对象实例从那里访问：

```
casper.evaluate(function() {
  __utils__.echo("Hello World!");
});
```

> ##### 提示

提供这些工具是为了避免将CasperJS与任何第三方库（如jQuery，Mootools或某些东西）相耦合，但是你可以随时使用这些工具，并设置Casper.options.clientScripts选项让这些工具在客户端可用。



### ClientUtils 原型

#### echo()

**调用方法**：echo(String message)

*版本1.0新增*

从远程页面DOM环境向casper控制台输出消息：

```
casper.start('http://foo.ner/').thenEvaluate(function() {
    __utils__.echo('plop'); // this will be printed to your shell at runtime
});
```

#### encode()

**调用方法**：encode(String contents)

此方法使用base64算法对字符串进行编码。对于记录，CasperJS不使用内置的window.btoa（）函数，因为它不能有效地处理使用> 8b字符编码的字符串：

```
var base64;
casper.start('http://foo.bar/', function() {
    base64 = this.evaluate(function() {
        return __utils__.encode("I've been a bit cryptic recently");
    });
});

casper.run(function() {
    this.echo(base64).exit();
});
```

#### exists()

**调用方法**：exists(String selector)

检查与给定选择器表达式匹配的DOM元素是否存在：

```
var exists;
casper.start('http://foo.bar/', function() {
    exists = this.evaluate(function() {
        return __utils__.exists('#some_id');
    });
});

casper.run(function() {
    this.echo(exists).exit();
});
```

#### findAll()

**调用方法**： findAll(String selector)

检索与给定选择器表达式匹配的所有DOM元素：

```
var links;
casper.start('http://foo.bar/', function() {
    links = this.evaluate(function() {
        var elements = __utils__.findAll('a.menu');
        return elements.map(function(e) {
            return e.getAttribute('href');
        });
    });
});

casper.run(function() {
    this.echo(JSON.stringify(links)).exit();
});
```

#### findOne()

**调用方法**：findOne(String selector)

通过选择器表达式检索单个DOM元素：

```
var href;
casper.start('http://foo.bar/', function() {
    href = this.evaluate(function() {
        return __utils__.findOne('#my_id').getAttribute('href');
    });
});

casper.run(function() {
    this.echo(href).exit();
});
```

#### forceTarget()

**调用方法**：forceTarget(String selector, String target)

强制引擎使用另一个目标而不是提供的目标。这对于限制打开的窗口数量并减少内存消耗非常有用：

```
casper.start('http://foo.bar/', function() {
    var href = this.evaluate(function() {
        return __utils__.forceTarget('#my_id', '_self').click();
    });
    this.echo(href);
});

casper.run(function() {
    this.exit();
});
```

#### getBase64()

**调用方法**:getBase64(String url[, String method, Object data])

此方法将检索url后面的任何资源的base64编码版本。例如，我们假设我们想要检索一些网站logo的base64表示:

```
var logo = null;
casper.start('http://foo.bar/', function() {
    logo = this.evaluate(function() {
        var imgUrl = document.querySelector('img.logo').getAttribute('src');
        return __utils__.getBase64(imgUrl);
    });
});

casper.run(function() {
    this.echo(logo).exit();
});
```

#### getBinary()

**调用方法**:getBinary(String url[, String method, Object data])

该方法将检索给定二进制资源的原始内容，不幸的是，PhantomJS无法直接处理这些数据，因此您必须在远程DOM环境中处理这些数据。如果您打算下载资源，请改用getBase64()或Casper.base64encode()。

```
casper.start('http://foo.bar/', function() {
    this.evaluate(function() {
        var imgUrl = document.querySelector('img.logo').getAttribute('src');
        console.log(__utils__.getBinary(imgUrl));
    });
});

casper.run();
```

#### getDocumentHeight()

**调用方法**: getDocumentHeight()

*版本1.0新增*

检索当前文档高度：

```
var documentHeight;

casper.start('http://google.com/', function() {
    documentHeight = this.evaluate(function() {
        return __utils__.getDocumentHeight();
    });
    this.echo('Document height is ' + documentHeight + 'px');
});

casper.run();
```

#### getElementBounds()

**调用方法**: getElementBounds(String selector)

检索与提供的选择器匹配的DOM元素的边界。

它返回一个Object，其中包含四个键：top，left，width和height;如果不存在匹配的元素，则返回null。

#### getElementsBounds()

**调用方法**: getElementsBounds(String selector)

检索与提供的选择器匹配的所有DOM元素的边界。

它返回一个对象数组，每个对象都有四个键：顶部，左边，宽度和高度。

#### getElementsByXPath()

**调用方法**: getElementByXPath(String expression [, HTMLElement scope])

检索与给定XPath表达式匹配的单个DOM元素。

*版本1.0新增*

scope参数允许设置执行XPath查询的上下文：

```
// will be performed against the whole document
__utils__.getElementByXPath('.//a');

// will be performed against a given DOM element
__utils__.getElementByXPath('.//a', __utils__.findOne('div.main'));
```

#### getFieldValue()

**调用方法**: getFieldValue(String selector[, HTMLElement scope])

*版本1.0新增*

以name为参数检测input域中的值：

```
<form>
    <input type="text" name="plop" value="42">
</form>
```

用该方法获取`plop`的值：

```
__utils__.getFieldValue('[name="plop"]'); // 42
```

#### getFormValues()

**调用方法**: getFormValues(String selector)

*版本1.0新增*

检索给定的表单及其所有字段值:

```
<form id="login" action="/login">
    <input type="text" name="username" value="foo">
    <input type="text" name="password" value="bar">
    <input type="submit">
</form>
```

要获取表单值：

```
__utils__.getFormValues('form#login'); // {username: 'foo', password: 'bar'}
```

#### log()

**调用方法**: log(String message[, String level])

你可以选择记录的级别。我们将CasperJS的消息格式化为能与phantomjs匹配的格式。记录的默认级别是`debug`：

```
casper.start('http://foo.ner/').thenEvaluate(function() {
    __utils__.log("We've got a problem on client side", 'error');
});
```

#### makeSelector()

**调用方法**: makeSelector(String selector [, String type])

*版本1.1-beta5新增*

通过定义的类型XPath，Name或Label进行选择。函数与用于XPath类型的Casper模块中的selectXPath具有相同的结果 - 它生成XPath对象。 Function还接受表单字段的名称属性，或者可以通过其标签文本来选择元素。

参数type的值可以是：

- css

  CSS3选择器 - 选择器将直接被返回

- ‘xpath’ || null

  XPath选择器-将返回XPath对象

- ‘name’ || ‘names’

  选择特定名称的输入，内部隐藏到CSS3选择器

- ‘label’ || ‘labels’

  选择特定标签的输入，由于选择器是标签的文本使用，在内部转换成XPath选择器。

举个例子：

```
__utils__.makeSelector('//li[text()="blah"]', 'xpath'); // return {type: 'xpath', path: '//li[text()="blah"]'}
__utils__.makeSelector('parameter', 'name'); // return '[name="parameter"]'
__utils__.makeSelector('My label', 'label'); // return {type: 'xpath', path: '//*[@id=string(//label[text()="My label"]/@for)]'}
```

#### mouseEvent()

**调用方法**: mouseEvent(String type, String selector, [Number|String X, Number|String Y])

将鼠标事件分派给提供的选择器后面的DOM元素。

支持的事件有：mouseup, mousedown, click, dblclick, mousemove, mouseover, mouseout, mouseenter, mouseleave 和 contextmenu：

```
.. index:: XPath
```

#### removeElementsByXPath()

**调用方法**: removeElementsByXPath(String expression)

删除与给定的XPath表达式匹配的所有DOM元素。

#### sendAJAX()

**调用方法**: sendAJAX(String url[, String method, Object data, Boolean async, Object settings])

*版本1.0新增*

按照以下的参数发送AJAX请求：

- `url`:请求的路径
- `method`:请求的方法（默认：GET）
- `data`:发送的数据（默认：null）
- `async`:请求是否同步(默认：false)
- `setting`:自定义AJAX发送时的头信息（默认：null），警告：此处无效的头可能会使请求失败。

> ##### 警告

不要忘记在CLI调用中传递`--web-security = no`选项，以便在需要时执行跨域请求：

```
var data, wsurl = 'http://api.site.com/search.json';

casper.start('http://my.site.com/', function() {
    data = this.evaluate(function(wsurl) {
        return JSON.parse(__utils__.sendAJAX(wsurl, 'GET', null, false));
    }, {wsurl: wsurl});
});

casper.then(function() {
    require('utils').dump(data);
});
```

#### setFieldValue()

**调用方法**: setFieldValue(String|Object selector, Mixed value [, HTMLElement scope])

*版本1.1-beta5新增*

通过CSS3或XPath选择器设置值以形成字段。使用makeSelector（）函数可以方便地使用name或label选择器。

- (String|Object) scope: selector :

  具体的表单范围

例子：

```
__utils__.setFieldValue("input[name='email']", 'chuck@norris.com');
__utils__.setFieldValue("input[name='email']", 'chuck@norris.com', {'formSelector': '#myform'});
__utils__.setFieldValue(__utils__.makeSelector('email', 'name'), 'chuck@norris.com');
```

#### visible()

**调用方法**: visible(String selector)

检查某个元素是否可见：

```
var logoIsVisible = casper.evaluate(function() {
    return __utils__.visible('h1');
});
```





## colorizer模块

colorizer（着色器）模块包含Colorizer类，可以生成ANSI彩色字符串：

```
var colorizer = require('colorizer').create('Colorizer');
console.log(colorizer.colorize("Hello World", "INFO"));
```

虽然大多数时候你会直接使用`Casper.echo()`方法：

```
casper.echo('an informative message', 'INFO'); // printed in green
casper.echo('an error message', 'ERROR');      // printed in red
```

### 忽略CasperJS的样式设置

如果你希望跳过整个着色操作并获取无色的纯文本，只需将colorizerType casper选项设置为Dummy：

```
var casper = require('casper').create({
    colorizerType: 'Dummy'
});

casper.echo("Hello", "INFO");
```

> ##### 提示

如果您在Windows平台上使用CasperJS，这一点尤其有用，因为在此平台上不支持彩色输出。

### 预定义样式

可用的预定义样式有：

- ERROR：红色背景，白色文字。
- INFO：绿色文字
- TRACE：绿色文字
- PARAMETER：青色文字
- COMMENT：黄色文字
- WARNING：红色文字
- GREEN_BAR：绿色背景，白色文字
- RAD_BAR：红色背景，白色文字
- INFO_BAR：青色文字
- WARN_BAR：橙色背景，白色文字

这里有一份输出的示例图：[![http://docs.casperjs.org/en/latest/_images/colorizer.png](https://github.com/RaHsu/casperjs-document-chinese/raw/master/img)](https://github.com/RaHsu/casperjs-document-chinese/blob/master/img)

#### colorize()

**调用方式**：colorize(String text, String styleName)

使用给定的预定义样式输出彩色字符：

```
var colorizer = require('colorizer').create();
console.log(colorizer.colorize("I'm a red error", "ERROR"));
```

> ##### 提示

大多数情况下，您不必直接使用Colorizer实例，因为CasperJS提供了所有必要的方法。

在这里查看[可用的预定义样式列表](http://docs.casperjs.org/en/latest/modules/colorizer.html#colorizer-styles)

#### format()

**调用方法**：format(String text, Object style)

你可以使用提供的样式定义格式化文本字符串。样式定义是一个标准的javascript对象实例，可以定义下列属性：

- String `bg`：背景颜色名
- String `fg`：文字颜色名
- Boolean `bold`：是否使用粗体格式
- Boolean `underscore`：是否使用下划线
- Boolean `blink`：是否使用闪烁样式
- Boolean `reverse`：是否将字符反转
- Boolean `conceal`：是否将字符隐藏

> ##### 提示

你可以使用的颜色有`black`,`red`,`yellow`,`blue`,`magenta`,`cyan`和`white`。

```
var colorizer = require('colorizer').create();
colorizer.format("We all live in a yellow submarine", {
    bg:   'yellow',
    fg:   'blue',
    bold: true
});
```





## Mouse 模块

### Mouse类

Mouse类是移动，点击，双击，滚动等各种鼠标操作的抽象。它需要Casper实例作为访问DOM的依赖项。你可以这样创建一个mouse对象：

```
var casper = require("casper").create();
var mouse = require("mouse").create(casper);
```

> ##### 提示

casper实例已经定义了鼠标属性，通常并不需要你手动去创建：

```
casper.then(function() {
    this.mouse.click(400, 300); // clicks at coordinates x=400; y=300
});
```

#### click()

**调用方式**：

- click(Number x, Number y)
- click(String selector)
- click(String selector, Number x, Number y)

如果传递的参数为一个选择器表达式，将会对第一个匹配的元素进行点击，如果传递的参数为一对数字，将会对这对数字所表示的坐标进行点击：

```
casper.then(function() {
    this.mouse.click("#my-link"); // clicks <a id="my-link">hey</a>
    this.mouse.click(400, 300);   // clicks at coordinates x=400; y=300
});
```

#### doubleclick()

**调用方式**：

- doubleclick(Number x, Number y)
- doubleclick(String selector)
- doubleclick(String selector, Number x, Number y)

将doubleclick鼠标事件发送到与提供的参数匹配的元素上：

```
casper.then(function() {
    this.mouse.doubleclick("#my-link"); // doubleclicks <a id="my-link">hey</a>
    this.mouse.doubleclick(400, 300);   // doubleclicks at coordinates x=400; y=300
});
```

#### rightclick()

**调用方式**：

- rightclick(Number x, Number y)
- rightclick(String selector)
- rightclick(String selector, Number x, Number y)

将一个contextmenu鼠标事件（即点击鼠标右键）发送到与提供的参数匹配的元素上：

```
casper.then(function() {
    this.mouse.rightclick("#my-link");
    this.mouse.rightclick(400, 300);   /
});
```

#### down()

**调用方式**：

- down(Number x, Number y)
- down(String selector)
- down(String selector, Number x, Number y)

将mousedown鼠标事件发送到与提供的参数匹配的元素上：

```
casper.then(function() {
    this.mouse.down("#my-link");
    this.mouse.down(400, 300);
});
```

#### move()

**调用方式**：

- move(Number x, Number y)
- move(String selector)
- move(String selector, Number x, Number y)

将鼠标光标移动到与提供的参数匹配的元素上：

```
casper.then(function() {
    this.mouse.move("#my-link"); // moves cursor over <a id="my-link">hey</a>
    this.mouse.move(400, 300);   // moves cursor over coordinates x=400; y=300
});
```

#### up()

**调用方式**：

- up(Number x, Number y)
- up(String selector)
- up(String selector, Number x, Number y)

将mouseup鼠标事件发送到与提供的参数匹配的元素上：

```
casper.then(function() {
    this.mouse.up("#my-link"); // release left button over <a id="my-link">hey</a>
    this.mouse.up(400, 300);   // release left button over coordinates x=400; y=300
});
```





## tester模块

Casper附带一个测试器模块和一个Tester类，提供用于单元和功能测试的API。默认情况下，您可以通过任何Casper类实例的测试属性来访问此类的实例。

> ##### 提示

学习如何使用Tester API并且看到它如何运作的最好方法可能是去看看CasperJS自己的测试套件。

### tester原型

#### assert()

**调用方法**：assert(Boolean condition[, String message])

断言所提供的条件严格解决为布尔值ture。

```
casper.test.assert(true, "true's true");
casper.test.assert(!false, "truth is out");
```

> ##### 提示

同样也去看看类似的assertNot()函数。

#### assertDoesntExist()

**调用方法**： assertDoesntExist(String selector[, String message])

断言在远程DOM环境中不存在与提供的选择器表达式匹配的元素：

```
casper.test.begin('assertDoesntExist() tests', 1, function(test) {
    casper.start().then(function() {
        this.setContent('<div class="heaven"></div>');
        test.assertDoesntExist('.taxes');
    }).run(function() {
        test.done();
    });
});
```

> ##### 提示

同样也去看看类似的assertExists()函数。

#### assertEquals()

**调用方法**：assertEquals(mixed testValue, mixed expected[, String message])

断言两个值是严格等价的：

```
casper.test.begin('assertEquals() tests', 3, function(test) {
    test.assertEquals(1 + 1, 2);
    test.assertEquals([1, 2, 3], [1, 2, 3]);
    test.assertEquals({a: 1, b: 2}, {a: 1, b: 2});
    test.done();
});
```

> ##### 提示

同样也去看看类似的assertNotEquals()函数。

#### assertEval()

**调用方法**：assertEval(Function fn[, String message, Mixed arguments])

断言远程DOM中的代码评估严格解决为布尔值ture：

```
casper.test.begin('assertEval() tests', 1, function(test) {
    casper.start().then(function() {
        this.setContent('<div class="heaven">beer</div>');
        test.assertEval(function() {
            return __utils__.findOne('.heaven').textContent === 'beer';
        });
    }).run(function() {
        test.done();
    });
});
```

#### assertEvalEquals()

**调用方法**： assertEvalEquals(Function fn, mixed expected[, String message, Mixed arguments])

断言远程DOM中的代码评估结果严格等于期望值：

```
casper.test.begin('assertEvalEquals() tests', 1, function(test) {
    casper.start().then(function() {
        this.setContent('<div class="heaven">beer</div>');
        test.assertEvalEquals(function() {
            return __utils__.findOne('.heaven').textContent;
        }, 'beer');
    }).run(function() {
        test.done();
    });
});
```

#### assertElementCount()

**调用方法**： assertElementCount(String selector, Number count[, String message])

断言选择器表达式匹配给定数量的元素：

```
casper.test.begin('assertElementCount() tests', 3, function(test) {
    casper.start().then(function() {
        this.page.content = '<ul><li>1</li><li>2</li><li>3</li></ul>';
        test.assertElementCount('ul', 1);
        test.assertElementCount('li', 3);
        test.assertElementCount('address', 0);
    }).run(function() {
        test.done();
    });
});
```

#### assertExists()

**调用方法**： assertExists(String selector[, String message])

断言在远程DOM环境中存在与提供的选择器表达式匹配的元素：

```
casper.test.begin('assertExists() tests', 1, function(test) {
    casper.start().then(function() {
        this.setContent('<div class="heaven">beer</div>');
        test.assertExists('.heaven');
    }).run(function() {
        test.done();
    });
});
```

> ##### 提示

同时建议你看看这个类似的函数assertDoesntExist()。

#### assertFalsy()

**调用方法**： assertFalsy(Mixed subject[, String message])

*版本1.0新增*

断言某个对象为假值。

> ##### 提示

同时建议你看看这个类似的函数assertTruthy()。

#### assertField()

**调用方法**： assertField(String|Object input, String expected[, String message, Object options])

通过name属性或选择器表达式选定表单域，并断言给定的表单域的值和提供的值相同：

```
casper.test.begin('assertField() tests', 1, function(test) {
    casper.start('http://www.google.fr/', function() {
        this.fill('form[name="gs"]', { q: 'plop' }, false);
        test.assertField('q', 'plop');
    }).run(function() {
        test.done();
    });
});

// Path usage with type 'css'
casper.test.begin('assertField() tests', 1, function(test) {
    casper.start('http://www.google.fr/', function() {
        this.fill('form[name="gs"]', { q: 'plop' }, false);
        test.assertField({type: 'css', path: '.q.foo'}, 'plop');
    }).run(function() {
        test.done();
    });
});
```

*版本1.0新增*

该方法也可以使用在`select`，`textarea`等文本域中了。

*版本1.1新增*

options参数允许设置与ClientUtils#getFieldValue()一起使用的选项。

输入参数将检测是否传入一个type与xpath或css和与它一起指定的属性路径。

#### assertFieldName()

**调用方法**： assertFieldName(String inputName, String expected[, String message, Object options])

*版本1.1-beta3新增*

断言给定的表单域具有提供的值。

```
casper.test.begin('assertFieldName() tests', 1, function(test) {
    casper.start('http://www.google.fr/', function() {
        this.fill('form[name="gs"]', { q: 'plop' }, false);
        test.assertFieldName('q', 'plop', 'did not plop', {formSelector: 'plopper'});
    }).run(function() {
        test.done();
    });
});
```

#### assertFieldCSS()

**调用方法**：assertFieldCSS(String cssSelector, String expected, String message)

*版本1.1新增*

断言给定的表单字段具有给定的CSS选择器提供的值

```
casper.test.begin('assertFieldCSS() tests', 1, function(test) {
    casper.start('http://www.google.fr/', function() {
        this.fill('form[name="gs"]', { q: 'plop' }, false);
        test.assertFieldCSS('q', 'plop', 'did not plop', 'input.plop');
    }).run(function() {
        test.done();
    });
});
```

#### assertFieldXPath()

**调用方法**：assertFieldXPath(String xpathSelector, String expected, String message)

*版本1.1新增*

断言给定的表单域具有提供给XPath选择器的值：

```
casper.test.begin('assertFieldXPath() tests', 1, function(test) {
    casper.start('http://www.google.fr/', function() {
        this.fill('form[name="gs"]', { q: 'plop' }, false);
        test.assertFieldXPath('q', 'plop', 'did not plop', '/html/body/form[0]/input[1]');
    }).run(function() {
        test.done();
    });
});
```

#### assertHttpStatus()

**调用方法**：assertHttpStatus(Number status[, String message])

断言当前的HTTP状态代码与作为参数传递的状态代码相同：

```
casper.test.begin('casperjs.org is up and running', 1, function(test) {
    casper.start('http://casperjs.org/', function() {
        test.assertHttpStatus(200);
    }).run(function() {
        test.done();
    });
});
```

#### assertMatch()

**调用方法**：assertMatch(mixed subject, RegExp pattern[, String message])

断言所提供的字符串与提供的JavaScript RegExp模式匹配：

```
casper.test.assertMatch('Chuck Norris', /^chuck/i, 'Chuck Norris\' first name is Chuck');
```

> ##### 提示

建议你也去看看类似的assertUrlMatch()和assertTitleMatch()函数。

#### assertNot()

**调用方法**：assertNot(mixed subject[, String message])

断言传入的对象为一个假值（falsy）。

```
casper.test.assertNot(false, "Universe is still operational");
```

#### assertNotEquals()

**调用方法**：assertNotEquals(mixed testValue, mixed expected[, String message])

*版本0.6.7新增*

断言两个值非严格相等。

```
casper.test.assertNotEquals(true, "true");
```

#### assertNotVisible()

**调用方法**：assertNotVisible(String selector[, String message])

断言匹配提供的选择器表达式的元素不可见：

```
casper.test.begin('assertNotVisible() tests', 1, function(test) {
    casper.start().then(function() {
        this.setContent('<div class="foo" style="display:none>boo</div>');
        test.assertNotVisible('.foo');
    }).run(function() {
        test.done();
    });
});
```

#### assertRaises()

**调用方法**：assertRaises(Function fn, Array args[, String message])

断言用给定的参数调用提供的函数引发一个javascript错误：

```
casper.test.assertRaises(function(throwIt) {
    if (throwIt) {
        throw new Error('thrown');
    }
}, [true], 'Error has been raised.');

casper.test.assertRaises(function(throwIt) {
    if (throwIt) {
        throw new Error('thrown');
    }
}, [false], 'Error has been raised.'); // fails
```

#### assertSelectorDoesntHaveText()

**调用方法**：assertSelectorDoesntHaveText(String selector, String text[, String message])

断言给定的文本在所有与提供的选择器表达式匹配的元素中不存在：

```
casper.test.begin('assertSelectorDoesntHaveText() tests', 1, function(test) {
    casper.start('http://google.com/', function() {
        test.assertSelectorDoesntHaveText('title', 'Yahoo!');
    }).run(function() {
        test.done();
    });
});
```

#### assertResourceExists()

**调用方法**:assertResourceExists(Function testFx[, String message])

testFx函数针对所有加载的资源执行，当至少有一个资源匹配时测试通过：

```
casper.test.begin('assertResourceExists() tests', 1, function(test) {
    casper.start('http://www.google.fr/', function() {
        test.assertResourceExists(function(resource) {
            return resource.url.match('logo3w.png');
        });
    }).run(function() {
        test.done();
    });
});
```

简写版：

```
casper.test.begin('assertResourceExists() tests', 1, function(test) {
    casper.start('http://www.google.fr/', function() {
        test.assertResourceExists('logo3w.png');
    }).run(function() {
        test.done();
    });
});
```

> ##### 提示

去看看这个类似的Casper.resourceExists()函数吧.

#### assertTextExists()

**调用方法**：assertTextExists(String expected[, String message])

断言body标签的**文本**中含有给定的字符串：

```
casper.test.begin('assertTextExists() tests', 1, function(test) {
    casper.start('http://www.google.fr/', function() {
        test.assertTextExists('google', 'page body contains "google"');
    }).run(function() {
        test.done();
    });
});
```

#### assertTextDoesntExist()

**调用方法**：assertTextDoesntExist(String unexpected[, String message])

*版本1.0新增* 断言body标签的**文本**中不含有给定的字符串：

```
casper.test.begin('assertTextDoesntExist() tests', 1, function(test) {
    casper.start('http://www.google.fr/', function() {
        test.assertTextDoesntExist('bing', 'page body does not contain "bing"');
    }).run(function() {
        test.done();
    });
});
```

#### assertTitle()

**调用方法**：assertTitle(String expected[, String message])

断言远程页面的标题等于预期的标题：

```
casper.test.begin('assertTitle() tests', 1, function(test) {
    casper.start('http://www.google.fr/', function() {
        test.assertTitle('Google', 'google.fr has the correct title');
    }).run(function() {
        test.done();
    });
});
```

#### assertTitleMatch()

**调用方法**：assertTitleMatch(RegExp pattern[, String message])

断言远程页面的标题与提供的RegExp模式匹配：

```
casper.test.begin('assertTitleMatch() tests', 1, function(test) {
    casper.start('http://www.google.fr/', function() {
        test.assertTitleMatch(/Google/, 'google.fr has a quite predictable title');
    }).run(function() {
        test.done();
    });
});
```

#### assertType()

**调用方法**：assertType(mixed input, String type[, String message])

断言提供的输入是给定的类型：

```
casper.test.begin('assertType() tests', 1, function suite(test) {
    test.assertType(42, "number", "Okay, 42 is a number");
    test.assertType([1, 2, 3], "array", "We can test for arrays too!");
    test.done();
});
```

> ##### 注意

类型名称总是以小写字母表示。

#### assertInstanceOf()

**调用方法**：assertInstanceOf(mixed input, Function constructor[, String message])

*版本1.1新增*

断言所提供的输入属于给定构造函数的：

```
function Cow() {
    this.moo = function moo() {
        return 'moo!';
    };
}
casper.test.begin('assertInstanceOf() tests', 2, function suite(test) {
    var daisy = new Cow();
    test.assertInstanceOf(daisy, Cow, "Ok, daisy is a cow.");
    test.assertInstanceOf(["moo", "boo"], Array, "We can test for arrays too!");
    test.done();
});
```

#### assertUrlMatch()

**调用方法**：assertUrlMatch(Regexp pattern[, String message])

断言当前页面的url匹配提供的RegExp模式：

```
casper.test.begin('assertUrlMatch() tests', 1, function(test) {
    casper.start('http://www.google.fr/', function() {
        test.assertUrlMatch(/^http:\/\//, 'google.fr is served in http://');
    }).run(function() {
        test.done();
    });
});
```

#### assertVisible()

**调用方法**：assertVisible(String selector[, String message])

断言至少有一个与提供的选择器表达式匹配的元素是可见的：

```
casper.test.begin('assertVisible() tests', 1, function(test) {
    casper.start('http://www.google.fr/', function() {
        test.assertVisible('h1');
    }).run(function() {
        test.done();
    });
});
```

#### assertAllVisible()

**调用方法**：assertAllVisible(String selector[, String message])

断言所有匹配提供的选择器表达式的元素都是可见的：

```
casper.test.begin('assertAllVisible() tests', 1, function(test) {
    casper.start('http://www.google.fr/', function() {
        test.assertAllVisible('input[type="submit"]');
    }).run(function() {
        test.done();
    });
});
```

#### begin()

**调用方法**：

- begin(String description, Number planned, Function suite)
- begin(String description, Function suite)
- begin(String description, Number planned, Object config)
- begin(String description, Object config)

*版本1.1新增*

开始一个`<planned>`测试套件，套件回调将获取当前的Tester实例作为其第一个参数：

```
function Cow() {
    this.mowed = false;
    this.moo = function moo() {
        this.mowed = true; // mootable state: don't do that
        return 'moo!';
    };
}

// unit style synchronous test case
casper.test.begin('Cow can moo', 2, function suite(test) {
    var cow = new Cow();
    test.assertEquals(cow.moo(), 'moo!');
    test.assert(cow.mowed);
    test.done();
});
```

> ##### 提示

`planned`参数是特别有用的，以防给定的测试脚本突然中断，让你没有明显的方式知道它和一个错误的成功状态。

一个更异步的例子：

```
casper.test.begin('Casperjs.org is navigable', 2, function suite(test) {
    casper.start('http://casperjs.org/', function() {
        test.assertTitleMatches(/casperjs/i);
        this.clickLabel('Testing');
    });

    casper.then(function() {
        test.assertUrlMatches(/testing\.html$/);
    });

    casper.run(function() {
        test.done();
    });
});
```

> ##### 重要提示

done（）必须被调用才能终止套件。在进行异步测试时，这是特别重要的，所以确保在所有事情都被执行时调用它。

`Tester#begin()`也接受一个测试配置对象，所以你可以添加`setUp()`和`tearDown()`方法：

```
// cow-test.js
casper.test.begin('Cow can moo', 2, {
    setUp: function(test) {
        this.cow = new Cow();
    },

    tearDown: function(test) {
        this.cow.destroy();
    },

    test: function(test) {
        test.assertEquals(this.cow.moo(), 'moo!');
        test.assert(this.cow.mowed);
        test.done();
    }
});
```

#### colorize()

**调用方法**：colorize(String message, String style)

呈现彩色输出。基本上是`Casper.Colorizer#colorize()`的代理方法。

#### comment()

**调用方法**：comment(String message)

将注释式格式的消息写入stdout：

```
casper.test.comment("Hi, I'm a comment");
```

#### done()

**调用方法**：done()

*版本1.1改动：planned参数已被弃用*

标记一个以begin()开头的测试套件作为处理过程：

```
casper.test.begin('my test suite', 2, function(test) {
    test.assert(true);
    test.assertNot(false);
    test.done();
});
```

异步写法：

```
casper.test.begin('Casperjs.org is navigable', 2, function suite(test) {
    casper.start('http://casperjs.org/', function() {
        test.assertTitleMatches(/casperjs/i);
        this.clickLabel('Testing');
    });

    casper.then(function() {
        test.assertUrlMatches(/testing\.html$/);
    });

    casper.run(function() {
        test.done();
    });
});
```

#### error()

**调用方法**：error(String message)

向stdout写入错误式格式的消息：

```
casper.test.error("Hi, I'm an error");
```

#### fail()

**调用方法**：fail(String message [, Object option])

将失败的测试条目添加到堆栈:

```
casper.test.fail("Georges W. Bush");
casper.test.fail("Here goes a really long and expressive message", {name:'shortfacts'});
```

#### formatMessage()

**调用方法**：formatMessage(String message, String style)

格式化消息以突出显示其中的某些部分。只在tester内部使用。

#### getFailures()

**调用方法**:getFailures()

*版本1.0新增*

*版本1.1后弃用*

检索当前测试套件的失败：

```
casper.test.assertEquals(true, false);
require('utils').dump(casper.test.getFailures());
casper.test.done();
```

会返回给你这样的信息：

```
$ casperjs test test-getFailures.js
Test file: test-getFailures.js
FAIL Subject equals the expected value
#    type: assertEquals
#    subject: true
#    expected: false
{
    "length": 1,
    "cases": [
        {
            "success": false,
            "type": "assertEquals",
            "standard": "Subject equals the expected value",
            "file": "test-getFailures.js",
            "values": {
                "subject": true,
                "expected": false
            }
        }
    ]
}
FAIL 1 tests executed, 0 passed, 1 failed.

Details for the 1 failed test:

In c.js:0
   assertEquals: Subject equals the expected value
```

> ##### 提示

在版本1.1中，你可以通过监听测试失败事件记录测试失败：

```
var failures = [];

casper.test.on("fail", function(failure) {
  failures.push(failure);
});
```

#### getPasses()

**调用方法**：getPasses()

*版本1.0新增*

*版本1.1后弃用*

检索当前测试套件中成功测试用例的报告：

```
casper.test.assertEquals(true, true);
require('utils').dump(casper.test.getPasses());
casper.test.done();
```

会返回给你这样的信息：

```
$ casperjs test test-getPasses.js
Test file: test-getPasses.js
PASS Subject equals the expected value
{
    "length": 1,
    "cases": [
        {
            "success": true,
            "type": "assertEquals",
            "standard": "Subject equals the expected value",
            "file": "test-getPasses.js",
            "values": {
                "subject": true,
                "expected": true
            }
        }
    ]
}
PASS 1 tests executed, 1 passed, 0 failed.
```

> ##### 提示

在版本1.1中，您可以通过听取测试成功事件来记录测试成功：

```
var successes = [];

casper.test.on("success", function(success) {
  successes.push(success);
});
```

#### info()

**调用方法**：info(String message)

将信息样式格式的消息写入标准输出：

```
casper.test.info("Hi, I'm an informative message.");
```

#### pass()

**调用方法**：pass(String message)

将成功的测试条目添加到堆栈中：

```
casper.test.pass("Barrack Obama");
```

#### renderResults()

**调用方法**：renderResults(Boolean exit, Number status, String save)

渲染测试结果，将结果保存在一个XUnit格式的文件中，并可以选择退出phantomjs：

```
casper.test.renderResults(true, 0, 'test-results.xml');
```

> ##### 提示

在使用casperjs测试命令（请参阅测试文档）时，不会调用此方法，它会自动为您执行。

#### setUp()

**调用方法**：setUp([Function fn])

定义一个在使用begin()定义的每个测试之前执行的函数：

```
casper.test.setUp(function() {
    casper.start().userAgent('Mosaic 0.1');
});
```

要执行异步操作，请使用done参数：

```
casper.test.setUp(function(done) {
    casper.start('http://foo').then(function() {
        // ...
    }).run(done);
});
```

> ##### 警告

如果您不打算异步使用该方法，则不要指定done参数。

#### skip()

**调用方法**：skip(Number nb, String message)

跳过给定数量的计划测试：

```
casper.test.begin('Skip tests', 4, function(test) {
    test.assert(true, 'First test executed');
    test.assert(true, 'Second test executed');
    test.skip(2, 'Two tests skipped');
    test.done();
});
```

#### tearDown()

**调用方法**：tearDown([Function fn])

定义一个在使用begin()定义的每个测试之后执行的函数：

```
casper.test.tearDown(function() {
    casper.echo('See ya');
});
```

要执行异步操作，请使用done参数：

```
casper.test.tearDown(function(done) {
    casper.start('http://foo/goodbye').then(function() {
        // ...
    }).run(done);
});
```

如果您不打算异步使用该方法，则不要指定done参数。