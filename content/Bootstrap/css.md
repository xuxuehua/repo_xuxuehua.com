---
title: "css 系统"
date: 2018-11-02 13:28
---


[TOC]


# css



# 网格系统

Bootstrap 提供了一套响应式、移动设备优先的流式网格系统，随着屏幕或视口（viewport）尺寸的增加，系统会自动分为最多12列。它包含了用于简单的布局选项的预定义类，也包含了用于生成更多语义布局的功能强大的混合类。



移动设备优先策略

- **内容**

- - 决定什么是最重要的。

- **布局**

- - 优先设计更小的宽度。
  - 基础的 CSS 是移动设备优先，媒体查询是针对于平板电脑、台式电脑。

- **渐进增强**

- - 随着屏幕大小的增加而添加元素。

响应式网格系统随着屏幕或视口（viewport）尺寸的增加，系统会自动分为最多12列。

![img](https://cdn.pbrd.co/images/HLg4Uan.png)



网格系统通过一系列包含内容的行和列来创建页面布局。下面列出了 Bootstrap 网格系统是如何工作的：

- 行必须放置在 **.container** class 内，以便获得适当的对齐（alignment）和内边距（[padding](https://www.w3cschool.cn/css/css-padding.html)）。
- 使用行来创建列的水平组。
- 内容应该放置在列内，且唯有列可以是行的直接子元素。
- 预定义的网格类，比如 **.row** 和 **.col-xs-4**，可用于快速创建网格布局。LESS 混合类可用于更多语义布局。
- 列通过内边距（padding）来创建列内容之间的间隙。该内边距是通过 **.rows** 上的外边距（margin）取负，表示第一列和最后一列的行偏移。
- 网格系统是通过指定您想要横跨的十二个可用的列来创建的。例如，要创建三个相等的列，则使用三个 **.col-xs-4**。



## 基本的网格结构

Bootstrap 网格系统如何跨多个设备工作

|              | 超小设备手机（<768px）          | 小型设备平板电脑（≥768px）      | 中型设备台式电脑（≥992px）      | 大型设备台式电脑（≥1200px）     |
| ------------ | ------------------------------- | ------------------------------- | ------------------------------- | ------------------------------- |
| 网格行为     | 一直是水平的                    | 以折叠开始，断点以上是水平的    | 以折叠开始，断点以上是水平的    | 以折叠开始，断点以上是水平的    |
| 最大容器宽度 | None (auto)                     | 750px                           | 970px                           | 1170px                          |
| Class 前缀   | **.col-xs-**                    | **.col-sm-**                    | **.col-md-**                    | **.col-lg-**                    |
| 列数量和     | 12                              | 12                              | 12                              | 12                              |
| 最大列宽     | Auto                            | 60px                            | 78px                            | 95px                            |
| 间隙宽度     | 30px  （一个列的每边分别 15px） | 30px  （一个列的每边分别 15px） | 30px  （一个列的每边分别 15px） | 30px  （一个列的每边分别 15px） |
| 可嵌套       | Yes                             | Yes                             | Yes                             | Yes                             |
| 偏移量       | Yes                             | Yes                             | Yes                             | Yes                             |
| 列排序       | Yes                             | Yes                             | Yes                             | Yes                             |



下面是 Bootstrap 网格的基本结构：

```
<div class="container">   
  <div class="row">      
    <div class="col-*-*"></div>      
    <div class="col-*-*"></div>         
  </div>   
  <div class="row">...</div>
  </div>
  <div class="container">....
```



## 媒体查询

媒体查询是非常别致的"有条件的 CSS 规则"。它只适用于一些基于某些规定条件的 CSS。如果满足那些条件，则应用相应的样式。

Bootstrap 中的媒体查询允许您基于视口大小移动、显示并隐藏内容。下面的媒体查询在 LESS 文件中使用，用来创建 Bootstrap 网格系统中的关键的分界点阈值。

```
/* 超小设备（手机，小于 768px） */
/* Bootstrap 中默认情况下没有媒体查询 */

/* 小型设备（平板电脑，768px 起） */
@media (min-width: @screen-sm-min) { ... }

/* 中型设备（台式电脑，992px 起） */
@media (min-width: @screen-md-min) { ... }

/* 大型设备（大台式电脑，1200px 起） */
@media (min-width: @screen-lg-min) { ... }
```

我们有时候也会在媒体查询代码中包含 **max-width**，从而将 CSS 的影响限制在更小范围的屏幕大小之内。

```
@media (max-width: @screen-xs-max) { ... }
@media (min-width: @screen-sm-min) and (max-width: @screen-sm-max) { ... }
@media (min-width: @screen-md-min) and (max-width: @screen-md-max) { ... }
@media (min-width: @screen-lg-min) { ... }
```

媒体查询有两个部分，先是一个设备规范，然后是一个大小规则。在上面的案例中，设置了下列的规则：

让我们来看下面这行代码：

```
@media (min-width: @screen-sm-min) and (max-width: @screen-sm-max) { ... }
```

对于所有带有 *min-width: @screen-sm-min* 的设备，如果屏幕的宽度小于 *@screen-sm-max*，则会进行一些处理。



## 偏移列

偏移是一个用于更专业的布局的有用功能。它们可用来给列腾出更多的空间。例如，**.col-xs=\*** 类不支持偏移，但是它们可以简单地通过使用一个空的单元格来实现该效果。

为了在大屏幕显示器上使用偏移，请使用 **.col-md-offset-\*** 类。这些类会把一个列的左外边距（margin）增加 ***** 列，其中 ***** 范围是从 **1** 到 **11**。

在下面的实例中，我们有 <div class="col-md-6">..</div>，我们将使用 **.col-md-offset-3** class 来居中这个 div。

```
<div class="container">   
  <h1>Hello, world!</h1>   
  <div class="row" >      
    <div class="col-xs-6 col-md-offset-3" style="background-color: #dedef8;box-shadow: inset 1px -1px 1px #444, inset -1px 1px 1px #444;">         
      <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit.</p>      
    </div>   
  </div> 
</div> 
```

![网格系统偏移列](https://7n.w3cschool.cn/attachments/image/20160225/1456378462543989.jpg)





## 嵌套列

为了在内容中嵌套默认的网格，请添加一个新的 **.row**，并在一个已有的 **.col-md-\*** 列内添加一组 **.col-md-\***列。被嵌套的行应包含一组列，这组列个数不能超过12（其实，没有要求你必须占满12列）。

在下面的实例中，布局有两个列，第二列被分为两行四个盒子。

```


<div class="container">   
  <h1>Hello, world!</h1>   
  <div class="row">      
    <div class="col-md-3" style="background-color: #dedef8;box-shadow:inset 1px -1px 1px #444, inset -1px 1px 1px #444;">         
      <h4>第一列</h4>         
      <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit.</p>     
    </div>      
    <div class="col-md-9" style="background-color: #dedef8;box-shadow:inset 1px -1px 1px #444, inset -1px 1px 1px #444;">         
      <h4>第二列 - 分为四个盒子</h4>         
      <div class="row">            
        <div class="col-md-6" style="background-color: #B18904;box-shadow: inset 1px -1px 1px #444, inset -1px 1px 1px #444;">               
        <p>Consectetur art party Tonx culpa semiotics. Pinterest assumenda minim organic quis.</p>            
      </div>            
      <div class="col-md-6" style="background-color: #B18904;box-shadow: inset 1px -1px 1px #444, inset -1px 1px 1px #444;">               
        <p> sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.</p>            
      </div>         
    </div>         
    <div class="row">            
      <div class="col-md-6" style="background-color: #B18904;box-shadow: inset 1px -1px 1px #444, inset -1px 1px 1px #444;">               
        <p>quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.</p>            
      </div>               
      <div class="col-md-6" style="background-color: #B18904;box-shadow: inset 1px -1px 1px #444, inset -1px 1px 1px #444;">               
   <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit,sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim.</p>            
      </div>         
    </div>      
  </div>   
 </div> 
 </div> 


```

![网格系统嵌套列](https://7n.w3cschool.cn/attachments/image/20160225/1456378462738496.jpg)



## 列排序

Bootstrap 网格系统另一个完美的特性，就是您可以很容易地以一种顺序编写列，然后以另一种顺序显示列。

您可以很轻易地改变带有 **.col-md-push-\*** 和 **.col-md-pull-\*** 类的内置网格列的顺序，其中 ***** 范围是从 **1**到 **11**。

在下面的实例中，我们有两列布局，左列很窄，作为侧边栏。我们将使用 **.col-md-push-\*** 和 **.col-md-pull-\*** 类来互换这两列的顺序。 



```


<div class="container">   
   <h1>Hello, world!</h1>   
   <div class="row">      
     <p>排序前</p>      
     <div class="col-md-4" style="background-color: #dedef8;box-shadow: inset 1px -1px 1px #444, inset -1px 1px 1px #444;">我在左边</div>     
     <div class="col-md-8" style="background-color: #dedef8;box-shadow: inset 1px -1px 1px #444, inset -1px 1px 1px #444;">我在右边</div>   
   </div> 
   <br>   
   <div class="row">      
     <p>排序后</p>      
     <div class="col-md-4 col-md-push-8" style="background-color: #dedef8;box-shadow: inset 1px -1px 1px #444,inset -1px 1px 1px #444;">我在左边</div>      
     <div class="col-md-8 col-md-pull-4" style="background-color: #dedef8;box-shadow: inset 1px -1px 1px #444,inset -1px 1px 1px #444;">我在右边</div> 
   </div> 
</div>


```

![网格系统排序列](https://7n.w3cschool.cn/attachments/image/20160225/1456378463661239.jpg)







# 排版

Bootstrap 使用 Helvetica Neue、 Helvetica、 Arial 和 sans-serif 作为其默认的字体栈。

## 内联子标题

如果需要向任何标题添加一个内联子标题，只需要简单地在元素两旁添加 <small>，或者添加 **.small** class，这样子您就能得到一个字号更小的颜色更浅的文本，如下面实例所示：

```
<!DOCTYPE html>
<html>
<head>
   <title>Bootstrap 实例 - 内联子标题</title>
   <link href="//cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
   <script src="//cdn.bootcss.com/jquery/2.1.1/jquery.min.js"></script>
   <script src="//cdn.bootcss.com/bootstrap/3.3.6/js/bootstrap.min.js"></script>
</head>
<body>
   <h1>我是标题1 h1. <small>我是副标题1 h1</small></h1> 
   <h2>我是标题2 h2. <small>我是副标题2 h2</small></h2>
   <h3>我是标题3 h3. <small>我是副标题3 h3</small></h3>
   <h4>我是标题4 h4. <small>我是副标题4 h4</small></h4>
   <h5>我是标题5 h5. <small>我是副标题5 h5</small></h5>
   <h6>我是标题6 h6. <small>我是副标题6 h6</small></h6>
</body>
</html>
```



![内联子标题](https://7n.w3cschool.cn/attachments/image/20160225/1456378558346688.jpg)







## 引导主体副本

为了给段落添加强调文本，则可以添加 class="lead"，这将得到更大更粗、行高更高的文本，如下面实例所示：

```
<!DOCTYPE html>
<html>
<head>
   <title>Bootstrap 实例 - 引导主体副本</title>
   <link href="//cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
   <script src="//cdn.bootcss.com/jquery/2.1.1/jquery.min.js"></script>
   <script src="//cdn.bootcss.com/bootstrap/3.3.6/js/bootstrap.min.js"></script>
</head>
<body>
<hr>
<h2>引导主体副本</h2>
<p class="lead">这是一个演示引导主体副本用法的实例。这是一个演示引导主体副本用法的实例。这是一个演示引导主体副本用法的实例。这是一个演示引导主体副本用法的实例。这是一个演示引导主体副本用法的实例。这是一个演示引导主体副本用法的实例。这是一个演示引导主体副本用法的实例。这是一个演示引导主体副本用法的实例。
</p>
</body>
</html>
```

结果如下所示：

![引导主体副本](https://7n.w3cschool.cn/attachments/image/20160225/1456378558791827.jpg)

------

## 强调

HTML 的默认强调标签 <small>（设置文本为父文本大小的 85%）、<strong>（设置文本为更粗的文本）、<em>（设置文本为斜体）。

Bootstrap 提供了一些用于强调文本的类，如下面实例所示：

```
<!DOCTYPE html>
<html>
<head>
   <title>Bootstrap 实例 - 强调</title>
   <link href="//cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
   <script src="//cdn.bootcss.com/jquery/2.1.1/jquery.min.js"></script>
   <script src="//cdn.bootcss.com/bootstrap/3.3.6/js/bootstrap.min.js"></script>
</head>
<body>

<small>本行内容是在标签内</small><br>
<strong>本行内容是在标签内</strong><br>
<em>本行内容是在标签内，并呈现为斜体</em><br>
<p class="text-left">向左对齐文本</p>
<p class="text-center">居中对齐文本</p>
<p class="text-right">向右对齐文本</p>
<p class="text-muted">本行内容是减弱的</p>
<p class="text-primary">本行内容带有一个 primary class</p>
<p class="text-success">本行内容带有一个 success class</p>
<p class="text-info">本行内容带有一个 info class</p>
<p class="text-warning">本行内容带有一个 warning class</p>
<p class="text-danger">本行内容带有一个 danger class</p>

</body>
</html>
```

结果如下所示：

![强调](https://7n.w3cschool.cn/attachments/image/20160225/1456378558106814.jpg)

------

## 缩写 \<abbr>

HTML元素提供了用于缩写的标记，比如 WWW 或 HTTP。Bootstrap 定义 <abbr> 元素的样式为显示在文本底部的一条虚线边框，当鼠标悬停在上面时会显示完整的文本（只要您为 <abbr> title 属性添加了文本）。为了得到一个更小字体的文本，请添加 .initialism 到 <abbr>。

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 缩写</title>
   <link href="//cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
   <script src="//cdn.bootcss.com/jquery/2.1.1/jquery.min.js"></script>
   <script src="//cdn.bootcss.com/bootstrap/3.3.6/js/bootstrap.min.js"></script></head>
<body>
<abbr title="World Wide Web">WWW</abbr><br>
<abbr title="Real Simple Syndication" class="initialism">RSS</abbr>
</body>
</html>
```

结果如下所示：

![缩写](https://7n.w3cschool.cn/attachments/image/20160225/1456378558693628.jpg)

------

## 地址（Address）

使用 <address> 标签，您可以在网页上显示联系信息。由于 <address> 默认为 display:block;，您需要使用
标签来为封闭的地址文本添加换行。

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 地址</title>
   <link href="//cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
   <script src="//cdn.bootcss.com/jquery/2.1.1/jquery.min.js"></script>
   <script src="//cdn.bootcss.com/bootstrap/3.3.6/js/bootstrap.min.js"></script></head><body><address>
  <strong>Some Company, Inc.</strong><br>
  007 street<br>
  Some City, State XXXXX<br>
  <abbr title="Phone">P:</abbr> (123) 456-7890</address><address>
  <strong>Full Name</strong><br>
  <a href="mailto:#">mailto@somedomain.com</a></address>
</body>
</html>
```

结果如下所示：

![地址](https://7n.w3cschool.cn/attachments/image/20160225/1456378559723415.jpg)

------

## 引用（Blockquote）

您可以在任意的 HTML 文本旁使用默认的 <blockquote>。其他选项包括，添加一个 <small> 标签来标识引用的来源，使用 class *.pull-right* 向右对齐引用。下面的实例演示了这些特性：

结果如下所示：

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 引用</title>
   <link href="//cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
   <script src="//cdn.bootcss.com/jquery/2.1.1/jquery.min.js"></script>
   <script src="//cdn.bootcss.com/bootstrap/3.3.6/js/bootstrap.min.js"></script>
</head>
<body>
<blockquote><p>这是一个默认的引用实例。这是一个默认的引用实例。这是一个默认的引用实例。这是一个默认的引用实例。这是一个默认的引用实例。这是一个默认的引用实例。这是一个默认的引用实例。这是一个默认的引用实例。</p></blockquote>
<blockquote>这是一个带有源标题的引用。<small>Someone famous in <cite title="Source Title">Source Title</cite></small></blockquote>
<blockquote class="pull-right">这是一个向右对齐的引用。<small>Someone famous in <cite title="Source Title">Source Title</cite></small></blockquote>
</body>
</html>
```

![引用](https://7n.w3cschool.cn/attachments/image/20160225/1456378559613672.jpg)





## 列表

Bootstrap 支持有序列表、无序列表和定义列表。

- **有序列表**：有序列表是指以数字或其他有序字符开头的列表。
- **无序列表**：无序列表是指没有特定顺序的列表，是以传统风格的着重号开头的列表。如果您不想显示这些着重号，您可以使用 class *.list-unstyled* 来移除样式。您也可以通过使用 class *.list-inline* 把所有的列表项放在同一行中。
- **定义列表**：在这种类型的列表中，每个列表项可以包含 <dt> 和 <dd> 元素。<dt> 代表 *定义术语*，就像字典，这是被定义的属于（或短语）。接着，<dd> 是 <dt> 的描述。您可以使用 class *dl-horizontal* 把 <dl> 行中的属于与描述并排显示。

下面的实例演示了这些类型的列表：

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 列表</title>
   <link href="//cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
   <script src="//cdn.bootcss.com/jquery/2.1.1/jquery.min.js"></script>
   <script src="//cdn.bootcss.com/bootstrap/3.3.6/js/bootstrap.min.js"></script></head><body><h4>有序列表</h4><ol>
  <li>Item 1</li>
  <li>Item 2</li>
  <li>Item 3</li>
  <li>Item 4</li></ol><h4>无序列表</h4><ul>
  <li>Item 1</li>
  <li>Item 2</li>
  <li>Item 3</li>
  <li>Item 4</li></ul><h4>未定义样式列表</h4><ul class="list-unstyled">
  <li>Item 1</li>
  <li>Item 2</li>
  <li>Item 3</li>
  <li>Item 4</li></ul><h4>内联列表</h4><ul class="list-inline">
  <li>Item 1</li>
  <li>Item 2</li>
  <li>Item 3</li>
  <li>Item 4</li></ul><h4>定义列表</h4><dl>
  <dt>Description 1</dt>
  <dd>Item 1</dd>
  <dt>Description 2</dt>
  <dd>Item 2</dd></dl><h4>水平的定义列表</h4><dl class="dl-horizontal">
  <dt>Description 1</dt>
  <dd>Item 1</dd>
  <dt>Description 2</dt>
  <dd>Item 2</dd></dl></body></html>
```

结果如下所示：

![列表](https://7n.w3cschool.cn/attachments/image/20160225/1456378559910221.jpg)



## 更多排版类

下表提供了 Bootstrap 更多排版类的实例：

| 类                  | 描述                                                         | 实例                                                         |
| ------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| .lead               | 使段落突出显示                                               | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_txt_lead) |
| .small              | 设定小文本 (设置为父文本的 85% 大小)                         | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_txt_small) |
| .text-left          | 设定文本左对齐                                               | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_text-left) |
| .text-center        | 设定文本居中对齐                                             | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_text-left) |
| .text-right         | 设定文本右对齐                                               | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_text-left) |
| .text-justify       | 设定文本对齐,段落中超出屏幕部分文字自动换行                  | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_text-left) |
| .text-nowrap        | 段落中超出屏幕部分不换行                                     | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_text-left) |
| .text-lowercase     | 设定文本小写                                                 | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_text-lowercase) |
| .text-uppercase     | 设定文本大写                                                 | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_text-lowercase) |
| .text-capitalize    | 设定单词首字母大写                                           | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_text-lowercase) |
| .initialism         | 显示在 <abbr> 元素中的文本以小号字体展示                     | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_txt_abbr2) |
| .blockquote-reverse | 设定引用右对齐                                               | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_txt_blockquote2) |
| .list-unstyled      | 移除默认的列表样式，列表项中左对齐 ( <ul> 和 <ol> 中)。 这个类仅适用于直接子列表项    (如果需要移除嵌套的列表项，你需要在嵌套的列表中使用该样式) | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_txt_list-unstyled) |
| .list-inline        | 将所有列表项放置同一行                                       | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_txt_list-inline) |
| .dl-horizontal      | 该类设置了浮动和偏移，应用于 <dl> 元素和 <dt> 元素中，具体实现可以查看实例 | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_txt_dl-horizontal) |
| .pre-scrollable     | 使 <pre> 元素可滚动 scrollable                               | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_txt_pre2) |



