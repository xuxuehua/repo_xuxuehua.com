---
title: "mvvm"
date: 2018-08-06 11:11
---

[TOC]

# mvvm 

[MVVM](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93viewmodel)是Model-View-ViewModel的缩写。

由于前端开发混合了HTML、CSS和JavaScript，而且页面众多，所以，代码的组织和维护难度其实更加复杂，这就是MVVM出现的原因。

现在，随着前端页面越来越复杂，用户对于交互性要求也越来越高，想要写出Gmail这样的页面，仅仅用jQuery是远远不够的。MVVM模型应运而生。

MVVM最早由微软提出来，它借鉴了桌面应用程序的MVC思想，在前端页面中，把Model用纯JavaScript对象表示，View负责显示，两者做到了最大限度的分离。

把Model和View关联起来的就是ViewModel。ViewModel负责把Model的数据同步到View显示出来，还负责把View的修改同步回Model。

 

## ViewModel

用JavaScript编写一个通用的ViewModel，这样，就可以复用整个MVVM模型了。



## Vue.js

选择MVVM的目标应该是入门容易，安装简单，能直接在页面写JavaScript，需要更复杂的功能时又能扩展支持



### 安装Vue

安装Vue有很多方法，可以用npm或者webpack。但是我们现在的目标是尽快用起来，所以最简单的方法是直接在HTML代码中像引用jQuery一样引用Vue。可以直接使用CDN的地址，例如：

```
<script src="https://unpkg.com/vue@2.0.1/dist/vue.js"></script>
```

也可以把`vue.js`文件下载下来，放到项目的`/static/js`文件夹中，使用本地路径：

```
<script src="/static/js/vue.js"></script>
```

这里需要注意，`vue.js`是未压缩的用于开发的版本，它会在浏览器console中输出很多有用的信息，帮助我们调试代码。当开发完毕，需要真正发布到服务器时，应该使用压缩过的`vue.min.js`，它会移除所有调试信息，并且文件体积更小。

 

### 编写MVVM

```
<html>
<head>

<!-- 引用jQuery -->
<script src="/static/js/jquery.min.js"></script>

<!-- 引用Vue -->
<script src="/static/js/vue.js"></script>

<script>
// 初始化代码:
$(function () {
    var vm = new Vue({
        el: '#vm',
        data: {
            name: 'Robot',
            age: 15
        }
    });
    window.vm = vm;
});
</script>

</head>

<body>

    <div id="vm">
        <p>Hello, {{ name }}!</p>
        <p>You are {{ age }} years old!</p>
    </div>

</body>
<html>
```



#### 单向绑定

我们创建一个VM的核心代码如下：

```
var vm = new Vue({
    el: '#vm',
    data: {
        name: 'Robot',
        age: 15
    }
});
```

其中，`el`指定了要把Model绑定到哪个DOM根节点上，语法和jQuery类似。这里的`'#vm'`对应ID为`vm`的一个`<div>`节点：

```
<div id="vm">
    ...
</div>
```

在该节点以及该节点内部，就是Vue可以操作的View。Vue可以自动把Model的状态映射到View上，但是*不能*操作View范围之外的其他DOM节点。

然后，`data`属性指定了Model，我们初始化了Model的两个属性`name`和`age`，在View内部的`<p>`节点上，可以直接用`{{ name }}`引用Model的某个属性。

一切正常的话，我们在浏览器中访问`http://localhost:3000/static/index.html`，可以看到页面输出为：

```
Hello, Robot!
You are 15 years old!
```

如果打开浏览器console，因为我们用代码`window.vm = vm`，把VM变量绑定到了window对象上，所以，可以直接修改VM的Model：

```
window.vm.name = 'Bob'
```

执行上述代码，可以观察到页面立刻发生了变化，原来的`Hello, Robot!`自动变成了`Hello, Bob!`。Vue作为MVVM框架会自动监听Model的任何变化，在Model数据变化时，更新View的显示。这种Model到View的绑定我们称为单向绑定。



在Vue中，可以直接写`{{ name }}`绑定某个属性。如果属性关联的是对象，还可以用多个`.`引用，例如，`{{ address.zipcode }}`。

另一种单向绑定的方法是使用Vue的指令`v-text`，写法如下：

```
<p>Hello, <span v-text="name"></span>!</p>
```

这种写法是把指令写在HTML节点的属性上，它会被Vue解析，该节点的文本内容会被绑定为Model的指定属性，注意不能再写双花括号`{{ }}`。

 



#### 双向绑定

如果用户更新了View，Model的数据也自动被更新了，这种情况就是双向绑定。

