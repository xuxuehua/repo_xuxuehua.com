---
title: "form"
date: 2019-07-21 11:07

---

[TOC]

# `<form>` 表单

表单本身在html中不可见

完整表单由表单控件，提示信息，表单域构成

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>form example</title>
</head>
<body>
    <form action="backend processing address" method="POST">
        <input type="text" name="text_name" value="123">
        <input type="button" value="add">
    </form>
</body>
</html>
```

## `<input>` 表单输入

### autofocus 自动焦点

在页面加载的时候，域自动获得焦点，即加载光标到输入框

```
<form action="" method="get">
<input type="submit" value="submit" autofocus />
</form>
```

### 

### autocomplete 属性

form或者input 域拥有自动完成功能

浏览器会记录输入的内容

```
<form action="" method="get" autocomplete="off">
<input type="submit" value="submit">
```



### placeholder 文本框提示



### required 验证

当前表单元素必须有验证



### multiple 多选

```
<input type="file" name="file" multiple="multiple">
```

## novalidate 关闭验证

```
<form action="" method="get" autocomplete="off" novalidate>
<input type="submit" value="submit">
```

enctype:     application/x-www-form-urlencoded multipart/form-data text/plain







## type 类型

```
<input type="email" name="email">

<input type="date" name="date">

<input type="month" name="month">

<input type="week" name="week">

<input type="time" name="time">

<input type="datetime" name="datetime">

<input type="datetime-local" name="datetime-local">

<input type="range" name="range" min="1" max="10"> //拖拉条
```



type: text 文本框

password 密码框

radio 单选

```
<form action="">
    <input type="radio" name="r" id="r1">
    <label for="r1">r1</label>
    <input type="radio" name="r" id="r2">
    <label for="r2">r2</label>
</form>
```

checkbox 复选 

```
    <form action="">
        <input type="checkbox" name="ckk_lq" value="1", id="ck_lq_id">
        <label for="ch_lq_id">myinfo1</label>
        <input type="checkbox" name="ckk_lq" id="ck_zq_id">
        <label for="ch_zq_id">myinfo2</label>
    </form>
```

​    submit

​    reset

​    image 图像式提交按钮

​    hidden 隐藏域

​    file 文件域

# 

### `<datalist>` 数据列表

标签需要有自己的id值，然后配合list属性来使用，才可以有数据列表的效果

```
<input type="text" value="" name="txtList" list="url_list" />
<datalist id="url_list">
    <option value="https://www.xurick.com">Xu Rick</option>
    <option value="https://www.xuxuehua.com">Xu Xuehua</option>
    <option value="https://www.english.page">English Page</option>
</datalist>
```



## `<select>` 菜单和列表

## `<option>` 菜单和列表项目

## `<textarea>` 文字域

## `<optgroup>` 菜单和项目分组

# form 属性

name： 控件名称,在没有method的情况下，会把对应的key value 传入到URL中

value： input控件中默认文本值

size： input控件在页面中显示宽度

readonly：只读，不能修改

disabled：显示控件为灰色

checked：默认被选中项

maxlength：控件允许的最多字符数

action:  路径， #表示当前页面

target： _blank, _self, _parent, _top



## method

### post

用户输入的数据通过请求体进行传输，即Form Data