# 显示代码

Bootstrap 允许您以两种方式显示代码：

- 第一种是 \<code> 如果您想要内联显示代码，那么您应该使用 <code> 标签。
- 第二种是 \<pre> 如果代码需要被显示为一个独立的块元素或者代码有多行，那么您应该使用 <pre> 标签。

请确保当您使用 <pre> 和 <code> 标签时，开始和结束标签使用了 unicode 变体： **<** 和 **>**。

让我们来看看下面的实例：
```
<!DOCTYPE html>
<html>
<head>
  <title>Bootstrap 实例 - 代码</title>
  <link href="//cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
  <script src="//cdn.bootcss.com/jquery/2.1.1/jquery.min.js"></script>
  <script src="//cdn.bootcss.com/bootstrap/3.3.6/js/bootstrap.min.js"></script>
</head>
<body>

<p><code>&lt;header&gt;</code> 作为内联元素被包围。</p>
<p>如果需要把代码显示为一个独立的块元素，请使用 <pre> 标签：</p>
<pre>
  &lt;article&gt;
    &lt;h1&gt;Article Heading&lt;/h1&gt;
  &lt;/article&gt;
</pre>
</body>
</html>
```

![代码](https://7n.w3cschool.cn/attachments/uploads/2014/06/code_demo.jpg)





# 表格

Bootstrap 提供了一个清晰的创建表格的布局。下表列出了 Bootstrap 支持的一些表格元素：

| 标签      | 描述                                                         |
| --------- | ------------------------------------------------------------ |
| <table>   | 为表格添加基础样式。                                         |
| <thead>   | 表格标题行的容器元素（<tr>），用来标识表格列。               |
| <tbody>   | 表格主体中的表格行的容器元素（<tr>）。                       |
| <tr>      | 一组出现在单行上的表格单元格的容器元素（<td> 或 <th>）。     |
| <td>      | 默认的表格单元格。                                           |
| <th>      | 特殊的表格单元格，用来标识列或行（取决于范围和位置）。必须在 <thead> 内使用。 |
| <caption> | 关于表格存储内容的描述或总结。                               |

### 表格类

下表样式可用于表格中：

| 类                   | 描述                                                         | 实例                                                         |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| .table               | 为任意 <table> 添加基本样式 (只有横向分隔线)                 | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_table&) |
| .table-striped       | 在 <tbody> 内添加斑马线形式的条纹 ( IE8 不支持)              | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_table-striped) |
| .table-bordered      | 为所有表格的单元格添加边框                                   | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_table-bordered) |
| .table-hover         | 在 <tbody> 内的任一行启用鼠标悬停状态                        | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_table-hover) |
| .table-condensed     | 让表格更加紧凑                                               | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_table-condensed) |
| *联合使用所有表格类* | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_table-all) |                                                              |

