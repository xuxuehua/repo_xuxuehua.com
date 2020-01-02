---
title: "basic_knowledge"
date: 2018-11-06 13:50
---

[TOC]

# basic_knowledge

## 简介

Hyper Text Markup Language 超文本标记语言

通过HTML标记对网页的文本，图像，声音等内容进行描述

## DOM

Document Object Model 

浏览器加载html文件，每一个html tag转换成DOM树中的对象

![img](https://www.w3schools.com/js/pic_htmltree.gif)

## 结构

```
<!DOCTYPE>
<html>
    <head>
    </head>
    <body>
        <h1>This is first page</h1>
        <p>This is paragraph</p>
        <p>This is paragraph</p>
        <p>This is paragraph</p>
        <p>This is paragraph</p>
        <p>This is paragraph</p>
    </body>
</html>
```

## 特点

成对出现，有开有闭合，尖括号包住了标签名，结束标签增加斜线

用于绘画的canvas标签

用于媒介回放的video和audio 元素

## URL协议

Uniform Resource Locator 统一资源定位符

是互联网上资源的位置和访问方法的一种简洁的表示，每一个文件都有唯一的URL，包含的信息指出文件的位置

### 协议格式

```
scheme://host.domain:port/path/filename
```

> scheme: 服务类型，如http, https, ftp
> 
> host: 定义域主机，如www
> 
> domain: 定义因特网域名, 如xuxuehua.com
> 
> port: 主机端口号， http默认是80
> 
> path: 服务器路径，省略则位于网站的根目录
> 
> filename: 定义文档资源名称

## 绝对路径

从根目录计算

## 相对路径

```
/  ../   ../../  
```

## 图片格式

### GIF

一种无损动态图片格式，支持全透明或全不透明

最多处理256种颜色

常用于Logo，小图标等其他色彩相对单一的图像

### JPG/JPEG

JPEG 是一种有损压缩格式，文件体积小，利于网络传输

常用于广告，宣传

### PNG

PNG-8和真色彩PNG-24，PNG-32

相对GIF，体积更小，支持全透明，半透明，全不透明。

颜色过渡平滑，不支持动画

半透明只能使用PNG-24

# 
