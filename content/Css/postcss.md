---
title: "postcss"
date: 2019-09-02 08:51
---
[TOC]



# PostCSS



## import 模块合并

```
const atImport = require('postcss-import');

module.exports = {
		plugins: [
				atImport,
				autoprefixer({
						browser:['Firefox > 30']
				}),
		]
};
```



## autoprefixer自动加前缀

```
const autoprefixer = require('autoprefixer');

module.exports = {
		plugins: [
				autoprefixer({
						browsers:['Firefox > 30']
				}),
		]
};
```

```
postcss src/plugins-main.css -o build/plugins-main.css
```



## cssnano 压缩代码

```
const autoprefixer = require('autoprefixer');

module.exports = {
		plugins: [
				autoprefixer({
						browsers:['Firefox > 30']
				}),
				cssnano
		]
};
```

```
postcss src/plugins-main.css -o build/plugins-main.css
```





## cssnext 使用css新特性





## precss 变量，mixin， 循环