### `<tr>`, `<th>` ,`<td>` 

下表的类可用于表格的行或者单元格：

| 类       | 描述                             | 实例                                                         |
| -------- | -------------------------------- | ------------------------------------------------------------ |
| .active  | 将悬停的颜色应用在行或者单元格上 | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_tr_active) |
| .success | 表示成功的操作                   | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_tr_success) |
| .info    | 表示信息变化的操作               | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_tr_info) |
| .warning | 表示一个警告的操作               | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_tr_warning) |
| .danger  | 表示一个危险的操作               | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_tr_danger) |

## 基本的表格

如果您想要一个只带有内边距（padding）和水平分割的基本表，请添加 class *.table*，如下面实例所示：

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 基本的表格</title>
   <link href="//cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
   <script src="//cdn.bootcss.com/jquery/2.1.1/jquery.min.js"></script>
   <script src="//cdn.bootcss.com/bootstrap/3.3.6/js/bootstrap.min.js"></script></head><body><table class="table">
   <caption>基本的表格布局</caption>
   <thead>
      <tr>
         <th>名称</th>
         <th>城市</th>
      </tr>
   </thead>
   <tbody>
      <tr>
         <td>Tanmay</td>
         <td>Bangalore</td>
      </tr>
      <tr>
         <td>Sachin</td>
         <td>Mumbai</td>
      </tr>
   </tbody></table></body></html>
```

结果如下所示：

![基本的表格](https://7n.w3cschool.cn/attachments/image/20160225/1456378936501309.jpg)



## 可选的表格类

除了基本的表格标记和 .table class，还有一些可以用来为标记定义样式的类。下面将向您介绍这些类。

### 条纹表格

通过添加 *.table-striped* class，您将在 <tbody> 内的行上看到条纹，如下面的实例所示：

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 条纹表格</title>
   <link href="//cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
   <script src="//cdn.bootcss.com/jquery/2.1.1/jquery.min.js"></script>
   <script src="//cdn.bootcss.com/bootstrap/3.3.6/js/bootstrap.min.js"></script></head><body><table class="table table-striped">
   <caption>条纹表格布局</caption>
   <thead>
      <tr>
         <th>名称</th>
         <th>城市</th>
         <th>密码</th>
      </tr>
   </thead>
   <tbody>
      <tr>
         <td>Tanmay</td>
         <td>Bangalore</td>
         <td>560001</td>
      </tr>
      <tr>
         <td>Sachin</td>
         <td>Mumbai</td>
         <td>400003</td>
      </tr>
      <tr>
         <td>Uma</td>
         <td>Pune</td>
         <td>411027</td>
      </tr>
   </tbody></table></body></html>
```

