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



## `<select>` 菜单和列表



## `<option>` 菜单和列表项目



## `<textarea>` 文字域 



## `<optgroup>` 菜单和项目分组





## type 属性

type: text 文本框

​	password 密码框

​	radio 单选

```
<form action="">
    <input type="radio" name="r" id="r1">
    <label for="r1">r1</label>
    <input type="radio" name="r" id="r2">
    <label for="r2">r2</label>
</form>
```

​	checkbox 复选 

```
    <form action="">
        <input type="checkbox" name="ckk_lq" value="1", id="ck_lq_id">
        <label for="ch_lq_id">myinfo1</label>
        <input type="checkbox" name="ckk_lq" id="ck_zq_id">
        <label for="ch_zq_id">myinfo2</label>
    </form>
```



​	submit

​	reset

​	image 图像式提交按钮

​	hidden 隐藏域

​	file 文件域





## form 属性

name： 控件名称

value： input控件中默认文本值

size： input控件在页面中显示宽度

readonly：只读，不能修改

disabled：显示控件为灰色

checked：默认被选中项

maxlength：控件允许的最多字符数



action:  路径， #表示当前页面

method： get， post

target： _blank, _self, _parent, _top

enctype: 	application/x-www-form-urlencoded multipart/form-data text/plain





