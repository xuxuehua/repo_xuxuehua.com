---
title: "basic_knowledge"
date: 2018-07-29 16:36
---

[TOC]





# Web developing

## 基本概念

MVC：为了解决直接用脚本语言嵌入HTML导致的可维护性差的问题，Web应用也引入了Model-View-Controller的模式，来简化Web开发。ASP发展为ASP.Net，JSP和PHP也有一大堆MVC框架。

目前，Web开发技术仍在快速发展中，异步开发、新的MVVM前端技术层出不穷。

由于Node.js把JavaScript引入了服务器端，因此，原来必须使用PHP/Java/C#/Python/Ruby等其他语言来开发服务器端程序，现在可以使用Node.js开发了！



可以用来部署构建工具webpack的环境依赖





## Node.js开发优势

一是后端语言也是JavaScript，以前掌握了前端JavaScript的开发人员，现在可以同时编写后端代码；

二是前后端统一使用JavaScript，就没有切换语言的障碍了；

三是速度快，非常快！这得益于Node.js天生是异步的







# nodejs



## 特点

借助JavaScript天生的事件驱动机制加V8高性能引擎，使编写高性能Web服务轻而易举。



# Installation 安装

偶数为稳定版本

https://nodejs.org/en/



## NVM

### Linux

Node Version Manager

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
```





Running either of the above commands downloads a script and runs it. The script clones the nvm repository to `~/.nvm`, and attempts to add the source lines from the snippet below to the correct profile file (`~/.bash_profile`, `~/.zshrc`, `~/.profile`, or `~/.bashrc`).

```
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```



```
nvm install 12.18.3
nvm use 12.18.3
```





## npm 包管理工具

Node Package Management

**but** doesn't make life easy *executing* any

```
curl -L https://www.npmjs.com/install.sh | sh
```



`NPM` by itself does not simply run any package. it doesn't run any package in a matter of fact. If you want to run a package using NPM, you must specify that package in your `package.json` file.

When executables are installed via NPM packages, NPM links to them:

1. *local* installs have "links" created at `./node_modules/.bin/` directory.
2. *global* installs have "links" created from the global `bin/` directory (e.g. `/usr/local/bin`) on Linux or at `%AppData%/npm` on Windows.





## npx

A tool for *executing* Node packages

`npx` will check whether `<command>` exists in `$PATH`, or in the local project binaries, and execute it.

if you wish to execute the locally-installed package `some-package` all you need to do is type:

```
npx some-package
```

Another **major** advantage of `npx` is the ability to execute a package which wasn't previously installed:

```
$ npx create-react-app my-app
```

The above example will generate a `react` app boilerplate *within* the path the command had run in, and ensures that you always use the latest version of a generator or build tool without having to upgrade each time you’re about to use it.





## npm VS cnpm

npm其实是Node.js的包管理工具（package manager）



因为npm安装插件是从国外服务器下载，受网络影响大，可能出现异常，如果npm的服务器在中国就好了，所以我们乐于分享的淘宝团队干了cnpm这事。



### proxy 配置

```
npm config set proxy http://127.0.01:8123
npm config set https-proxy http://127.0.0.1:8123
```





### cnpm 使用

```
$ npm install -g cnpm --registry=https://registry.npm.taobao.org
```



# hello world

```
'use strict';
console.log('Hello, World!');
```



# 全局对象

JavaScript有且仅有一个全局对象，在浏览器中，叫`window`对象。而在Node.js环境中，也有唯一的全局对象，叫`global`，

进入Node.js交互环境，可以直接输入：

```
> global.console
Console {
  log: [Function: bound ],
  info: [Function: bound ],
  warn: [Function: bound ],
  error: [Function: bound ],
  dir: [Function: bound ],
  time: [Function: bound ],
  timeEnd: [Function: bound ],
  trace: [Function: bound trace],
  assert: [Function: bound ],
  Console: [Function: Console] }
```



## process 对象

`process`也是Node.js提供的一个对象，它代表当前Node.js进程。通过`process`对象可以拿到许多有用信息：

```
> process === global.process;
true
> process.version;
'v5.2.0'
> process.platform;
'darwin'
> process.arch;
'x64'
> process.cwd(); //返回当前工作目录
'/Users/michael'
> process.chdir('/private/tmp'); // 切换当前工作目录
undefined
> process.cwd();
'/private/tmp'
```

 

## process 响应

在下一次事件响应中执行代码，可以调用`process.nextTick()`：

```
// test.js

// process.nextTick()将在下一轮事件循环中调用:
process.nextTick(function () {
    console.log('nextTick callback!');
});
console.log('nextTick was set!');
```

用Node执行上面的代码`node test.js`，你会看到，打印输出是：

```
nextTick was set!
nextTick callback!
```

> 这说明传入`process.nextTick()`的函数不是立刻执行，而是要等到下一次事件循环。



Node.js进程本身的事件就由`process`对象来处理。如果我们响应`exit`事件，就可以在程序即将退出时执行某个回调函数：

```
// 程序即将退出时的回调函数:
process.on('exit', function (code) {
    console.log('about to exit with code: ' + code);
});
```



# 全局strict 模式

省去每个js文件都加上'use strict'; 

```
node --use_strict example.js
```





# 判断JavaScript执行环境

有很多JavaScript代码既能在浏览器中执行，也能在Node环境执行，但有些时候，程序本身需要判断自己到底是在什么环境下执行的，常用的方式就是根据浏览器和Node环境提供的全局变量名称来判断：

```
if (typeof(window) === 'undefined') {
    console.log('node.js');
} else {
    console.log('browser');
}
```