结果如下所示：

![条纹表格](https://7n.w3cschool.cn/attachments/image/20160225/1456378936231991.jpg)

### 边框表格

通过添加 *.table-bordered* class，您将看到每个元素周围都有边框，且占整个表格是圆角的，如下面的实例所示：

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 边框表格</title>
   <link href="//cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
   <script src="//cdn.bootcss.com/jquery/2.1.1/jquery.min.js"></script>
   <script src="//cdn.bootcss.com/bootstrap/3.3.6/js/bootstrap.min.js"></script></head><body><table class="table table-bordered">
   <caption>边框表格布局</caption>
   <thead>
      <tr>
         <th>名称</th>
         <th>城市</th>
         <th>密码</th>
      </tr>
   </thead>
   <tbody>
      <tr>
         <td>Tanmay</td>
         <td>Bangalore</td>
         <td>560001</td>
      </tr>
      <tr>
         <td>Sachin</td>
         <td>Mumbai</td>
         <td>400003</td>
      </tr>
      <tr>
         <td>Uma</td>
         <td>Pune</td>
         <td>411027</td>
      </tr>
   </tbody></table></body></html>
```

结果如下所示：

![边框表格](https://7n.w3cschool.cn/attachments/image/20160225/1456378936401558.jpg)

### 悬停表格

通过添加 *.table-hover* class，当指针悬停在行上时会出现浅灰色背景，如下面的实例所示：

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 悬停表格</title>
   <link href="//cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
   <script src="//cdn.bootcss.com/jquery/2.1.1/jquery.min.js"></script>
   <script src="//cdn.bootcss.com/bootstrap/3.3.6/js/bootstrap.min.js"></script></head><body><table class="table table-hover">
   <caption>悬停表格布局</caption>
   <thead>
      <tr>
         <th>名称</th>
         <th>城市</th>
         <th>密码</th>
      </tr>
   </thead>
   <tbody>
      <tr>
         <td>Tanmay</td>
         <td>Bangalore</td>
         <td>560001</td>
      </tr>
      <tr>
         <td>Sachin</td>
         <td>Mumbai</td>
         <td>400003</td>
      </tr>
      <tr>
         <td>Uma</td>
         <td>Pune</td>
         <td>411027</td>
      </tr>
   </tbody></table></body></html>
```

结果如下所示：

![悬停表格](https://7n.w3cschool.cn/attachments/image/20160225/1456378936878471.jpg)

### 精简表格

通过添加 *.table-condensed* class，行内边距（padding）被切为两半，以便让表看起来更紧凑，如下面的实例所示。这在想让信息看起来更紧凑时非常有用。

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 精简表格</title>
   <link href="//cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
   <script src="//cdn.bootcss.com/jquery/2.1.1/jquery.min.js"></script>
   <script src="//cdn.bootcss.com/bootstrap/3.3.6/js/bootstrap.min.js"></script></head><body><table class="table table-condensed">
   <caption>精简表格布局</caption>
   <thead>
      <tr>
         <th>名称</th>
         <th>城市</th>
         <th>密码</th>
      </tr>
   </thead>
   <tbody>
      <tr>
         <td>Tanmay</td>
         <td>Bangalore</td>
         <td>560001</td>
      </tr>
      <tr>
         <td>Sachin</td>
         <td>Mumbai</td>
         <td>400003</td>
      </tr>
      <tr>
         <td>Uma</td>
         <td>Pune</td>
         <td>411027</td>
      </tr>
   </tbody></table></body></html>
```

结果如下所示：

![精简表格](https://7n.w3cschool.cn/attachments/image/20160225/1456378936836734.jpg)



## 上下文类

下表中所列出的上下文类允许您改变表格行或单个单元格的背景颜色。

| 类       | 描述                               |
| -------- | ---------------------------------- |
| .active  | 对某一特定的行或单元格应用悬停颜色 |
| .success | 表示一个成功的或积极的动作         |
| .warning | 表示一个需要注意的警告             |
| .danger  | 表示一个危险的或潜在的负面动作     |

这些类可被应用到 <tr>、<td> 或 <th>。

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 上下文类</title>
   <link href="//cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
   <script src="//cdn.bootcss.com/jquery/2.1.1/jquery.min.js"></script>
   <script src="//cdn.bootcss.com/bootstrap/3.3.6/js/bootstrap.min.js"></script></head><body><table class="table">
   <caption>上下文表格布局</caption>
   <thead>
      <tr>
         <th>产品</th>
         <th>付款日期</th>
         <th>状态</th>
      </tr>
   </thead>
   <tbody>
      <tr class="active">
         <td>产品1</td>
         <td>23/11/2013</td>
         <td>待发货</td>
      </tr>
      <tr class="success">
         <td>产品2</td>
         <td>10/11/2013</td>
         <td>发货中</td>
      </tr>
      <tr  class="warning">
         <td>产品3</td>
         <td>20/10/2013</td>
         <td>待确认</td>
      </tr>
      <tr  class="danger">
         <td>产品4</td>
         <td>20/10/2013</td>
         <td>已退货</td>
      </tr>
   </tbody></table></body></html>
```

结果如下所示：

![上下文类](https://7n.w3cschool.cn/attachments/image/20160225/1456378936775973.jpg)



## 响应式表格

通过把任意的 *.table* 包在 *.table-responsive* class 内，您可以让表格水平滚动以适应小型设备（小于 768px）。当在大于 768px 宽的大型设备上查看时，您将看不到任何的差别。

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 响应式表格</title>
   <link href="//cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
   <script src="//cdn.bootcss.com/jquery/2.1.1/jquery.min.js"></script>
   <script src="//cdn.bootcss.com/bootstrap/3.3.6/js/bootstrap.min.js"></script></head><body><div class="table-responsive">
   <table class="table">
      <caption>响应式表格布局</caption>
      <thead>
         <tr>
            <th>产品</th>
            <th>付款日期</th>
            <th>状态</th>
         </tr>
      </thead>
      <tbody>
         <tr>
            <td>产品1</td>
            <td>23/11/2013</td>
            <td>待发货</td>
         </tr>
         <tr>
            <td>产品2</td>
            <td>10/11/2013</td>
            <td>发货中</td>
         </tr>
         <tr>
            <td>产品3</td>
            <td>20/10/2013</td>
            <td>待确认</td>
         </tr>
         <tr>
            <td>产品4</td>
            <td>20/10/2013</td>
            <td>已退货</td>
         </tr>
      </tbody>
   </table></div>  	</body></html> 	
```

结果如下所示：

![响应式表格](https://7n.w3cschool.cn/attachments/image/20160225/1456378937695123.jpg)



# 表单

## 表单布局

Bootstrap 提供了下列类型的表单布局：

- 垂直表单（默认）
- 内联表单
- 水平表单

### 垂直或基本表单

基本的表单结构是 Bootstrap 自带的，个别的表单控件自动接收一些全局样式。下面列出了创建基本表单的步骤：

- 向父 [ ](https://www.w3cschool.cn/htmltags/tag-form.html)元素添加 *role="form"*。
- 把标签和控件放在一个带有 class *.form-group* 的 <div> 中。这是获取最佳间距所必需的。
- 向所有的文本元素 [](https://www.w3cschool.cn/html5/html5-input.html)、<textarea> 和 <select> 添加 class *.form-control*。

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 基本表单</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><form role="form">
   <div class="form-group">
      <label for="name">名称</label>
      <input type="text" class="form-control" id="name" 
         placeholder="请输入名称">
   </div>
   <div class="form-group">
      <label for="inputfile">文件输入</label>
      <input type="file" id="inputfile">
      <p class="help-block">这里是块级帮助文本的实例。</p>
   </div>
   <div class="checkbox">
      <label>
      <input type="checkbox"> 请打勾      </label>
   </div>
   <button type="submit" class="btn btn-default">提交</button></form></body></html>
```

结果如下所示：

![基本表单](https://7n.w3cschool.cn/attachments/image/20160225/1456379124595404.jpg)

### 内联表单

如果需要创建一个表单，它的所有元素是内联的，向左对齐的，标签是并排的，请向 <form> 标签添加 class *.form-inline*。

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 内联表单</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><form class="form-inline" role="form">
   <div class="form-group">
      <label class="sr-only" for="name">名称</label>
      <input type="text" class="form-control" id="name" 
         placeholder="请输入名称">
   </div>
   <div class="form-group">
      <label class="sr-only" for="inputfile">文件输入</label>
      <input type="file" id="inputfile">
   </div>
   <div class="checkbox">
      <label>
      <input type="checkbox"> 请打勾      </label>
   </div>
   <button type="submit" class="btn btn-default">提交</button></form></body></html>
```

结果如下所示：

![内联表单](https://7n.w3cschool.cn/attachments/image/20160225/1456379124212830.jpg)

- 默认情况下，Bootstrap 中的 input、select 和 textarea 有 100% 宽度。在使用内联表单时，您需要在表单控件上设置一个宽度。
- 使用 class *.sr-only*，您可以隐藏内联表单的标签。

### 水平表单

水平表单与其他表单不仅标记的数量上不同，而且表单的呈现形式也不同。如需创建一个水平布局的表单，请按下面的几个步骤进行：

- 向父 <form> 元素添加 class *.form-horizontal*。
- 把标签和控件放在一个带有 class *.form-group* 的 <div> 中。
- 向标签添加 class *.control-label*。

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 水平表单</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><form class="form-horizontal" role="form">
   <div class="form-group">
      <label for="firstname" class="col-sm-2 control-label">名字</label>
      <div class="col-sm-10">
         <input type="text" class="form-control" id="firstname" 
            placeholder="请输入名字">
      </div>
   </div>
   <div class="form-group">
      <label for="lastname" class="col-sm-2 control-label">姓</label>
      <div class="col-sm-10">
         <input type="text" class="form-control" id="lastname" 
            placeholder="请输入姓">
      </div>
   </div>
   <div class="form-group">
      <div class="col-sm-offset-2 col-sm-10">
         <div class="checkbox">
            <label>
               <input type="checkbox"> 请记住我            </label>
         </div>
      </div>
   </div>
   <div class="form-group">
      <div class="col-sm-offset-2 col-sm-10">
         <button type="submit" class="btn btn-default">登录</button>
      </div>
   </div></form></body></html>
```

结果如下所示：

![水平表单](https://7n.w3cschool.cn/attachments/image/20160225/1456379124234854.jpg)

**提示：**到此为止，相信你已经会灵活使用Bootstrap表单了，那么通过本站的编程实战部分来练习使用[Bootstrap水平排列的表单](https://www.w3cschool.cn/codecamp/line-up-form-elements-responsively-with-bootstrap.html)吧！

## 支持的表单控件

Bootstrap 支持最常见的表单控件，主要是 *input、textarea、checkbox、radio 和 select*。

### 输入框（Input）

最常见的表单文本字段是输入框 input。用户可以在其中输入大多数必要的表单数据。Bootstrap 提供了对所有原生的 HTML5 的 input 类型的支持，包括：*text、password、datetime、datetime-local、date、month、time、week、number、email、url、search、tel* 和 *color*。适当的 *type* 声明是必需的，这样才能让 *input* 获得完整的样式。

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 输入框</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><form role="form">
  <div class="form-group">
    <label for="name">标签</label>
    <input type="text" class="form-control" placeholder="文本输入">
  </div>
 </form></body></html>
```

结果如下所示：

![输入框](https://7n.w3cschool.cn/attachments/image/20160225/1456379124528342.jpg)

### 文本框（Textarea）

当您需要进行多行输入的时，则可以使用文本框 textarea。必要时可以改变 *rows* 属性（较少的行 = 较小的盒子，较多的行 = 较大的盒子）。

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 文本框</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><form role="form">
  <div class="form-group">
    <label for="name">文本框</label>
    <textarea class="form-control" rows="3"></textarea>
  </div></form></body></html>
```

结果如下所示：

![文本框](https://7n.w3cschool.cn/attachments/image/20160225/1456379124310388.jpg)

### 复选框（（Checkbox）和单选框（Radio）

复选框和单选按钮用于让用户从一系列预设置的选项中进行选择。

- 当创建表单时，如果您想让用户从列表中选择若干个选项时，请使用 *checkbox*。如果您限制用户只能选择一个选项，请使用 *radio*。
- 对一系列复选框和单选框使用 *.checkbox-inline* 或 *.radio-inline* class，控制它们显示在同一行上。

下面的实例演示了这两种类型（默认和内联）：

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 复选框和单选按钮</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><label for="name">默认的复选框和单选按钮的实例</label><div class="checkbox">
   <label><input type="checkbox" value="">选项 1</label></div><div class="checkbox">
   <label><input type="checkbox" value="">选项 2</label></div><div class="radio">
   <label>
      <input type="radio" name="optionsRadios" id="optionsRadios1" 
         value="option1" checked> 选项 1   </label></div><div class="radio">
   <label>
      <input type="radio" name="optionsRadios" id="optionsRadios2" 
         value="option2">
         选项 2 - 选择它将会取消选择选项 1   </label></div><label for="name">内联的复选框和单选按钮的实例</label><div>
   <label class="checkbox-inline">
      <input type="checkbox" id="inlineCheckbox1" value="option1"> 选项 1   
    </label>
   <label class="checkbox-inline">
      <input type="checkbox" id="inlineCheckbox2" value="option2"> 选项 2   
    </label>
   <label class="checkbox-inline">
      <input type="checkbox" id="inlineCheckbox3" value="option3"> 选项 3   
    </label>
   <label class="checkbox-inline">
      <input type="radio" name="optionsRadiosinline" id="optionsRadios3" 
         value="option1" checked> 选项 1   
    </label>
   <label class="checkbox-inline">
      <input type="radio" name="optionsRadiosinline" id="optionsRadios4" 
         value="option2"> 选项 2   
    </label>
</div></body></html>
```

结果如下所示：

![复选框和单选按钮](https://7n.w3cschool.cn/attachments/image/20160225/1456379124872322.jpg)

### 选择框（Select）

当您想让用户从多个选项中进行选择，但是默认情况下只能选择一个选项时，则使用选择框。

- 使用 [ ](https://www.w3cschool.cn/html5/html5-select.html)展示列表选项，通常是那些用户很熟悉的选择列表，比如州或者数字。
- 使用 *multiple="multiple"* 允许用户选择多个选项。

下面的实例演示了这两种类型（select 和 multiple）：

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 选择框</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><form role="form">
   <div class="form-group">
      <label for="name">选择列表</label>
      <select class="form-control">
         <option>1</option>
         <option>2</option>
         <option>3</option>
         <option>4</option>
         <option>5</option>
      </select>

      <label for="name">可多选的选择列表</label>
      <select multiple class="form-control">
         <option>1</option>
         <option>2</option>
         <option>3</option>
         <option>4</option>
         <option>5</option>
      </select>
   </div></form></body></html>
```

结果如下所示：

![选择框](https://7n.w3cschool.cn/attachments/image/20160225/1456379124657565.jpg)



## 静态控件

当您需要在一个水平表单内的表单标签后放置纯文本时，请在 <p> 上使用 class *.form-control-static*。

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 静态控件</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><form class="form-horizontal" role="form">
  <div class="form-group">
    <label class="col-sm-2 control-label">Email</label>
    <div class="col-sm-10">
      <p class="form-control-static">email@example.com</p>
    </div>
  </div>
  <div class="form-group">
    <label for="inputPassword" class="col-sm-2 control-label">密码</label>
    <div class="col-sm-10">
      <input type="password" class="form-control" id="inputPassword" 
         placeholder="请输入密码">
    </div>
  </div></form></body></html>
```

结果如下所示：

![静态控件](https://7n.w3cschool.cn/attachments/image/20160225/1456379124161279.jpg)



## 表单控件状态

除了 *:focus* 状态（即，用户点击 input 或使用 tab 键聚焦到 input 上），Bootstrap 还为禁用的输入框定义了样式，并提供了表单验证的 class。

### 输入框焦点

当输入框 input 接收到 *:focus* 时，输入框的轮廓会被移除，同时应用 *box-shadow*。

### 禁用的输入框 input

如果您想要禁用一个输入框 input，只需要简单地添加 *disabled* 属性，这不仅会禁用输入框，还会改变输入框的样式以及当鼠标的指针悬停在元素上时鼠标指针的样式。

### 禁用的字段集 fieldset

对 <fieldset> 添加 disabled 属性来禁用 <fieldset> 内的所有控件。

### 验证状态

Bootstrap 包含了错误、警告和成功消息的验证样式。只需要对父元素简单地添加适当的 class（*.has-warning、 .has-error 或 .has-success*）即可使用验证状态。

下面的实例演示了所有控件状态：

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 表单控件状态</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><form class="form-horizontal" role="form">
   <div class="form-group">
      <label class="col-sm-2 control-label">聚焦</label>
      <div class="col-sm-10">
         <input class="form-control" id="focusedInput" type="text" 
            value="该输入框获得焦点...">
      </div>
   </div>
   <div class="form-group">
      <label for="inputPassword" class="col-sm-2 control-label">
         禁用      </label>
      <div class="col-sm-10">
         <input class="form-control" id="disabledInput" type="text" 
            placeholder="该输入框禁止输入..." disabled>
      </div>
   </div>
   <fieldset disabled>
      <div class="form-group">
         <label for="disabledTextInput"  class="col-sm-2 control-label">
            禁用输入（Fieldset disabled）         </label>
         <div class="col-sm-10">
            <input type="text" id="disabledTextInput" class="form-control" 
               placeholder="禁止输入">
         </div>
      </div>
      <div class="form-group">
         <label for="disabledSelect"  class="col-sm-2 control-label">
            禁用选择菜单（Fieldset disabled）         </label>
         <div class="col-sm-10">
            <select id="disabledSelect" class="form-control">
               <option>禁止选择</option>
            </select>
         </div>
      </div>
   </fieldset>
   <div class="form-group has-success">
      <label class="col-sm-2 control-label" for="inputSuccess">
         输入成功      </label>
      <div class="col-sm-10">
         <input type="text" class="form-control" id="inputSuccess">
      </div>
   </div>
   <div class="form-group has-warning">
      <label class="col-sm-2 control-label" for="inputWarning">
         输入警告      </label>
      <div class="col-sm-10">
         <input type="text" class="form-control" id="inputWarning">
      </div>
   </div>
   <div class="form-group has-error">
      <label class="col-sm-2 control-label" for="inputError">
         输入错误      </label>
      <div class="col-sm-10">
         <input type="text" class="form-control" id="inputError">
      </div>
   </div></form></body></html>
```

结果如下所示：

![表单控件状态](https://7n.w3cschool.cn/attachments/image/20160225/1456379124459729.jpg)



## 表单控件大小

您可以分别使用 class *.input-lg* 和 *.col-lg-\** 来设置表单的高度和宽度。下面的实例演示了这点：

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 表单控件大小</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><form role="form">
   <div class="form-group">
      <input class="form-control input-lg" type="text" 
         placeholder=".input-lg">
   </div>

   <div class="form-group">
      <input class="form-control" type="text" placeholder="默认输入">
   </div>

   <div class="form-group">
      <input class="form-control input-sm" type="text" 
         placeholder=".input-sm">
   </div>
   <div class="form-group">
   </div>
   <div class="form-group">
      <select class="form-control input-lg">
         <option value="">.input-lg</option>
      </select>
   </div>
   <div class="form-group">
      <select class="form-control">
         <option value="">默认选择</option>
      </select>
   </div>
   <div class="form-group">
      <select class="form-control input-sm">
         <option value="">.input-sm</option>
      </select>
   </div>

   <div class="row">
      <div class="col-lg-2">
         <input type="text" class="form-control" placeholder=".col-lg-2">
      </div>
      <div class="col-lg-3">
         <input type="text" class="form-control" placeholder=".col-lg-3">
      </div>
      <div class="col-lg-4">
         <input type="text" class="form-control" placeholder=".col-lg-4">
      </div>
   </div></form></body></html>
```

结果如下所示：

![表单控件大小](https://7n.w3cschool.cn/attachments/image/20160225/1456379124438620.jpg)



## 表单帮助文本

Bootstrap 表单控件可以在输入框 input 上有一个块级帮助文本。为了添加一个占用整个宽度的内容块，请在 <input> 后使用 *.help-block*。下面的实例演示了这点：

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 表单帮助文本</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><form role="form">
   <span>帮助文本实例</span>
   <input class="form-control" type="text" placeholder="">
   <span class="help-block">一个较长的帮助文本块，超过一行，
   需要扩展到下一行。本实例中的帮助文本总共有两行。</span></form></body></html>
```

结果如下所示：

![表单帮助文本](https://7n.w3cschool.cn/attachments/image/20160225/1456379124720163.jpg)



# 按钮

任何带有 class **.btn** 的元素都会继承圆角灰色按钮的默认外观。但是 Bootstrap 提供了一些选项来定义按钮的样式，具体如下表所示：

以下样式可用于[](https://www.w3cschool.cn/htmltags/tag-a.html), [](https://www.w3cschool.cn/html5/html5-button.html), 或 [](https://www.w3cschool.cn/htmltags/tag-input.html) 元素上：

| 类           | 描述                                    | 实例                                                         |
| ------------ | --------------------------------------- | ------------------------------------------------------------ |
| .btn         | 为按钮添加基本样式                      | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_btn) |
| .btn-default | 默认/标准按钮                           | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_btn-default) |
| .btn-primary | 原始按钮样式（未被操作）                | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_btn-primary) |
| .btn-success | 表示成功的动作                          | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_btn-success) |
| .btn-info    | 该样式可用于要弹出信息的按钮            | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_btn-info) |
| .btn-warning | 表示需要谨慎操作的按钮                  | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_btn-warning) |
| .btn-danger  | 表示一个危险动作的按钮操作              | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_btn-danger) |
| .btn-link    | 让按钮看起来像个链接 (仍然保留按钮行为) | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_btn-link) |
| .btn-lg      | 制作一个大按钮                          | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_btn-lg) |
| .btn-sm      | 制作一个小按钮                          | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_btn-sm) |
| .btn-xs      | 制作一个超小按钮                        | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_btn-xs) |
| .btn-block   | 块级按钮(拉伸至父元素100%的宽度)        | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_btn-block) |
| .active      | 按钮被点击                              | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_btn_active) |
| .disabled    | 禁用按钮                                | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_btn_disabled) |

