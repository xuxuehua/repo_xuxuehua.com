---
title: "basic_knowledge"
date: 2018-11-02 13:16
---


[TOC]


# basic_knowledge



## 简介

Bootstrap 是一个用于快速开发 Web 应用程序和网站的前端框架。Bootstrap 是基于 HTML、CSS、JAVASCRIPT 的。



## Bootstrap 包的内容

- **基本结构**：Bootstrap 提供了一个带有网格系统、链接样式、背景的基本结构。
- **CSS**：Bootstrap 自带以下特性：全局的 CSS 设置、定义基本的 HTML 元素样式、可扩展的 class，以及一个先进的网格系统。
- **组件**：Bootstrap 包含了十几个可重用的组件，用于创建图像、下拉菜单、导航、警告框、弹出框等等。
- **JavaScript 插件**：Bootstrap 包含了十几个自定义的 jQuery 插件。您可以直接包含所有的插件，也可以逐个包含这些插件。
- **定制**：您可以定制 Bootstrap 的组件、LESS 变量和 jQuery 插件来得到您自己的版本。



## 安装

可以从 [http://getbootstrap.com/](https://getbootstrap.com/) 上下载 Bootstrap 的最新版本。

- *Download Bootstrap*：可以下载 Bootstrap CSS、JavaScript 和字体的预编译的压缩版本。不包含文档和最初的源代码文件。
- *Download Source*：可以直接从 from 上得到最新的 Bootstrap LESS 和 JavaScript 源代码。



### 预编译的 Bootstrap

当您下载了 Bootstrap 的已编译的版本，解压缩 ZIP 文件，您将看到下面的文件/目录结构：

![img](https://7n.w3cschool.cn/attachments/uploads/2014/06/compiledfilestructure.jpg)

如上图所示，可以看到已编译的 CSS 和 JS（bootstrap.*），以及已编译压缩的 CSS 和 JS（bootstrap.min.*）。同时也包含了 Glyphicons 的字体，这是一个可选的 Bootstrap 主题。



## Bootstrap CDN 

国内推荐使用 BootCDN 上的库：

```
<!-- 新 Bootstrap 核心 CSS 文件 -->
<link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
 
<!-- 可选的Bootstrap主题文件（一般不使用） -->
<script src="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap-theme.min.css"></script>
 
<!-- jQuery文件。务必在bootstrap.min.js 之前引入 -->
<script src="https://cdn.bootcss.com/jquery/2.1.1/jquery.min.js"></script>
 
<!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
<script src="https://cdn.bootcss.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
```

此外，你还可以使用以下的 CDN 服务：

- 国内推荐使用 : <https://www.staticfile.org/>
- 国际推荐使用：<https://cdnjs.com/>





## HTML 模板

一个使用了 Bootstrap 的基本的 HTML 模板如下所示：

```
<!DOCTYPE html>
<html>
   <head>
      <title>Bootstrap 模板</title>
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <!-- 引入 Bootstrap -->
      <link href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css" rel="stylesheet">

      <!-- HTML5 Shim 和 Respond.js 用于让 IE8 支持 HTML5元素和媒体查询 -->
      <!-- 注意： 如果通过 file://  引入 Respond.js 文件，则该文件无法起效果 -->
      <!--[if lt IE 9]>          <script src="https://oss.maxcdn.com/libs/html5shiv/3.7.0/html5shiv.js"></script>          <script src="https://oss.maxcdn.com/libs/respond.js/1.3.0/respond.min.js"></script>       <![endif]-->
   </head>
   <body>
      <h1>Hello, world!</h1>

      <!-- jQuery (Bootstrap 的 JavaScript 插件需要引入 jQuery) -->
      <script src="https://code.jquery.com/jquery.js"></script>
      <!-- 包括所有已编译的插件 -->
      <script src="js/bootstrap.min.js"></script>
   </body>
</html>
```



## HTML 5 文档类型（Doctype）

Bootstrap 使用了一些 HTML5 元素和 CSS 属性。为了让这些正常工作，您需要使用 HTML5 文档类型（Doctype）。 因此，请在使用 Bootstrap 项目的开头包含下面的代码段。

```
<!DOCTYPE html>
<html>
....
</html>
```

>  如果在 Bootstrap 创建的网页开头不使用 HTML5 的文档类型（Doctype），您可能会面临一些浏览器显示不一致的问题，甚至可能面临一些特定情境下的不一致，以致于您的代码不能通过 W3C 标准的验证。



## 移动设备优先

Bootstrap 3 默认的 CSS 本身就对移动设备友好支持

为了让 Bootstrap 开发的网站对移动设备友好，确保适当的绘制和触屏缩放，需要在网页的 head 之中添加 **viewport meta** 标签，如下所示：

```
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

*width* 属性控制设备的宽度。假设您的网站将被带有不同屏幕分辨率的设备浏览，那么将它设置为 *device-width*可以确保它能正确呈现在不同设备上。

*initial-scale=1.0* 确保网页加载时，以 1:1 的比例呈现，不会有任何的缩放。

在移动设备浏览器上，通过为 **viewport meta** 标签添加 *user-scalable=no* 可以禁用其缩放（zooming）功能。

通常情况下，*maximum-scale=1.0* 与 user-scalable=no 一起使用。这样禁用缩放功能后，用户只能滚动屏幕，就能让您的网站看上去更像原生应用的感觉。



## 响应式图像

```
<img src="..." class="img-responsive" alt="响应式图像">
```

通过添加 *img-responsive* class 可以让 Bootstrap 3 中的图像对响应式布局的支持更友好。

接下来让我们看下这个 class 包含了哪些 css 属性。

在下面的代码中，可以看到*img-responsive* class 为图像赋予了 max-width: 100%; 和 height: auto; 属性，可以让图像按比例缩放，不超过其父元素的尺寸。

```
.img-responsive {
  display: inline-block;
  height: auto;
  max-width: 100%;
}
```

这表明相关的图像呈现为 *inline-block*。当您把元素的 display 属性设置为 inline-block，元素相对于它周围的内容以内联形式呈现，但与内联不同的是，这种情况下我们可以设置宽度和高度。

设置 *height:auto*，相关元素的高度取决于浏览器。

设置 *max-width* 为 100% 会重写任何通过 width 属性指定的宽度。这让图片对响应式布局的支持更友好。



## 全局显示、排版和链接

### 基本的全局显示

Bootstrap 3 使用 *body {margin: 0;}* 来移除 body 的边距。

请看下面有关 body 的设置：

```
body {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 14px;
  line-height: 1.428571429;
  color: #333333;
  background-color: #ffffff;
}
```

第一条规则设置 body 的默认字体样式为 *"Helvetica Neue", Helvetica, Arial, sans-serif*。

第二条规则设置文本的默认字体大小为 14 像素。

第三条规则设置默认的行高度为 1.428571429。

第四条规则设置默认的文本颜色为 #333333。

最后一条规则设置默认的背景颜色为白色。

### 排版

使用 @font-family-base、 @font-size-base 和 @line-height-base 属性作为排版样式。

### 链接样式

通过属性 @link-color 设置全局链接的颜色。

对于链接的默认样式，如下设置：

```
a:hover,
a:focus {
  color: #2a6496;
  text-decoration: underline;
}

a:focus {
  outline: thin dotted #333;
  outline: 5px auto -webkit-focus-ring-color;
  outline-offset: -2px;
}
```

所以，当鼠标悬停在链接上，或者点击过的链接，颜色会被设置为 #2a6496。同时，会呈现一条下划线。

除此之外，点击过的链接，会呈现一个颜色码为 #333 的细的虚线轮廓。另一条规则是设置轮廓为 5 像素宽，且对于基于 webkit 浏览器有一个 *-webkit-focus-ring-color* 的浏览器扩展。轮廓偏移设置为 -2 像素。

以上所有这些样式都可以在 scaffolding.less 中找到。



## 容器 Container

```
<div class="container">
  ...
</div>
```

Bootstrap 3 的 container class 用于包裹页面上的内容。让我们一起来看看 bootstrap.css 文件中的这个 .container class。

```
.container {
   padding-right: 15px;
   padding-left: 15px;
   margin-right: auto;
   margin-left: auto;
}
```

通过上面的代码，把 container 的左右外边距（margin-right、margin-left）交由浏览器决定。

请注意，由于内边距（padding）是固定宽度，默认情况下容器是不可嵌套的。

```
.container:before,
.container:after {
  display: table;
  content: " ";
}
```

这会产生伪元素。设置 *display* 为 *table*，会创建一个匿名的 table-cell 和一个新的块格式化上下文。*:before*伪元素防止上边距崩塌，*:after* 伪元素清除浮动。

如果 *conteneditable* 属性出现在 HTML 中，由于一些 Opera bug，围绕上述元素创建一个空格。这可以通过使用 content: " " 来修复。

```
.container:after {
  clear: both;
}
```

它创建了一个伪元素，并确保了所有的容器包含所有的浮动元素。

Bootstrap 3 CSS 有一个申请响应的媒体查询，在不同的媒体查询阈值范围内都为 container 设置了max-width，用以匹配网格系统。

```
@media (min-width: 768px) {
   .container {
      width: 750px;
}
```