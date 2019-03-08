---
title: "text_operation 文本操作"
date: 2018-11-18 21:08
---


[TOC]

# font



## font-size 大小

Chrome 浏览器默认字体像素为16px，即2em = 32px



* 可选参数 

```
xx-small
x-small
small
medium
large
x-large
xx-large
smaller
larger
```



## font-family 字体

第一个字体找不到，导入第二种字体

```
font-family: Verdana, Arial, "Times New Roman"
font-family: "微软雅黑","宋体"
```

> 英文字体中间有空格需要加引号
>
> 中文字体默认需要加引号



### 中文字体的英文名称



| 字体中文名 | 字体英文名           | Unicode 编码 |
| ---------- | -------------------- | ------------ |
| 宋体       | SimSun（浏览器默认） |              |
| 黑体       | SimHei               |              |
| 微软雅黑   | Microsoft Yahei      |              |
| 微软正黑体 | Microsoft JhengHei   |              |
| 楷体       | KaiTi                |              |
| 新宋体     | NSimSun              |              |
| 仿宋       | FangSong             |              |



| 字体名称    | Unicode 编码         |
| ----------- | -------------------- |
| 宋体        | \5B8B\4F53           |
| 新宋体      | \65B0\5B8B\4F53      |
| 黑体        | \9ED1\4F53           |
| 微软雅黑    | \5FAE\8F6F\96C5\9ED1 |
| 楷体_GB2312 | \6977\4F53_GB2312    |
| 隶书        | \96B6\4E66           |
| 幼园        | \5E7C\5706           |



### 最终字体

衬线字体serif

所有字体都不存在，系统自动寻找sans-serif 及serif

如果将sans-serif放到最前面，后面的全部失效



### taobao 所用方法

```
textarea{font:12px/1.5 tahoma,arial,'Hiragino Sans GB','\5b8b\4f53',sans-serif}
```



## font-weight 粗细

属性值

```
normal
bold
bolder
lighter
100-900 （100的整数倍）
```



### font-style 风格

默认值 normal

斜体 italic

倾斜显示 oblique



## font 综合设置

格式语法，注意顺序

```
{font: font-style font-weight font-size/line-height font-family}
```



## 字间距

### letter-spacing 

字符与字符之间的空白，默认为normal

```
p {
    letter-spacing: 10px;
}
```



### word-spacing

英文单词之间的间距，对中文无效。默认为normal

```
p {
    word-spacing: 10px;
}
```



# text

## text-align 文本对齐

```
div {
    text-align: left;
}
div {
    text-align: center;
}
div {
    text-align: right;
}
```



## text-indent 首行缩进

只能设置于块级标签，其属性值可为不同单位的数值，em字符宽度的倍数，浏览器窗口宽度百分比等



## text-decoration 文本装饰

设置文本的下划线，上划线，删除线等



参数

```
none 没有装饰
underline 下划线
overline 上划线
line-through 删除线
```



```
p {
    text-decoration: underline line-through;
}

p1 {
    text-decoration: none;
    border: 1px solid black;
    width: 200px;
    white-space: nowrap;
    overflow: hidden;  /* 隐藏超出部分 */
}
```



## white-space 空白符处理

默认HTML无论多少空格，只显示一个字符的空白



参数

```
normal 常规，文本中的空格，空行无效，满行（到达区域边界）后自动换行
pre 预格式化，按文档的书写格式保留空格，空行原样显示
nowrap 空行空格无效，强制文本不能换行，除非遇到换行标记
<br /> 内容超出元素边界也不换行，若超出浏览器页面则自动添加滚动条
```





## text-shadow 文本阴影

```
p {
    text-shadow: 1px 5px 2px #FF0000;
}
```



## word-wrap

规定文本换行规则

```
p {
    word-wrap: normal;
}
```