下面的实例演示了上面所有的按钮 class：

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 按钮选项</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body>
<!-- 标准的按钮 -->
<button type="button" class="btn btn-default">默认按钮</button>
<!-- 提供额外的视觉效果，标识一组按钮中的原始动作 -->
<button type="button" class="btn btn-primary">原始按钮</button>
<!-- 表示一个成功的或积极的动作 -->
<button type="button" class="btn btn-success">成功按钮</button>
<!-- 信息警告消息的上下文按钮 -->
<button type="button" class="btn btn-info">信息按钮</button>
<!-- 表示应谨慎采取的动作 -->
<button type="button" class="btn btn-warning">警告按钮</button>
<!-- 表示一个危险的或潜在的负面动作 -->
<button type="button" class="btn btn-danger">危险按钮</button>
<!-- 并不强调是一个按钮，看起来像一个链接，但同时保持按钮的行为 -->
<button type="button" class="btn btn-link">链接按钮</button>
</body></html>
```

结果如下所示：

![按钮选项](https://7n.w3cschool.cn/attachments/image/20160225/1456379158141151.jpg)



## 按钮大小

下表列出了获得各种大小按钮的 class：

| Class      | 描述                                         |
| ---------- | -------------------------------------------- |
| .btn-lg    | 这会让按钮看起来比较大。                     |
| .btn-sm    | 这会让按钮看起来比较小。                     |
| .btn-xs    | 这会让按钮看起来特别小。                     |
| .btn-block | 这会创建块级的按钮，会横跨父元素的全部宽度。 |

下面的实例演示了上面所有的按钮 class：

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 按钮大小</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><p>
   <button type="button" class="btn btn-primary btn-lg">
      大的原始按钮   </button>
   <button type="button" class="btn btn-default btn-lg">
      大的按钮   </button></p><p>
   <button type="button" class="btn btn-primary">
      默认大小的原始按钮   </button>
   <button type="button" class="btn btn-default">
      默认大小的按钮   </button></p><p>
   <button type="button" class="btn btn-primary btn-sm">
      小的原始按钮   </button>
   <button type="button" class="btn btn-default btn-sm">
      小的按钮   </button></p><p>
   <button type="button" class="btn btn-primary btn-xs">
      特别小的原始按钮   </button>
   <button type="button" class="btn btn-default btn-xs">
      特别小的按钮   </button></p><p>
   <button type="button" class="btn btn-primary btn-lg btn-block">
      块级的原始按钮   </button>
   <button type="button" class="btn btn-default btn-lg btn-block">
      块级的按钮   </button></p></body></html>
```

结果如下所示：

![按钮大小](https://7n.w3cschool.cn/attachments/image/20160225/1456379158428602.jpg)



## 按钮状态

Bootstrap 提供了激活、禁用等按钮状态的 class，下面将进行详细讲解。

### 激活状态

按钮在激活时将呈现为被按压的外观（深色的背景、深色的边框、阴影）。

下表列出了让按钮元素和锚元素呈激活状态的 class：

| 元素     | Class                                                |
| -------- | ---------------------------------------------------- |
| 按钮元素 | 添加 **.active** class 来显示它是激活的。            |
| 锚元素   | 添加 **.active** class 到 <a> 按钮来显示它是激活的。 |

下面的实例演示了这点：

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 按钮激活状态</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><p>
   <button type="button" class="btn btn-default btn-lg ">
      默认按钮   </button>
   <button type="button" class="btn btn-default btn-lg active">
      激活按钮   </button></p><p>
   <button type="button" class="btn btn-primary btn-lg ">
      原始按钮   </button>
   <button type="button" class="btn btn-primary btn-lg active">
      激活的原始按钮   </button></p></body></html>
```

结果如下所示：

![按钮激活状态](https://7n.w3cschool.cn/attachments/image/20160225/1456379158638183.jpg)

### 禁用状态

当您禁用一个按钮时，它的颜色会变淡 50%，并失去渐变。

下表列出了让按钮元素和锚元素呈禁用状态的 class：

| 元素     | Class                                                        |
| -------- | ------------------------------------------------------------ |
| 按钮元素 | 添加 **disabled** *属性* 到 <button> 按钮。                  |
| 锚元素   | 添加 **disabled** *class* 到 <a> 按钮。 *注意：该 class 只会改变 <a> 的外观，不会改变它的功能。在这里，您需要使用自定义的 JavaScript 来禁用链接。* |

下面的实例演示了这点：

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 按钮禁用状态</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><p>
   <button type="button" class="btn btn-default btn-lg">
      默认按钮   </button>
   <button type="button" class="btn btn-default btn-lg" disabled="disabled">
      禁用按钮   </button></p><p>
   <button type="button" class="btn btn-primary btn-lg ">
      原始按钮   </button>
   <button type="button" class="btn btn-primary btn-lg" disabled="disabled">
      禁用的原始按钮   </button></p><p>
   <a href="#" class="btn btn-default btn-lg" role="button">
      链接   </a>
   <a href="#" class="btn btn-default btn-lg disabled" role="button">
      禁用链接   </a></p><p>
   <a href="#" class="btn btn-primary btn-lg" role="button">
      原始链接   </a>
   <a href="#" class="btn btn-primary btn-lg disabled" role="button">
      禁用的原始链接   </a></p></body></html>
```

结果如下所示：

![按钮禁用状态](https://7n.w3cschool.cn/attachments/image/20160225/1456379158798341.jpg)



## 按钮标签

您可以在 <a>、<button> 或 <input> 元素上使用按钮 class。但是建议您在 <button> 元素上使用按钮 class，避免跨浏览器的不一致性问题。

下面的实例演示了这点：

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 按钮标签</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><a class="btn btn-default" href="#" role="button">链接</a><button class="btn btn-default" type="submit">按钮</button><input class="btn btn-default" type="button" value="输入"><input class="btn btn-default" type="submit" value="提交"></body></html>
```

结果如下所示：

![按钮标签](https://7n.w3cschool.cn/attachments/image/20160225/1456379158894212.jpg)



#  图片

Bootstrap 提供了三个可对图片应用简单样式的 class：

- *.img-rounded*：添加 *border-radius:6px* 来获得图片圆角。
- *.img-circle*：添加 *border-radius:500px* 来让整个图片变成圆形。
- *.img-thumbnail*：添加一些内边距（padding）和一个灰色的边框。

请看下面的实例演示：

```
<!DOCTYPE html>
<html>
<head>
   <title>Bootstrap 实例 - 图片</title>
   <link href="//cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
   <script src="//cdn.bootcss.com/jquery/2.1.1/jquery.min.js"></script>
   <script src="//cdn.bootcss.com/bootstrap/3.3.6/js/bootstrap.min.js"></script>
</head>
<body>

<img src="https://www.w3cschool.cn/statics/demosource/download.png" class="img-rounded">
<img src="https://www.w3cschool.cn/statics/demosource/download.png" class="img-circle">
<img src="https://www.w3cschool.cn/statics/demosource/download.png" class="img-thumbnail">

</body>
</html>
```



结果如下所示：

![图片](https://7n.w3cschool.cn/attachments/uploads/2014/06/image_demo.jpg)

## `<img>` 类

以下类可用于任何图片中。

| 类              | 描述                              | 实例                                                         |
| --------------- | --------------------------------- | ------------------------------------------------------------ |
| .img-rounded    | 为图片添加圆角 (IE8 不支持)       | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_img-rounded) |
| .img-circle     | 将图片变为圆形 (IE8 不支持)       | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_img-circle) |
| .img-thumbnail  | 缩略图功能                        | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_img-thumbnail) |
| .img-responsive | 图片响应式 (将很好地扩展到父元素) | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_img-responsive) |

## 响应式图片

通过在`<img>`添加 .img-responsive 类来让图片支持响应式设计。 图片将很好地扩展到父元素。

.img-responsive 类将 max-width: 100%; 和 height: auto; 样式应用在图片上：



<img src="cinqueterre.jpg" class="img-responsive" alt="Cinque Terre">

尝试一下 »

**提示：**通过使用Bootstrap的图片响应式类.img-responsive，你可以让[图片适配手机显示](https://www.w3cschool.cn/codecamp/make-images-mobile-responsive.html)！





# 辅助类

## 文本

以下不同的类展示了不同的文本颜色。如果文本是个链接鼠标移动到文本上会变暗：

| 类            | 描述                        | 实例                                                         |
| ------------- | --------------------------- | ------------------------------------------------------------ |
| .text-muted   | "text-muted" 类的文本样式   | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_text-muted) |
| .text-primary | "text-primary" 类的文本样式 | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_text-primary) |
| .text-success | "text-success" 类的文本样式 | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_text-success) |
| .text-info    | "text-info" 类的文本样式    | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_text-info) |
| .text-warning | "text-warning" 类的文本样式 | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_text-warning) |
| .text-danger  | "text-danger" 类的文本样式  | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_text-danger) |

## 背景

以下不同的类展示了不同的背景颜色。 如果文本是个链接鼠标移动到文本上会变暗：

| 类          | 描述                             | 实例                                                         |
| ----------- | -------------------------------- | ------------------------------------------------------------ |
| .bg-primary | 表格单元格使用了 "bg-primary" 类 | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_bg-primary) |
| .bg-success | 表格单元格使用了 "bg-success" 类 | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_bg-success) |
| .bg-info    | 表格单元格使用了 "bg-info" 类    | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_bg-info) |
| .bg-warning | 表格单元格使用了 "bg-warning" 类 | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_bg-warning) |
| .bg-danger  | 表格单元格使用了 "bg-danger" 类  | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_bg-danger) |

## 其他

| 类                 | 描述                                                         | 实例                                                         |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| .pull-left         | 元素浮动到左边                                               | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_pull-left) |
| .pull-right        | 元素浮动到右边                                               | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_pull-left) |
| .center-block      | 设置元素为 display:block 并居中显示                          | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_center-block) |
| .clearfix          | 清除浮动                                                     | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_clearfix) |
| .show              | 强制元素显示                                                 | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_show) |
| .hidden            | 强制元素隐藏                                                 | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_hidden) |
| .sr-only           | 除了屏幕阅读器外，其他设备上隐藏元素                         | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_sr-only) |
| .sr-only-focusable | 与 .sr-only 类结合使用，在元素获取焦点时显示(如：键盘操作的用户) | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_sr-only-focusable) |
| .text-hide         | 将页面元素所包含的文本内容替换为背景图                       | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_text-hide) |
| .close             | 显示关闭按钮                                                 | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_close) |
| .caret             | 显示下拉式功能                                               | [尝试一下](https://www.w3cschool.cn/tryrun/showhtml/trybs_ref_hlp_caret) |

------

## 更多实例

### 关闭图标

使用通用的关闭图标来关闭模态框和警告框。使用 class **close** 得到关闭图标。

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 关闭图标</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><p>关闭图标实例   <button type="button" class="close" aria-hidden="true">
      &times;   </button></p></body></html>
```

结果如下所示：

![关闭图标](https://7n.w3cschool.cn/attachments/image/20160225/1456379230883834.jpg)

### 插入符

使用插入符表示下拉功能和方向。使用带有 class **caret** 的 <span> 元素得到该功能。

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 插入符</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><p>插入符实例   <span class="caret"></span></p></body></html>
```

结果如下所示：

![插入符](https://7n.w3cschool.cn/attachments/image/20160225/1456379230934490.jpg)

### 快速浮动

您可以分别使用 class **pull-left** 或 **pull-right** 来把元素向左或向右浮动。下面的实例演示了这点。

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 快速浮动</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><div class="pull-left">
   向左快速浮动</div><div class="pull-right">
   向右快速浮动</div></body></html>
```

结果如下所示：

![快速浮动](https://7n.w3cschool.cn/attachments/image/20160225/1456379230578463.jpg)

如需对齐导航栏中的组件，请使用 **.navbar-left** 或 **.navbar-right** 代替。请查看 [Bootstrap 导航栏](https://www.w3cschool.cn/bootstrap/cqye4fnh.html)。

### 内容居中

使用 class **center-block** 来居中元素。

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 居中内容块</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><div class="row">
   <div class="center-block" style="width:200px;background-color:#ccc;">
      这是 center-block 实例   </div></div></body></html>
```

结果如下所示：

![居中内容块](https://7n.w3cschool.cn/attachments/image/20160225/1456379230954300.jpg)

### 清除浮动

如需清除元素的浮动，请使用 **.clearfix** class。

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 清除浮动</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><div class="clearfix"  style="background: #D8D8D8;border: 1px solid #000;padding: 10px;">
   <div class="pull-left" style="background:#58D3F7;">
      向左快速浮动   </div>
   <div class="pull-right" style="background: #DA81F5;">
      向右快速浮动   </div></div></body></html>
```

结果如下所示：

![清除浮动](https://7n.w3cschool.cn/attachments/image/20160225/1456379230496562.jpg)

### 显示和隐藏内容

您可以通过使用 class **.show** 和 **.hidden** 来强行设置元素显示或隐藏（包括屏幕阅读器）。

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 显示和隐藏内容</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><div class="row" style="padding: 91px 100px 19px 50px;">
   <div class="show" style="left-margin:10px;width:300px;background-color:#ccc;">
      这是 show class 的实例   </div>
   <div class="hidden" style="width:200px;background-color:#ccc;">
      这是 hide class 的实例   </div></div></body></html>
```

结果如下所示：

![显示和隐藏内容](https://7n.w3cschool.cn/attachments/image/20160225/1456379230870389.jpg)

### 屏幕阅读器

您可以通过使用 class **.sr-only** 来把元素对所有设备隐藏，除了屏幕阅读器。

```
<!DOCTYPE html><html><head>
   <title>Bootstrap 实例 - 屏幕阅读器</title>
   <link rel="stylesheet" href="//apps.bdimg.com/libs/bootstrap/3.3.0/css/bootstrap.min.css">
   <script src="//apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js"></script>
   <script src="//apps.bdimg.com/libs/bootstrap/3.3.0/js/bootstrap.min.js"></script></head><body><div class="row" style="padding: 91px 100px 19px 50px;">
   <form class="form-inline" role="form">
   <div class="form-group">
      <label class="sr-only" for="email">Email 地址</label>
      <input type="email" class="form-control" placeholder="Enter email">
   </div>
   <div class="form-group">
      <label class="sr-only" for="pass">密码</label>
      <input type="password" class="form-control" placeholder="Password">
   </div>
   </form></div></body></html>
```

结果如下所示：

![屏幕阅读器](https://7n.w3cschool.cn/attachments/image/20160225/1456379230219171.jpg)

在这里，我们看到两个 input 类型的 label 标签都带有 class **sr-only**，因此标签将只对屏幕阅读器可见。



# 响应式实用工具

Bootstrap 提供了一些辅助类，以便更快地实现对移动设备友好的开发。这些可以通过媒体查询结合大型、小型和中型设备，实现内容对设备的显示和隐藏。

需要谨慎使用这些工具，避免在同一个站点创建完全不同的版本。**响应式实用工具目前只适用于块和表切换。**

|               | 超小屏幕 手机 (<768px) | 小屏幕 平板 (≥768px) | 中等屏幕 桌面 (≥992px) | 大屏幕 桌面 (≥1200px) |
| ------------- | ---------------------- | -------------------- | ---------------------- | --------------------- |
| .visible-xs-* | 可见                   | 隐藏                 | 隐藏                   | 隐藏                  |
| .visible-sm-* | 隐藏                   | 可见                 | 隐藏                   | 隐藏                  |
| .visible-md-* | 隐藏                   | 隐藏                 | 可见                   | 隐藏                  |
| .visible-lg-* | 隐藏                   | 隐藏                 | 隐藏                   | 可见                  |
| .hidden-xs    | 隐藏                   | 可见                 | 可见                   | 可见                  |
| .hidden-sm    | 可见                   | 隐藏                 | 可见                   | 可见                  |
| .hidden-md    | 可见                   | 可见                 | 隐藏                   | 可见                  |
| .hidden-lg    | 可见                   | 可见                 | 可见                   | 隐藏                  |

从 v3.2.0 版本起，形如 .visible-*-* 的类针对每种屏幕大小都有了三种变体，每个针对 CSS 中不同的 display 属性，列表如下：

| 类组                    | CSS display            |
| ----------------------- | ---------------------- |
| .visible-*-block        | display: block;        |
| .visible-*-inline       | display: inline;       |
| .visible-*-inline-block | display: inline-block; |

因此，以超小屏幕（xs）为例，可用的 .visible-*-* 类是：.visible-xs-block、.visible-xs-inline 和 .visible-xs-inline-block。

.visible-xs、.visible-sm、.visible-md 和 .visible-lg 类也同时存在。但是从 v3.2.0 版本开始不再建议使用。除了 <table> 相关的元素的特殊情况外，它们与 .visible-*-block 大体相同。

**提示：**Bootstrap可以通过一个清晰的布局来创建表，具体请参考“[Bootstrap 表格](https://www.w3cschool.cn/bootstrap/bootstrap-v2-tables.html)”部分的内容！

## 打印类

下表列出了打印类。使用这些切换打印内容。

| class                                                        | 浏览器 | 打印机 |
| ------------------------------------------------------------ | ------ | ------ |
| .visible-print-block .visible-print-inline .visible-print-inline-block | 隐藏   | 可见   |
| .hidden-print                                                | 可见   | 隐藏   |

## 实例

下面的实例演示了上面所列举的帮助器类的用法。调整浏览器的窗口大小，或者在不同的设备上加载实例，测试响应式实用工具类。

```
<!DOCTYPE html>
<html>
<head>
   <title>Bootstrap 实例 - 响应式实用工具</title>
   <link href="//libs.baidu.com/bootstrap/3.0.3/css/bootstrap.min.css" rel="stylesheet">
   <script src="//libs.baidu.com/jquery/2.0.0/jquery.min.js"></script>
   <script src="//libs.baidu.com/bootstrap/3.0.3/js/bootstrap.min.js"></script>
</head>
<body>

<div class="container" style="padding: 40px;">
   <div class="row visible-on">
      <div class="col-xs-6 col-sm-3" style="background-color: #dedef8;          box-shadow: inset 1px -1px 1px #444, inset -1px 1px 1px #444;">
         <span class="hidden-xs">特别小型</span>
         <span class="visible-xs">✔ 在特别小型设备上可见</span>
      </div>
      <div class="col-xs-6 col-sm-3" style="background-color: #dedef8;          box-shadow: inset 1px -1px 1px #444, inset -1px 1px 1px #444;">
         <span class="hidden-sm">小型</span>
         <span class="visible-sm">✔ 在小型设备上可见</span>
      </div>
      <div class="clearfix visible-xs"></div>
      <div class="col-xs-6 col-sm-3" style="background-color: #dedef8;          box-shadow: inset 1px -1px 1px #444, inset -1px 1px 1px #444;">
         <span class="hidden-md">中型</span>
         <span class="visible-md">✔ 在中型设备上可见</span>
      </div>
      <div class="col-xs-6 col-sm-3" style="background-color: #dedef8;          box-shadow: inset 1px -1px 1px #444, inset -1px 1px 1px #444;">
         <span class="hidden-lg">大型</span>
         <span class="visible-lg">✔ 在大型设备上可见</span>
      </div>
</div>

</body>
</html>
```



结果如下所示：

![响应式实用工具](https://7n.w3cschool.cn/attachments/uploads/2014/06/resonsive_utilies_demo.jpg)

**勾号（✔）** 表示元素在当前视口中可见。









# 修改bootstrap 4 样式



## 同名CSS覆盖

```
<style>
	.btn-primary {
		background: green;
	}
</style>
```



## 修改源码构建

```
node-sass --output-style expanded bootstrap-custom/scss/bootstrap.scss>bootstrap-custom/dist/css/bootstrap.css
```



## 引用scss源文件修改变量

```
$primary: red;

@import './bootstrap-custom/scss/bootstrap';
```









